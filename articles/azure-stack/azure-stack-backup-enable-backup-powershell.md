---
title: Biztonsági mentés engedélyezése a PowerShell-lel Azure verem |} Microsoft Docs
description: Engedélyezze az infrastruktúra biztonsági másolat szolgáltatás a Windows PowerShell használatával, hogy az Azure-verem állítható vissza, ha hiba történik.
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
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Azure verem PowerShell és a biztonsági mentés engedélyezése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az infrastruktúra biztonsági másolat szolgáltatás a Windows PowerShell-lel engedélyezze ezért tegye a rendszeres biztonsági mentést:
 - Belső azonosító szolgáltatás és a legfelső szintű tanúsítvány
 - Felhasználói tervek, ajánlatokat az előfizetések
 - Keyvault titkos kulcsok
 - Az RBAC-szerepkörök felhasználói és házirendek

A PowerShell-parancsmagokat engedélyezése a biztonsági mentés, indítsa el a biztonsági mentési és biztonsági mentési adatok a operátor felügyeleti végpont keresztül érheti el.

## <a name="prepare-powershell-environment"></a>PowerShell-környezet előkészítése

A PowerShell-környezet konfigurálása, lásd: [verem Azure PowerShell telepítése ](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Hozzon létre egy új titkosítási kulcsot

Azure verem konfigurált PowerShell és a telepítése és az Azure-verem eszközök.
 - Lásd: [, amelyekből megismerheti a PowerShell használatával a Azure verem](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Lásd: [letöltése Azure verem eszközök a Githubról](azure-stack-powershell-download.md)

Nyissa meg a Windows PowerShell egy rendszergazdai jogú parancssorba, majd futtassa a következő parancsokat:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> A kulcs létrehozásához a AzureStack-eszközöket kell használnia.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Adja meg a biztonsági mentési megosztást, a hitelesítő adatokat és a titkosítási kulcs biztonsági mentés engedélyezése

Ugyanabban a PowerShell munkamenetben szerkessze a következő PowerShell-parancsfájlt a környezetnek a változók hozzáadásával. A frissített parancsprogrammal adja meg a biztonsági mentési megosztást, a hitelesítő adatokat és a titkosítási kulcs infrastruktúra a biztonsági mentési szolgáltatás.

| Változó        | Leírás   |
|---              |---                                        |
| $username       | Típus a **felhasználónév** segítségével a tartomány és felhasználónév megfelelő hozzáféréssel rendelkező megosztott meghajtóhelyét a fájlok olvasását és írását. Például: `Contoso\backupshareuser`. |
| $password       | Típus a **jelszó** a felhasználó számára. |
| $sharepath      | Írja be a elérési útját a **biztonsági másolat tárolási helye**. Egy különálló eszköz üzemeltetett fájlmegosztás elérési útja egy univerzális elnevezési konvenció (UNC) szerinti karakterlánc kell használnia. UNC-karakterláncnak erőforrások, például a megosztott fájlok vagy az eszközök helyét adja meg. Ahhoz, hogy a biztonsági mentési adatok rendelkezésre állását, az eszköz külön kell lennie. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Biztonsági mentési beállításainak megerősítése

Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
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
