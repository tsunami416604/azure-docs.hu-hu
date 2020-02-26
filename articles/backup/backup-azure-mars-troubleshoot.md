---
title: A Azure Backup ügynök hibáinak megoldása
description: Ebből a cikkből megtudhatja, hogyan lehet elhárítani a Azure Backup ügynök telepítését és regisztrálását.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: fdaad7e12a5f473a368b9249928591daddd68519
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583809"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS-) ügynök hibáinak megoldása

Ez a cikk a konfiguráció, a regisztráció, a biztonsági mentés és a visszaállítás során esetlegesen megjelenő hibák megoldását ismerteti.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a Microsoft Azure Recovery Services (MARS) ügynökével kapcsolatos hibaelhárítás megkezdése előtt győződjön meg a következőkről:

- [Győződjön meg arról, hogy a Mars-ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Győződjön meg arról, hogy van hálózati kapcsolat a Mars-ügynök és az Azure között](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Győződjön meg arról, hogy a MARS fut (a szolgáltatás konzolján). Ha szükséges, indítsa újra, majd próbálja megismételni a műveletet.
- Ügyeljen arra, hogy a kihelyezett [mappa helyén 5% – 10% szabad lemezterület álljon rendelkezésre](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Ellenőrizze, hogy egy másik folyamat vagy víruskereső szoftver zavarja-e Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Ha az ütemezett biztonsági mentés sikertelen, de a manuális biztonsági mentés működik, [a biztonsági mentések nem az ütemezés szerint futnak](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule).
- Győződjön meg arról, hogy az operációs rendszer rendelkezik a legújabb frissítésekkel.
- [Győződjön meg arról, hogy a nem támogatott, nem támogatott attribútumokkal rendelkező meghajtók és fájlok ki vannak zárva a biztonsági mentésből](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Győződjön meg arról, hogy a védett rendszer órája a megfelelő időzónára van konfigurálva.
- [Győződjön meg arról, hogy a .NET-keretrendszer 4.5.2-es vagy újabb verziója telepítve van a kiszolgálón](https://www.microsoft.com/download/details.aspx?id=30653).
- Ha a kiszolgálót egy tárolóba próbálja újra regisztrálni:
  - Győződjön meg arról, hogy az ügynök el lett távolítva a kiszolgálón, és hogy a portálról törölve lett.
  - Ugyanazt a jelszót használja, amelyet eredetileg a kiszolgáló regisztrálásához használt.
- Offline biztonsági mentések esetén győződjön meg arról, hogy a biztonsági mentés megkezdése előtt a forráson és a másolási számítógépen Azure PowerShell 3.7.0 van telepítve.
- Ha a biztonsági mentési ügynök Azure-beli virtuális gépen fut, tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>A tár megadott hitelesítő adatai érvénytelenek

**Hibaüzenet**: Érvénytelen tároló hitelesítő adatok lettek megadva. A fájl vagy sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal. (AZONOSÍTÓ: 34513)

| Ok | Javasolt teendők |
| ---     | ---    |
| **A tár hitelesítő adatai nem érvényesek** <br/> <br/> Előfordulhat, hogy a tár hitelesítő adatainak fájljai sérültek vagy lejártak. (Előfordulhat például, hogy a regisztráció időpontja előtt több mint 48 órával korábban töltötték le.)| Töltse le az új hitelesítő adatokat Recovery Services-tárból a Azure Portal. (Lásd a [Mars-ügynök letöltése](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) című szakasz 6. lépését.) Ezután hajtsa végre az alábbi lépéseket a megfelelő módon: <ul><li> Ha már telepítette és regisztrálta a MARSot, nyissa meg az Microsoft Azure Backup Agent MMC konzolt, majd a **műveletek** ablaktáblán válassza a **kiszolgáló regisztrálása** lehetőséget a regisztráció az új hitelesítő adatokkal való elvégzéséhez. <br/> <li> Ha az új telepítés meghiúsul, próbálja meg újratelepíteni az új hitelesítő adatokkal.</ul> **Megjegyzés**: Ha több tároló hitelesítő adatait letöltötte, csak a legújabb fájl érvényes a következő 48 órán belül. Azt javasoljuk, hogy töltsön le egy új, a tároló hitelesítő adatait tartalmazó fájlt.
| **A proxykiszolgáló/tűzfal blokkolja a regisztrációt** <br/>vagy <br/>**Nincs internetkapcsolat** <br/><br/> Ha a számítógép vagy a proxykiszolgáló korlátozott internetkapcsolattal rendelkezik, és nem biztosít hozzáférést a szükséges URL-címekhez, a regisztráció sikertelen lesz.| Hajtsa végre a következő lépéseket:<br/> <ul><li> Az informatikai csapattal együttműködve gondoskodhat arról, hogy a rendszeren legyen internetkapcsolat.<li> Ha nincs proxykiszolgáló, győződjön meg arról, hogy a proxy beállítás nincs kiválasztva az ügynök regisztrálása során. [Keresse meg a proxybeállításokat](#verifying-proxy-settings-for-windows).<li> Ha tűzfal-vagy proxykiszolgáló van, a hálózati csapattal együttműködve biztosíthatja, hogy ezek az URL-címek és IP-címek hozzáférhessenek:<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-címek**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Az előző hibaelhárítási lépések elvégzése után próbálkozzon újra a regisztrálással.<br></br> Ha a kapcsolatok az Azure ExpressRoute keresztül érhetők el, győződjön meg arról, hogy a beállítások az [Azure ExpressRoute-támogatás](backup-support-matrix-mars-agent.md#azure-expressroute-support)című témakörben leírtak szerint vannak konfigurálva.
| **A víruskereső szoftver blokkolja a regisztrációt** | Ha a kiszolgálón telepítve van víruskereső szoftver, adja hozzá a szükséges kizárási szabályokat a következő fájlokhoz és mappákhoz tartozó víruskereső vizsgálathoz: <br/><ul> <li> CBengine.exe <li> CSC. exe<li> A Scratch mappa. Alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A bin mappa a C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>További javaslatok

- Lépjen a C:/Windows/Temp értékre, és győződjön meg arról, hogy a. tmp kiterjesztéssel több mint 60 000 vagy 65 000 fájl található. Ha vannak ilyenek, törölje ezeket a fájlokat.
- Győződjön meg arról, hogy a gép dátumának és időpontjának egyeznie kell a helyi időzónával.
- Győződjön meg arról, hogy [ezek a helyek](backup-configure-vault.md#verify-internet-access) hozzá vannak adva a megbízható helyekhez az Internet Explorerben.

### <a name="verifying-proxy-settings-for-windows"></a>Proxybeállítások ellenőrzése Windows rendszeren

1. Töltse le a PsExec a [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) lapról.
1. `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` futtatása rendszergazda jogú parancssorból.

   Ezzel a paranccsal megnyílik az Internet Explorer.
1. Lépjen az **eszközök** > Internetbeállítások > **kapcsolatok** > **LAN-beállítások** **lehetőségre** .
1. Keresse meg a rendszerfiók proxybeállításait.
1. Ha nincs proxy konfigurálva, és a proxy adatai meg vannak adva, távolítsa el a részleteket.
1. Ha egy proxy konfigurálva van, és a proxy adatai helytelenek, győződjön meg arról, hogy a **proxy IP-címe** és a **port** adatai helyesek.
1. Az Internet Explorer bezárásához.

## <a name="unable-to-download-vault-credential-file"></a>Nem tölthető le a tár hitelesítőadat-fájlja

| Hiba   | Javasolt teendők |
| ---     | ---    |
|Nem sikerült letölteni a tároló hitelesítőadat-fájlját. (AZONOSÍTÓ: 403) | <ul><li> Próbálja meg letölteni a tár hitelesítő adatait egy másik böngészőben, vagy hajtsa végre a következő lépéseket: <ul><li> Start Internet Explorer. Válassza az F12 lehetőséget. </li><li> Nyissa meg a **hálózat** lapot, és törölje a gyorsítótárat és a cookie-kat. </li> <li> Frissítse az oldalt.<br></li></ul> <li> Ellenőrizze, hogy az előfizetés le van-e tiltva/lejárt-e.<br></li> <li> Ellenőrizze, hogy a tűzfalszabály blokkolja-e a letöltést. <br></li> <li> Gondoskodjon róla, hogy a tárolón ne legyen kimerítve a korlát (50 gép/tároló).<br></li>  <li> Győződjön meg arról, hogy a felhasználó rendelkezik a tár hitelesítő adatainak letöltéséhez szükséges Azure Backup engedélyekkel, és regisztrálja a kiszolgálót a tárolóban. [A Azure Backup helyreállítási pontok kezeléséhez lásd: szerepköralapú Access Control használata](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| Hiba  | Lehetséges ok | Javasolt teendők |
| ---     | ---     | ---    |
| <br /><ul><li>A Microsoft Azure Recovery szolgáltatás ügynökének nem sikerült kapcsolódnia a Microsoft Azure Backuphoz. (AZONOSÍTÓ: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy tud-e csatlakozni az internethez.<li>(407) proxy hitelesítés szükséges. |A proxy blokkolja a csatlakozást. |  <ul><li>Az Internet Explorerben nyissa meg a **Tools** > **internet Options** > **Security** > **Internet**lehetőséget. Válassza az **Egyéni szint** lehetőséget, majd görgessen le a **Fájl letöltése** szakaszhoz. Válassza az **Engedélyezés** lehetőséget.<p>Előfordulhat, hogy az Internet Explorerben [URL-címeket és IP-címeket](backup-configure-vault.md#verify-internet-access) is hozzá kell adnia a megbízható helyekhez.<li>Módosítsa a beállításokat proxykiszolgáló használatára. Ezután adja meg a proxykiszolgáló részleteit.<li> Ha a számítógépén korlátozott az Internet-hozzáférés, győződjön meg arról, hogy a számítógépen vagy a proxyn a tűzfalbeállítások engedélyezik ezeket az [URL-címeket és IP-címeket](backup-configure-vault.md#verify-internet-access). <li>Ha a kiszolgálón telepítve van a víruskereső szoftver, zárja ki ezeket a fájlokat a víruskeresőből: <ul><li>CBEngine. exe (a dpmra. exe helyett).<li>CSC. exe (a .NET-keretrendszerhez kapcsolódóan). Van egy CSC. exe a kiszolgálón telepített összes .NET-keretrendszer-verzióhoz. Zárja ki a CSC. exe fájlokat a .NET-keretrendszer összes verziójához az érintett kiszolgálón. <li>A mappa vagy a gyorsítótár helye. <br>A Scratch mappa vagy a gyorsítótár elérési útjának alapértelmezett helye: C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A bin mappa a C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a biztonságos biztonsági mentések titkosítási kulcsát

| Hiba | Lehetséges okok | Javasolt teendők |
| ---     | ---     | ---    |
| <br />Nem sikerült beállítani a biztonságos biztonsági mentések titkosítási kulcsát. Az aktiválás nem sikerült, de a titkosítási jelszó a következő fájlba lett mentve. |<li>A kiszolgáló már regisztrálva van egy másik tárolóban.<li>A konfiguráció során a jelszó megsérült.| Szüntesse meg a kiszolgáló regisztrációját a tárból, és regisztrálja újra az új jelszóval.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem fejeződött be sikeresen

| Hiba  | Lehetséges okok | Javasolt teendők |
|---------|---------|---------|
|<br />Az aktiválás nem fejeződött be sikeresen. Az aktuális művelet végrehajtása egy belső szolgáltatási hiba miatt meghiúsult [0x1FC07]. Próbálja meg újra a műveletet később. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához.     | <li> A kaparós mappa olyan köteten található, amely nem rendelkezik elegendő hellyel. <li> A Scratch mappa helytelenül lett áthelyezve. <li> A OnlineBackup. KEK fájl hiányzik.         | <li>Frissítsen a MARS-ügynök [legújabb verziójára](https://aka.ms/azurebackup_agent) .<li>Helyezze át a kihelyezett mappát vagy a gyorsítótár helyét egy olyan kötetre, amelynek szabad területe a biztonsági mentési adatmennyiség 5%-a és 10%-a között van. A gyorsítótár helyének megfelelő áthelyezéséhez tekintse meg a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)című témakör lépéseit.<li> Győződjön meg arról, hogy a OnlineBackup. KEK fájl megtalálható. <br>A *Scratch mappa vagy a gyorsítótár elérési útjának alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A titkosítási jelszó helytelenül van konfigurálva

| Hiba  | Lehetséges okok | Javasolt teendők |
|---------|---------|---------|
| <br />34506-es hiba. A számítógépen tárolt titkosítási jelszó nincs megfelelően konfigurálva.    | <li> A kaparós mappa olyan köteten található, amely nem rendelkezik elegendő hellyel. <li> A Scratch mappa helytelenül lett áthelyezve. <li> A OnlineBackup. KEK fájl hiányzik.        | <li>Frissítsen a MARS-ügynök [legújabb verziójára](https://aka.ms/azurebackup_agent) .<li>Helyezze át a kihelyezett mappát vagy a gyorsítótár helyét egy olyan kötetre, amelynek szabad területe a biztonsági mentési adatmennyiség 5%-a és 10%-a között van. A gyorsítótár helyének megfelelő áthelyezéséhez tekintse meg a [fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)című témakör lépéseit.<li> Győződjön meg arról, hogy a OnlineBackup. KEK fájl megtalálható. <br>A *Scratch mappa vagy a gyorsítótár elérési útjának alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>A biztonsági mentések nem az ütemezés szerint futnak

Ha az ütemezett biztonsági mentések nem indulnak el automatikusan, de a manuális biztonsági mentések megfelelően működnek, próbálkozzon a következő műveletekkel:

- Győződjön meg arról, hogy a Windows Server biztonsági másolatának ütemterve nem ütközik az Azure Files és mappák biztonsági mentési ütemtervével.

- Győződjön meg arról, hogy az online biztonsági mentés állapota beállítás **engedélyezve**értékre van állítva. Az állapot ellenőrzéséhez hajtsa végre a következő lépéseket:

  1. A Feladatütemezőben bontsa ki a **Microsoft** elemet, majd válassza az **online biztonsági mentés**lehetőséget.
  1. Kattintson duplán a **Microsoft-OnlineBackup** elemre, és lépjen az **Eseményindítók** lapra.
  1. Ellenőrizze, hogy az állapot **engedélyezve**értékre van-e állítva. Ha nem, válassza a **Szerkesztés**lehetőséget, válassza az **engedélyezve**lehetőséget, majd kattintson **az OK gombra**.

- Győződjön meg arról, hogy a feladat futtatásához kiválasztott felhasználói fiók vagy **rendszer** vagy **helyi Rendszergazdák csoport** a kiszolgálón. A felhasználói fiók ellenőrzéséhez lépjen az **általános** lapra, és ellenőrizze a **biztonsági** beállításokat.

- Győződjön meg arról, hogy a PowerShell 3,0-es vagy újabb verziója telepítve van a kiszolgálón. A PowerShell verziójának ellenőrzéséhez futtassa ezt a parancsot, és ellenőrizze, hogy a `Major` verziószáma 3 vagy újabb:

  `$PSVersionTable.PSVersion`

- Győződjön meg arról, hogy ez az elérési út a `PSMODULEPATH` környezeti változó része:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a `LocalMachine` PowerShell-végrehajtási szabályzata `restricted`értékre van állítva, akkor a biztonsági mentési feladatot kiváltó PowerShell-parancsmag sikertelen lehet. Futtassa az alábbi parancsokat emelt szintű módban a végrehajtási házirendnek a `Unrestricted` vagy `RemoteSigned`hoz való megadásához és beállításához:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Győződjön meg arról, hogy nincs hiányzó vagy sérült PowerShell-modul MSOnlineBackup-fájlja. Ha vannak hiányzó vagy sérült fájlok, hajtsa végre a következő lépéseket:

  1. Másolja a MSOnlineBackup mappát a C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules. webhelyről minden olyan gépről, amely megfelelően működik a MARS-ügynökkel.
  1. A problémás gépen illessze be a másolt fájlokat ugyanazon a mappában (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Ha már van MSOnlineBackup mappa a gépen, illessze be a fájlokat, vagy cserélje le a meglévő fájlokat.

> [!TIP]
> A módosítások következetes alkalmazása érdekében indítsa újra a kiszolgálót az előző lépések végrehajtása után.

## <a name="troubleshoot-restore-problems"></a>Visszaállítási problémák elhárítása

Előfordulhat, hogy a Azure Backup néhány perc elteltével sem sikerült csatlakoztatni a helyreállítási kötetet. A folyamat során hibaüzenetek jelenhetnek meg. A normál helyreállítás megkezdéséhez hajtsa végre a következő lépéseket:

1. Ha több percig is fut, szakítsa meg a csatlakoztatási folyamatot.

2. Ellenőrizze, hogy a biztonsági mentési ügynök legújabb verziója van-e telepítve. A verzió vizsgálatához a MARS-konzol **műveletek** paneljén válassza az **Microsoft Azure Recovery Services ügynök**elemet. Győződjön meg arról, hogy a **verziószám** értéke vagy magasabb, mint a [cikkben](https://go.microsoft.com/fwlink/?linkid=229525)említett verzió. Válassza ezt a hivatkozást [a legújabb verzió letöltéséhez](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Nyissa meg **Eszközkezelő** > **Storage-vezérlőket** , és keresse meg a **Microsoft iSCSI-kezdeményezőt**. Ha megtalálta, lépjen közvetlenül a 7. lépésre.

4. Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, próbáljon meg egy bejegyzést keresni **Eszközkezelő** > az **ismeretlen eszköz** nevű **Storage-vezérlőket** a **ROOT\ISCSIPRT**hardver-azonosítóval.

5. Kattintson a jobb gombbal az **ismeretlen eszköz** elemre, és válassza az **illesztőprogram-szoftver frissítése**lehetőséget.

6. Frissítse az illesztőprogramot úgy, hogy kijelöli a **frissített illesztőprogram-szoftver automatikus keresésének**lehetőségét. A frissítésnek **ismeretlen eszközt** kell megváltoztatnia a **Microsoft iSCSI-kezdeményezőnek**:

    ![Képernyőkép a Azure Backup Eszközkezelőról, a Storage-vezérlők kiemelésével](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Nyissa meg a **feladatkezelő** > **Services (helyi)**  > **Microsoft iSCSI-kezdeményező szolgáltatás**:

    ![Képernyőkép a Azure Backup Task Managerről, a szolgáltatások (helyi) kiemelve](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatást. Ehhez kattintson a jobb gombbal a szolgáltatásra, és válassza a **Leállítás**lehetőséget. Ezután kattintson rá a jobb gombbal, és válassza az **Indítás**lehetőséget.

9. Próbálja megismételni a helyreállítást az [azonnali visszaállítással](backup-instant-restore-capability.md).

Ha a helyreállítás továbbra is sikertelen, indítsa újra a kiszolgálót vagy az ügyfelet. Ha nem kívánja újraindítani a rendszert, vagy ha a helyreállítás még a kiszolgáló újraindítása után is sikertelen, próbálja meg [egy másik gépről helyreállítani](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Gyorsítótárral kapcsolatos problémák elhárítása

A biztonsági mentési művelet sikertelen lehet, ha a gyorsítótár mappája (más néven a Scratch mappa) helytelenül van konfigurálva, hiányoznak az előfeltételek, vagy korlátozott hozzáférése van.

### <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a MARS-ügynök műveletei sikeresek legyenek, a gyorsítótár mappájának meg kell felelnie az alábbi követelményeknek:

- [Győződjön meg arról, hogy a kihelyezett mappa helye 5% – 10% szabad lemezterülettel rendelkezik](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Győződjön meg arról, hogy a mappa helye érvényes és elérhető](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Győződjön meg arról, hogy a gyorsítótár mappában található fájlattribútumok támogatottak](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Győződjön meg arról, hogy a lefoglalt árnyékmásolat tárolóhelye elegendő a biztonsági mentési folyamathoz.](#increase-shadow-copy-storage)
- [Győződjön meg arról, hogy nincsenek más folyamatok (például víruskereső szoftverek) a gyorsítótár mappájához való hozzáférés korlátozásához](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Árnyékmásolat-tároló javítása

A biztonsági mentési műveletek sikertelenek lehetnek, ha nincs elegendő árnyékmásolat-tárolóhely az adatforrás védelme érdekében. A probléma megoldásához növelje az árnyékmásolat tárolóhelyét a védett köteten a vssadmin használatával az alábbi ábrán látható módon:

- A rendszergazda jogú parancssorban keresse meg az aktuális árnyékmásolat-tárolóhelyet:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Növelje az árnyék tárolóhelyét az alábbi parancs használatával:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Egy másik folyamat vagy víruskereső szoftver blokkolja a gyorsítótár mappához való hozzáférést

Ha a kiszolgálón telepítve van víruskereső szoftver, adja hozzá a szükséges kizárási szabályokat a következő fájlokhoz és mappákhoz tartozó víruskereső vizsgálathoz:  

- A Scratch mappa. Alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- A bin mappa a C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- CSC. exe

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz a MARS-ügynök használata során felmerülő gyakori hibákat ismerteti.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Hibaüzenet | Javasolt művelet |
-- | --
A Microsoft Azure Recovery Services-ügynök nem tudta elérni a biztonsági mentés ideiglenes helyen tárolt ellenőrzőösszegét | A probléma megoldásához hajtsa végre az alábbi műveleteket, majd indítsa újra a kiszolgálót <br/> - [ellenőrizze, hogy van-e víruskereső vagy más folyamat, amely zárolja a fájlokat a kaparós hely fájljaival](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [ellenőrizze, hogy a kiinduló hely érvényes-e, és elérhető-e a Mars-ügynök számára.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Hibaüzenet | Javasolt művelet |
-- | --
A Microsoft Azure Recovery Services-ügynök nem tudta elérni az ideiglenes helyet virtuális merevlemez inicializálásához | A probléma megoldásához hajtsa végre az alábbi műveleteket, majd indítsa újra a kiszolgálót <br/> - [ellenőrizze, hogy van-e víruskereső vagy más folyamat, amely zárolja a fájlokat a kaparós hely fájljaival](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [ellenőrizze, hogy a kiinduló hely érvényes-e, és elérhető-e a Mars-ügynök számára.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés nem sikerült, mert nincs elég hely a kötetben, ahol a kaparós mappa található. | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálja megismételni a műveletet:<br/>- [a Mars-ügynök legújabb ellenőrzése](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [a biztonsági másolatok területét érintő tárolási problémák ellenőrzése és feloldása](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Hibaüzenet | Javasolt művelet |
-- | --
Nem találhatók módosítások a fájlban. Ennek több oka lehet. Próbálkozzon újra a művelettel | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálja megismételni a műveletet:<br/> - [a Mars-ügynök legújabb ellenőrzése](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [a biztonsági másolatok területét érintő tárolási problémák ellenőrzése és feloldása](#prerequisites)

## <a name="next-steps"></a>Következő lépések

- További információ a [Windows Server biztonsági mentéséről a Azure Backup ügynökkel](tutorial-backup-windows-server-to-azure.md).
- Ha vissza kell állítania egy biztonsági mentést, olvassa el a [fájlok visszaállítása Windows rendszerű gépre](backup-azure-restore-windows-server.md)című témakört.
