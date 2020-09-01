---
title: Azure-beli virtuális gépek rendszerképének ellenőrzése – Azure Marketplace
description: Megtudhatja, hogyan tesztelheti és küldheti el a virtuálisgép-ajánlatokat a kereskedelmi piactéren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: fd8f41f88b6184eee15477c460dc9d2e521d25e6
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144187"
---
# <a name="azure-virtual-machine-image-validation"></a>Azure-beli virtuális gépek rendszerképének ellenőrzése

Ez a cikk azt ismerteti, hogyan lehet tesztelni és elküldeni a virtuális gép (VM) lemezképét a kereskedelmi piactéren, hogy az megfeleljen az Azure Marketplace legújabb közzétételi követelményeinek.

A virtuális gép ajánlatának elküldése előtt végezze el a következő lépéseket:

- Helyezzen üzembe egy Azure-beli virtuális gépet az általánosított rendszerkép használatával.
- Érvényesítések futtatása.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Azure-beli virtuális gép üzembe helyezése az általánosított rendszerkép használatával

Ez a szakasz azt ismerteti, hogyan helyezhet üzembe egy általánosított virtuális merevlemezt (VHD) egy új Azure-beli virtuálisgép-erőforrás létrehozásához. Ehhez a folyamathoz a megadott Azure Resource Manager sablont és Azure PowerShell szkriptet használjuk.

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager sablon előkészítése

Ez a szakasz azt ismerteti, hogyan hozhat létre és helyezhet üzembe egy felhasználó által megadott virtuálisgép-lemezképet. Ezt úgy teheti meg, hogy az operációs rendszer és az adatlemez VHD-lemezképeit egy Azure-beli üzembe helyezett virtuális merevlemezről biztosítja. Ezek a lépések általánosított VHD használatával helyezik üzembe a virtuális gépet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Töltse fel az általánosított operációs rendszer VHD-jét és az adatlemez virtuális merevlemezeit az Azure Storage-fiókjába.
3. A kezdőlapon válassza az **erőforrás létrehozása**lehetőséget, keressen rá a "sablon központi telepítése" kifejezésre, és válassza a **Létrehozás**lehetőséget.
4. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Megjeleníti a sablon kiválasztását.":::

5. Illessze be a következő JSON-sablont a Szerkesztőbe, majd válassza a **Mentés**lehetőséget.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Adja meg a paraméterek értékeit az egyéni központi telepítési tulajdonságlapon.

| ResourceGroupName | Meglévő Azure-erőforráscsoport neve. Általában ugyanazt a RG-t használja, mint a Key Vault. |
| --- | --- |
| TemplateFile | A (z) VHDtoImage.jsfájl teljes elérési útja. |
| userStorageAccountName | A Storage-fiók neve. |
| dnsNameForPublicIP | A nyilvános IP-cím DNS-neve; kisbetűsnek kell lennie. |
| subscriptionId | Azure-előfizetés azonosítója. |
| Hely | Az erőforráscsoport szabványos Azure-beli földrajzi helye. |
| vmName | A virtuális gép neve. |
| vhdUrl | A virtuális merevlemez webcíme. |
| vmSize | A virtuálisgép-példány mérete. |
| publicIPAddressName | A nyilvános IP-cím neve. |
| virtualNetworkName | A virtuális hálózat neve. |
| nicName | A virtuális hálózat hálózati kártyájának neve. |
| adminUserName | A rendszergazdai fiók felhasználóneve. |
| adminPassword | Rendszergazdai jelszó. |
|

7. Az értékek megadása után válassza a **vásárlás**lehetőséget.

Az Azure elindítja az üzembe helyezést. Egy új virtuális gépet hoz létre a megadott nem felügyelt VHD-vel a megadott Storage-fiók elérési útján. A Azure Portal előrehaladását a portál bal oldalán található **Virtual Machines** kiválasztásával követheti nyomon. A virtuális gép létrehozása után az állapot az indítástól a futtatásig változik.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>A 2. generációs virtuális gépek üzembe helyezéséhez használja a következő sablont:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Azure-beli virtuális gép üzembe helyezése a PowerShell-lel

Másolja és szerkessze a következő parancsfájlt a és a változók értékeinek megadásához `$storageaccount` `$vhdUrl` . Futtassa azt egy Azure-beli virtuálisgép-erőforrás létrehozásához a meglévő általánosított VHD-ről.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Érvényesítések futtatása

Az üzembe helyezett lemezképen kétféleképpen futtathat érvényességet.

### <a name="use-certification-test-tool-for-azure-certified"></a>Az Azure Certified minősítési teszt eszköz használata

#### <a name="download-and-run-the-certification-test-tool"></a>A minősítési teszt eszköz letöltése és futtatása

Az Azure Certified minősítési teszt eszköze egy helyi Windows-gépen fut, de egy Azure-alapú Windows vagy Linux rendszerű virtuális gépet tesztel. Tanúsítja, hogy a felhasználói virtuálisgép-rendszerképet Microsoft Azure használhatja, és a virtuális merevlemez előkészítésével kapcsolatos útmutatás és követelmények teljesültek.

1. Töltse le és telepítse a legújabb [minősítési teszt eszközt az Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299)minősítéshez.
2. Nyissa meg a minősítési eszközt, majd kattintson az **új teszt indítása**lehetőségre.
3. A teszt adatai képernyőn adjon meg egy **teszt nevet** a teszt futtatásához.
4. Válassza ki a virtuális géphez tartozó platformot, amely **Windows Server** vagy **Linux**. A platform választása hatással van a fennmaradó lehetőségekre.
5. Ha a virtuális gép ezt az adatbázis-szolgáltatást használja, jelölje be a **Azure SQL Database teszt** jelölőnégyzetet.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítési eszköz összekötése egy virtuálisgép-lemezképpel

1. Válassza ki az SSH hitelesítési módot: jelszó-hitelesítés vagy kulcsfájl-hitelesítés.
2. Jelszó-alapú hitelesítés használata esetén adja meg a **virtuális gép DNS-nevének**, **felhasználónevének**és **jelszavának**értékeit. Módosíthatja az alapértelmezett SSH-portszámot is.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="A virtuális gép tesztelési adatainak kiválasztását mutatja.":::

3. Ha Key file-alapú hitelesítést használ, adja meg a virtuális gép DNS-neve, a Felhasználónév és a titkos kulcs helyének értékeit. Egy hozzáférési kódot is megadhat, vagy megváltoztathatja az alapértelmezett SSH-portszámot.
4. Adja meg a teljesen minősített virtuális gép DNS-nevét (például MyVMName.Cloudapp.net).
5. Adja meg a **felhasználónevet** és a **jelszót**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Megjeleníti a virtuális gép felhasználónevének és jelszavának kiválasztását.":::

6. Kattintson a **Tovább** gombra.

#### <a name="run-a-certification-test"></a>Minősítési teszt futtatása

Miután megadta a virtuálisgép-rendszerkép paramétereinek értékét a minősítési eszközben, válassza a kapcsolódás tesztelése lehetőséget a virtuális géphez való érvényes kapcsolatok létrehozásához. A kapcsolatok ellenőrzése után kattintson a **tovább** gombra a teszt elindításához. A teszt befejezésekor az eredmények egy táblázatban jelennek meg. Az Állapot oszlopban az egyes tesztek (Pass/Fail/Warning) láthatók. Ha a tesztek bármelyike meghiúsul, a rendszerkép nem rendelkezik tanúsítvánnyal. Ebben az esetben tekintse át a követelményeket és a hibaüzeneteket, végezze el a javasolt módosításokat, majd futtassa újra a tesztet.

Az automatikus teszt befejezése után további információkat adhat meg a virtuálisgép-rendszerképről a kérdőív képernyő két lapján, az általános értékelés és a rendszermag testreszabásában, majd kattintson a Tovább gombra.

Az utolsó képernyőn több információt is megadhat, például az SSH-hozzáférési információkat egy linuxos virtuálisgép-rendszerképhez, valamint a sikertelen értékelések magyarázatát, ha kivételeket keres.

Végül válassza a jelentés készítése lehetőséget, hogy letöltse a teszt eredményeit és a naplófájlokat a végrehajtott tesztelési esetekhez, valamint a kérdőívre adott válaszokat. Mentse az eredményeket ugyanabba a tárolóba, mint a virtuális merevlemezeket.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Az önteszt API használata a PowerShell használatával

### <a name="on-linux-os"></a>Linux operációs rendszeren

Az API meghívása a PowerShellben:

1. Az API meghívásához használja a meghívás-webkérés parancsot.
2. A metódus a post és a Content típus JSON, ahogy az alábbi kódrészletben látható.
3. A szövegtörzs paramétereit JSON formátumban kell megadni.

A következő példa egy PowerShell-hívást mutat be az API-ra:

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Íme egy példa az API meghívására a PowerShellben:

[![Példa az API PowerShellben való meghívására.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Az előző példa használatával lekérheti a JSON-t, és elemezheti a következő részletek beszerzéséhez:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Ez a minta képernyő, amely a `$res.Content` teszt eredményének részleteit jeleníti meg JSON formátumban:

[![Példa az API PowerShellben való meghívására a tesztelési eredmények részleteivel.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Íme egy példa a JSON-teszt eredményeire, amely egy online JSON-megjelenítőben (például a [Code szépít](https://codebeautify.org/jsonviewer) vagy a [JSON Viewer](https://jsonformatter.org/json-viewer)) tekinthető meg.

![További tesztelési eredmények egy online JSON-megjelenítőben.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Windows operációs rendszeren

Az API meghívása a PowerShellben:

1. Az API meghívásához használja a meghívás-webkérés parancsot.
2. A metódus a post és a Content típus JSON, ahogy az a következő példában látható, például a minta képernyőn.
3. Hozza létre a törzs paramétereit JSON formátumban.

Ez a mintakód egy PowerShell-hívást mutat be az API-nak:

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

Ezek a példák az API PowerShellben való meghívására mutatnak példát:

**SSH-kulccsal**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Az API meghívása a PowerShellben SSH-kulccsal.":::

**Jelszóval**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Az API meghívása a PowerShellben jelszóval.":::

<br>Az előző példa használatával lekérheti a JSON-t, és elemezheti a következő részletek beszerzéséhez:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Ez a képernyő azt mutatja `$res.Content` , hogy a teszt eredményei JSON formátumban jelennek meg:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="A teszt eredményének részletei JSON formátumban.":::

<br>Az alábbi példa egy online JSON-megjelenítőben megtekintett teszt eredményeire mutat (például a [Code szépít](https://codebeautify.org/jsonviewer) vagy a [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Teszt eredménye egy online JSON-megjelenítőben.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Az önteszt API használata Linux rendszeren a CURL használatával

Az API meghívása a CURLban:

1. Hívja meg az API-t a curl parancs használatával.
2. A metódus a post és a Content típus JSON, ahogy az a következő kódrészletben látható.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Íme egy példa a CURL használatára az API meghívásához:

![Példa a CURL használatára az API meghívásához.](media/vm/use-curl-call-api.png)

<br>A CURL-hívás JSON-eredményei:

![JSON-eredmények a CURL-hívásból.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Következő lépés

- [Általános sas URI-problémák és javítások](common-sas-uri-issues.md)olvasása.
