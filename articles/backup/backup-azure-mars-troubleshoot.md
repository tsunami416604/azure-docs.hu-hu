---
title: Az Azure Backup agent hibaelhárítása
description: Telepítési és regisztrációs az Azure Backup-ügynök hibaelhárítása
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434011"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS) ügynök hibaelhárítása

Ez a cikk bemutatja, hogyan megoldásához esetlegesen előforduló hibák során konfigurációja, a regisztráció, a biztonsági mentés, visszaállítás és.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy megkezdése előtt hibáinak elhárítása a Microsoft az Azure Recovery Services-(MARS-) ügynök ellenőrizze a következőket:

- [Győződjön meg arról, a MARS-ügynök a](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Ellenőrizze, hogy a MARS-ügynök és az Azure közötti hálózati kapcsolat](https://aka.ms/AB-A4dp50).
- Győződjön meg arról, MARS fut (a szolgáltatási konzolon). Ha szeretné, indítsa újra, és próbálja megismételni a műveletet.
- [Győződjön meg arról, 5 – 10 % kötet szabad terület érhető el az ideiglenes mappa helye](https://aka.ms/AB-AA4dwtt).
- [Annak ellenőrzése, hogy egy másik folyamat vagy víruskereső zavarja az Azure Backup](https://aka.ms/AB-AA4dwtk).
- Ha az ütemezett biztonsági mentés meghiúsul, de manuális biztonsági mentés működik, tekintse meg [ütemezés szerint ne futtassa a biztonsági mentések](https://aka.ms/ScheduledBackupFailManualWorks).
- Győződjön meg arról az operációs rendszer legújabb frissítéseit.
- [Győződjön meg, hogy a biztonsági mentés nem támogatott meghajtókkal és nem támogatott attribútumokkal rendelkező fájlok tartoznak](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Győződjön meg arról, az óra, a védett számítógépen a megfelelő időzónában van konfigurálva.
- [Győződjön meg, hogy a .NET-keretrendszer 4.5.2-es vagy újabb van telepítve a kiszolgálón](https://www.microsoft.com/download/details.aspx?id=30653).
- Ha szeretne regisztrálja újra a kiszolgálót, hogy egy tárolót:
  - Győződjön meg arról, nem távolítja az ügynököt a kiszolgálón, és törli a portálról.
  - Használja ugyanazt a jelszót, amely eredetileg használt regisztrálja a kiszolgálót.
- Offline biztonsági mentés hogy az Azure PowerShell 3.7.0 telepítve van a forrás- és a példány számítógép a biztonsági mentés megkezdése előtt.
- Ha a biztonsági mentést ügynököt egy Azure virtuális gépen fut, lásd: [Ez a cikk](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Megadott tárhitelesítő adatok érvénytelenek

**Chybová zpráva**: A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatok a helyreállítási szolgáltatáshoz hozzárendelt. (Azonosító: 34513)

| Ok | Ajánlott műveletek |
| ---     | ---    |
| **A tároló hitelesítő adatai érvénytelenek.** <br/> <br/> Tároló hitelesítő adatfájljait esetleg sérült, vagy esetleg elévült. (Például, akkor előfordulhat, hogy le vannak töltve a regisztráció előtt 48 óránál hosszabb.)| Töltse le az új hitelesítő adatokat a Recovery Services-tárolót az Azure Portalon. (Lásd a 6. lépés: a [töltse le a MARS-ügynök](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) szakaszban.) Ezután hajtsa végre a megfelelő lépéseket: <ul><li> Ha korábban már telepítette és MARS regisztrált, nyissa meg a Microsoft Azure Backup ügynök MMC konzolt, majd **kiszolgáló regisztrálása** a a **műveletek** panelen az új a regisztráció befejezéséhez hitelesítő adatok. <br/> <li> Ha az új telepítése nem sikerül, telepítse újra az új hitelesítő adatokkal.</ul> **Megjegyzés**: Ha több tároló hitelesítő adatfájljait le lett töltve, csak a legújabb fájl érvényes lesz a következő 48 órán át. Azt javasoljuk, hogy le kell tölteni a tároló új hitelesítőadat-fájlja.
| **Proxy server/tűzfal blokkolja a regisztráció** <br/>vagy <br/>**Nincs internetkapcsolat** <br/><br/> Ha a gép vagy a proxy-kiszolgálót csak korlátozott internetkapcsolat, és nem biztosítása érdekében a hozzáférést a szükséges URL-címek, a regisztráció sikertelen lesz.| Hajtsa végre az alábbi lépéseket:<br/> <ul><li> Működik az informatikai csapat a rendszer ne legyen internetkapcsolat.<li> Ha nem rendelkezik egy proxykiszolgáló, győződjön meg arról, a proxy lehetőség nincs kiválasztva, ha regisztrálja az ügynököt. [Ellenőrizze a proxybeállításokat](#verifying-proxy-settings-for-windows).<li> Ha a tűzfal /-proxy kiszolgáló rendelkezik, annak biztosítása érdekében az alábbi URL-címek a hálózatkezelésért felelős csapat dolgozhat, és IP-címek hozzáférése:<br/> <br> **URLs**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-címek**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Próbálja meg ismét regisztrálni a fenti hibaelhárítási lépések végrehajtása után.
| **Víruskereső szoftver blokkolja a regisztráció** | Ha a kiszolgálón telepített víruskereső szoftver, hozzáadása a víruskeresés ezeket a fájlokat és mappákat a szükséges kizárási szabályokat: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> Az ideiglenes mappát. Az alapértelmezett hely a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A bin mappa: C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>További javaslatok
- C:/Windows/Temp nyissa meg, és ellenőrizze, hogy vannak-e több mint 60 000 vagy 65,000 .tmp kiterjesztésű fájlt. Ha vannak, ezeket a fájlokat törli.
- Győződjön meg, hogy a gép dátuma és időpontja egyezik a helyi időzónában.
- Győződjön meg, hogy [ezeken a webhelyeken](backup-configure-vault.md#verify-internet-access) adnak hozzá az Internet Explorer Megbízható helyek.

### <a name="verifying-proxy-settings-for-windows"></a>Windows proxybeállításainak ellenőrzése

1. Töltse le a PsExec a [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) lapot.
1. Futtatás `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` egy rendszergazda jogú parancssorból.

   Ez a parancs az Internet Explorer nyílik meg.
1. Lépjen a **eszközök** > **Internetbeállítások** > **kapcsolatok** > **LAN-beállítások**.
1. Ellenőrizze a proxykiszolgáló beállításait a system fiók.
1. Ha nincs proxy van konfigurálva, és proxy részletei is szerepelnek, távolítsa el a részleteket.
1. Ha a proxy van konfigurálva, és a proxy adatait nem megfelelő, győződjön meg róla a **Proxy IP** és **Port** helyesek információk.
1. Zárja be az Internet Explorerben.

## <a name="unable-to-download-vault-credential-file"></a>Nem sikerült letölteni a tároló hitelesítőadat-fájlja

| Hiba   | Ajánlott műveletek |
| ---     | ---    |
|Nem sikerült letölteni a tár hitelesítő adatait tartalmazó fájlt. (Azonosító: 403) | <ul><li> Próbálja meg letölteni a tároló hitelesítő adatait egy másik böngészővel, vagy ezeket a lépéseket: <ul><li> Start Internet Explorer. Válassza ki az F12 billentyűt. </li><li> Nyissa meg a **hálózati** lapra, és törölje a gyorsítótárat, és a cookie-kat. </li> <li> Frissítse az oldalt.<br></li></ul> <li> Ellenőrizze, ha az előfizetés le van tiltva vagy lejárt.<br></li> <li> Ellenőrizze, hogy ha bármilyen tűzfalszabály blokkolja a letöltést. <br></li> <li> Győződjön meg arról, még nem merül ki a tároló (tárolónként 50 gépet) a határértéket.<br></li>  <li> Győződjön meg arról, a felhasználók számára az Azure biztonsági mentési tároló hitelesítő adatainak letöltése és a kiszolgáló regisztrálása a tárolóhoz szükséges. Lásd: [Use Role-Based hozzáférés-vezérlés kezelése az Azure Backup helyreállítási pontok](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| Hiba  | Lehetséges ok | Ajánlott műveletek |
| ---     | ---     | ---    |
| <br /><ul><li>A Microsoft Azure Recovery Service Agent nem tudott kapcsolatot a Microsoft Azure Backup szolgáltatásban. (Azonosító: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy képes csatlakozni az internethez.<li>(407) Proxyhitelesítés szükséges. |A proxy blokkolja a kapcsolatot. |  <ul><li>Az Internet Explorerben nyissa meg **eszközök** > **Internetbeállítások** > **biztonsági** > **Internet**. Válassza ki **Egyéni szint** , és görgessen le a **File download** szakasz. Válassza ki **engedélyezése**.<p>Akkor is lehet hozzáadni [URL-címek és IP-címek](backup-configure-vault.md#verify-internet-access) az Internet Explorer Megbízható helyek.<li>Módosítsa a beállításokat, egy proxykiszolgáló használatára. Adja meg a proxy adatait.<li> Ha a gép korlátozott internet-hozzáféréssel, győződjön meg arról, hogy a gépen, vagy a proxy tűzfal beállításai engedélyezik-e [URL-címek és IP-címek](backup-configure-vault.md#verify-internet-access). <li>Ha a kiszolgálón telepített víruskereső szoftver, ezek a fájlok kizárása a víruskeresés: <ul><li>CBEngine.exe (helyett dpmra.exe).<li>A CSC.exe (.NET-keretrendszer kapcsolatos). Nincs a CSC.exe a minden .NET-keretrendszer verziója telepítve van a kiszolgálón. Az érintett kiszolgálón .NET-keretrendszer összes verziójához a CSC.exe fájlok kizárása. <li>Az ideiglenes mappát vagy a gyorsítótár helye. <br>Az ideiglenes mappát vagy a gyorsítótár elérési útját az alapértelmezett hely a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A bin mappa: C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni

| Hiba | Lehetséges okok | Ajánlott műveletek |
| ---     | ---     | ---    |
| <br />Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni. Az aktiválás nem sikerült teljesen, de a titkosítási jelszó mentése a következő fájlhoz. |<li>A kiszolgáló már regisztrálva van egy másik tárolóval.<li>A jelszó konfigurálása során sérült volt.| Törölje a tárolót a kiszolgáló regisztrációját, és regisztrálja újra az új jelszót.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem sikerült

| Hiba  | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
|<br />Az aktiválás nem sikerült. Az aktuális művelet belső szolgáltatáshiba [0x1FC07] miatt nem sikerült. Némi várakozás után próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.     | <li> Az ideiglenes mappát, amely nem rendelkezik elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül át lett helyezve. <li> A OnlineBackup.KEK fájl hiányzik.         | <li>Frissítés a [legújabb verzió](https://aka.ms/azurebackup_agent) a MARS-ügynök.<li>Helyezze át az ideiglenes mappát vagy a gyorsítótár-hely közötti 5 %-os és a biztonsági mentési adatok méretének 10 % szabad lemezterülettel rendelkező kötetre. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdésekre](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útját az alapértelmezett hely a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Titkosítási jelszó nincs megfelelően konfigurálva

| Hiba  | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
| <br />Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszava nem megfelelően történik meg.    | <li> Az ideiglenes mappát, amely nem rendelkezik elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül át lett helyezve. <li> A OnlineBackup.KEK fájl hiányzik.        | <li>Frissítés a [legújabb verzió](https://aka.ms/azurebackup_agent) a MARS-ügynök.<li>Helyezze át az ideiglenes mappát vagy a gyorsítótár-hely közötti 5 %-os és a biztonsági mentési adatok méretének 10 % szabad lemezterülettel rendelkező kötetre. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdésekre](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útját az alapértelmezett hely a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Biztonsági mentések nem futtatása ütemezés szerint
Ha ütemezett biztonsági mentések nem lekérése automatikusan aktiválódik, de a manuális biztonsági mentések megfelelően működik, próbálja meg a következő műveleteket:

- Győződjön meg arról, a Windows Server biztonsági mentés ütemezése nem ütközik az Azure fájlok és mappák biztonsági mentés ütemezése.

- Győződjön meg arról, az online biztonsági mentés állapota értéke **engedélyezése**. Az állapot ellenőrzéséhez hajtsa végre ezeket a lépéseket:

  1. Bontsa ki a Feladatütemező **Microsoft** válassza **Online biztonsági mentés**.
  1. Kattintson duplán a **Microsoft-OnlineBackup** és nyissa meg a **eseményindítók** fülre.
  1. Ellenőrizze, hogy ha az állapot értéke **engedélyezve**. Ha nem, válassza ki a **szerkesztése**, jelölje be **engedélyezve**, majd válassza ki **OK**.

- Győződjön meg, hogy a feladat futtatásához a kiválasztott felhasználói fiók vagy **rendszer** vagy **a helyi Rendszergazdák csoport** a kiszolgálón. A felhasználói fiók ellenőrzéséhez nyissa meg a **általános** fülre és ellenőrizze a **biztonsági** beállítások.

- Győződjön meg arról, a PowerShell 3.0-s vagy újabb verziója telepítve van a kiszolgálón. A PowerShell-verziójának ellenőrzéséhez futtassa a következő parancsot, és ellenőrizze, hogy a `Major` verziószáma 3 vagy újabb:

  `$PSVersionTable.PSVersion`

- Győződjön meg arról, az elérési út része a `PSMODULEPATH` környezeti változót:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a PowerShell végrehajtási szabályzata a `LocalMachine` van beállítva a korlátozott, a PowerShell-parancsmag, amely elindítja a biztonsági mentési feladat sikertelen lehet. Futtassa az alábbi parancsokat emelt jogosultságszintű módban, és a végrehajtási házirend beállítására vagy `Unrestricted` vagy `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Győződjön meg arról, nem hiányzik vagy sérült PowerShell modul MSOnlineBackup fájlokat. Ha hiányzik vagy sérült fájlokat, hajtsa végre ezeket a lépéseket:

  1. Minden olyan gép, amely rendelkezik egy MARS-ügynök megfelelően működik másolja a MSOnlineBackup mappa a C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. A problémás számítógépen illessze be a másolt fájlokat ugyanazon a helyen mappa (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Ha a számítógépen már van egy MSOnlineBackup mappát, illessze be a fájlokat, vagy cserélje le a meglévő fájlokat.


> [!TIP]
> Győződjön meg, hogy a módosítások következetesek legyenek, a fenti lépések végrehajtása után indítsa újra a kiszolgálót.


## <a name="troubleshoot-restore-problems"></a>Visszaállítási hibák elhárítása

Az Azure Backup előfordulhat, hogy nem sikerült csatlakoztatni a helyreállítási kötetet, akár néhány perc múlva. És a folyamat során hibaüzenetet kaphat. Általában a helyreállítás megkezdéséhez hajtsa végre ezeket a lépéseket:

1.  A csatlakozási folyamat megszakítása, ha több percig futott.

2.  Ellenőrizze, hogy van-e a Backup szolgáltatás ügynökének legújabb verzióját. A verzió ellenőrzéséhez a a **műveletek** a MARS-konzolon válassza a panel **kapcsolatban a Microsoft Azure Recovery Services Agent**. Ellenőrizze, hogy a **verzió** szám egyenlő vagy nagyobb, mint az említett verzió [Ez a cikk](https://go.microsoft.com/fwlink/?linkid=229525). Válassza ezt a hivatkozást [a legújabb verzió letöltéséhez](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Lépjen a **Eszközkezelő** > **tárolóvezérlők** , és keresse meg **Microsoft iSCSI-kezdeményező**. Ha megtalálta, folytassa a 7.

4.  Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatás, próbálja meg, bejegyzés alatt található **Eszközkezelő** > **tárolóvezérlők** nevű **ismeretlen eszköz** a Hardverazonosítót **ROOT\ISCSIPRT**.

5.  Kattintson a jobb gombbal **ismeretlen eszköz** válassza **illesztőprogram frissítése**.

6.  A beállítás kiválasztásával az illesztőprogram frissítésével **keressen automatikusan a frissített illesztőprogram**. Ez a frissítés kell megváltoztatnia **ismeretlen eszköz** való **Microsoft iSCSI-kezdeményező**:

    ![Képernyőkép az Azure biztonsági mentés az Eszközkezelő, a kiemelt tárolóvezérlők](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Lépjen a **Feladatkezelő** > **szolgáltatások (helyi)**  > **Microsoft iSCSI-kezdeményező szolgáltatás**:

    ![Képernyőkép az Azure biztonsági mentési feladat-kezelő szolgáltatások (helyi) kiemeli a](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatás. Ehhez kattintson a jobb gombbal a szolgáltatás, és válassza ki **leállítása**. Ezután a jobb gombbal ismét, és válassza ki **Start**.

9.  Próbálja meg újra a helyreállítást a [azonnali visszaállítása](backup-instant-restore-capability.md).

Ha a helyreállítás is sikertelen, indítsa újra a kiszolgáló vagy ügyfél. Ha nem szeretné újraindítására, vagy ha a helyreállítás továbbra is sikertelen, a kiszolgáló újraindítása után is [helyreállítása egy másik gépről](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések
* Részletes adatainak [biztonsági mentése a Windows Server az Azure Backup-ügynökkel](tutorial-backup-windows-server-to-azure.md).
* Ha egy biztonsági másolat visszaállítása van szüksége, tekintse meg [fájlok visszaállítása Windows gépekre](backup-azure-restore-windows-server.md).
