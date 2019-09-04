---
title: Azure Key Vault tanúsítvány létrehozása | Azure piactér
description: A cikk azt ismerteti, hogyan regisztrálhat egy virtuális gépet egy Azure-ban üzembe helyezett VHD-ről.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: c27605d2f9b87a9d4ba3d2326c0ce7ad437d3441
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240982"
---
# <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault tanúsítványok létrehozása

Ez a cikk a Rendszerfelügyeleti webszolgáltatások (WinRM) Azure-ban üzemeltetett virtuális géphez (VM) való létrehozásához szükséges önaláírt tanúsítványok kiépítését ismerteti. Ez a folyamat három lépésből áll:

1.  Hozza létre a biztonsági tanúsítványt. 
2.  Hozza létre a Azure Key Vault a tanúsítvány tárolásához. 
3.  Tárolja a tanúsítványokat a Key vaultban. 

Ehhez a munkához egy új vagy egy meglévő Azure-erőforráscsoportot is használhat.  A korábbi módszert a következő magyarázat használja.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>A tanúsítvány létrehozása

Szerkessze és futtassa a következő Azure PowerShell-parancsfájlt a tanúsítványfájl (. pfx) helyi mappában való létrehozásához.  A következő paraméterek értékeit kell lecserélnie:

|  **A paraméter**        |   **Leírás**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Helyi mappa, ahová menteni szeretné a. pfx-fájlt  |
| `$location`    | Az Azure standard földrajzi helyeinek egyike  |
| `$vmName`      | A tervezett Azure-beli virtuális gép neve   |
| `$certname`    | A tanúsítvány neve; meg kell egyeznie a tervezett virtuális gép teljes tartománynevével  |
| `$certpassword` | A tanúsítványokhoz tartozó jelszónak meg kell egyeznie a tervezett virtuális géphez használt jelszóval  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
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
> A fenti lépések során tartsa aktív módon ugyanezt a PowerShell-konzolt, hogy megőrizze a különböző paraméterek értékeit.

> [!WARNING]
> Ha menti ezt a parancsfájlt, csak biztonságos helyen tárolja, mert biztonsági adatokat (jelszót) tartalmaz.


## <a name="create-the-key-vault"></a>A Key Vault létrehozása

Másolja a [Key Vault telepítési sablon](./cpp-key-vault-deploy-template.md) tartalmát a helyi számítógép egyik fájljába. (az alábbi parancsfájlban ez az erőforrás `C:\certLocation\keyvault.json`.)  Szerkessze és futtassa az alábbi Azure PowerShell-szkriptet Azure Key Vault-példány és a társított erőforráscsoport létrehozásához.  A következő paraméterek értékeit kell lecserélnie:

|  **A paraméter**        |   **Leírás**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | A központi telepítési azonosítóhoz fűzött tetszőleges numerikus karakterlánc                     |
| `$rgName`             | Létrehozandó Azure-erőforráscsoport (RG) neve                                        |
|  `$location`          | Az Azure standard földrajzi helyeinek egyike                                  |
| `$kvTemplateJson`     | A Key Vault Resource Manager-sablonját tartalmazó fájl (kulcstartó. JSON) elérési útja |
| `$kvname`             | Az új kulcstároló neve                                                       |
|  |  |

```powershell
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

## <a name="store-the-certificate"></a>A tanúsítvány tárolása

A következő szkript futtatásával a. pfx fájlban található tanúsítványokat az új kulcstartóba is tárolhatja. 

```powershell
    #push certificate to key vault secret

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>További lépések

A következő lépésként [üzembe kell helyeznie egy virtuális gépet a felhasználói VM-rendszerképből](./cpp-deploy-vm-user-image.md).
