---
title: "Az Azure Backup szolgáltatás ügynöke (a MARS Agent) hibaelhárítása |} Microsoft Docs"
description: "Telepítés és az Azure Backup szolgáltatás ügynökének regisztrációs hibáinak elhárítása"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: 42b622e081d266da81b83c2140424f9d47008131
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>Azure biztonsági mentési ügynök konfigurációs/regisztrációs problémák elhárítása
## <a name="recommended-steps"></a>Javasolt lépések
Tekintse át a megadott feltételeknek megfelelő konfigurációs vagy az Azure Backup szolgáltatás ügynökének regisztráció során tapasztalt hibák ajánlott elemek.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Érvénytelen megadott tárolói hitelesítő adatok. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó.
| Hiba részletei | Lehetséges okok | Ajánlott elemek |
| ---     | ---     | ---    |
| **Hiba történt** </br> *Érvénytelen megadott tárolói hitelesítő adatok. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó. (AZONOSÍTÓ: 34513)* | <ol><li> A tárolói hitelesítő adatok érvénytelenek (vagyis. le lettek töltve 48 óránál regisztráció időpont előtt).<li>   Az Azure Backup szolgáltatás ügynöke nincs ideiglenes fájlt letöltheti a Windows ideiglenes mappát. <li>A tárolói hitelesítő adatokat a hálózati hely esetén. <li>A TLS 1.0 le van tiltva.<li> Konfigurált proxykiszolgálón blokkolja a kapcsolatot <br> |  <ol><li>Töltse le új tárolói hitelesítő adatokat<li>Nyissa meg az internet-beállítások > biztonsági > Internet > Egyéni szint gombra > görgessen, amíg nem fájl letöltése szakasz látja, és jelölje engedélyezése.<li>Is lehet, hogy vegye fel a webhelyet [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat proxyt használ, és adja meg a proxy adatait <li> A dátum és idő (UTC) felelnek meg a számítógépen<li>Ugrás a C:/Windows/Temp, és ellenőrizze, hogy vannak-e több mint 60 000 vagy 65,000 (.tmp kiterjesztésű) fájlokat, és törli ezeket a fájlokat.<li>A kiszolgálón a Szoftverterjesztési csomag futtatásával tesztelheti. Ha a hiba, azzal fájlletöltések nem engedélyezettek, sok fájlt a C:/Windows/Temp könyvtárba kapcsolatos ésszerűen biztos lehet.<li>Győződjön meg arról, hogy rendelkezik-e telepítve a .NET-keretrendszer 4.6.2-es verziójához <li>A TLS 1.0 letiltása miatt PCI-megfelelőséget, tekintse meg a [hivatkozás hibaelhárítása](https://support.microsoft.com/help/4022913). <li>Ha egy víruskereső a kiszolgálón telepítve van, a következő fájlok kizárása a víruskereső vizsgálatból. <ol><li>CBengine.exe<li>A CSC.exe (.NET-keretrendszer kapcsolódik. A CSC.exe .NET verziónként a kiszolgálón telepítve van. Zárja ki minden CSC.exe fájl összes verzióját az érintett kiszolgálón .NET-keretrendszer kötve.) <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az alapértelmezett hely az ideiglenes mappa vagy a gyorsítótár elérési útjának: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure helyreállítási szolgáltatás ügynökének (Agent) nem lehet kapcsolódni a Microsoft Azure Backup szolgáltatás

| Hiba részletei | Lehetséges okok | Ajánlott elemek |
| ---     | ---     | ---    |
| **Hiba történt** </br><ol><li>*A Microsoft Azure helyreállítási szolgáltatás ügynökének (Agent) nem lehet kapcsolódni a Microsoft Azure Backup szolgáltatáshoz. (AZONOSÍTÓ: 100050) A hálózati beállításokat, és győződjön meg arról, hogy csatlakozni tudjanak internet*<li>*(407) Proxyhitelesítés szükséges* |A proxy blokkolja-e a kapcsolat * |  <ol><li>Nyissa meg az internet-beállítások > biztonsági > Internet > Egyéni szint gombra > görgessen, amíg nem fájl letöltése szakasz látja, és jelölje engedélyezése.<li>Is lehet, hogy vegye fel a webhelyet [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat proxyt használ, és adja meg a proxy adatait <li>Ha egy víruskereső a kiszolgálón telepítve van, a következő fájlok kizárása a víruskereső vizsgálatból. <ol><li>CBengine.exe<li><li>A CSC.exe (.NET-keretrendszer kapcsolódik. A CSC.exe .NET verziónként a kiszolgálón telepítve van. Zárja ki minden CSC.exe fájl összes verzióját az érintett kiszolgálón .NET-keretrendszer kötve.) <li>Ideiglenes mappa vagy a gyorsítótár helye <br>*Az alapértelmezett hely az ideiglenes mappa vagy a gyorsítótár elérési útjának: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a titkosítási kulcs biztonságos biztonsági másolatok

| Hiba részletei | Lehetséges okok | Ajánlott elemek |
| ---     | ---     | ---    |      
| **Hiba történt** </br>*Nem sikerült beállítani a titkosítási kulcs biztonságos biztonsági mentés. Belső szolgáltatási hiba "érvénytelen bemeneti hiba miatt" az aktuális művelet nem sikerült. Próbálkozzon újra a művelettel valamivel később. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához* |Kiszolgáló egy másik tárolóban már regisztrálva van| Regisztrációjának a kiszolgálót a tárolóban, és regisztrálja újra.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Az aktiválás sikertelen volt. Az aktuális művelet sikertelen volt, mert belső szolgáltatási hiba [0x1FC07]

| Hiba részletei | Lehetséges okok | Ajánlott elemek |
| ---     | ---     | ---    |          
| **Hiba történt** </br><ol><li>*Az aktiválás sikertelen volt. Az aktuális művelet [0x1FC07] belső szolgáltatási hiba miatt sikertelen volt. Próbálkozzon újra a művelettel valamivel később. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához* <li>*Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszó nem megfelelően van beállítva* | <li> Nincs elegendő lemezterület a köteten található ideiglenes mappa <li> Ideiglenes mappa áthelyezése nem megfelelően egy másik helyre <li> OnlineBackup.KEK fájl hiányzik. | <li>Helyezze át egy kötetet a biztonsági mentési adatok teljes mérete 5 – 10 %-ának megfelelő szabad lemezterület az ideiglenes mappa vagy a gyorsítótár helyét. Tekintse meg azokat a lépéseket, az említett [Ez a cikk](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup) áthelyezése a gyorsítótár helyét.<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl megtalálható-e <br>*Az alapértelmezett hely az ideiglenes mappa vagy a gyorsítótár elérési útjának: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

## <a name="next-steps"></a>Következő lépések
* További információkhoz juthat [biztonsági mentése a Windows Server az Azure Backup szolgáltatás ügynökének](tutorial-backup-windows-server-to-azure.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
