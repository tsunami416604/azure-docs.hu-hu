---
title: Az Azure virtuális gépek minősítése – Azure Piactér
description: Ismerje meg, hogyan tesztelheti és küldheti el a virtuálisgép-ajánlatot a kereskedelmi piacon.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 009a8e3db097790788f71486431a3b5b05c488ea
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265966"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Az Azure virtuális gép (VM) lemezképének minősítése

> [!NOTE]
> Az Azure virtuális gép ajánlatainak kezelését áthelyezzük a Cloud Partner Portalról a Partnerközpontba. Az ajánlatok áttelepítéséig kövesse az [Azure Key Vault tanúsítványok létrehozása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) a Felhőpartner-portálon című útmutató utasításait az ajánlatok kezeléséhez.

Ez a cikk ismerteti, hogyan tesztelheti és küldheti el a virtuális gép (VM) rendszerkép a kereskedelmi piacon annak érdekében, hogy megfeleljen a legújabb Azure Marketplace közzétételi követelményeknek.

A virtuális gép ajánlatának elküldése előtt hajtsa végre az alábbi lépéseket:

1. Tanúsítványok létrehozása és üzembe helyezése.
2. Üzembe helyezhet egy Azure-beli virtuális gép az általános rendszerképet.
3. Futtassa az érvényesítéseket.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Tanúsítványok létrehozása és üzembe helyezése az Azure Key Vaulthoz

Ez a szakasz azt ismerteti, hogyan hozhat létre és telepíthet önaláírt tanúsítványokat a Windows távkezelés (WinRM) kapcsolatának azure-beli virtuális gépen való beállításához.

### <a name="create-certificates-for-azure-key-vault"></a>Tanúsítványok létrehozása az Azure Key Vaulthoz

Ez a folyamat három lépésből áll:

1. Hozza létre a biztonsági tanúsítványt.
2. Hozza létre az Azure Key Vault a tanúsítvány tárolására.
3. Tárolja a tanúsítványokat a key vaultban.

Ehhez a munkához használhat egy új vagy egy meglévő Azure-erőforráscsoportot.

#### <a name="create-the-security-certificate"></a>A biztonsági tanúsítvány létrehozása

A tanúsítványfájl (.pfx) helyi mappában való létrehozásához szerkesztse és futtassa a következő Azure PowerShell-parancsfájlt. Cserélje le az alábbi táblázatban látható paraméterek értékeit.

| **Paraméter** | **Leírás** |
| --- | --- |
| $certroopath | Helyi mappa a .pfx fájl mentéséhez. |
| $location | Az Azure szabványos földrajzi helyeinek egyike. |
| $vmName | A tervezett Azure virtuális gép neve. |
| $certname | A bizonyítvány neve; meg kell egyeznie a tervezett virtuális gép teljesen minősített tartománynevével. |
| $certpassword | A tanúsítványok jelszavának meg kell egyeznie a tervezett virtuális géphez használt jelszóval. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> Tartsa meg ugyanazt az Azure PowerShell-konzolmunkamenetet nyitva és futva a lépések során a különböző paraméterek értékeinek megőrzéséhez.

> [!WARNING]
> Ha menti ezt a parancsfájlt, csak biztonságos helyre mentse, mert biztonsági információkat (jelszót) tartalmaz.

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Az Azure-kulcstartó létrehozása a tanúsítvány tárolásához

Másolja az alábbi sablon tartalmát egy fájlba a helyi számítógépen. Az alábbi példaparancsfájlban ez `C:\certLocation\keyvault.json`az erőforrás a.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Szerkessze és futtassa a következő Azure PowerShell-parancsfájlt egy Azure Key Vault és a kapcsolódó erőforráscsoport létrehozásához. Az alábbi táblázatban látható paraméterek értékeinek lecserélése

| **Paraméter** | **Leírás** |
| --- | --- |
| $postfix | A központi telepítési azonosítókhoz csatolt véletlenszerű numerikus karakterlánc. |
| $rgName | Létrehozandó Azure-erőforráscsoport (RG) neve. |
| $location | Az Azure szabványos földrajzi helyeinek egyike. |
| $kvTemplateJson | A key vault erőforrás-kezelősablont tartalmazó fájl elérési útja (keyvault.json). |
| $kvname | Az új kulcstartó neve.|
|   |   |

```PowerShell
    # Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all

```

#### <a name="store-the-certificates-to-the-key-vault"></a>A tanúsítványok tárolása a key vaultban

A .pfx fájlban található tanúsítványokat a parancsfájl használatával tárolja az új kulcstartóban:

```PowerShell
     $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Azure-beli virtuális gép üzembe helyezése az általános rendszerkép használatával

Ez a szakasz ismerteti, hogyan telepíthet egy általános virtuális merevlemez-rendszerképet egy új Azure virtuális gép erőforrás létrehozásához. Ehhez a folyamathoz a mellékelt Azure Resource Manager-sablont és az Azure PowerShell-parancsfájlt fogjuk használni.

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon előkészítése

Másolja a következő Azure Resource Manager-sablont a virtuális merevlemez üzembe helyezéséhez egy VHDtoImage.json nevű helyi fájlba. A következő parancsfájl kérni fogja a helyet a helyi számítógépen a JSON használatához.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
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
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
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
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
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
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
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
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

A fájl szerkesztése a következő paraméterek értékeinek megadásához:

| **Paraméter** | **Leírás** |
| --- | --- |
| ResourceGroupName | Meglévő Azure-erőforráscsoport neve. Általában használja ugyanazt az RG-t, mint a key vault. |
| Sablonfájl | Teljes elérési út a VHDtoImage.json fájlhoz. |
| userStorageAccountName | A tárfiók neve. |
| sNameForPublicIP | A nyilvános IP DNS-neve; kisbetűsnek kell lennie. |
| subscriptionId | Azure-előfizetés-azonosító. |
| Hely | Az erőforráscsoport szabványos Azure földrajzi helye. |
| vmName | A virtuális gép neve. |
| vaultName | A kulcstartó neve. |
| vaultResourceGroup | A kulcstartó erőforráscsoportja. |
| certificateUrl | A tanúsítvány webcíme (URL-címe), beleértve a key vaultban `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`tárolt verziót is, például: . |
| vhdUrl | A virtuális merevlemez webcíme. |
| vmSize | A virtuálisgép-példány mérete. |
| nyilvánosIPAddressName | A nyilvános IP-cím neve. |
| virtualNetworkName | A virtuális hálózat neve. |
| nicName (név) | A virtuális hálózat hálózati kártya neve. |
| adminUserName | A rendszergazdai fiók felhasználóneve. |
| adminPassword | Rendszergazdai jelszó. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Azure-beli virtuális gép üzembe helyezése

Másolja és szerkesztse a következő `$storageaccount` parancsfájlt, hogy értékeket adjon meg a és `$vhdUrl` a változókszámára. Hajtsa végre egy Azure virtuális gép erőforrás a meglévő általános virtuális merevlemez létrehozásához.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Érvényesítés futtatása

Az üzembe helyezett lemezképen kétféleképpen futtathatok érvényesítéseket:

- Az Azure Certified minősítéssel rendelkező tanúsítványteszteszköz használata
- Az önteszt API használata

### <a name="download-and-run-the-certification-test-tool"></a>A tanúsítási teszteszköz letöltése és futtatása

Az Azure Certified minősítési teszteszköze helyi Windows-gépen fut, de azure-alapú Windows vagy Linux virtuális gépet tesztel. Tanúsítja, hogy a felhasználói virtuális gép lemezképe használható a Microsoft Azure-ral, és hogy a virtuális merevlemez előkészítésére vonatkozó útmutatás és követelmények teljesültek. Az eszköz kimenete egy kompatibilitási jelentés, amelyet a Partnerközpont portálra kell feltöltenie a virtuális gép minősítésének kéréséhez.

1. Töltse le és telepítse az Azure Certified legújabb [minősítési teszteszközét.](https://www.microsoft.com/download/details.aspx?id=44299)
2. Nyissa meg a minősítő eszközt, majd válassza **az Új teszt indítása**lehetőséget.
3. A **Tesztinformáció** képernyőn adja meg a **tesztnevét** a tesztfuttatáshoz.
4. Válassza ki a **platform** a virtuális gép, windows Server vagy Linux. A platform választása hatással van a fennmaradó lehetőségekre.
5. Ha a virtuális gép használja ezt az adatbázis-szolgáltatást, jelölje be az **Azure SQL-adatbázis tesztelése** jelölőnégyzetet.

### <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítő eszköz csatlakoztatása virtuális gép lemezképéhez

Az eszköz az [Azure PowerShell](https://docs.microsoft.com/powershell/) segítségével windowsos virtuális gépekhez csatlakozik, és [SSH.Net](https://www.ssh.com/ssh/protocol/)keresztül csatlakozik a Linux-virtuális gépekhez.

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>A minősítő eszköz csatlakoztatása Linux virtuális géplemezképhez

1. Válassza az **SSH hitelesítési** módot: Jelszó-hitelesítés vagy kulcsfájl-hitelesítés.
2. Jelszóalapú hitelesítés használata esetén adja meg a **virtuális gép DNS-neve**, **felhasználóneve**és **jelszó értékét.** Módosíthatja az alapértelmezett **SSH portszámot** is.

    ![Azure Certified test tool, Linux VM image jelszóhitelesítés](media/avm-cert2.png)

3. Kulcsfájl-alapú hitelesítés használata esetén adja meg a **virtuális gép DNS-neve**, **felhasználónév**és **személyes kulcs** helyének értékeit. Megadhat egy **jelszót is,** vagy módosíthatja az alapértelmezett **SSH-portszámot.**

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**A minősítő eszköz csatlakoztatása Windows-alapú virtuálisgép-lemezképhez**

1. Adja meg a teljesen minősített **virtuális gép DNS-nevét** (például MyVMName.Cloudapp.net).
2. Adja meg a **Felhasználónév** és **a Jelszó**értékét.

    ![Azure Certified test tool, Windows-alapú virtuálisgép-lemezkép jelszóhitelesítése](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Hitelesítési vizsgálat futtatása

Miután megadta a virtuális gép lemezképének paraméterértékeit a minősítő eszközben, válassza a **Kapcsolat tesztelése** lehetőséget, hogy érvényes kapcsolatot hozzon létre a virtuális géppel. A kapcsolat ellenőrzése után válassza a **Tovább** gombot a teszt elindításához. Amikor a vizsgálat befejeződött, a vizsgálati eredmények egy táblázatban jelennek meg. Az Állapot oszlop ban (Pass/Fail/Warning) látható az egyes teszteknél. Ha a tesztek bármelyike sikertelen, a rendszerkép _nem_ lesz hitelesítve. Ebben az esetben tekintse át a követelményeket és a hibaüzeneteket, hajtsa végre a javasolt módosításokat, és futtassa újra a tesztet.

Az automatikus teszt befejezése után adjon meg további információkat a virtuális gép lemezképéről a Kérdőív képernyő, az **Általános értékelés** és a **Kernel testreszabása**képernyő két **lapján,** majd válassza a **Tovább**gombot.

Az utolsó képernyőn további információkat adhat meg, például egy Linux virtuális géplemezkép SSH hozzáférési adatait, és magyarázatot adhat a sikertelen értékelésekre, ha kivételeket keres.

Végül válassza **a Jelentés készítése lehetőséget** a teszteredmények és a naplófájlok letöltéséhez a végrehajtott tesztesetekhez, valamint a kérdőívre adott válaszokhoz. Mentse az eredményeket ugyanabban a tárolóban, mint a Virtuálisszámítógép.Save the results in the same container as your VHDs.

## <a name="next-step"></a>Következő lépés

- [Egységes erőforrás-azonosítók (URI) létrehozása minden egyes virtuális merevlemezhez](https://aka.ms/AzureSASURI)
