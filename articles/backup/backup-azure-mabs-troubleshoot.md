---
title: Az Azure Backup Server hibaelhárítása
description: A telepítés, az Azure Backup Server regisztrálása, valamint az alkalmazás-munkaterhelések biztonsági mentése és visszaállítása.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: deff49a7d00a335c396a6fa36d3846ef353331c5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421290"
---
# <a name="troubleshoot-azure-backup-server"></a>Az Azure Backup Server hibaelhárítása

Az alábbi táblázatokban található információk segítségével elháríthatja az Azure Backup Server használata során előforduló hibákat.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a Microsoft Azure Backup Server (MABS) hibaelhárításának megkezdése előtt hajtsa végre az alábbi érvényesítést:

- [Annak biztosítása, hogy a Microsoft Azure Recovery Services (MARS) ügynök naprakész legyen](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra, majd próbálkozzon újra a művelettel
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Ha a regisztráció sikertelen, akkor győződjön meg arról, hogy az azure backup servert telepíteni kívánt kiszolgáló még nincs regisztrálva egy másik tárolóban
- Ha a leküldéses telepítés sikertelen, ellenőrizze, hogy a DPM-ügynök már jelen van-e a rendszeren. Ha igen, távolítsa el az ügynököt, és próbálkozzon újra a telepítéssel
- [Győződjön meg arról, hogy nem zavarja másik folyamat vagy víruskereső szoftver az Azure Backup működését](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Annak ellenőrzése, hogy az SQL Agent szolgáltatás fut-e, és automatikusra van állítva a MABS-kiszolgálón<br>

## <a name="invalid-vault-credentials-provided"></a>A tároló megadott hitelesítő adatai érvénytelenek

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Regisztráció a trezorba | A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal. | Ajánlott művelet: <br> <ul><li> Töltse le a legújabb hitelesítő adatokat a tárolóból, majd próbálkozzon újra. <br>(1)</li> <li> Ha az előző művelet nem működött, próbálja meg letölteni a hitelesítő adatokat egy másik helyi könyvtárba, vagy hozzon létre egy új tárolót. <br>(1)</li> <li> Próbálja meg frissíteni a dátum- és időbeállításokat a [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided)leírtak szerint. <br>(1)</li> <li> Ellenőrizze, hogy a c:\windows\temp több mint 65000 fájlt tartalmaz-e. Helyezze át az elavult fájlokat egy másik helyre, vagy törölje az Ideiglenes mappában lévő elemeket. <br>(1)</li> <li> Ellenőrizze a tanúsítványok állapotát. <br> a. Nyissa meg **a Számítógép-tanúsítványok kezelése segédprogramot** (a Vezérlőpulton). <br> b. Bontsa ki a **Személyes** csomópontot és annak gyermekcsomóponti **tanúsítványokat.**<br> c.  Távolítsa el a **Windows Azure Tools tanúsítványt.** <br> d. Próbálkozzon újra a regisztrációval az Azure Backup-ügyfélben. <br> (1) </li> <li> Ellenőrizze, hogy van-e érvényben csoportházirend. </li></ul> |

## <a name="replica-is-inconsistent"></a>A replika inkonzisztens

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | A replika inkonzisztens | Ellenőrizze, hogy a Védelmi csoport varázsló automatikus konzisztencia-ellenőrzési beállítása be van-e kapcsolva. A replikainkonkonkonkonkonkonkonkonkonkonkonvaló konzisztencia okairól és a vonatkozó javaslatokról a [Replika inkonzisztens](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10))című cikkben olvashat bővebben.<br> <ol><li> Rendszerállapot/BMR biztonsági mentés esetén ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett kiszolgálón.</li><li> Ellenőrizze, hogy vannak-e térközrel kapcsolatos problémák a DPM/Microsoft Azure backup kiszolgálóDPM-tárolókészletében, és szükség szerint foglalja le a tárhelyet.</li><li> Ellenőrizze a kötet árnyékmásolata szolgáltatás állapotát a védett kiszolgálón. Ha letiltott állapotban van, állítsa be manuálisan elindulna. Indítsa el a szolgáltatást a kiszolgálón. Ezután lépjen vissza a DPM/Microsoft Azure Backup Server konzolra, és indítsa el a szinkronizálást a konzisztencia-ellenőrzési feladattal.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Az online helyreállítási pont létrehozása nem sikerült

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Az online helyreállítási pont létrehozása nem sikerült | **Hibaüzenet: A**Windows Azure biztonsági másolat ügynöke nem tudott pillanatképet létrehozni a kijelölt kötetről. <br> **Megoldás:** Próbálja meg növelni a replika- és helyreállításipont-kötet területét.<br> <br> **Hibaüzenet:** A Windows Azure biztonságimásolat-ügynök nem tud csatlakozni az OBEngine szolgáltatáshoz <br> **Megoldás:** ellenőrizze, hogy az OBEngine létezik-e a számítógépen futó szolgáltatások listájában. Ha az OBEngine szolgáltatás nem fut, használja a "net start OBEngine" parancsot az OBEngine szolgáltatás elindításához. <br> <br> **Hibaüzenet**: A kiszolgáló titkosítási jelmondata nincs beállítva. Konfiguráljon titkosítási jelszót. <br> **Megoldás:** Próbáljon meg beállítani egy titkosítási jelszót. Ha nem sikerül, tegye a következő lépéseket: <br> <ol><li>Ellenőrizze, hogy létezik-e a tervezőhely. Ez az a hely, amely a rendszerleíró adatbázisban szerepel **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config,** amelynek **ScratchLocation** nevű nevét kell léteznie.</li><li> Ha a tervezőnév létezik, próbálja meg újra regisztrálni a régi jelszót. *Amikor titkosítási jelszót állít be, mentse azt biztonságos helyre.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Az eredeti és a külső DPM-kiszolgálókat ugyanahhoz a tárolóhoz kell regisztrálni

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Visszaállítás | **Hibakód**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Hiba: 100110 <br/> <br/>**Hibaüzenet:** Az eredeti és a külső DPM-kiszolgálókat ugyanahhoz a tárolóhoz kell regisztrálni | **Ok**: Ez a probléma akkor fordul elő, amikor a külső DPM helyreállítási beállítással megpróbálja visszaállítani a fájlokat a másik kiszolgálóra az eredeti kiszolgálóról, és ha a helyreállított kiszolgáló és az eredeti kiszolgáló, ahonnan az adatokról biztonsági mentés készül, nem ugyanahhoz a helyreállítási szolgáltatás tárolóhoz van társítva.<br/> <br/>**Kerülő megoldás** A probléma megoldásához győződjön meg arról, hogy az eredeti és a másodlagos kiszolgáló is ugyanahhoz a tárolóhoz van regisztrálva.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>A VMware VM online helyreállítási pontlétrehozása sikertelen

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | A VMware VM online helyreállítási pont létrehozása sikertelen. A DPM hibát észlelt a VMware-től, miközben changetracking információkat próbált beszerezni. Hibakód - fájlhiba (id 33621) |  <ol><li> Állítsa alaphelyzetbe a virtuális gépekkel kapcsolatos CTK-t az érintett virtuális gépeken.</li> <li>Ellenőrizze, hogy nincs-e független lemez a VMware-en.</li> <li>Állítsa le az érintett virtuális gépek védelmét, és védje újra a **Frissítés** gombot. </li><li>Cc futtatása az érintett virtuális gépekhez.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Az ügynökművelet nem sikerült, mert kommunikációs hiba történt a dpm-ügynökkoordinátor szolgáltatással a kiszolgálón

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök(ek) lenyomása védett kiszolgálókra | Az ügynökművelet nem sikerült, mert kommunikációs hiba történt \<a Kiszolgálónév> DPM-ügynök-koordinátor szolgáltatásával. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre az alábbi lépéseket:** <ul><li> Ha nem megbízható tartományból csatol számítógépet, kövesse [az alábbi lépéseket.](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019) <br> (1) </li><li> Ha megbízható tartományból csatol számítógépet, a [blogban](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)ismertetett lépésekkel hárítsa el a hibákat. <br>(1)</li><li> Próbálja meg hibaelhárítási lépésként letiltani a víruskeresőt. Ha ez megoldja a problémát, módosítsa a víruskereső beállításait a cikkben javasoltak [szerint.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12))</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A telepítő nem tudta frissíteni a beállításjegyzékbeli metaadatokat

| Művelet | A hiba részletei | Áthidaló megoldás |
|-----------|---------------|------------|
|Telepítés | A telepítő nem tudta frissíteni a rendszerleíró adatbázis metaadatait. Ez a frissítési hiba a tárhelyfelhasználás túlzott használatához vezethet. A frissítés elkerülése érdekében a ReFS Trimming rendszerleíró bejegyzés. | Állítsa be a RENDSZERLEÍRÓ kulcsot **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Állítsa a Dword értéket 1-re. |
|Telepítés | A telepítő nem tudta frissíteni a rendszerleíró adatbázis metaadatait. Ez a frissítési hiba a tárhelyfelhasználás túlzott használatához vezethet. Ennek elkerülése érdekében frissítse a Volume SnapOptimization rendszerleíró bejegyzést. | Hozza létre a **SZOFTVER\Microsoft Adatvédelmi kezelő\Configuration\VolSnapOptimization\WriteIds** üres karakterlánc-értékkel rendelkező rendszerleíró kulcsot. |

## <a name="registration-and-agent-related-issues"></a>Regisztrációval és ügynökkel kapcsolatos kérdések

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök(ek) lenyomása védett kiszolgálókra | A kiszolgálóhoz megadott hitelesítő adatok érvénytelenek. | **Ha a termékben bemutatott javasolt művelet nem működik, kövesse az alábbi lépéseket:** <br> Próbálja meg manuálisan telepíteni a védelmi ügynököt az üzemi kiszolgálóra az ebben a [cikkben meghatározottmódon.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)|
| Az Azure backup ügynök nem tudott csatlakozni az Azure Backup szolgáltatáshoz (ID: 100050) | Az Azure backup ügynök nem tudott csatlakozni az Azure Backup szolgáltatáshoz. | **Ha a termékben bemutatott javasolt művelet nem működik, kövesse az alábbi lépéseket:** <br>1. Futtassa a következő parancsot emelt szintű parancsból: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Ezzel megnyitja az Internet Explorer ablakot. <br/> 2. Nyissa meg **az Eszközök Internetbeállítások** > **Internet Options** > **kapcsolatok** > **HELYI beállításait.** <br/> 3. Módosítsa a beállításokat proxykiszolgáló használatához. Ezután adja meg a proxykiszolgáló adatait.<br/> 4. Ha a készülék korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a tűzfal beállításai a számítógépen vagy a proxyn engedélyezik ezeket az [URL-címeket](install-mars-agent.md#verify-internet-access) és [IP-címeket.](install-mars-agent.md#verify-internet-access)|
| Az Azure backup ügynök telepítése nem sikerült | A Microsoft Azure Recovery Services telepítése nem sikerült. A Microsoft Azure Recovery Services telepítése által a rendszeren végrehajtott összes módosítást visszalett állítva. (ID: 4024) | Manuálisan telepítse az Azure Agentet.

## <a name="configuring-protection-group"></a>Védelmi csoport konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Védelmi csoportok konfigurálása | A DPM nem tudta felsorolni az alkalmazásösszetevőt a védett számítógépen (védett számítógépnév). | Válassza a **Frissítés** lehetőséget a felhasználói felület konfigurálása képernyőn a megfelelő adatforrás/összetevő szintjén. |
| Védelmi csoportok konfigurálása | Nem konfigurálható védelem | Ha a védett kiszolgáló SQL-kiszolgáló, ellenőrizze, hogy a védett számítógépen található rendszerfiók (NTAuthority\System) rendszergazdai szerepkör-engedélyeket kapott-e a cikkben leírtak [szerint.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12))
| Védelmi csoportok konfigurálása | Nincs elegendő szabad hely a tárolókészletben ehhez a védelmi csoporthoz. | A tárolókészlethez hozzáadott lemezek [nem tartalmazhatnak partíciót.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)) Törölje a lemezeken lévő köteteket. Ezután adja hozzá őket a tárolókészlethez.|
| Házirend-módosítás |A biztonsági mentési házirend nem módosítható. Hiba: Az aktuális művelet belső szolgáltatáshiba miatt nem sikerült (0x29834]. Próbálkozzon újra a művelettel egy idő eltelte után. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. | **Ok:**<br/>Ez a hiba három feltétel között fordul elő: ha a biztonsági beállítások engedélyezve vannak, amikor megpróbálja csökkenteni a megőrzési tartományt a korábban megadott minimális értékek alá, és ha nem támogatott verziót alkalmaz. (A nem támogatott verziók a Microsoft Azure Backup Server 2.0.9052-es verziója és az Azure Backup Server 1.) <br/>**Ajánlott művelet:**<br/> A házirenddel kapcsolatos frissítések folytatásához állítsa a megőrzési időszakot a megadott minimális megőrzési időszak fölé. (A minimális megőrzési időszak napi hét nap, heti négy hét, havonta három hét, évente egy év.) <br><br>Szükség esetén egy másik előnyben részesített megközelítés a biztonsági mentési ügynök és az Azure Backup Server frissítése az összes biztonsági frissítés kihasználásához. |

## <a name="backup"></a>Backup

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Váratlan hiba történt a feladat futása közben. Az eszköz nem áll készen. | **Ha a termékben bemutatott javasolt művelet nem működik, kövesse az alábbi lépéseket:** <br> <ul><li>Állítsa az árnyékmásolat-tárolóterületet korlátlanra a védelmi csoport elemein, majd futtassa a konzisztencia-ellenőrzést.<br></li> (1) <li>Próbálja meg a meglévő védelmi csoportot, és hozzon létre több új csoportot. Minden új védelmi csoportban külön elemnek kell lennie.</li></ul> |
| Backup | Ha csak a rendszerállapotról készít biztonsági másolatot, ellenőrizze, hogy van-e elegendő szabad hely a védett számítógépen a rendszerállapot biztonsági másolatának tárolásához. | <ol><li>Ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett számítógépen.</li><li>Ellenőrizze, hogy van-e elég hely a védett számítógépen a rendszerállapotszámára. Ennek ellenőrzésére legegyszerűbben úgy lehet menni a védett számítógépre, megnyithatja a Windows Server biztonsági másolat segédprogramot, végigkattinthat a beállításokon, majd kiválaszthatja a BMR lehetőséget. A felhasználói felület ezután megmutatja, hogy mennyi helyre van szükség. WsB **WSB** > **Helyi biztonsági mentés** > **ütemezése:** > **Válassza ki a Biztonsági másolat konfigurációja** > **Teljes kiszolgálót** (a méret megjelenik). Ezt a méretet használja az ellenőrzéshez.</li></ol>
| Backup | Biztonsági másolatot a BMR hibájáról | Ha a BMR-méret nagy, helyezzen át néhány alkalmazásfájlt az operációs rendszer meghajtójára, és próbálkozzon újra. |
| Backup | A VMware virtuális gépek új Microsoft Azure backup kiszolgálón való újbóli védelmének lehetősége nem jelenik meg hozzáadhatóként. | A VMware-tulajdonságok a Microsoft Azure Backup Server egy régi, kivisszavonult példányára mutatnak. A probléma megoldása:<br><ol><li>A VCenterben (SC-VMM megfelelője) lépjen az **Összegzés** lapra, majd az **Egyéni attribútumok gombra.**</li>  <li>Törölje a régi Microsoft Azure backup kiszolgálónevet a **DPMServer** értékből.</li>  <li>Lépjen vissza az új Microsoft Azure biztonsági másolat készítő kiszolgálóra, és módosítsa a PG-t.  Miután **kiválasztotta** a Frissítés gombot, a virtuális gép egy jelölőnégyzettel jelenik meg, mint amely elérhető a védelemhez.</li></ol> |
| Backup | Hiba a fájlok/megosztott mappák elérése közben | Próbálja meg módosítani a víruskereső beállításait a cikkben javasolt módon [Víruskereső szoftver futtatása a DPM-kiszolgálón](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).|

## <a name="change-passphrase"></a>Jelszó módosítása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Jelszó módosítása |A megadott biztonsági PIN-kód helytelen. Adja meg a megfelelő biztonsági PIN-kódot a művelet végrehajtásához. |**Ok:**<br/> Ez a hiba akkor fordul elő, ha kritikus művelet (például egy jelszó módosítása) közben érvénytelen vagy lejárt biztonsági PIN-kódot ad meg. <br/>**Ajánlott művelet:**<br/> A művelet végrehajtásához érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód leése, jelentkezzen be az Azure Portalon, és nyissa meg a Recovery Services-tároló. Ezután nyissa meg a **Beállítások** > **tulajdonságai** > **biztonsági PIN-kód generálása lehetőséget.** Ezzel a PIN-kóddal módosíthatja a jelszót. |
| Jelszó módosítása |A művelet nem sikerült. Azonosító: 120002 |**Ok:**<br/>Ez a hiba akkor fordul elő, ha a biztonsági beállítások engedélyezve vannak, vagy ha nem támogatott verzió használata esetén megpróbálja módosítani a jelszót.<br/>**Ajánlott művelet:**<br/> A jelszó módosításához először frissítenie kell a biztonságimásolat-készítő ügynököt a minimális verzióra, amely a 2.0.9052. Az Azure Backup Servert is frissítenie kell az 1. A PIN-kód leése, jelentkezzen be az Azure Portalon, és nyissa meg a Recovery Services-tároló. Ezután nyissa meg a **Beállítások** > **tulajdonságai** > **biztonsági PIN-kód generálása lehetőséget.** Ezzel a PIN-kóddal módosíthatja a jelszót. |

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| E-mail értesítések beállítása Office 365-fiókkal |Azonosító: 2013| **Ok:**<br> Az Office 365-fiók használatának megkísérlése <br>**Ajánlott művelet:**<ol><li> Az első dolog, ami biztosítja, hogy a "Névtelen továbbítás engedélyezése egy fogadási összekötőn" a DPM-kiszolgálóhoz az Exchange rendszeren van beállítva. A konfigurálásáról a Névtelen [továbbítás engedélyezése fogadási összekötőn](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019)című témakörben talál további információt.</li> <li> Ha nem tud belső SMTP-továbbítát használni, és az Office 365-kiszolgálóhasználatával kell beállítania, beállíthatja az IIS-t továbbítóként. Konfigurálja úgy a DPM-kiszolgálót, [hogy az SMTP-t az IIS segítségével továbbítsa az O365-nek.](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)<br><br>  Ügyeljen arra, hogy\@a felhasználó domain.com formátumot *használja, ne* a tartomány\felhasználó.<br><br><li>Pont DPM a helyi kiszolgáló nevét használja SMTP-kiszolgálóként, 587-es portként. Ezután mutasson a felhasználó e-mailcímére, amelyből az e-maileket meg kell kapnia.<li> A DPM SMTP beállítási lapján található felhasználónévnek és jelszónak annak a tartománynak a tartományában lévő tartományhoz kell tartoznia, amelyen a DPM található. </li><br> Az SMTP-kiszolgáló címének módosításakor módosítsa az új beállításokat, zárja be a beállítások at, majd nyissa meg újra, hogy az tükrözze az új értéket.  Előfordulhat, hogy a módosítás és a tesztelés nem mindig eredményezi az új beállítások érvénybe léptetését, ezért az ily módon végzett tesztelés ajánlott eljárás.<br><br>A folyamat során bármikor törölheti ezeket a beállításokat a DPM konzol bezárásával és a következő beállításkulcsok szerkesztésével: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword és SMTPUserName kulcsok**. Amikor újra elindítja őket, visszaveheti őket a felhasználói felületre.

## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz ismerteti a gyakori hibákat, amelyek az Azure Backup Server használata során találkozhatnak.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés sikertelen, mert a lemez biztonsági másolati replikája érvénytelen vagy hiányzik. | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálkozzon újra a művelettel: <br/> 1. Lemez-helyreállítási pont létrehozása<br/> 2. Konzisztencia-ellenőrzés futtatása adatforráson <br/> 3. Állítsa le az adatforrás védelmét, majd konfigurálja újra az adatforrás védelmét

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A forráskötet pillanatképe nem sikerült, mert a replika metaadatai érvénytelenek. | Az adatforrás lemez-helyreállítási pontjának létrehozása és az online biztonsági mentés újbóli megkísérlése

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Hibaüzenet | Javasolt művelet |
-- | --
A forráskötet pillanatképe inkonzisztens adatforrás-replika miatt nem sikerült. | Konzisztencia-ellenőrzés futtatása ezen az adatforráson, majd próbálkozzon újra

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés nem sikerült, mert a lemez biztonsági másolati replikáját nem lehetett klónozni.| Győződjön meg arról, hogy az összes korábbi lemezbiztonsági másolat replikája (.vhdx) le van szerelve, és nincs folyamatban lemezről lemezre erősítés az online biztonsági mentések során
