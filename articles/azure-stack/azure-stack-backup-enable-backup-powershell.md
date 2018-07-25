---
title: Biztonsági mentés engedélyezése a PowerShell-lel az Azure Stack |} A Microsoft Docs
description: Engedélyezze az infrastruktúra Backup szolgáltatás a Windows PowerShell-lel, úgy, hogy az Azure Stack állíthatók, ha hiba történik.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242954"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>A PowerShell-lel az Azure Stack biztonsági mentés engedélyezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Engedélyezze az infrastruktúra Backup szolgáltatás a Windows PowerShell-lel így rendszeres biztonsági mentést hajtsa végre:
 - Belső identity service és a legfelső szintű tanúsítvány
 - Felhasználói csomagok, ajánlatok, előfizetések
 - A Keyvault titkos
 - RBAC-szerepkörök felhasználói és szabályzatok

A PowerShell-parancsmagok, biztonsági mentés engedélyezése, indítsa el a biztonsági mentési és biztonsági mentési információ az operátor felügyeleti végponton keresztül érheti el.

## <a name="prepare-powershell-environment"></a>PowerShell-környezet előkészítése

A PowerShell-környezet konfigurálása, lásd: [Azure Stack PowerShell telepítése ](azure-stack-powershell-install.md). Jelentkezzen be az Azure Stack, lásd: [konfigurálja az operátor környezetet, és jelentkezzen be az Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Adja meg a biztonsági mentési megosztást, a hitelesítő adatok és a titkosítási kulcs biztonsági mentésének engedélyezése

Ugyanazon PowerShell-munkamenetben szerkessze a következő PowerShell-parancsfájlt a környezetnek a változók hozzáadásával. A frissített parancsprogrammal adja meg a biztonsági mentési megosztást, a hitelesítő adatok és a titkosítási kulcsot a biztonsági mentési infrastruktúra-szolgáltatáshoz.

| Változó        | Leírás   |
|---              |---                                        |
| $username       | Írja be a **felhasználónév** fájlok olvasása és írása a megfelelő szintű hozzáféréssel rendelkező megosztott meghajtóhelyét tartomány és felhasználónév használata. Például: `Contoso\backupshareuser`. |
| $key            | Írja be a **titkosítási kulcs** minden egyes biztonsági másolat titkosításához használt. |
| $password       | Írja be a **jelszó** a felhasználó számára. |
| $sharepath      | Írja be a elérési útját a **biztonsági mentési tárhelyet**. Egy univerzális elnevezési konvenció (UNC) karakterlánc egy különálló eszköz található fájlmegosztás elérési útját kell használnia. Karakterláncnak UNC helyét adja meg az erőforrások, például megosztott fájlokhoz vagy eszközökön. A biztonsági mentési adatok rendelkezésre állásának biztosításához, hogy az eszköz egy külön helyen kell lennie. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Biztonsági mentési beállításainak megerősítése

Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

Az eredmény a következő kimenet hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>További lépések

 - Arról, hogyan futtathat egy biztonsági mentési, lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés, lásd: [megerősítése a biztonsági mentés a felügyeleti portál](azure-stack-backup-back-up-azure-stack.md ).