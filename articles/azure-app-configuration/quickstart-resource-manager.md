---
title: Automatizált virtuális gépek üzembe helyezése az Azure-alkalmazás konfigurálásával – rövid útmutató
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure PowerShell modul és Azure Resource Manager sablonok egy Azure-alkalmazás konfigurációs tárolójának üzembe helyezéséhez. Ezután a tárolóban lévő értékek használatával helyezzen üzembe egy virtuális gépet.
author: lisaguthrie
ms.author: lcozzens
ms.date: 04/14/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 96d09de73e8b904a8e26eb4f365d34fab1401203
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82137552"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Gyors útmutató: automatikus virtuálisgép-telepítés az alkalmazás-konfigurációval és Resource Manager-sablonnal

A Azure PowerShell modul Azure-erőforrások létrehozásához és kezeléséhez használható PowerShell-parancsmagokkal vagy parancsfájlokkal. Ez a rövid útmutató bemutatja, hogyan használhatók Azure PowerShell és Azure Resource Manager sablonok az Azure-alkalmazások konfigurációs tárolójának üzembe helyezéséhez. Ezután megtudhatja, hogyan használhatja a tárolóban lévő kulcs-értékeket a virtuális gépek üzembe helyezéséhez.

Az előfeltételként szükséges sablonnal létrehozhat egy alkalmazás-konfigurációs tárolót, majd a Azure Portal vagy az Azure CLI használatával adhat hozzá kulcs-értékeket az áruházhoz. Az elsődleges sablon meglévő konfigurációs tárolóból származó létező kulcs-érték konfigurációkra hivatkozik. A beolvasott értékek a sablon által létrehozott erőforrások tulajdonságainak beállítására szolgálnak, például egy virtuális gépre ebben a példában.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Ehhez a rövid útmutatóhoz a Azure PowerShell modul szükséges. Ahhoz, hogy megtudja, melyik verzió van telepítve a helyi gépen, futtassa a `Get-Module -ListAvailable Az` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és adja meg az Azure-beli hitelesítő adatait az előugró böngészőben:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a rövid útmutatóhoz használni kívánt előfizetést a következő parancsmagok futtatásával. Ne felejtse el `<your subscription name>` lecserélni az előfizetés nevét:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Azure-alkalmazás konfigurációs tárolójának üzembe helyezése

A kulcs-értékeknek a virtuális gépre való alkalmazása előtt rendelkeznie kell egy meglévő Azure app Configuration Store-tárolóval. Ez a szakasz részletesen ismerteti, hogyan helyezhet üzembe egy Azure-alkalmazás konfigurációs tárolóját egy Azure Resource Manager sablon használatával. Ha már rendelkezik egy alkalmazás-konfigurációs tárolóval, a cikk következő szakaszába léphet. 

1. Másolja és illessze be a következő JSON-kódot egy új, *prereq. azuredeploy. JSON*nevű fájlba.

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

1. Másolja és illessze be a következő JSON-kódot egy új, *prereq. azuredeploy. Parameters. JSON*nevű fájlba. Cserélje le a **Get-Unique** nevet a konfigurációs tároló egyedi nevére.

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

1. A PowerShell-ablakban futtassa a következő parancsot az Azure-alkalmazás konfigurációs tárolójának üzembe helyezéséhez. Ne felejtse el lecserélni az erőforráscsoport nevét, a sablonfájl elérési útját és a Template paraméter fájljának elérési útját.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" `
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Virtuális gép konfigurációs kulcsának hozzáadása – értékek

Azure Resource Manager sablonnal létrehozhat egy alkalmazás-konfigurációs tárolót, de a Azure Portal vagy az Azure parancssori felület használatával is fel kell vennie a kulcs-értékeket. Ebben a rövid útmutatóban kulcs-értékeket adhat hozzá a Azure Portal használatával.

1. Az üzembe helyezés befejezését követően navigáljon az újonnan létrehozott alkalmazás-konfigurációs tárolóhoz [Azure Portal](https://portal.azure.com).

1. Válassza a **Beállítások** > **hozzáférési kulcsok**elemet. Jegyezze fel az elsődleges írásvédett kulcs-összekapcsolási karakterláncot. Ezt a kapcsolódási karakterláncot később fogja használni az alkalmazás konfigurálásához az Ön által létrehozott alkalmazás-konfigurációs tárolóval való kommunikációhoz.

1. Válassza a Configuration **Explorer** > **Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

   |Kulcs|Érték|
   |-|-|
   |windowsOsVersion|2019 – Datacenter|
   |diskSizeGB|1023|
  
   Adja meg a **címke** *sablonját* , de hagyja üresen a **tartalom típusát** .

## <a name="deploy-vm-using-stored-key-values"></a>Virtuális gép üzembe helyezése tárolt kulcs-értékek használatával

Most, hogy hozzáadta a kulcs-értékeket a tárolóhoz, készen áll a virtuális gép üzembe helyezésére egy Azure Resource Manager sablon használatával. A sablon a létrehozott **windowsOsVersion** és **diskSizeGB** kulcsokra hivatkozik.

> [!WARNING]
> Az ARM-sablonok nem hivatkozhatnak olyan alkalmazás-konfigurációs tároló kulcsaira, amelyeken engedélyezve van a privát kapcsolat.

1. Másolja és illessze be a következő JSON-kódot egy *azuredeploy. JSON*nevű új fájlba, vagy töltse le a fájlt az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

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

1. Másolja és illessze be a következő JSON-kódot egy új, *azuredeploy. Parameters. JSON*nevű fájlba, vagy töltse le a fájlt az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

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

   Cserélje le a sablonban szereplő paramétereket a következő értékekre:

   |Paraméter|Érték|
   |-|-|
   |adminPassword|A virtuális gép rendszergazdai jelszava.|
   |appConfigStoreName|Az Azure-alkalmazás konfigurációs tárolójának neve.|
   |appConfigStoreResourceGroup|Az alkalmazás-konfigurációs tárolót tartalmazó erőforráscsoport.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|A virtuális gép rendszergazdai felhasználóneve.|
   |storageAccountName|A virtuális géphez társított Storage-fiók egyedi neve.|
   |Domainnamelabel értékkel|Egy egyedi tartománynév.|

1. A PowerShell-ablakban futtassa a következő parancsot a virtuális gép üzembe helyezéséhez. Ne felejtse el lecserélni az erőforráscsoport nevét, a sablonfájl elérési útját és a Template paraméter fájljának elérési útját.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>"
       -TemplateFile "<path to azuredeploy.json>" `
       -TemplateParameterFile "<path to azuredeploy.parameters.json>"
   ```

Gratulálunk! Üzembe helyezett egy virtuális gépet az Azure-alkalmazás konfigurációjában tárolt konfigurációk használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, az alkalmazás konfigurációs tárolóját, a virtuális gépet és az összes kapcsolódó erőforrást. Ha a későbbiekben szeretné használni az alkalmazás konfigurációs tárolóját vagy a virtuális gépet, kihagyhatja a törlését. Ha nem kívánja tovább használni ezt a feladatot, törölje az ebben a rövid útmutatóban létrehozott összes erőforrást a következő parancsmag futtatásával:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy virtuális gépet egy Azure Resource Manager sablonnal és a kulcs-értékekkel az Azure-alkalmazás konfigurációjától.

További információt az Azure-alkalmazás konfigurálásával kapcsolatos egyéb alkalmazások létrehozásáról a következő cikkben talál:

> [!div class="nextstepaction"]
> [Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával](quickstart-aspnet-core-app.md)
