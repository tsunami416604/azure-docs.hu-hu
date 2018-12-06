---
title: Az Azure Stack biztonsági mentés használatával a ASDK ellenőrzése |} A Microsoft Docs
description: Hogyan érvényesítheti az Azure Stack integerated rendszerek biztonsági mentés a ASDK használatával.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 181f37fb72584e18cc963ba1ffde070379a1b0c6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961431"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>A ASDK használata az Azure Stack biztonsági másolat ellenőrzése
Azure Stack üzembe helyezése és felhasználói erőforrások, például az ajánlatok, tervek, kvóták és az előfizetések kiépítése, után kell [Azure Stack infrastruktúrájának biztonsági mentés engedélyezése](../azure-stack-backup-enable-backup-console.md). Az ütemezés és az infrastruktúra rendszeres biztonsági mentések futtatása biztosítja, hogy infrastruktúra felügyeleti ne legyen adatvesztés a katasztrofális hardver vagy szolgáltatás hibája esetén.

> [!TIP]
> Azt javasoljuk, hogy [futtasson igény szerinti biztonsági mentést](../azure-stack-backup-back-up-azure-stack.md) biztosíthatja, hogy a legújabb infrastrcuture adatok másolatát az eljárás megkezdése előtt. Ellenőrizze, hogy a biztonsági mentési azonosító rögzítése után a biztonsági mentés sikeresen befejeződött. Ez az azonosító felhőalapú helyreállítás során lesz szükség. 

Azure Stack infrastruktúrájának biztonsági mentések a felhőben, amely az Azure Stack újbóli üzembe helyezés során vonatkozó fontos adatokat tartalmaz. A ASDK segítségével ellenőrizze ezeket a biztonsági másolatokat az éles felhőbeli befolyásolása nélkül. 

Biztonsági mentések ASDK érvényesítése támogatott a következő esetekben:

|Forgatókönyv|Cél|
|-----|-----|
|Ellenőrizze az integrált megoldást infrastruktúra biztonsági másolatait.|Rövid élettartamú ellenőrzés, hogy a biztonsági adatok érvényességét.|
|Ismerje meg, hogy a teljes körű helyreállítási munkafolyamat.|ASDK használatával ellenőrizze a teljes biztonsági mentés és visszaállítás a felhasználói élményt.|
|     |     |

Az alábbi forgatókönyv **nem** támogatott, ha a biztonsági mentések a ASDK ellenőrzése:

|Forgatókönyv|Cél|
|-----|-----|
|ASDK hozhat létre a biztonsági mentés és visszaállítás hozhat létre.|Biztonsági másolat adatainak visszaállítása a ASDK egy korábbi verziójáról egy újabb verzióra.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Felhőbeli helyreállítási üzemelő példány
Az integrált rendszerek üzembe helyezés infrastruktúra biztonsági másolatait a ASDK üzembe helyezése felhőbeli helyreállítási elvégzésével érvényesíthető. A központi telepítési típus, az adott szolgáltatás adatainak biztonsági másolatból történt visszaállítása után a ASDK telepítve van a gazdagépen.



### <a name="cloud-recovery-prerequisites"></a>Felhőbeli helyreállítási Előfeltételek
Mielőtt elkezdené a ASDK helyreállítási üzembe a felhőben, ellenőrizze, hogy a következő információkat:

|Előfeltétel|Leírás|
|-----|-----|
|Biztonsági mentési megosztás elérési útja.|Az UNC megosztást elérési útja a legújabb Azure Stack biztonsági másolat használatával állítsa helyre az Azure Stack-infrastruktúra információkat használandó. A helyi megosztás a felhőbeli helyreállítási üzembe helyezési folyamat során jön létre.|
|Titkosítási kulcs biztonsági mentése.|A titkosítási kulcs használt infrastruktúra biztonsági mentés futtatása az Azure Stack felügyeleti portál használatával ütemezése.|
|A visszaállítandó biztonsági mentési azonosítója.|A biztonsági mentési azonosító, ": xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", alfanumerikus formájában, amely azonosítja a biztonsági mentés felhőalapú helyreállítás során vissza kell állítani.|
|Kiszolgáló IP-címe.|Egy érvényes idő kiszolgáló IP-címet, például 132.163.97.2, szükség az Azure Stack üzemelő példányához.|
|Külső tanúsítvány jelszavát.|Az Azure Stack által használt külső tanúsítvány jelszava. A hitelesítésszolgáltató biztonsági mentésére kell állítani a jelszót külső tanúsítványok tartalmazza.|
|     |     | 

## <a name="prepare-the-host-computer"></a>A számítógép előkészítése 
Mint egy normál ASDK központi telepítés rendszer ASDK gazdakörnyezetben telepítés kell készíteni. A fejlesztői csomag gazdaszámítógép elő van készítve, amikor az merevlemez-meghajtóról a CloudBuilder.vhdx virtuális gép ASDK üzembe helyezés megkezdéséhez fog elindulni.

A ASDK állomás számítógépen töltse le a megfelelő ugyanazt a verzióját, amely készült biztonsági másolat az Azure Stack egy új cloudbuilder.vhdx, és kövesse az utasításokat [a ASDK számítógép előkészítése](asdk-prepare-host.md).

A cloudbuilder.vhdx, a gazdakiszolgáló újraindítása után hozzon létre egy fájlmegosztást, és másolja be a biztonsági mentési adatok. A fájlmegosztás, amelyhez a telepítő; futtató fiók PowerShell-parancs a példában a rendszergazda: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Ezután másolja a legújabb Azure Stack biztonsági másolat az újonnan létrehozott megosztásba. A mappastruktúra a megosztáson belüli kell lennie: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Felhőbeli helyreállítási módban a ASDK üzembe helyezése
A **InstallAzureStackPOC.ps1** parancsfájl felhőbeli helyreállítási indításához használt. 

> [!IMPORTANT]
> ASDK telepítési pontosan egy hálózati kártya (NIC) támogatja a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy van engedélyezve (és a többi le van tiltva) az üzembe helyezési parancsfájl futtatása előtt.

Módosítsa a következő PowerShell-parancsokat a környezetben, és futtassa őket a felhőbeli helyreállítási módban ASDK üzembe helyezéséhez:

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Infrastruktúra-adatok visszaállítása biztonsági másolatból
Egy sikeres felhőbeli helyreállítási telepítése után hajtsa végre a visszaállítást használatával kell a **Restore-AzureStack** parancsmagot. 

Az Azure Stack-operátorokról, mint a bejelentkezés után [Azure Stack PowerShell telepítésének](asdk-post-deploy.md#install-azure-stack-powershell) majd, és cserélje le a biztonsági mentés azonosítója és a `Name` paramétert, a következő parancsot:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Várjon, amíg 60 perc után indítsa el a biztonsági mentési adatok ellenőrzése a felhőben, a parancsmag meghívása ASDK helyreállítani.

## <a name="next-steps"></a>További lépések
[Az Azure Stack regisztrálása](asdk-register.md)

