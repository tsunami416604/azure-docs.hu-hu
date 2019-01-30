---
title: Biztonsági mentés engedélyezése a PowerShell-lel az Azure Stack |} A Microsoft Docs
description: Engedélyezze az infrastruktúra Backup szolgáltatás a Windows PowerShell-lel, úgy, hogy az Azure Stack állíthatók, ha hiba történik.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 08/16/2018
ms.openlocfilehash: 10d7303c4323305e177cf006b9a259a817dc695e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247476"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>A PowerShell-lel az Azure Stack biztonsági mentés engedélyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

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
| $password       | Írja be a **jelszó** a felhasználó számára. |
| $sharepath      | Írja be a elérési útját a **biztonsági mentési tárhelyet**. Egy univerzális elnevezési konvenció (UNC) karakterlánc egy különálló eszköz található fájlmegosztás elérési útját kell használnia. Karakterláncnak UNC helyét adja meg az erőforrások, például megosztott fájlokhoz vagy eszközökön. A biztonsági mentési adatok rendelkezésre állásának biztosításához, hogy az eszköz egy külön helyen kell lennie. |
| $frequencyInHours | A gyakorisága (óra) határozza meg, hogy milyen gyakran jönnek létre biztonsági mentéseket. Az alapértelmezett érték: 12. A Scheduler támogatja a legfeljebb 12 és a egy legalább 4.|
| $retentionPeriodInDays | A megőrzési időszak napban határozza meg, hány napig a biztonsági mentések megmaradnak a külső helyen. Az alapértelmezett értéke a 7. A Scheduler támogatja a legfeljebb 14 és a egy legalább 2. A megőrzési időszak régebbi biztonsági másolatok beolvasása automatikusan törli a külső helyről.|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Biztonsági mentési beállításainak megerősítése

Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

Az eredmény az alábbi példa kimenetében hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Biztonsági mentés beállításainak frissítése
Ugyanebben a PowerShell munkamenetben frissítheti a megőrzési időtartam és a biztonsági mentések gyakoriságát az alapértelmezett értékeket. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Az eredmény az alábbi példa kimenetében hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>További lépések

 - Arról, hogyan futtathat egy biztonsági mentési, lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés, lásd: [megerősítése a biztonsági mentés a felügyeleti portál](azure-stack-backup-back-up-azure-stack.md ).
