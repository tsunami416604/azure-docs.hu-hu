---
title: Az Azure Backup Agent hibaelhárítása
description: Telepítési és regisztrációs Azure Backup Agent hibaelhárítása
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060215"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS-) ügynök hibaelhárítása

Oldja meg az esetlegesen előforduló hibák során konfigurációja, a regisztráció, a biztonsági mentés, a következőképpen és visszaállításához.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Azt javasoljuk, hogy hajtsa végre az alábbi érvényesítési, mielőtt használatba hibáinak elhárítása a Microsoft Azure Recovery Services-(MARS) ügynök:

- [Győződjön meg arról a Microsoft Azure Recovery Services (MARS) ügynöke naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://aka.ms/AB-A4dp50)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Ha szükséges, indítsa újra a gépet, és próbálja meg újra a műveletet
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://aka.ms/AB-AA4dwtt)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver nem zavarja-e az Azure Backup működését](https://aka.ms/AB-AA4dwtk)
- [Az ütemezett biztonsági mentés meghiúsul, de a manuális biztonsági mentés sikeres](https://aka.ms/ScheduledBackupFailManualWorks)
- Ellenőrizze, hogy az operációs rendszer rendelkezik-e a legújabb frissítésekkel
- [Győződjön meg, hogy a biztonsági mentés nem támogatott meghajtókkal és nem támogatott attribútumokkal rendelkező fájlok kizárva](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a védett rendszer **rendszerórája** a megfelelő időzónára van állítva <br>
- [Győződjön meg arról, hogy a kiszolgáló rendelkezik a .NET-keretrendszer 4.5.2-es vagy annál frissebb verziójával](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Ha a **kiszolgálót újra regisztrálni** szeretné egy tárolóhoz, akkor: <br>
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálóról és törölve lett a portálról <br>
  - Használja ugyanazt a jelszót, amelyet kezdetben használt a kiszolgáló regisztrálásához <br>
- Offline biztonsági mentés esetén ellenőrizze, hogy az Azure PowerShell verziója 3.7.0 telepítve van a forrás- és másolási számítógépen offline biztonsági mentés megkezdése előtt
- [Figyelembe veszi, amikor biztonsági mentést ügynököt egy Azure virtuális gépen fut.](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>Megadott tárhitelesítő adatok érvénytelenek

**Chybová zpráva**: A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatok a helyreállítási szolgáltatáshoz hozzárendelt. (Azonosító: 34513)

| Ok | Javasolt művelet |
| ---     | ---    |
| **A tároló hitelesítő adatai érvénytelenek.** <br/> <br/> Tároló hitelesítő adatfájljait esetleg sérült, vagy esetleg elévült (azaz letöltött regisztrációs ideje előtt legfeljebb 48 óra)| Töltse le az új hitelesítő adat a Recovery Services-tárolót az Azure Portalon (lásd: *6. lépés* alatt [ **töltse le a MARS-ügynök** ](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) szakaszban), és végezze el az alábbi: <ul><li> Ha már telepítve és regisztrálva a Microsoft Azure Backup ügynököt, majd nyissa meg a Microsoft Azure Backup ügynök MMC konzolt, és válassza a **kiszolgáló regisztrálása** a műveletpanelen az újonnan letöltött a regisztráció befejezéséhez hitelesítő adatok <br/> <li> Ha új telepítése nem sikerült majd telepítse újra az új hitelesítő adatokkal</ul> **Megjegyzés**: Ha több tároló hitelesítő adatfájljait a korábban letöltött, csak a legújabb letöltött fájl nem érvényes 48 órán belül. Ezért javasoljuk, hogy friss új tároló hitelesítőadat-fájljának letöltése.
| **Proxy Server/tűzfal blokkolja a <br/>vagy <br/>nincs internetkapcsolat** <br/><br/> Ha a gép vagy a proxykiszolgáló korlátozott Internet-hozzáféréssel rendelkezik majd anélkül, hogy a szükséges URL-címek listázása a regisztráció sikertelen lesz.| A probléma megoldásához hajtsa végre az alábbi:<br/> <ul><li> Az informatikai csapat a rendszer ne legyen internetkapcsolat használata<li> Ha nem kell proxykiszolgálót, majd győződjön meg arról, a proxy-beállítás nincs bejelölve, az ügynök regisztrálása során, ellenőrizze a proxy beállítások jelennek [Itt](#verifying-proxy-settings-for-windows)<li> Ha van tűzfal /-proxy kiszolgáló, a hálózatkezelésért felelős csapat, győződjön meg arról, hogy alábbi URL-címek és IP oldja meg a munkahelyi rendelkezik hozzáféréssel<br/> <br> **URLs**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**IP-cím**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>Próbálja meg ismét regisztrálni a fenti hibaelhárítási lépések végrehajtása után
| **Víruskereső szoftver blokkolja a** | Ha a kiszolgálón telepített víruskereső szoftver, szükséges kizárási szabályokat felvenni a következő fájlok a a víruskeresési vizsgálatból: <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> Ideiglenes mappát az alapértelmezett hely a *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* <li> Bin mappában *C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>További javaslatok
- Lépjen a *C:/Windows/Temp* , és ellenőrizze, hogy vannak-e több mint 60 000 vagy 65,000 .tmp kiterjesztésű fájlt. Ha vannak, ezeket a fájlokat törli.
- Győződjön meg arról, a gép dátum és idő helyi időzóna egyezéseit
- Győződjön meg, hogy a [következő](backup-configure-vault.md#verify-internet-access) helyek kerülnek IE megbízható helyek

### <a name="verifying-proxy-settings-for-windows"></a>Windows proxybeállításainak ellenőrzése

- Töltse le **psexec** a [Itt](https://docs.microsoft.com/sysinternals/downloads/psexec)
- Futtassa a következő parancsot `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` parancsot rendszergazda jogú parancssorból:
- Ekkor elindul *az Internet Explorer* ablak
- Lépjen a *eszközök* -> *Internetbeállítások* -> *kapcsolatok* -> *LAN-beállítások*
- Ellenőrizze a proxybeállítások *rendszer* fiók
- Ha nincs proxy van konfigurálva, és proxy részletes információkat, majd távolítsa el a részleteket
-   Ha proxy van konfigurálva, és helytelenek a proxy adatait, majd győződjön meg róla *Proxy IP* és *port* részletek pontosak.
- Bezárás *az Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>Nem sikerült letölteni a tároló hitelesítőadat-fájlja

| A hiba részletei | Ajánlott műveletek |
| ---     | ---    |
|Nem sikerült letölteni a tár hitelesítő adatait tartalmazó fájlt. (Azonosító: 403) | <ul><li> Próbálja meg letölteni a tároló hitelesítő adatait a másik böngészővel, vagy hajtsa végre az alábbi lépéseket: <ul><li> F12 billentyű IE, indítsa el. </li><li> Lépjen a **hálózati** fülre, és törölje az Internet Explorer cache és a cookie-k </li> <li> Frissítse a lapot<br>(OR)</li></ul> <li> Ellenőrizze, hogy az előfizetés le van tiltva vagy lejárt<br>(OR)</li> <li> Ellenőrizze, hogy ha bármilyen tűzfalszabály blokkolja a tároló hitelesítőadat-fájl letöltése <br>(OR)</li> <li> Győződjön meg arról, nem merül ki a korlátot, a tároló (tárolónként 50 gépet)<br>(OR)</li>  <li> Győződjön meg arról, felhasználói töltse le a tároló hitelesítő adatait, és regisztrálja a kiszolgálót a tárolóban, olvassa el az Azure Backup engedély szükséges [cikk](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| A hiba részletei | Lehetséges okok | Ajánlott műveletek |
| ---     | ---     | ---    |
| **Hiba történt** <br /><ol><li>*A Microsoft Azure Recovery Service Agent nem tudott kapcsolatot a Microsoft Azure Backup szolgáltatásban. (Azonosító: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy tud csatlakozni az internethez*<li>*(407) Proxyhitelesítés szükséges* |A proxy blokkolja a kapcsolatot. |  <ul><li>Indítsa el a **IE** > **beállítás** > **Internetbeállítások** > **biztonsági**  >  **Internet**. Válassza ki **Egyéni szint** görgessen, amíg megjelenik a fájl letöltése szakaszban. Válassza ki **engedélyezése**.<li>Akkor is lehet ezeken a webhelyeken hozzáadása az Internet Explorer [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Módosítsa a beállításokat, egy proxykiszolgáló használatára. Adja meg a proxy adatait.<li> Ha a gép korlátozott internet-hozzáféréssel, győződjön meg arról, hogy a gépen, vagy a proxy tűzfal beállításai engedélyezik-e [URL-címek](backup-configure-vault.md#verify-internet-access) és [IP-cím](backup-configure-vault.md#verify-internet-access). <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskeresés. <ul><li>CBEngine.exe (helyett dpmra.exe).<li>A CSC.exe (.NET-keretrendszer kapcsolatos). A CSC.exe minden .NET-verzió, amely a kiszolgálón van telepítve van. Az érintett kiszolgálón .NET-keretrendszer összes verziójához kötött CSC.exe fájlok kizárása. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>A bin mappa: C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni

| A hiba részletei | Lehetséges okok | Ajánlott műveletek |
| ---     | ---     | ---    |
| **Hiba történt** <br />*Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni. Az aktiválás nem sikerült teljesen, de a titkosítási jelszó mentése a következő fájl*. |<li>A kiszolgáló már regisztrálva van egy másik tárolóval.<li>A jelszó konfigurálása során sérült volt.| Törölje a tárolót a kiszolgáló regisztrációját, és regisztrálja újra az új jelszóval.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem sikerült

| A hiba részletei | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
|**Hiba történt** <br />*Az aktiválás sikertelenül zárult. Az aktuális művelet belső szolgáltatáshiba [0x1FC07] miatt nem sikerült. Némi várakozás után próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához*     | <li> Az ideiglenes mappát, amely nincs elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül áthelyezték egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik.         | <li>Frissítés a [legújabb verzió](https://aka.ms/azurebackup_agent) a MARS-ügynök.<li>A biztonsági mentési adatok teljes mérete 5 – 10 %-ával egyenlő szabad lemezterülettel rendelkező kötetre helyezze át az ideiglenes mappát vagy a gyorsítótár helyét. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [az Azure Backup ügynök kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Titkosítási jelszó nincs megfelelően konfigurálva

| A hiba részletei | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
|**Hiba történt** <br />*Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszava helytelenül van konfigurálva*.    | <li> Az ideiglenes mappát, amely nincs elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül áthelyezték egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik.        | <li>Frissítés a [legújabb verzió](https://aka.ms/azurebackup_agent) a MARS-ügynök.<li>Helyezze át az ideiglenes mappát vagy a gyorsítótár helyét egy biztonsági mentési adatok teljes mérete 5 – 10 %-ának megfelelő szabad lemezterülettel rendelkező kötetre. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [az Azure Backup ügynök kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Biztonsági másolatok ne futtassa a megadott ütemezés szerint
Ha ütemezett biztonsági mentések nem lekérése automatikusan, amíg a manuális biztonsági mentések problémamentesen működik, próbálja meg a következő műveleteket:

- Győződjön meg arról, a Windows Server biztonsági mentés ütemezése nem ütközik az Azure fájlok és mappák biztonsági mentés ütemezése.

- Ellenőrizze az Online biztonsági mentés állapotának beállítása **engedélyezése**. Ellenőrizze, hogy az állapot hajtsa végre az alábbi:

  - Nyissa meg **Feladatütemező** csomópontot **Microsoft**, és válassza ki **Online biztonsági mentés**.
  - Kattintson duplán a **Microsoft-OnlineBackup**, és nyissa meg a **eseményindítók** fülre.
  - Győződjön meg arról, ha az állapot értéke **engedélyezve**. Ha nem fut, majd válassza ki **szerkesztése** > **engedélyezve** jelölőnégyzetet, majd kattintson **OK**.

- Győződjön meg, hogy a feladat futtatásához a kiválasztott felhasználói fiók vagy **rendszer** vagy **a helyi Rendszergazdák csoport** a kiszolgálón. A felhasználói fiók ellenőrzéséhez nyissa meg a **általános** fülre és ellenőrizze a **biztonsági** beállítások.

- Győződjön meg arról, a PowerShell 3.0-s vagy újabb verziója telepítve van a kiszolgálón. A PowerShell-verziójának ellenőrzéséhez futtassa a következő parancsot, és ellenőrizze, hogy a *fő* verziószáma 3-nál nagyobb vagy egyenlő.

  `$PSVersionTable.PSVersion`

- Győződjön meg arról, a következő elérési út része a *PSMODULEPATH* környezeti változó

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a PowerShell végrehajtási szabályzata a *LocalMachine* van beállítva a korlátozott, a PowerShell-parancsmag, amely elindítja a biztonsági mentési feladat sikertelen lehet. Futtassa az alábbi parancsokat emelt jogosultságszintű módban, és a végrehajtási házirend beállítására vagy *Unrestricted* vagy *RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Gondoskodjon, hogy nem hiányzik vagy sérült állapotba kerül **PowerShell** modul **MSonlineBackup**. Abban az esetben minden olyan fájl hiányzik vagy sérült, oldja meg a probléma hajtsa végre az alábbi:

  - Másolja az MSOnlineBackup mappát a MARS-ügynök működik megfelelően, hogy bármely gépről *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* elérési útja.
  - Illessze be ezt a problémás gép elérési útja *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* .
  - Ha **MSOnlineBackup** mappa már létezik a gépen, illessze be vagy cserélje le a tartalomfájlokat, azon belül.


> [!TIP]
> Győződjön meg arról, hogy módosítások következetesek legyenek, hogy a fenti lépések végrehajtása után indítsa újra a kiszolgálót.


## <a name="troubleshoot-restore-issues"></a>Visszaállítási hibáinak elhárítása

Az Azure Backup előfordulhat, hogy nem sikerült csatlakoztatni a helyreállítási kötetet, akár néhány perc múlva. Hibaüzenetek is kaphat a folyamat során. Általában a helyreállítás megkezdéséhez kövesse az alábbi lépéseket:

1.  Megszakítja a folyamatban lévő csatlakozási folyamat, abban az esetben több percig futott.

2.  Tekintse meg, hogy a Backup szolgáltatás ügynökének legújabb verziója. Tudja meg a verziót a a **műveletek** a MARS-konzolon válassza a panel **kapcsolatban a Microsoft Azure Recovery Services Agent**. Ellenőrizze, hogy a **verzió** szám egyenlő vagy nagyobb, mint az említett verzió [Ez a cikk](https://go.microsoft.com/fwlink/?linkid=229525). A legújabb verziót [innen](https://go.microsoft.com/fwLink/?LinkID=288905) töltheti le.

3.  Lépjen a **Eszközkezelő** > **tárolóvezérlők**, és keresse meg **Microsoft iSCSI-kezdeményező**. Ha meg tudja találni, folytassa a 7.

4.  Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, próbálja meg, bejegyzés alatt található **Eszközkezelő** > **tárolóvezérlők** nevű **ismeretlen eszköz**, a Hardverazonosítót **ROOT\ISCSIPRT**.

5.  Kattintson a jobb gombbal a **ismeretlen eszköz**, és válassza ki **illesztőprogram frissítése**.

6.  A beállítás kiválasztásával az illesztőprogram frissítésével **keressen automatikusan a frissített illesztőprogram**. A frissítés befejezése után meg kell változtatni **ismeretlen eszköz** való **Microsoft iSCSI-kezdeményező**lent látható módon.

    ![Képernyőkép az Azure biztonsági mentés az Eszközkezelő, a kiemelt tárolóvezérlők](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Lépjen a **Feladatkezelő** > **szolgáltatások (helyi)**  > **Microsoft iSCSI-kezdeményező szolgáltatás**.

    ![Képernyőkép az Azure biztonsági mentési feladat-kezelő szolgáltatások (helyi) kiemeli a](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatás. Ehhez kattintson a jobb gombbal a szolgáltatáshoz, válassza a **leállítása**, a jobb gombbal ismét, és válassza ki **Start**.

9.  Próbálja meg újra a helyreállítást a [ **azonnali visszaállítása**](backup-instant-restore-capability.md).

Ha a helyreállítás is sikertelen, indítsa újra a kiszolgáló vagy ügyfél. Ha nem szeretne újraindítani, vagy a helyreállítás továbbra is a kiszolgáló újraindítása után is sikertelen, próbálkozzon egy másik számítógépre történő helyreállítás. Kövesse a [Ez a cikk](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések
* Részletes adatainak [hogyan készíthet biztonsági másolatot a Windows Server az Azure Backup-ügynökkel](tutorial-backup-windows-server-to-azure.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
