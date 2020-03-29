---
title: Az Azure Backup ügynök – hibaelhárítás
description: Ebből a cikkből megtudhatja, hogyan háríthatja el az Azure Backup-ügynök telepítését és regisztrációját.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 24169356600c25e664221af397051bb0fec3e459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673109"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services (MARS) ügynök – hibaelhárítása

Ez a cikk a konfiguráció, a regisztráció, a biztonsági mentés és a visszaállítás során előforduló hibák elhárítását ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a Microsoft az Azure Recovery Services (MARS) ügynökhiba-elhárításának megkezdése előtt ellenőrizze az alábbiakat:

- [Győződjön meg arról, hogy a MARS-ügynök naprakész.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy van hálózati kapcsolat a MARS ügynök és az Azure között.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a MARS fut (a Service console-ban). Ha szükséges, indítsa újra, majd próbálkozzon újra a művelettel.
- [Győződjön meg arról, hogy 5–10%-os szabad kötetterület áll rendelkezésre a tervezőasztal helyén.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver zavarja-e az Azure Backup szolgáltatást.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- Ha az ütemezett biztonsági mentés sikertelen, de a manuális biztonsági mentés működik, olvassa el a [Biztonsági mentések nem az ütemezés nek megfelelően való futtatása.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Győződjön meg arról, hogy az operációs rendszer rendelkezik a legújabb frissítésekkel.
- [Győződjön meg arról, hogy a nem támogatott meghajtók és a nem támogatott attribútumokkal rendelkező fájlok ki vannak zárva a biztonsági mentésből.](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Győződjön meg arról, hogy a védett rendszer órája a megfelelő időzónára van konfigurálva.
- [Győződjön meg arról, hogy a .NET Framework 4.5.2-es vagy újabb rendszer telepítve van a kiszolgálón.](https://www.microsoft.com/download/details.aspx?id=30653)
- Ha újra regisztrálni szeretné a kiszolgálót egy trezorba:
  - Győződjön meg arról, hogy az ügynök eltávolításra kerül a kiszolgálón, és hogy törlődik a portálról.
  - Használja ugyanazt a jelszót, amelyet eredetileg a kiszolgáló regisztrálásához használt.
- Offline biztonsági mentések esetén győződjön meg arról, hogy az Azure PowerShell 3.7.0 telepítve van a forrásra és a másolószámítógépre a biztonsági mentés megkezdése előtt.
- Ha a biztonságimásolat-ügynök egy Azure virtuális gépen fut, olvassa el [ezt a cikket.](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

## <a name="invalid-vault-credentials-provided"></a>A tároló megadott hitelesítő adatai érvénytelenek

**Hibaüzenet**: Érvénytelen tárolóhitelesítő adatok at. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatással társított legújabb hitelesítő adatokkal. (ID: 34513)

| Ok | Ajánlott műveletek |
| ---     | ---    |
| **A tároló hitelesítő adatai érvénytelenek** <br/> <br/> Lehet, hogy a tároló hitelesítő fájljai sérültek vagy lejártak. (Előfordulhat például, hogy a regisztráció előtt több mint 48 órával töltötték le őket.)| Új hitelesítő adatok letöltése a Recovery Services-tárolóból az Azure Portalon. (Lásd a [MARS-ügynök letöltése](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) című szakasz 6. lépését.) Ezután tegye meg az alábbi lépéseket: <ul><li> Ha már telepítette és regisztrálta a MARS-ot, nyissa meg a Microsoft Azure Backup Agent MMC konzolt, majd válassza a **Kiszolgáló regisztrálása** lehetőséget a **Műveletek** ablaktáblán a regisztráció befejezéséhez az új hitelesítő adatokkal. <br/> <li> Ha az új telepítés sikertelen, próbálja meg újratelepíteni az új hitelesítő adatokkal.</ul> **Megjegyzés:** Ha több tároló hitelesítő fájl töltle, csak a legújabb fájl érvényes a következő 48 órában. Azt javasoljuk, hogy töltse le egy új tároló hitelesítő fájl letöltését.
| **A proxykiszolgáló/tűzfal blokkolja a regisztrációt** <br/>vagy <br/>**Nincs internetkapcsolat** <br/><br/> Ha a számítógép vagy a proxykiszolgáló korlátozott internetkapcsolattal rendelkezik, és nem biztosítja a szükséges URL-címekhez való hozzáférést, a regisztráció sikertelen lesz.| Tegye meg az alábbi lépéseket:<br/> <ul><li> Működjön együtt informatikai csapatával annak érdekében, hogy a rendszer rendelkezik internetkapcsolattal.<li> Ha nem rendelkezik proxykiszolgálóval, győződjön meg arról, hogy a proxybeállítás nincs bejelölve az ügynök regisztrálásakor. [Ellenőrizze a proxy beállításokat](#verifying-proxy-settings-for-windows).<li> Ha rendelkezik tűzfal-proxykiszolgálóval, a hálózati csapattal együttműködve győződjön meg arról, hogy ezek az URL-címek és IP-címek hozzáférnek:<br/> <br> **Urls**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-címek**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Próbálkozzon újra a regisztrációval az előző hibaelhárítási lépések elvégzése után.<br></br> Ha az Azure ExpressRoute-on keresztül kapcsolódik a kapcsolathoz, győződjön meg arról, hogy a beállítások az [Azure ExpressRoute-támogatásban](backup-support-matrix-mars-agent.md#azure-expressroute-support)leírtak szerint vannak konfigurálva.
| **A víruskereső szoftver blokkolja a regisztrációt** | Ha víruskereső szoftver van telepítve a kiszolgálón, adja hozzá a szükséges kizárási szabályokat a fájlok és mappák víruskereső vizsgálatához: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> A semmiből dosszié. Az alapértelmezett helye: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A C:\Program Files\Microsoft Azure Recovery Services Agent\Bin raktárhely tárolómappája.

### <a name="additional-recommendations"></a>További ajánlások

- Nyissa meg a C:/Windows/Temp fájlt, és ellenőrizze, hogy több mint 60 000 vagy 65 000 fájl van-e .tmp kiterjesztéssel. Ha vannak, törölje ezeket a fájlokat.
- Győződjön meg arról, hogy a készülék dátuma és időpontja megegyezik a helyi időzónával.
- Győződjön meg [arról,](install-mars-agent.md#verify-internet-access) hogy ezek a helyek hozzá vannak adva a megbízható helyekhez az Internet Explorer programban.

### <a name="verifying-proxy-settings-for-windows"></a>A Windows proxybeállításainak ellenőrzése

1. Töltse le a PsExec-et a [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) oldalról.
1. Fusson `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` egy rendszergazda jogú parancssorból.

   Ez a parancs megnyitja az Internet Explorer t.
1. Nyissa meg **az Eszközök** > **– Internetbeállítások** > **Kapcsolatok** > **LAN-beállításai**.
1. Ellenőrizze a rendszerfiók proxybeállításait.
1. Ha nincs beállítva proxy, és proxy részletek vannak megadva, távolítsa el a részleteket.
1. Ha egy proxy konfigurálva van, és a proxy részletei helytelenek, győződjön meg arról, hogy a **proxy IP-és** **portadatai** helyesek.
1. Zárja be az Internet Explorert.

## <a name="unable-to-download-vault-credential-file"></a>Nem lehet letölteni a tároló hitelesítő fájlját

| Hiba   | Ajánlott műveletek |
| ---     | ---    |
|Nem sikerült letölteni a tároló hitelesítő adatfájlját. (ID: 403) | <ul><li> Próbálja meg letölteni a tároló hitelesítő adatait egy másik böngészővel, vagy tegye meg az alábbi lépéseket: <ul><li> Indítsa el az Internet Explorert. Válassza az F12 lehetőséget. </li><li> Lépjen a **Hálózat** lapra, és törölje a gyorsítótárat és a cookie-kat. </li> <li> Frissítse az oldalt.<br></li></ul> <li> Ellenőrizze, hogy az előfizetés le van-e tiltva/lejárt.Check if the subscription is disabled/expired.<br></li> <li> Ellenőrizze, hogy a tűzfalszabály blokkolja-e a letöltést. <br></li> <li> Győződjön meg róla, hogy nem merítette ki a tárolókorlátot (50 gép tárolónként).<br></li>  <li> Győződjön meg arról, hogy a felhasználó rendelkezik az Azure Backup engedélyekkel, amelyek szükségesek a tároló hitelesítő adatainak letöltéséhez és a kiszolgáló regisztrálásához a tárolóval. Az [Azure Biztonsági másolat helyreállítási pontjainak kezeléséhez olvassa el a Szerepköralapú hozzáférés-vezérlés használata című témakört.](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| Hiba  | Lehetséges ok | Ajánlott műveletek |
| ---     | ---     | ---    |
| <br /><ul><li>A Microsoft Azure helyreállítási szolgáltatásügynök nem tudott csatlakozni a Microsoft Azure biztonsági másolathoz. (ID: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy képes csatlakozni az internethez.<li>(407) Proxy hitelesítés szükséges. |Egy proxy blokkolja a kapcsolatot. |  <ul><li>Az Internet Explorer ben nyissa meg **az Eszközök** > internetbeállítások**Biztonsági** > **internet****lehetőséget.** >  Válassza **az Egyéni szint lehetőséget,** és görgessen le a Fájl **letöltése** szakaszhoz. Válassza az **Engedélyezés** lehetőséget.<p>Előfordulhat, hogy [URL-címeket és IP-címeket](install-mars-agent.md#verify-internet-access) is hozzá kell adnia a megbízható helyekhez az Internet Explorer programban.<li>Módosítsa a beállításokat proxykiszolgáló használatához. Ezután adja meg a proxykiszolgáló adatait.<li> Ha a számítógép korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a tűzfal beállításai a számítógépen vagy a proxyn engedélyezik ezeket az [URL-címeket és IP-címeket.](install-mars-agent.md#verify-internet-access) <li>Ha víruskereső szoftver van telepítve a kiszolgálón, zárja ki ezeket a fájlokat a víruskereső vizsgálatból: <ul><li>CBEngine.exe (a dpmra.exe helyett).<li>CSC.exe (a .NET framework programmal kapcsolatos). A kiszolgálón telepített összes . Zárja ki a CSC.exe fájlokat a .NET Framework összes verziójához az érintett kiszolgálón. <li>A kaparós sorsjegy mappája vagy gyorsítótára. <br>A kaparós mappa vagy a gyorsítótár elérési útja a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch alapértelmezett helye.<li>A C:\Program Files\Microsoft Azure Recovery Services Agent\Bin raktárhely tárolómappája.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült a biztonsági mentések biztonságossá tételéhez szükséges titkosítási kulcs beállítása

| Hiba | Lehetséges okok | Ajánlott műveletek |
| ---     | ---     | ---    |
| <br />Nem sikerült beállítani a titkosítási kulcsot a biztonságos biztonsági mentések hez. Az aktiválás nem sikerült teljesen, de a titkosítási jelszó a következő fájlba lett mentve. |<li>A kiszolgáló már regisztrálva van egy másik trezorral.<li>A konfiguráció során a jelszó sérült.| Törölje a kiszolgáló regisztrációját a tárolóból, és regisztrálja újra egy új jelszóval.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem fejeződött be sikeresen

| Hiba  | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
|<br />Az aktiválás sikertelenül zárult. Az aktuális művelet belső szolgáltatáshiba miatt nem sikerült [0x1FC07] miatt. Próbálja meg újra a műveletet később. If the issue persists, please contact Microsoft support. (Az Azure Key Vault-művelet meghiúsult. Próbálja meg újból végrehajtani a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.)     | <li> A kaparós sorsú mappa olyan köteten található, amelynek nincs elég helye. <li> A semmiből vett mappa helytelenül lett áthelyezve. <li> Hiányzik az OnlineBackup.KEK fájl.         | <li>Frissítsen a MARS-ügynök [legújabb verziójára.](https://aka.ms/azurebackup_agent)<li>Helyezze át a tervezőmappa vagy a gyorsítótár helyét egy olyan kötetre, amelynek szabad területe a teljes biztonsági mentési adatméretének 5–10%-a. A gyorsítótár helyének helyes áthelyezéséhez olvassa el a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)című részben leírt lépéseket.<li> Győződjön meg arról, hogy az OnlineBackup.KEK fájl jelen van. <br>*A kaparós sorsmappa vagy a gyorsítótár elérési útja a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A titkosítási jelszó helytelenül van konfigurálva

| Hiba  | Lehetséges okok | Ajánlott műveletek |
|---------|---------|---------|
| <br />34506-os hiba. A számítógépen tárolt titkosítási jelszó nincs megfelelően konfigurálva.    | <li> A kaparós sorsú mappa olyan köteten található, amelynek nincs elég helye. <li> A semmiből vett mappa helytelenül lett áthelyezve. <li> Hiányzik az OnlineBackup.KEK fájl.        | <li>Frissítsen a MARS-ügynök [legújabb verziójára.](https://aka.ms/azurebackup_agent)<li>Helyezze át a tervezőmappa vagy a gyorsítótár helyét egy olyan kötetre, amelynek szabad területe a teljes biztonsági mentési adatméretének 5–10%-a. A gyorsítótár helyének helyes áthelyezéséhez olvassa el a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)című részben leírt lépéseket.<li> Győződjön meg arról, hogy az OnlineBackup.KEK fájl jelen van. <br>*A kaparós sorsmappa vagy a gyorsítótár elérési útja a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>A biztonsági mentések nem az ütemezés szerint futnak

Ha az ütemezett biztonsági mentések nem aktiválódnak automatikusan, de a manuális biztonsági mentések megfelelően működnek, próbálkozzon a következő műveletekkel:

- Győződjön meg arról, hogy a Windows Server biztonsági mentési ütemezése nem ütközik az Azure-fájlok és mappák biztonsági mentési ütemezésével.

- Győződjön meg arról, hogy az online biztonsági mentés állapota **Engedélyezés.** Az állapot ellenőrzéséhez tegye a következő lépéseket:

  1. A Feladatütemezőben bontsa ki a **Microsoft** csomópontot, és válassza **az Online biztonsági másolat lehetőséget.**
  1. Kattintson duplán a **Microsoft-OnlineBackup elemre,** és lépjen az **Eseményindítók** lapra.
  1. Ellenőrizze, hogy az állapot Engedélyezve értékre **van-e állítva.** Ha nem, válassza a **Szerkesztés**, majd **az Engedélyezve**lehetőséget, majd az **OK**gombot.

- Győződjön meg arról, hogy a feladat futtatásához kijelölt felhasználói fiók a **RENDSZER** vagy a **Helyi rendszergazdák csoportja** a kiszolgálón. A felhasználói fiók ellenőrzéséhez lépjen az **Általános** lapra, és ellenőrizze a **Biztonsági** beállításokat.

- Győződjön meg arról, hogy a PowerShell 3.0-s vagy újabb verzió telepítve van a kiszolgálón. A PowerShell-verzió ellenőrzéséhez futtassa `Major` ezt a parancsot, és ellenőrizze, hogy a verziószám 3-as vagy újabb:

  `$PSVersionTable.PSVersion`

- Győződjön meg arról, hogy ez az elérési út a `PSMODULEPATH` környezeti változó része:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a PowerShell `LocalMachine` végrehajtási szabályzata van beállítva, hogy `restricted`a PowerShell-parancsmag, amely elindítja a biztonsági mentési feladat sikertelen lehet. A parancsok magas szintű módban futtatva ellenőrizze `Unrestricted` és `RemoteSigned`állítsa a végrehajtási házirendet vagy a következőre:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Győződjön meg arról, hogy nincsenek hiányzó vagy sérült PowerShell-modul MSOnlineBackup fájlok. Ha hiányzó vagy sérült fájlok vannak, tegye az alábbi lépéseket:

  1. Minden olyan gépről, amelyen megfelelően működik a MARS-ügynök, másolja az MSOnlineBackup mappát a C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules mappából.
  1. A problémás gépen illessze be a másolt fájlokat ugyanarra a mappára (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Ha már van MSOnlineBackup mappa a számítógépen, illessze be a fájlokat, vagy cserélje le a meglévő fájlokat.

> [!TIP]
> A módosítások következetes alkalmazásának biztosításához indítsa újra a kiszolgálót az előző lépések végrehajtása után.

## <a name="troubleshoot-restore-problems"></a>Visszaállítási problémák elhárítása

Előfordulhat, hogy az Azure Backup nem lehet sikeresen csatlakoztatni a helyreállítási kötetet, még néhány perc elteltével is. És előfordulhat, hogy hibaüzeneteket kap a folyamat során. A normális helyreállítás megkezdéséhez tegye meg az alábbi lépéseket:

1. Szakítsa meg a csatlakoztatási folyamatot, ha már több perce fut.

2. Ellenőrizze, hogy a biztonsági másolat ügynök legújabb verzióját használja-e. A verzió ellenőrzéséhez a MARS konzol **Műveletek** ablaktábláján válassza a **Microsoft Azure Recovery Services Agent – Beszerzés– alkalmazásról lehetőséget.** Ellenőrizze, hogy a **verziószám** megegyezik-e vagy magasabb-e, mint a [cikkben](https://go.microsoft.com/fwlink/?linkid=229525)említett verzió. Válassza ezt a linket [a legújabb verzió letöltéséhez.](https://go.microsoft.com/fwLink/?LinkID=288905)

3. Nyissa meg az **Eszközkezelő** > **tárolóvezérlőit,** és keresse meg **a Microsoft iSCSI-kezdeményezőt.** Ha megtalálta, lépjen közvetlenül a 7.

4. Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, próbáljon meg egy bejegyzést keresni az **Ismeretlen eszköz** hardverazonosítóval/ISCSIPRT **ROOT\ISCSIPRT**nevű **Eszközkezelő-vezérlők** > **Storage controllers** csoportban.

5. Kattintson a jobb gombbal **az Ismeretlen eszköz elemre,** és válassza **az Illesztőprogram frissítése parancsot.**

6. Frissítse az illesztőprogramot a **frissített illesztőprogram automatikus keresése**lehetőség kiválasztásával. A frissítésnek az **Ismeretlen eszközt** **Microsoft iSCSI-kezdeményezőre kell módosítania:**

    ![Képernyőkép az Azure biztonsági másolat eszközkezelőjéről, kiemelve a Storage-vezérlőket](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Nyissa meg a **Feladatkezelő** > **szolgáltatások (helyi)** > **Microsoft iSCSI-kezdeményező szolgáltatását**:

    ![Képernyőkép az Azure Biztonsági mentés feladatkezelőjéről, kiemelve szolgáltatások (helyi)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatást. Ehhez kattintson a jobb gombbal a szolgáltatásra, és válassza a **Leállítás parancsot.** Ezután kattintson rá a jobb gombbal, és válassza a **Start parancsot.**

9. Próbálja meg újra a helyreállítást az [Azonnali visszaállítás használatával.](backup-instant-restore-capability.md)

Ha a helyreállítás továbbra is sikertelen, indítsa újra a kiszolgálót vagy az ügyfelet. Ha nem szeretné újraindítani, vagy ha a helyreállítás a kiszolgáló újraindítása után is sikertelen, próbáljon [meg egy másik gépről helyreállítva.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Gyorsítótárproblémák elhárítása

A biztonsági mentési művelet sikertelen lehet, ha a gyorsítótármappa (más néven kaparós mappa) helytelenül van konfigurálva, hiányoznak az előfeltételek, vagy korlátozott hozzáféréssel rendelkezik.

### <a name="prerequisites"></a>Előfeltételek

A MARS ügynök műveletek sikeres a cache mappát kell tartania az alábbi követelményeknek:

- [Annak biztosítása, hogy 5–10%-os szabad kötetterület legyen elérhető a tervezőasztal helyén](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [A kaparós sorsmappa helyének érvényességének és akadályának biztosítása](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [A gyorsítótár-mappa fájlattribútumainak támogatása](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Annak biztosítása, hogy a lefoglalt árnyékmásolat-tárterület elegendő legyen a biztonsági mentési folyamathoz](#increase-shadow-copy-storage)
- [Győződjön meg arról, hogy nincs más folyamat (pl. anti-vírus szoftver), amely korlátozza a gyorsítótármappához való hozzáférést](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Árnyékmásolat-tárolás növelése

A biztonsági mentési műveletek sikertelenek lehetnek, ha nincs elegendő árnyékmásolat-tárolóhely az adatforrás védelméhez. A probléma megoldásához növelje a védett kötet árnyékmásolati tárhelyét a vssadmin használatával az alábbiak szerint:

- Ellenőrizze az aktuális árnyéktároló helyet a rendszergazda jogú parancssorból:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Növelje az árnyéktárhelyet az alábbi paranccsal:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Egy másik folyamat vagy víruskereső szoftver, amely blokkolja a gyorsítótár mappához való hozzáférést

Ha víruskereső szoftver van telepítve a kiszolgálón, adja hozzá a szükséges kizárási szabályokat a fájlok és mappák víruskereső vizsgálatához:  

- A semmiből dosszié. Az alapértelmezett helye: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- A C:\Program Files\Microsoft Azure Recovery Services Agent\Bin raktárhely mappája
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz a MARS-ügynök használata során előforduló gyakori hibákat ismerteti.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Hibaüzenet | Javasolt művelet |
-- | --
A Microsoft Azure Recovery Services-ügynök nem tudta elérni a biztonsági mentés ideiglenes helyen tárolt ellenőrzőösszegét | A probléma megoldásához hajtsa végre az alábbi műveletet, és indítsa újra a kiszolgálót <br/> - [Ellenőrizze, hogy van-e víruskereső vagy más folyamatok zár a semmiből hely fájlokat](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Ellenőrizze, hogy a kaparós sorsjegy érvényes és hozzáférhető-e a Mars-ügynök számára.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Hibaüzenet | Javasolt művelet |
-- | --
A Microsoft Azure Recovery Services-ügynök nem tudta elérni az ideiglenes helyet virtuális merevlemez inicializálásához | A probléma megoldásához hajtsa végre az alábbi műveletet, és indítsa újra a kiszolgálót <br/> - [Ellenőrizze, hogy van-e víruskereső vagy más folyamatok zár a semmiből hely fájlokat](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Ellenőrizze, hogy a kaparós sorsjegy érvényes és hozzáférhető-e a Mars-ügynök számára.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace (SalLowDiskSpace)

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés nem sikerült, mert nem volt elegendő a köteten lévő tároló, ahol a semmiből tároló mappa található | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálkozzon újra a művelettel:<br/>- [Győződjön meg róla, hogy a MARS-ügynök a legújabb](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [A biztonsági mentés takarását érintő tárolási problémák ellenőrzése és megoldása](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Hibaüzenet | Javasolt művelet |
-- | --
Nem találhatók módosítások a fájlban. Ennek több oka lehet. Próbálkozzon újra a művelettel | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálkozzon újra a művelettel:<br/> - [Győződjön meg róla, hogy a MARS-ügynök a legújabb](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [A biztonsági mentés takarását érintő tárolási problémák ellenőrzése és megoldása](#prerequisites)

## <a name="next-steps"></a>További lépések

- További részletek a Windows Server biztonsági mentésének az [Azure Backup ügynökkel való biztonsági mentésével kapcsolatban.](tutorial-backup-windows-server-to-azure.md)
- Ha biztonsági másolatot kell visszaállítania, olvassa el [a Fájlok visszaállítása Windows rendszerbe](backup-azure-restore-windows-server.md).
