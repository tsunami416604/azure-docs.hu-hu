---
title: Az Azure Backup Agent hibaelhárítása
description: Telepítési és regisztrációs Azure Backup Agent hibaelhárítása
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 2c8978cfba8fc56d4dbc565cb3a91c75d9d54679
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700195"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>A Microsoft Azure Recovery Services-(MARS) kapcsolatos problémák elhárítása
## <a name="recommended-steps"></a>Javasolt lépések
Ez a cikk a konfigurációja, a regisztráció, a biztonsági mentés során hibák megoldásához tekintse meg, és állítsa vissza a MARS-ügynök használatával.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Megadott tárhitelesítő adatok érvénytelenek. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatok a helyreállítási szolgáltatáshoz hozzárendelt.
| A hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |
| **Hiba történt** </br> *A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatok a helyreállítási szolgáltatáshoz hozzárendelt. (ID: 34513)* | <ul><li> A tároló hitelesítő adatai érvénytelenek. (azt jelenti, azok letöltése megtörtént a regisztráció előtt legfeljebb 48 óra).<li>A MARS-ügynök nem tudja fájlok letöltése a Windows Temp könyvtárában. <li>A tároló hitelesítő adatai vannak egy hálózati helyre. <li>A TLS 1.0 le van tiltva.<li> Egy konfigurált proxykiszolgálón blokkolja a kapcsolatot. <br> |  <ul><li>Töltse le a tár új hitelesítő adatait.<li>Lépjen a **Internetbeállítások** > **biztonsági** > **Internet**. Majd **Egyéni szint**, amíg meg nem látja a fájl letöltése szakasz görgessen. Válassza ki **engedélyezése**.<li>Is szükség lehet a hely hozzáadása [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat, egy proxykiszolgáló használatára. Adja meg a proxy adatait. <li> A dátum és idő egyezik a gépen.<li>C:/Windows/Temp nyissa meg, és ellenőrizze, hogy vannak-e több mint 60 000 vagy 65,000 .tmp kiterjesztésű fájlt. Ha vannak, ezeket a fájlokat törli.<li>Ez a Szoftverterjesztési csomagot a kiszolgálón futó ellenőrizheti. Ha hibaüzenet jelenik meg, hogy a fájlok letöltése nem engedélyezettek, valószínű, hogy nincsenek-e egy nagy mennyiségű fájlt a C:/Windows/Temp könyvtárba.<li>Győződjön meg arról, hogy a .NET-keretrendszer 4.6.2-es. <li>Ha PCI-megfelelőség miatt le van tiltva a TLS 1.0, tekintse meg a [hibaelhárítási lap](https://support.microsoft.com/help/4022913). <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskeresés: <ul><li>CBengine.exe<li>CSC.exe, amely kapcsolódik a .NET-keretrendszer. A CSC.exe minden .NET-verzió, amely a kiszolgálón van telepítve van. Az érintett kiszolgálón .NET-keretrendszer összes verziójához kötött CSC.exe fájlok kizárása. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service Agent nem tudott kapcsolatot a Microsoft Azure Backup

| A hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |
| **Hiba történt** </br><ol><li>*A Microsoft Azure Recovery Service Agent nem tudott kapcsolatot a Microsoft Azure Backup szolgáltatásban. (AZONOSÍTÓ: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy tud csatlakozni az internethez*<li>*(407) Proxyhitelesítés szükséges* |A proxy blokkolja a kapcsolatot. |  <ul><li>Nyissa meg a távoli **Internetbeállítások** > **biztonsági** > **Internet**. Válassza ki **Egyéni szint** görgessen, amíg megjelenik a fájl letöltése szakaszban. Válassza az **Engedélyezés** lehetőséget.<li>Is szükség lehet a hely hozzáadása [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat, egy proxykiszolgáló használatára. Adja meg a proxy adatait. <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskeresés. <ul><li>CBEngine.exe (helyett dpmra.exe).<li>A CSC.exe (.NET-keretrendszer kapcsolatos). A CSC.exe minden .NET-verzió, amely a kiszolgálón van telepítve van. Az érintett kiszolgálón .NET-keretrendszer összes verziójához kötött CSC.exe fájlok kizárása. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni

| A hiba részletei | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |      
| **Hiba történt** </br>*Nem sikerült beállítani a titkosítási kulcs biztonságos biztonsági másolatokat az aktiválás nem sikerült teljesen, de a titkosítási jelszó mentése a következő fájl*. |<li>Kiszolgáló már regisztrálva van egy másik tárolóval.<li>A hozzáférési kód sérült konfigurálása során| Törölje a tárolót a kiszolgáló regisztrációját, és regisztrálja újra az új jelszóval.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Az aktiválás nem sikerült. Az aktuális művelet nem sikerült, mert egy belső szolgáltatáshiba [0x1FC07]

| A hiba részletei | Lehetséges okok | Javasolt teendők |
|---------|---------|---------|
|**Hiba történt** </br><ol>*Az aktiválás sikertelenül zárult. Az aktuális művelet belső szolgáltatáshiba [0x1FC07] miatt nem sikerült. Próbálja meg újra a műveletet később. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához*     | <li> Az ideiglenes mappát, amely nincs elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül áthelyezték egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik.         | <li>Frissítés a [legújabb verzió](http://aka.ms/azurebackup_agent) a MARS-ügynök.<li>A biztonsági mentési adatok teljes mérete 5 – 10 %-ával egyenlő szabad lemezterülettel rendelkező kötetre helyezze át az ideiglenes mappát vagy a gyorsítótár helyét. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [az Azure Backup ügynök kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszava helytelenül van konfigurálva

| A hiba részletei | Lehetséges okok | Javasolt teendők |
|---------|---------|---------|
|**Hiba történt** </br><ol>*Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszava helytelenül van konfigurálva*.    | <li> Az ideiglenes mappát, amely nincs elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül áthelyezték egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik.        | <li>Frissítés a [legújabb verzió](http://aka.ms/azurebackup_agent) a MARS-ügynök.<li>Helyezze át az ideiglenes mappát vagy a gyorsítótár helyét egy biztonsági mentési adatok teljes mérete 5 – 10 %-ának megfelelő szabad lemezterülettel rendelkező kötetre. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [az Azure Backup ügynök kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>Nem futnak a biztonsági mentések ütemezve
Az alábbi lépések végrehajtásával, amikor ütemezett biztonsági mentések nem lekérése indul el automatikusan, miközben manuális biztonsági mentések problémamentesen működik. 
 
<li>Győződjön meg arról, hogy a PowerShell 3.0 vagy újabb van telepítve a kiszolgálón. A PowerShell-verziójának ellenőrzéséhez hajtsa végre a következő parancsot, és ellenőrizze, hogy a *fő* verziószáma 3-nál nagyobb vagy egyenlő.

`$PSVersionTable.PSVersion`
<li>Győződjön meg arról, hogy a következő elérési út része a *PSMODULEPATH* környezeti változót.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>Ha a powershell végrehajtási szabályzata a a *LocalMachine* van beállítva, a korlátozott, a powershell-parancsmag, amely elindítja a biztonsági mentési feladat sikertelen lehet. Hajtsa végre a következő parancsokat emelt jogosultságszintű módban, és a végrehajtási házirend beállítására vagy *Unrestricted* vagy *RemoteSigned*

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>Kattintson a Vezérlőpult -> Felügyeleti eszközök -> Feladatütemező -> bontsa ki a Microsoft -> Válassza ki az Online biztonsági mentés
<li>Kattintson duplán a "Microsoft-OnlineBackup" feladatot, és nyissa meg a "Eseményindítókként" lapot.
<li>Győződjön meg arról, hogy a feladat "Status" értéke "Enabled". Ha nem, kattintson a "Szerkesztés" és "Enabled" jelölőnégyzet bejelölésével
<li>Keresse meg a *biztonsági beállítások* szakaszában a *általános* lap
<li>Győződjön meg arról, hogy a feladat futtatásához a kiválasztott felhasználói fiók vagy *rendszer* vagy a helyi Rendszergazdák csoportnak azon a kiszolgálón

> [!TIP]
> Javasoljuk, hogy indítsa újra a kiszolgálót annak biztosítására, hogy végzett módosítások következetesek legyenek a fenti lépések végrehajtása után


## <a name="troubleshooting-restore-issues"></a>Visszaállítás kapcsolatos hibák elhárítása

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>Nem sikerült csatlakoztatni a helyreállítási kötetet, fájlok és mappák helyreállítása során
Ha az Azure Backup nem sikerült csatlakoztatja a helyreállítási kötetet még a parancsra néhány perc múlva **csatlakoztatási** vagy nem tudja csatlakoztatni a helyreállítási kötetet, egy vagy több hibás, az alábbi lépésekkel, általában helyreállítását.

1.  Megszakítja a folyamatban lévő csatlakoztatási folyamatot, abban az esetben több percig futott.

2.  Győződjön meg arról, hogy az az Azure Backup szolgáltatás ügynökének legújabb verzióját használja. Ismerje meg, az Azure Backup-ügynök fájlverzió-információkat, kattintson a **kapcsolatban a Microsoft Azure Recovery Services Agent** a a **műveletek** a Microsoft Azure Backup panelen konzolon, és ellenőrizze, hogy a **Verzió** szám egyenlő vagy nagyobb, mint az említett verzió [Ez a cikk](https://go.microsoft.com/fwlink/?linkid=229525). Letöltheti a legújabb verziót [Itt](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Lépjen a **Eszközkezelő** -> **tárolóvezérlők** , és győződjön meg arról, hogy tud-e keresni **Microsoft iSCSI-kezdeményező**. Azt is talál, akkor közvetlenül ugorjon a 7 alatt. 

4.  Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, a 3. lépésben említett, ellenőrizze, hogy amennyiben egy bejegyzés alatt található **Eszközkezelő** -> **tárolóvezérlők** nevű  **Ismeretlen eszköz** hardver azonosítójú **ROOT\ISCSIPRT**.

5.  Kattintson a jobb gombbal **ismeretlen eszköz** válassza **illesztőprogram frissítése**.

6.  A beállítás kiválasztásával az illesztőprogram frissítésével **keressen automatikusan a frissített illesztőprogram**. A frissítés befejezése után meg kell változtatni **ismeretlen eszköz** való **Microsoft iSCSI-kezdeményező** alább látható módon. 

    ![Titkosítás](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Lépjen a **Feladatkezelő** -> **szolgáltatások (helyi)** -> **Microsoft iSCSI-kezdeményező szolgáltatás**. 

    ![Titkosítás](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  A Microsoft iSCSI-kezdeményező szolgáltatás újraindításához kattintson a jobb gombbal a szolgáltatásban, kattintson a Leállítás, és további kattintson a jobb gombbal ismét, és kattintson a **Start**.

9.  Ismételje meg a helyreállítás, azonnali visszaállítás segítségével. 

Ha a helyreállítás is sikertelen, indítsa újra a kiszolgáló vagy ügyfél. Ha újraindítás nem kívánatos vagy a helyreállítás továbbra is a kiszolgáló újraindítása után is sikertelen, próbálja meg a helyreállítás egy másik gépről, hajtsa végre szereplő [Ez a cikk](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések
* Részletes adatainak [hogyan készíthet biztonsági másolatot a Windows Server az Azure Backup-ügynökkel](tutorial-backup-windows-server-to-azure.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
