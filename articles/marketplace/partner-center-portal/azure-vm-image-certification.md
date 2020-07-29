---
title: VHD-ről üzembe helyezett virtuális gép (VM) tesztelése – Azure Marketplace
description: Megtudhatja, hogyan tesztelheti és küldheti el a virtuálisgép-ajánlatokat a kereskedelmi piactéren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 3d4ec077ac0e92d26cf82ba96593a76a21ed885f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324675"
---
# <a name="test-virtual-machine-vm-deployed-from-vhd"></a>VHD-ről üzembe helyezett virtuális gép (VM) tesztelése

Ez a cikk azt ismerteti, hogyan lehet üzembe helyezni és tesztelni az Azure-beli virtuális gépeket (VM) az előző szakaszban létrehozott általánosított VHD-rendszerképből ([Azure VM technikai eszköz létrehozása)](create-azure-vm-technical-asset.md) annak biztosítására, hogy a VHD-rendszerkép megfeleljen az Azure Marketplace közzétételi követelményeinek.

Hajtsa végre ezeket a lépéseket egy kompatibilitási jelentés létrehozásához, amely tanúsítja, hogy a VHD-rendszerkép használható az Azure Marketplace-en.

1. Hozzon létre és telepítsen tanúsítványokat a távoli virtuálisgép-kezeléshez a Azure Key Vaulthoz.
2. Helyezzen üzembe egy Azure-beli virtuális gépet az Azure-beli [virtuális gép létrehozása technikai eszköz létrehozásával](create-azure-vm-technical-asset.md)létrehozott általános VHD-rendszerképből.
3. Futtasson teszteket a telepített virtuális gépen annak biztosítása érdekében, hogy a VHD-lemezkép készen álljon a közzétételre és a virtuális gépek üzembe helyezésére.

## <a name="running-scripts"></a>Parancsfájlok futtatása

Ez a cikk három parancsfájlt tartalmaz a PowerShellben való futtatáshoz. Az asztali PowerShell a legjobban működik, azonban a Azure Cloud Shell is használható a kiválasztott PowerShell-lehetőséggel (az ablak bal felső részén).

1. Győződjön meg arról, hogy a PowerShell a parancsfájlok futtatására van konfigurálva.

    - Mindig nyissa meg a PowerShellt a **Futtatás rendszergazdaként** beállítással.
    - Győződjön meg arról, hogy a következő parancsfájlokat futtathatja: `Set-ExecutionPolicy` és `RemoteSigned` .

2. [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t.

3. Telepítse Azure PowerShell az modult.
    1. **A. lehetőség**: még nincsenek telepített modulok.
        - `Install-Module -Name Az -AllowClobber -Scope AllUsers`

        További információ: [Install Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.2.0).

    2. **B. lehetőség**: jelenleg a AzureRM modult használja.

        - Eltávolítás – AzureRM
        - Telepítési modul neve az-AllowClobber-scope AllUsers
        - Enable-AzureRmAlias – hatókör CurrentUser

        További információ: [Azure PowerShell migrálása a AzureRM-ről az-](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-4.2.0)ra.

4. Munkamenet paramétereinek mentése.

Az ebben a szakaszban található parancsfájlok munkamenet-változókat/paramétereket használnak. Ha bezárta a munkamenetet, a rendszer törli a paramétereket. Azt javasoljuk, hogy az összes parancsfájl futtatásához használjon egy munkamenetet, hogy elkerülje a paraméter értékének hibáit. Ha ez nem lehetséges, akkor újra kell inicializálnia a paramétereket új munkamenet megnyitásakor, különösen a későbbi parancsfájlokhoz.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Azure Key Vault tanúsítványok létrehozása és telepítése

Ez a szakasz azt ismerteti, hogyan hozhatók létre és helyezhetők üzembe a Rendszerfelügyeleti webszolgáltatások (WinRM) Azure-ban üzemeltetett virtuális géphez való beállításához szükséges önaláírt tanúsítványok.

### <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault tanúsítványok létrehozása

Ez a folyamat három lépésből áll:

1. Hozza létre a biztonsági tanúsítványt.
2. Hozza létre a Azure Key Vault a tanúsítvány tárolásához.
3. Tárolja a tanúsítványokat a Key vaultban.

Ehhez a munkához egy új vagy egy meglévő Azure-erőforráscsoportot is használhat.

#### <a name="create-the-security-certificate"></a>A biztonsági tanúsítvány létrehozása

Futtassa ezt a parancsfájlt a tanúsítványfájl (. pfx) helyi mappában való létrehozásához. A tanúsítvány a tervezett Azure-beli virtuális géphez tartozik, amely a VHD-rendszerképből lesz üzembe helyezve. A virtuális gépnek a parancsfájl paraméterei által megadott névvel, hellyel és jelszóval kell rendelkeznie. Szerkessze a következő Azure PowerShell **tanúsítvány-létrehozási parancsfájlt** a táblázatban látható paraméterek megfelelő értékeinek megadásához.

| **Paraméter** | **Leírás** |
| --- | --- |
| $certroopath | Helyi mappa, ahová menteni szeretné a. pfx-fájlt. |
| $location | Az Azure standard földrajzi helyeinek egyike. |
| $vmName | A tervezett Azure-beli virtuális gép neve. |
| $certname | A tanúsítvány neve; meg kell egyeznie a tervezett virtuális gép teljes tartománynevével. |
| $certpassword | A tanúsítványokhoz tartozó jelszónak meg kell egyeznie a tervezett virtuális géphez használt jelszóval. |
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
> A különböző paraméterek értékeinek megőrzéséhez tartsa megnyitva a Azure PowerShell-konzol munkamenetét, és futtassa a következő lépéseket.

> [!WARNING]
> Ha menti ezt a parancsfájlt, csak biztonságos helyen mentse, mert biztonsági adatokat (jelszót) tartalmaz.

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Az Azure Key Vault létrehozása a tanúsítvány tárolásához

Másolja az alábbi sablon tartalmát a helyi számítógép egyik fájljába. Az alábbi példában szereplő parancsfájlban ez az erőforrás `C:\certLocation\keyvault.json` ).

```JSON
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

Szerkessze és futtassa a következő Azure PowerShell parancsfájlt egy Azure Key Vault és a társított erőforráscsoport létrehozásához. Cserélje le az alábbi táblázatban látható paraméterek értékeit.

| **Paraméter** | **Leírás** |
| --- | --- |
| $postfix | A központi telepítési azonosítóhoz csatolt véletlenszerű numerikus karakterlánc. |
| $rgName | A létrehozandó Azure-erőforráscsoport (RG) neve. |
| $location | Az Azure standard földrajzi helyeinek egyike. |
| $kvTemplateJson | A Key Vault Resource Manager-sablonját tartalmazó fájl (keyvault.js) elérési útja. |
| $kvname | Az új kulcstároló neve.|
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
     az group create --name $rgName --location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys Decrypt,Encrypt,UnwrapKey,WrapKey,Verify,Sign,Get,List,Update,Create,Import,Delete,Backup,Restore,Recover,Purge -PermissionsToSecrets Get,List,Set,Delete,Backup,Restore,Recover,Purge

```

#### <a name="store-the-certificates-to-the-key-vault"></a>A tanúsítványok tárolása a Key vaultban

Tárolja a. pfx-fájlban található tanúsítványokat az új Key vaultban a következő parancsfájl használatával:

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

## <a name="deploy-an-azure-vm-from-your-generalized-vhd-image"></a>Azure-beli virtuális gép üzembe helyezése az általános VHD-rendszerképből

Ez a szakasz azt ismerteti, hogyan helyezhet üzembe egy általánosított VHD-rendszerképet egy új Azure-beli virtuálisgép-erőforrás létrehozásához. Ehhez a folyamathoz a megadott Azure Resource Manager sablont és Azure PowerShell szkriptet használjuk.

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager sablon előkészítése

Másolja a következő Azure Resource Manager sablonok egyikét a VHD-telepítéshez (Windows vagy Linux rendszerhez) egy VHDtoImage.jsnevű helyi fájlra. A következő parancsfájl a helyi gépen található helyet fogja használni a JSON használatához.

#### <a name="for-windows-based-vms"></a>Windows-alapú virtuális gépek esetén

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

#### <a name="for-linux-based-vms"></a>Linux-alapú virtuális gépek esetén

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
            "defaultValue": "linux",
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
                        "enabled": false,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Másolja és szerkessze a következő parancsfájlt a paraméterek értékeinek megadásához:

| **Paraméter** | **Leírás** |
| --- | --- |
| ResourceGroupName | Meglévő Azure-erőforráscsoport neve. Általában ugyanazt a RG-t használja, mint a Key Vault. |
| TemplateFile | A (z) VHDtoImage.jsfájl teljes elérési útja. |
| userStorageAccountName | A Storage-fiók neve. |
| sNameForPublicIP | A nyilvános IP-cím DNS-neve; kisbetűsnek kell lennie. |
| subscriptionId | Azure-előfizetés azonosítója. |
| Hely | Az erőforráscsoport szabványos Azure-beli földrajzi helye. |
| vmName | A virtuális gép neve. |
| vaultName | A Key Vault neve. |
| vaultResourceGroup | A Key Vault erőforráscsoport. |
| certificateUrl | A tanúsítvány webcíme (URL-címe), beleértve a Key vaultban tárolt verziót, például: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` . |
| vhdUrl | A virtuális merevlemez webcíme. |
| vmSize | A virtuálisgép-példány mérete. |
| publicIPAddressName | A nyilvános IP-cím neve. |
| virtualNetworkName | A virtuális hálózat neve. |
| nicName | A virtuális hálózat hálózati kártyájának neve. |
| adminUserName | A rendszergazdai fiók felhasználóneve. |
| adminPassword | Rendszergazdai jelszó. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Azure-beli virtuális gép üzembe helyezése

Másolja és szerkessze a következő parancsfájlt a és a változók értékeinek megadásához `$storageaccount` `$vhdUrl` . Futtassa azt egy Azure-beli virtuálisgép-erőforrás létrehozásához a meglévő általánosított VHD-ről.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

# Full pathname to the file VHDtoImage.json. inserted these highlighted lines
$templateFile = "$certroopath\VHDtoImage.json"

# Size of the virtual machine instance.
$vmSize = "Standard_D2s_v3"

# Name of the public IP address.
$publicIPAddressName = "myPublicIP1"

# Name of the virtual network
$virtualNetworkName = "myVNET1"

# Name of the network interface card for the virtual network
$nicName = "myNIC1"

# Username of the administrator account
$adminUserName = "isv"

# The OS of the virtual machine
$osType = "windows"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl “$objAzureKeyVaultSecret.Id” -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName"myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword “$pwd"

```

## <a name="run-tests-on-the-deployed-vm"></a>Tesztek futtatása a telepített virtuális gépen

### <a name="download-and-run-the-certification-test-tool"></a>A minősítési teszt eszköz letöltése és futtatása

Az Azure Certified minősítési teszt eszköze egy önteszt eszköz, amely egy helyi Windows-gépen fut, de Azure-alapú Windows vagy Linux rendszerű virtuális gépek tesztelésére szolgál. Tanúsítja, hogy a felhasználói virtuálisgép-rendszerképet Microsoft Azure használhatja, és a virtuális merevlemez előkészítésével kapcsolatos útmutatás és követelmények teljesültek. Ez az eszköz biztosítja, hogy a virtuális gép készen álljon az Azure Marketplace-re vonatkozó követelmények közzétételére. "

1. Töltse le és telepítse a legújabb [minősítési teszt eszközt az Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299)minősítéshez.
2. Nyissa meg a minősítési eszközt, majd kattintson az **új teszt indítása**lehetőségre.
3. A teszt **adatai** képernyőn adjon meg egy **teszt nevet** a teszt futtatásához.
4. Válassza ki a virtuális géphez tartozó **platformot** , amely Windows Server vagy Linux. A platform választása hatással van a fennmaradó lehetőségekre.
5. Ha a virtuális gép ezt az adatbázis-szolgáltatást használja, jelölje be a **Azure SQL Database teszt** jelölőnégyzetet.

### <a name="connect-the-certification-tool-to-a-vm-image"></a>A minősítési eszköz összekötése egy virtuálisgép-lemezképpel

Az eszköz a Windows-alapú virtuális gépekhez csatlakozik [Azure PowerShell](https://docs.microsoft.com/powershell/) és a [SSH.net](https://www.ssh.com/ssh/protocol/)-on keresztül csatlakozik a Linux rendszerű virtuális gépekhez. A következő két lehetőség közül választhat: Linux vagy Windows.

#### <a name="option-1-connect-the-certification-tool-to-a-linux-vm-image"></a>1. lehetőség: a minősítési eszköz összekötése Linux rendszerű virtuálisgép-lemezképpel

1. Válassza ki az **SSH hitelesítési** módot: jelszó-hitelesítés vagy kulcsfájl-hitelesítés.
2. Jelszó-alapú hitelesítés használata esetén adja meg a **virtuális gép DNS-nevének**, **felhasználónevének**és **jelszavának**értékeit. Módosíthatja az alapértelmezett **SSH-portszámot** is.

    ![Azure Certified test eszköz, linuxos virtuálisgép-rendszerkép jelszavas hitelesítése](media/avm-cert2.png)

3. Ha Key file-alapú hitelesítést használ, adja meg a **virtuális gép DNS-neve**, a **Felhasználónév**és a **titkos kulcs** helyének értékeit. Egy **hozzáférési kódot** is megadhat, vagy megváltoztathatja az alapértelmezett **SSH-portszámot** .

#### <a name="option-2-connect-the-certification-tool-to-a-windows-based-vm-image"></a>2. lehetőség: a minősítési eszköz összekötése egy Windows-alapú virtuálisgép-lemezképpel

1. Adja meg a teljesen minősített **virtuális gép DNS-nevét** (például MyVMName.Cloudapp.net).
2. Adja meg a **Felhasználónév** és a **jelszó**értékeit.

    ![Azure Certified test eszköz, Windows-alapú VM-rendszerkép jelszavas hitelesítése](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Minősítési teszt futtatása

Miután megadta a virtuálisgép-rendszerkép paramétereinek értékét a minősítési eszközben, válassza a **Kapcsolódás tesztelése** lehetőséget a virtuális géphez való érvényes kapcsolatok létrehozásához. A kapcsolatok ellenőrzése után kattintson a **tovább** gombra a teszt elindításához. A teszt befejezésekor a tesztek eredményei megjelennek a táblázatban. Az Állapot oszlopban az egyes tesztek (Pass/Fail/Warning) láthatók. Ha a tesztek bármelyike meghiúsul, a rendszerkép _nem_ rendelkezik tanúsítvánnyal. Ebben az esetben tekintse át a követelményeket és a hibaüzeneteket, végezze el a javasolt módosításokat, majd futtassa újra a tesztet.

Az automatikus teszt befejezése után további információkat adhat meg a virtuálisgép-rendszerképről a **Kérdőív** képernyő két lapján, az **általános értékelés** és a **rendszermag testreszabásában**, majd kattintson a **tovább**gombra.

Az utolsó képernyőn több információt is megadhat, például az SSH-hozzáférési információkat egy linuxos virtuálisgép-rendszerképhez, valamint a sikertelen értékelések magyarázatát, ha kivételeket keres.

Végül válassza a **jelentés készítése** lehetőséget, hogy letöltse a teszt eredményeit és a naplófájlokat a végrehajtott tesztelési esetekhez, valamint a kérdőívre adott válaszokat. Mentse az eredményeket ugyanabba a tárolóba, mint a virtuális merevlemezeket.

## <a name="next-step"></a>Következő lépés

- [Gyakori SAS URI-problémák és javítások](common-sas-uri-issues.md)
