---
title: Hozzon létre egy Azure Key Vault-tanúsítvánnyal |} A Microsoft Docs
description: Azt ismerteti, hogyan kell regisztrálni a virtuális gép Azure által üzembe helyezett virtuális merevlemezről.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 4223f9ec3bfaeacf7843508b13b5b5d81474311f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197012"
---
# <a name="create-certificates-for-azure-key-vault"></a>Tanúsítványok létrehozása az Azure Key Vault

Ez a cikk azt ismerteti, hogyan építheti ki az Azure-ban üzemeltetett virtuális gépeken (VM) a Windows Rendszerfelügyeleti (webszolgáltatások WinRM) kapcsolat létesítéséhez szükséges önaláírt tanúsítványokat. Ez a folyamat három lépésből áll:

1.  A biztonsági tanúsítvány létrehozásához. 
2.  Hozzon létre az Azure Key Vaultban tárolni ezt a tanúsítványt. 
3.  A tanúsítványok, a kulcstartó Store. 

Ezt a munkát használhatja az új vagy meglévő Azure-erőforráscsoportban.  A korábbi megközelítés a következő magyarázatot használatban van.


## <a name="create-the-certificate"></a>A tanúsítvány létrehozása

Szerkessze és futtassa a következő Azure Powershell-parancsprogram létrehozásához a tanúsítványfájlt (.pfx) a helyi mappában.  Cserélje le a következő paraméterek értékeit kell:

|  **A paraméter**        |   **Leírás**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | A .pfx fájlt a helyi mappa  |
| `$location`    | Az Azure standard földrajzi helyek egyikén  |
| `$vmName`      | A tervezett Azure virtuális gép neve   |
| `$certname`    | A tanúsítvány nevét meg kell egyeznie a tervezett virtuális gép teljesen minősített tartománynevét  |
| `$certpassword` | A tanúsítvány jelszavát meg kell egyeznie a tervezett virtuális gép használt jelszó  |
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
> Tartsa meg a PowerShell-konzol ugyanazon munkamenet aktív, ezek a lépések során úgy, hogy a különböző paraméterek megőrzi a rendszer.

> [!WARNING]
> Ha menti ezt a szkriptet, tárolja biztonságos helyen csak, mert tartalmaz biztonsági információk (jelszó).


## <a name="create-the-key-vault"></a>A kulcstartó létrehozása

Másolja ki a tartalmát a [központi telepítési sablont a key vault](./cpp-key-vault-deploy-template.md) egy fájl a helyi gépen. (az alábbi példa parancsprogram, ehhez az erőforráshoz van `C:\certLocation\keyvault.json`.)  Szerkessze és futtassa a következő Azure Powershell-parancsfájlt hozhat létre egy Azure Key Vault-példány és a társított erőforráscsoportokhoz.  Cserélje le a következő paraméterek értékeit kell:

|  **A paraméter**        |   **Leírás**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Üzembe helyezés azonosítók hozzáfűzi tetszőleges numerikus karakterlánc                     |
| `$rgName`             | Hozhat létre Azure-erőforrás (RG) csoport neve                                        |
|  `$location`          | Az Azure standard földrajzi helyek egyikén                                  |
| `$kvTemplateJson`     | A key vaulthoz Resource Manager-sablont tartalmazó könyvtárban (keyvault.json) fájl elérési útja |
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
        $myobjectId=(Get-AzureRmADUser -Mail $id)[0].Id
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
    New-AzureRmResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>A tanúsítvány Store

Most már tárolhat a tanúsítványok, a .pfx fájlban a következő szkript futtatásával új kulcstartóba. 

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

A rendszer ezután [virtuális gép üzembe helyezése a felhasználói Virtuálisgép-rendszerképből](./cpp-deploy-vm-user-image.md).
