---
title: Azure Key Vault-tanúsítvány létrehozása | Azure Piactér
description: Bemutatja, hogyan regisztrálhat virtuális gépet egy Azure által telepített virtuális merevlemezről.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: dc106ecf40367172f28c0f339bbcce8bddb50a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278075"
---
# <a name="create-certificates-for-azure-key-vault"></a>Tanúsítványok létrehozása az Azure Key Vaulthoz

Ez a cikk bemutatja, hogyan lehet kiépíteni az önaláírt tanúsítványok létrehozásához szükséges Windows Remote Management (WinRM) kapcsolat egy Azure által üzemeltetett virtuális gép (VM). Ez a folyamat három lépésből áll:

1.  Hozza létre a biztonsági tanúsítványt. 
2.  Hozza létre az Azure Key Vault a tanúsítvány tárolásához. 
3.  Tárolja a tanúsítványokat a kulcstartóban. 

Ehhez a munkához használhat egy új vagy egy meglévő Azure-erőforráscsoportot.  Az előbbi megközelítést a következő magyarázat használja.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>A tanúsítvány létrehozása

A tanúsítványfájl (.pfx) helyi mappában való létrehozásához szerkesztse és futtassa a következő Azure Powershell-parancsfájlt.  A következő paraméterek értékeit kell lecserélnie:

|  **Paraméter**        |   **Leírás**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Helyi mappa a .pfx fájl mentéséhez  |
| `$location`    | Az Azure szabványos földrajzi helyeinek egyike  |
| `$vmName`      | A tervezett Azure virtuális gép neve   |
| `$certname`    | A bizonyítvány neve; meg kell egyeznie a tervezett virtuális gép teljesen minősített tartománynevével  |
| `$certpassword` | A tanúsítványok jelszavának meg kell egyeznie a tervezett virtuális géphez használt jelszóval  |
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
> Tartsa ugyanazt a PowerShell-konzolmunkamenetet aktívan ezekben a lépésekben, hogy a különböző paraméterek értékei megmaradjanak.

> [!WARNING]
> Ha menti ezt a parancsfájlt, tárolja csak biztonságos helyen, mert biztonsági információkat (jelszót) tartalmaz.


## <a name="create-the-key-vault"></a>A kulcstartó létrehozása

Másolja a [key vault központi telepítési sablon](./cpp-key-vault-deploy-template.md) tartalmát egy fájlba a helyi számítógépen. (az alábbi példaparancsfájlban ez `C:\certLocation\keyvault.json`az erőforrás .)  Szerkessze és futtassa a következő Azure Powershell-parancsfájlt egy Azure Key Vault-példány és a társított erőforráscsoport létrehozásához.  A következő paraméterek értékeit kell lecserélnie:

|  **Paraméter**        |   **Leírás**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Tetszőleges numerikus karakterlánc a központi telepítési azonosítókhoz fűzve                     |
| `$rgName`             | Létrehozandó Azure-erőforráscsoport (RG) neve                                        |
|  `$location`          | Az Azure szabványos földrajzi helyeinek egyike                                  |
| `$kvTemplateJson`     | A key vault erőforrás-kezelősablonját tartalmazó fájl elérési útja (keyvault.json) |
| `$kvname`             | Az új kulcstartó neve                                                       |
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

A .pfx fájlban található tanúsítványokat a következő parancsfájl futtatásával tárolhatja az új kulcstartóban. 

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>További lépések

Ezután [egy virtuális gép a felhasználói virtuális gép lemezkép.](./cpp-deploy-vm-user-image.md)
