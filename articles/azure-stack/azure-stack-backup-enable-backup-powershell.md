---
title: "Biztonsági mentés engedélyezése a PowerShell-lel Azure verem |} Microsoft Docs"
description: "Az infrastruktúra vissza szolgáltatás engedélyezése a Windows PowerShell segítségével, hogy az Azure-verem állítható vissza, ha hiba történik."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: e0be5f1916ddb653550e6428201356290560c00e
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Azure verem PowerShell és a biztonsági mentés engedélyezése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az infrastruktúra vissza szolgáltatás engedélyezése a Windows PowerShell segítségével, hogy az Azure-verem állítható vissza, ha hiba történik. A PowerShell-parancsmagokat engedélyezése a biztonsági mentés, indítsa el a biztonsági mentési és biztonsági mentési adatok a operátor felügyeleti végpont keresztül érheti el.

## <a name="download-azure-stack-tools"></a>Töltse le az Azure verem eszközök

Azure verem konfigurált PowerShell és a telepítése és az Azure-verem eszközök. Lásd: [, amelyekből megismerheti a PowerShell használatával a Azure verem](https://review.docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>A csatlakozás és az infrastruktúra moduljainak betöltése

Nyissa meg a Windows PowerShell egy rendszergazdai jogú parancssorba, majd futtassa a következő parancsokat:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>A telepítő Rm környezet és napló be a operátor felügyeleti végpont

Ugyanabban a PowerShell munkamenetben szerkessze a következő PowerShell-parancsfájlt a környezetnek a változók hozzáadásával. Futtassa a frissített RM környezetet, és jelentkezzen be a operátor felügyeleti végpontja.

| Változó    | Leírás |
|---          |---          |
| $TenantName | Az Azure Active Directory-bérlő nevét. |
| Operátor neve        | Az Azure-verem operátor fióknevet. |
| Az Azure Resource Manager-végpont | Az az Azure Resource Manager URL-címe |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Hozzon létre egy új titkosítási kulcsot

Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Adja meg a biztonsági mentési megosztást, a hitelesítő adatokat és a titkosítási kulcs biztonsági mentés engedélyezése

Ugyanabban a PowerShell munkamenetben szerkessze a következő PowerShell-parancsfájlt a környezetnek a változók hozzáadásával. A frissített parancsprogrammal adja meg a biztonsági mentési megosztást, a hitelesítő adatokat és a titkosítási kulcs infrastruktúra a biztonsági mentési szolgáltatás.

| Változó        | Leírás   |
|---              |---                                        |
| $username       | Típus a **felhasználónév** megosztott meghajtóhelyét a tartomány és felhasználónév használatával. Például: `Contoso\administrator`. |
| $password       | Típus a **jelszó** a felhasználó számára. |
| $sharepath      | Írja be a elérési útját a **biztonsági másolat tárolási helye**. Egy univerzális elnevezési konvenció (UNC) szerinti karakterlánc kell használnia egy különálló eszköz üzemeltetett fájlmegosztás elérési útja. UNC-karakterláncnak erőforrások, például a megosztott fájlok vagy az eszközök helyét adja meg. Ahhoz, hogy a biztonsági mentési adatok rendelkezésre állását, az eszköz külön kell lennie. |

   ```powershell
   $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

Set-AzSBackupShare -Location $location -Path $path -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey 

   ```
##  <a name="confirm-backup-settings"></a>Biztonsági mentési beállításainak megerősítése

Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

Az eredmény a következő JSON-kimenetét hasonlóan kell kinéznie:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>További lépések

 - Ismerje meg, a biztonsági másolat, lásd: futtatásához [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md ).  
- Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés című [ellenőrizze a biztonsági mentés felügyeleti portál](azure-stack-backup-back-up-azure-stack.md ).