---
title: Automatikus virtuális géptelepítés az Azure App Konfigurációs rövid útmutatójával
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell-modult és az Azure Resource Manager-sablonokat egy Azure App Configuration Store üzembe helyezéséhez. Ezután használja az értékeket az üzletben egy virtuális gép üzembe helyezéséhez.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126379"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Rövid útmutató: Automatikus virtuális gép telepítés alkalmazáskonfigurációs és erőforrás-kezelő sablonnal

Az Azure PowerShell-modul segítségével azure-erőforrásokat hozhat létre és kezelhet PowerShell-parancsmagokkal vagy parancsfájlokkal. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure PowerShell és az Azure Resource Manager-sablonokat egy Azure App Configuration Store üzembe helyezéséhez. Ezután megtudhatja, hogyan használhatja a kulcsértékeket az üzletben egy virtuális gép üzembe helyezéséhez.

Az előfeltételi sablon használatával hozzon létre egy App Configuration Store, majd adja hozzá a kulcsértékeket az áruházhoz az Azure Portalon vagy az Azure CLI használatával. Az elsődleges sablon meglévő kulcsérték-konfigurációkra hivatkozik egy meglévő konfigurációs tárolóból. A beolvasott értékek a sablon által létrehozott erőforrások tulajdonságainak beállítására szolgálnak, például egy virtuális gépre ebben a példában.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Ehhez a rövid útmutatóhoz az Azure PowerShell-modul szükséges. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable Az` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és adja meg az Azure-hitelesítő adatait az előugró böngészőben:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a rövid útmutatóhoz használni kívánt előfizetést a következő parancsmagok futtatásával. Ne felejtse el `<your subscription name>` helyettesíteni az előfizetés nevét:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup segítségével.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Azure App Configuration Store üzembe helyezése

Mielőtt kulcsértékeket alkalmazhatna a virtuális gépre, rendelkeznie kell egy meglévő Azure App Configuration Store-ral. Ez a szakasz ismerteti, hogyan telepítheti az Azure App Configuration Store egy Azure Resource Manager-sablon használatával. Ha már rendelkezik alkalmazáskonfigurációs tárolóval, a cikk következő szakaszába léphet. 

1. Másolja és illessze be a következő jsonkódot egy *prereq.azuredeploy.json*nevű új fájlba.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Másolja és illessze be a következő jsonkódot egy *prereq.azuredeploy.parameters.json*nevű új fájlba. Cserélje le **a GET-UNIQUE-ot** a configuration store egyedi nevére.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. A PowerShell-ablakban futtassa a következő parancsot az Azure App Configuration Store üzembe helyezéséhez. Ne felejtse el lecserélni az erőforráscsoport nevét, a sablonfájl elérési útját és a sablon paraméterfájl elérési útját.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Virtuálisgép-konfigurációs kulcsértékek hozzáadása

Létrehozhat egy Alkalmazáskonfigurációs áruházat egy Azure Resource Manager-sablon használatával, de kulcsértékeket kell hozzáadnia az Azure Portalon vagy az Azure CLI-n keresztül. Ebben a rövid útmutatóban kulcsértékeket adhat hozzá az Azure Portal használatával.

1. A központi telepítés befejezése után keresse meg az Azure Portal újonnan létrehozott alkalmazáskonfigurációs [tárolóját.](https://portal.azure.com)

1. Válassza **a Beállítások** > **elérési kulcsok lehetőséget**. Jegyezze fel az elsődleges írásvédett kulcskapcsolati karakterláncot. Ezt a kapcsolati karakterláncot később fogja használni az alkalmazás konfigurálásához, hogy kommunikáljon a létrehozott alkalmazáskonfigurációs tárolóval.

1. Válassza a Configuration **Explorer** > **Create lehetőséget** a következő kulcsérték-párok hozzáadásához:

   |Kulcs|Érték|
   |-|-|
   |windowsOsVersion|2019-Adatközpont|
   |lemezméret|1023|
  
   Adja meg a **Címke** *sablonját,* de tartsa üresen **a Tartalomtípust.**

## <a name="deploy-vm-using-stored-key-values"></a>Virtuális gép telepítése tárolt kulcsértékek használatával

Most, hogy kulcsértékeket adott hozzá az áruházhoz, készen áll egy virtuális gép üzembe helyezésére egy Azure Resource Manager-sablon használatával. A sablon a létrehozott **windowsOsVersion** és **diskSizeGB** kulcsokra hivatkozik.

1. Másolja és illessze be a következő jsonkódot egy *azuredeploy.json*nevű új fájlba, vagy töltse le a fájlt az [Azure gyorsindítási sablonjaiból.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json)

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Másolja és illessze be a következő jsonkódot egy *azuredeploy.parameters.json*nevű új fájlba, vagy töltse le a fájlt az [Azure gyorsindítási sablonjaiból.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json)

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Cserélje le a sablon paraméterértékeit a következő értékekre:

   |Paraméter|Érték|
   |-|-|
   |adminPassword|A virtuális gép rendszergazdai jelszava.|
   |appConfigStoreName|Az Azure App Configuration Store neve.|
   |appConfigStoreResourceGroup|Az alkalmazáskonfigurációs tárolót tartalmazó erőforráscsoport.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*lemezméret*|
   |adminUsername|A virtuális gép rendszergazdai felhasználóneve.|
   |storageAccountName|A virtuális géphez társított tárfiók egyedi neve.|
   |tartományNévfelirat|Egyedi tartománynév.|

1. A PowerShell-ablakban futtassa a következő parancsot az Azure App Configuration Store üzembe helyezéséhez. Ne felejtse el lecserélni az erőforráscsoport nevét, a sablonfájl elérési útját és a sablon paraméterfájl elérési útját.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Gratulálunk! Virtuális gép üzembe helyezése az Azure App konfigurációjában tárolt konfigurációk használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje az erőforráscsoportot, az Alkalmazáskonfigurációs tárolót, a virtuális gépés az összes kapcsolódó erőforrást. Ha azt tervezi, hogy az App Configuration Store vagy a virtuális gép a jövőben, kihagyhatja a törlést. Ha nem fogja tovább használni ezt a feladatot, törölje a rövid útmutató által létrehozott összes erőforrást a következő parancsmag futtatásával:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy virtuális gép egy Azure Resource Manager sablon és az Azure App konfigurációja kulcsértékek használatával.

Ha többet szeretne tudni arról, hogyan hozhat létre más alkalmazásokat az Azure App Configuration alkalmazással, folytassa a következő cikkel:

> [!div class="nextstepaction"]
> [Rövid útmutató: Hozzon létre egy ASP.NET Core alkalmazást az Azure App konfigurációjával](quickstart-aspnet-core-app.md)
