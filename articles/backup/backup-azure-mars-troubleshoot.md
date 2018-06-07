---
title: Az Azure Backup Agent hibaelhárítása
description: Telepítés és az Azure Backup szolgáltatás ügynökének regisztrációs hibáinak elhárítása
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 12/4/2017
ms.author: saurse
ms.openlocfilehash: aee0a3044ea4d1b9b867e795e94a37f8835ad212
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605756"
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Azure Backup szolgáltatás ügynöke konfigurációs és regisztrációs problémák elhárítása
## <a name="recommended-steps"></a>Javasolt lépések
Tekintse meg a javasolt művelet a következő táblázatok hárítsa el a konfigurációs vagy az Azure Backup szolgáltatás ügynökének regisztrációs során előforduló hibákat.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Érvénytelen megadott tárolói hitelesítő adatok. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó.
| Hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |
| **Hiba történt** </br> *Érvénytelen megadott tárolói hitelesítő adatok. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó. (ID: 34513)* | <ul><li> A tárolói hitelesítő adatok érvénytelenek (Ez azt jelenti, hogy azok lettek letöltve a regisztráció időpont előtt 48 óránál többet).<li>Az Azure Backup szolgáltatás ügynöke nincs tudja tölteni az ideiglenes fájlt a Windows ideiglenes mappát. <li>A tárolói hitelesítő adatokat a hálózati hely esetén. <li>A TLS 1.0 le van tiltva.<li> Konfigurált proxykiszolgálón blokkolja a kapcsolatot. <br> |  <ul><li>Töltse le új tárolói hitelesítő adatokat.<li>Ugrás a **Internetbeállítások** > **biztonsági** > **Internet**. Válassza ki, **Egyéni szint**, amíg megjelenik a fájl letöltése szakasz görgessen. Válassza ki **engedélyezése**.<li>Akkor is szükség lehet arra, hogy vegye fel a webhelyet [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat egy proxykiszolgáló használatára. Adja meg a proxy kiszolgáló adatait. <li> A dátum és idő felelnek meg a számítógépen.<li>Lépjen a C:/Windows/Temp, és ellenőrizze, hogy vannak-e több mint 60 000 vagy 65,000 .tmp kiterjesztésű fájlokat. Ha vannak, törölje ezeket a fájlokat.<li>A kiszolgálón a Szoftverterjesztési csomag futtatásával tesztelheti. Hibaüzenet arról, hogy a fájlok letöltése nem engedélyezettek, ha nagyon valószínű, hogy vannak-e nagyszámú C:/Windows/Temp könyvtárában található fájlokat.<li>Győződjön meg arról, hogy rendelkezik-e telepítve a .NET-keretrendszer 4.6.2-es verziójához. <li>A TLS 1.0 letiltása miatt PCI-megfelelőséget, tekintse meg a [oldal hibaelhárítási](https://support.microsoft.com/help/4022913). <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskereső vizsgálat: <ul><li>CBengine.exe<li>CSC.exe, amely kapcsolódik a .NET-keretrendszer. A CSC.exe minden .NET verziójához, amely a kiszolgálóra van telepítve van. Zárja ki a CSC.exe fájlok összes verzióját az érintett kiszolgálón .NET-keretrendszer vannak társítva. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az alapértelmezett hely az ideiglenes mappa vagy a gyorsítótár elérési útjának: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure helyreállítási szolgáltatás ügynökének (Agent) nem lehet kapcsolódni a Microsoft Azure Backup szolgáltatás

| Hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |
| **Hiba történt** </br><ol><li>*A Microsoft Azure helyreállítási szolgáltatás ügynökének (Agent) nem lehet kapcsolódni a Microsoft Azure Backup szolgáltatáshoz. (AZONOSÍTÓ: 100050) A hálózati beállításokat, és győződjön meg arról, hogy csatlakozni tudjanak internet*<li>*(407) Proxyhitelesítés szükséges* |A proxy blokkolja a kapcsolatot. |  <ul><li>Nyissa meg a **Internetbeállítások** > **biztonsági** > **Internet**. Válassza ki **Egyéni szint** görgessen, amíg megjelenik a fájl letöltése szakasz. Válassza az **Engedélyezés** lehetőséget.<li>Akkor is szükség lehet arra, hogy vegye fel a webhelyet [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat egy proxykiszolgáló használatára. Adja meg a proxy kiszolgáló adatait. <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskereső vizsgálat. <ul><li>CBEngine.exe (dpmra.exe) helyett.<li>A CSC.exe (.NET-keretrendszer kapcsolatos). A CSC.exe minden .NET verziójához, amely a kiszolgálóra van telepítve van. Zárja ki a CSC.exe fájlok összes verzióját az érintett kiszolgálón .NET-keretrendszer vannak társítva. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az alapértelmezett hely az ideiglenes mappa vagy a gyorsítótár elérési útjának: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a titkosítási kulcs biztonságos biztonsági másolatok

| Hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |      
| **Hiba történt** </br>*Nem sikerült beállítani a titkosítási kulcs biztonságos biztonsági mentés. Belső szolgáltatási hiba "érvénytelen bemeneti hiba miatt" az aktuális művelet nem sikerült. Próbálkozzon újra a művelettel valamivel később. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási*. |Kiszolgáló már regisztrálva van egy másik tárolóban.| Szüntesse meg a kiszolgáló a tárolóból, és regisztrálja újra.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Az aktiválás sikertelen volt. Az aktuális művelet sikertelen volt, mert belső szolgáltatási hiba [0x1FC07]

| Hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |          
| **Hiba történt** </br><ol><li>*Az aktiválás sikertelenül zárult. Az aktuális művelet [0x1FC07] belső szolgáltatási hiba miatt sikertelen volt. Próbálkozzon újra a művelettel valamivel később. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához* <li>*Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszó nem megfelelően van beállítva*. | <li> Az ideiglenes mappa nincs elegendő lemezterület a köteten. <li> Az ideiglenes mappa helytelenül került át egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik. | <li>Helyezze át az ideiglenes mappa vagy a gyorsítótár helye olyan kötetet, amelyen a biztonsági mentési adatok teljes mérete 5 – 10 %-ának megfelelő szabad lemezterület. A gyorsítótár helye áthelyezni, tekintse meg a lépéseket [az Azure Backup szolgáltatás ügynökének kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Gondoskodjon arról, hogy a OnlineBackup.KEK fájlt. <br>*Az alapértelmezett hely az ideiglenes mappa vagy a gyorsítótár elérési útjának: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

## <a name="next-steps"></a>További lépések
* További információkhoz juthat [biztonsági mentése a Windows Server az Azure Backup szolgáltatás ügynökének](tutorial-backup-windows-server-to-azure.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
