---
title: Az Azure Backup Server hibaelhárítása
description: Hibaelhárítás, Azure Backup Server regisztrációja, valamint az alkalmazások számítási feladatainak biztonsági mentése és helyreállítása.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 0f9c2d1d2081ec22898ed3a4fbc73305ff0995e3
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954679"
---
# <a name="troubleshoot-azure-backup-server"></a>Az Azure Backup Server hibaelhárítása

A következő táblázatokban található információk segítségével elháríthatja a Azure Backup Server használata során felmerülő hibákat.

## <a name="basic-troubleshooting"></a>Alapszintű hibaelhárítás

Javasoljuk, hogy a Microsoft Azure Backup Server (MABS) hibaelhárítása előtt végezze el az alábbi érvényesítést:

- [Győződjön meg arról, Microsoft Azure Recovery Services (MARS) ügynök naprakész](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Győződjön meg arról, hogy a MARS-ügynök és az Azure között van hálózati kapcsolat](https://aka.ms/AB-A4dp50)
- Győződjön meg arról, hogy a Microsoft Azure Recovery Services fut (a Szolgáltatás konzolon). Szükség esetén indítsa újra, és ismételje meg a műveletet.
- [Győződjön meg arról, hogy 5-10% szabad hellyel rendelkezik az ideiglenes mappa helyén](https://aka.ms/AB-AA4dwtt)
- Ha a regisztráció sikertelen, akkor győződjön meg arról, hogy a kiszolgáló, amelyre telepíteni kívánja, Azure Backup Server még nincs regisztrálva egy másik tárolóban.
- Ha a leküldéses telepítés sikertelen, ellenőrizze, hogy a DPM-ügynök már jelen van-e a rendszeren. Ha igen, távolítsa el az ügynököt, és próbálkozzon újra a telepítéssel
- [Győződjön meg arról, hogy nem zavarja másik folyamat vagy víruskereső szoftver az Azure Backup működését](https://aka.ms/AA4nyr4)<br>
- Győződjön meg róla, hogy fut-e az SQL Agent szolgáltatás, és az MAB-kiszolgálón a beállítása „automatikus”<br>


## <a name="invalid-vault-credentials-provided"></a>A tár megadott hitelesítő adatai érvénytelenek

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Regisztráció egy tárba | A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem rendelkezik a helyreállítási szolgáltatáshoz társított legújabb hitelesítő adatokkal. | Javasolt művelet: <br> <ul><li> Töltse le a legújabb hitelesítő adatokat tartalmazó fájlt a tárból, és próbálkozzon újra. <br>VAGY</li> <li> Ha az előző művelet nem működött, próbálja meg letölteni a hitelesítő adatokat egy másik helyi könyvtárba, vagy hozzon létre egy új tárolót. <br>VAGY</li> <li> Próbálja meg frissíteni a dátum-és időbeállításokat [](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/)a blogbejegyzésben leírtak szerint. <br>VAGY</li> <li> Ellenőrizze, hogy a c:\Windows\Temp több mint 65000 fájllal rendelkezik-e. Helyezze át az elavult fájlokat egy másik helyre, vagy törölje a temp mappában lévő elemeket. <br>VAGY</li> <li> A tanúsítványok állapotának ellenőrzését. <br> a. Nyissa meg a **számítógép-tanúsítványok kezelése** (a Vezérlőpulton). <br> b. Bontsa ki a **személyes** csomópontot és annak alárendelt csomópontjának **tanúsítványait**.<br> c.  Távolítsa el a **Windows Azure-eszközök**tanúsítványát. <br> d. Próbálja megismételni a regisztrációt az Azure Backup-ügyfélen. <br> VAGY </li> <li> Ellenőrizze, hogy van-e érvényben a csoportházirend. </li></ul> |

## <a name="replica-is-inconsistent"></a>Inkonzisztens replika

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Tartalék | Inkonzisztens replika | Ellenőrizze, hogy a védelmi csoport varázslóban be van-e kapcsolva az automatikus konzisztencia-ellenőrzés lehetőség. A replika inkonzisztencia és a kapcsolódó javaslatok okaival kapcsolatos további információkért tekintse meg a következő cikket: a [replika inkonzisztens](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Rendszerállapot/BMR biztonsági mentés esetén ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett kiszolgálón.</li><li> Győződjön meg arról, hogy a DPM/Microsoft Azure Backup-kiszolgálón a DPM-tárolóban a tárterülettel kapcsolatos problémák vannak, és szükség szerint foglalja le a tárolót.</li><li> A védett kiszolgálón lévő Kötet árnyékmásolata szolgáltatás állapotának megtekintése. Ha letiltott állapotban van, állítsa be manuálisan a kezdéshez. Indítsa el a szolgáltatást a kiszolgálón. Ezután térjen vissza a DPM/Microsoft Azure Backup Server-konzolra, és indítsa el a szinkronizálást a konzisztencia-ellenőrzési feladatokkal.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Az online helyreállítási pont létrehozása sikertelen volt.

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Tartalék | Az online helyreállítási pont létrehozása sikertelen volt. | **Hibaüzenet**: A Windows Azure Backup ügynök nem tudott pillanatképet készíteni a kijelölt kötetről. <br> **Áthidaló megoldás**: Próbálja meg növelni a helyet a replika és a helyreállítási pont kötetén.<br> <br> **Hibaüzenet**: A Windows Azure Backup ügynök nem tud csatlakozni a OBEngine szolgáltatáshoz <br> **Áthidaló megoldás**: Ellenőrizze, hogy a OBEngine létezik-e a számítógépen futó szolgáltatások listájában. Ha a OBEngine szolgáltatás nem fut, használja a "net start OBEngine" parancsot a OBEngine szolgáltatás elindításához. <br> <br> **Hibaüzenet**: Nincs beállítva a kiszolgáló titkosítási jelszava. Konfigurálja a titkosítási jelszót. <br> **Áthidaló megoldás**: Próbáljon meg titkosítási hozzáférési kódot beállítani. Ha nem sikerül, hajtsa végre a következő lépéseket: <br> <ol><li>Ellenőrizze, hogy létezik-e a megkarcolt hely. Ez az a hely, amely szerepel a beállításjegyzékben a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config**, és léteznie kell a **ScratchLocation** névvel.</li><li> Ha a megjelenő hely létezik, próbálja meg újból regisztrálni a régi jelszó használatával. *Ha titkosítási jelszót állít be, mentse biztonságos helyre.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>A tár megadott hitelesítő adatai eltérnek a kiszolgáló által regisztrált tárolótól

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Visszaállítás | **Hibakód**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Hibaüzenet**: A tár megadott hitelesítő adatai eltérnek a kiszolgáló által regisztrált tárolótól | **Ok**: Ez a probléma akkor fordul elő, ha az eredeti kiszolgálóról a külső DPM-helyreállítási lehetőség használatával kívánja visszaállítani a fájlokat a másodlagos kiszolgálóra, és ha a helyreállított kiszolgáló és az az eredeti kiszolgáló, ahonnan az adatok biztonsági mentése történik, nincs társítva ugyanahhoz a művelethez. Recovery Service-tároló.<br/> <br/>**Áthidaló megoldás** A probléma megoldásához ellenőrizze, hogy az eredeti és az alternatív kiszolgáló is regisztrálva van-e ugyanahhoz a tárolóhoz.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>A VMware virtuális gép online helyreállítási pontjának létrehozási feladatai sikertelenek

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Tartalék | A VMware virtuális gép online helyreállítási pontjának létrehozási feladatai sikertelenek. A DPM a VMware hibába ütközött, miközben megpróbálta beolvasni a változáskövetési-információkat. ErrorCode-FileFaultFault (azonosító: 33621) |  <ol><li> Állítsa alaphelyzetbe a VMware-CTK az érintett virtuális gépeken.</li> <li>Győződjön meg arról, hogy a független lemez nem áll helyre a VMware-en.</li> <li>Állítsa le a védelmet az érintett virtuális gépek számára, és a refresh ( **frissítés** ) gombbal tegye meg újra a védelmet. </li><li>Egy CC futtatása az érintett virtuális gépekhez.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Az ügynök művelete sikertelen volt, mert kommunikációs hiba történt a DPM-ügynök koordinátori szolgáltatásával a kiszolgálón

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök (ek) leküldése a védett kiszolgálókra | Az ügynök művelete sikertelen volt, mert kommunikációs hiba történt a \<servername > DPM-ügynökkoordinátor szolgáltatásával. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket**: <ul><li> Ha nem megbízható tartományból csatlakoztat egy számítógépet, kövesse [az alábbi lépéseket](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> VAGY </li><li> Ha megbízható tartományból csatlakoztat egy számítógépet, a [jelen blogban](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/)ismertetett lépések végrehajtásával hibaelhárítást végezhet. <br>VAGY</li><li> Próbálja meg letiltani a víruskeresőt hibaelhárítási lépésként. Ha feloldja a problémát, módosítsa a vírusvédelmi beállításokat a [jelen cikkben](https://technet.microsoft.com/library/hh757911.aspx)javasolt módon.</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A telepítő nem tudja frissíteni a beállításjegyzék metaadatait

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
|-----------|---------------|------------|
|Telepítés | A telepítő nem tudja frissíteni a beállításjegyzék metaadatait. Ez a frissítési hiba a tárterület-használat túlterheléséhez vezethet. Ennek elkerüléséhez frissítse a ReFS-vágási beállításjegyzékbeli bejegyzést. | Módosítsa a **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**beállításkulcsot. Állítsa a DWORD értéket 1-re. |
|Telepítés | A telepítő nem tudja frissíteni a beállításjegyzék metaadatait. Ez a frissítési hiba a tárterület-használat túlterheléséhez vezethet. Ennek elkerüléséhez frissítse a kötet SnapOptimization beállításjegyzékbeli bejegyzését. | Hozza létre a SOFTWARE\Microsoft-adatvédelmi **Manager\Configuration\VolSnapOptimization\WriteIds** beállításkulcsot egy üres karakterlánc-értékkel. |

## <a name="registration-and-agent-related-issues"></a>Regisztráció és ügynökkel kapcsolatos problémák

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök (ek) leküldése a védett kiszolgálókra | A kiszolgálóhoz megadott hitelesítő adatok érvénytelenek. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket**: <br> Próbálja meg manuálisan telepíteni a védelmi ügynököt az üzemi kiszolgálón az ebben a [cikkben](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)megadott módon.|
| Azure Backup ügynök nem tudott csatlakozni a Azure Backup szolgáltatáshoz (azonosító: 100050) | A Azure Backup ügynök nem tudott kapcsolódni a Azure Backup szolgáltatáshoz. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket**: <br>1. Futtassa a következő parancsot egy emelt szintű parancssorból: **PsExec-i-s "C:\Program Files\Internet Explorer\iexplore.exe**. Ekkor megnyílik az Internet Explorer ablaka. <br/> 2. Lépjen az **eszközök** > **internetes beállítások** > kapcsolatokLAN > -**Beállítások menüpontra**. <br/> 3. Módosítsa a beállításokat proxykiszolgáló használatára. Ezután adja meg a proxykiszolgáló részleteit.<br/> 4. Ha a számítógépén korlátozott az Internet-hozzáférés, győződjön meg arról, hogy a számítógépen vagy a proxyn a tűzfalbeállítások engedélyezik ezeket az [URL-címeket](backup-configure-vault.md#verify-internet-access) és [IP-címeket](backup-configure-vault.md#verify-internet-access).|
| Azure Backup ügynök telepítése nem sikerült | A Microsoft Azure Recovery Services telepítése sikertelen volt. A rendszer által a Microsoft Azure Recovery Services telepítés során végrehajtott összes módosítás vissza lett állítva. (Azonosító: 4024) | Manuálisan telepítse az Azure-ügynököt.


## <a name="configuring-protection-group"></a>Védelmi csoport konfigurálása

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Védelmi csoportok konfigurálása | A DPM nem tudja felsorolni az alkalmazás összetevőjét a védett számítógépen (a védett számítógép neve). | Válassza a **frissítés** lehetőséget a védelmi csoport felhasználói felületének konfigurálása képernyőn a megfelelő DataSource/Component szinten. |
| Védelmi csoportok konfigurálása | Nem sikerült beállítani a védelmet. | Ha a védett kiszolgáló egy SQL-kiszolgáló, ellenőrizze, hogy az [ebben a cikkben](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)leírtak szerint meg lett-e biztosítva a rendszerfiók (NTAuthority\System) rendszergazdai szerepkör engedélyei a védett számítógépen.
| Védelmi csoportok konfigurálása | Nincs elegendő szabad terület a tárolóban ehhez a védelmi csoporthoz. | A tárolóhelyhez hozzáadott lemezek [nem tartalmazhatnak partíciót](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Törölje a lemezeken lévő összes meglévő kötetet. Ezután adja hozzá őket a Storage-készlethez.|
| Szabályzat módosítása |Nem lehet módosítani a biztonsági mentési szabályzatot. Hiba: Az aktuális művelet végrehajtása egy belső szolgáltatási hiba miatt meghiúsult [0x29834]. Némi idő elteltével próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. | **Okozhat**<br/>Ez a hiba három feltétel teljesülése esetén fordul elő: Ha a biztonsági beállítások engedélyezve vannak, akkor a rendszer a korábban megadott minimális értékek alatti megőrzési időtartamot próbálja csökkenteni, és ha nem támogatott verziót szeretne használni. (A nem támogatott verziók a Microsoft Azure Backup Server 2.0.9052 és a Azure Backup Server Update 1 verziónál régebbiek.) <br/>**Javasolt művelet:**<br/> A házirendekkel kapcsolatos frissítések folytatásához állítsa a megadott megőrzési időszakot a minimális megőrzési időtartam fölé. (A minimális megőrzési idő napi hét nap, heti három hét, havi vagy egy év éves időszakra szól.) <br><br>Egy másik előnyben részesített módszer a biztonsági mentési ügynök és a Azure Backup Server frissítése az összes biztonsági frissítés kihasználása érdekében. |

## <a name="backup"></a>Tartalék

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Tartalék | Váratlan hiba történt a feladat futása közben. Az eszköz nem áll készen. | **Ha a termékben bemutatott javasolt művelet nem működik, hajtsa végre a következő lépéseket:** <br> <ul><li>Állítsa az árnyékmásolat tárolóhelyét korlátlanra a védelmi csoport elemeinél, majd futtassa a konzisztencia-ellenőrzés parancsot.<br></li> VAGY <li>Próbálja meg törölni a meglévő védelmi csoportot, és hozzon létre több új csoportot. Minden új védelmi csoportnak külön elemmel kell rendelkeznie.</li></ul> |
| Tartalék | Ha csak a rendszerállapotról készít biztonsági másolatot, ellenőrizze, hogy van-e elegendő szabad hely a védett számítógépen a rendszerállapot biztonsági másolatának tárolásához. | <ol><li>Ellenőrizze, hogy a Windows Server biztonsági másolat telepítve van-e a védett gépen.</li><li>Ellenőrizze, hogy van-e elegendő hely a védett számítógépen a rendszerállapothoz. Ennek a legegyszerűbb módja a védett számítógép megnyitása, a Windows Server biztonsági másolat megnyitása, a kijelölések átadása, majd a BMR kiválasztása. A felhasználói felület ekkor megadja, hogy mekkora lemezterületre van szükség. Nyissa meg a **WSB** > **helyi biztonsági mentési** > **ütemtervét** > ,**válassza a biztonsági mentési konfiguráció** > **teljes kiszolgáló** elemet (a méret megjelenik). Az ellenőrzéshez használja ezt a méretet.</li></ol>
| Tartalék | A BMR biztonsági mentése sikertelen | Ha a BMR mérete nagyméretű, helyezzen át néhány alkalmazást az operációsrendszer-meghajtóra, és próbálkozzon újra. |
| Tartalék | A VMware virtuális gépek új Microsoft Azure Backup-kiszolgálón történő ismételt védetté tétele nem érhető el a hozzáadáshoz. | A VMware-tulajdonságokat a Microsoft Azure Backup kiszolgáló egy régi, kivont példánya irányítja. A probléma megoldása:<br><ol><li>A VCenter (SC-VMM egyenértékű) lapon lépjen az **Összefoglalás** lapra, majd az **Egyéni attribútumok**elemre.</li>  <li>Törölje a régi Microsoft Azure Backup-kiszolgáló nevét a **DPMServer** értékből.</li>  <li>Térjen vissza az új Microsoft Azure Backup-kiszolgálóra, és módosítsa a PG-t.  A **frissítés** gomb kiválasztását követően a virtuális gép a védelemhez való hozzáadáshoz elérhető jelölőnégyzettel jelenik meg.</li></ol> |
| Tartalék | Hiba történt a fájlok/megosztott mappák elérésekor | Próbálja meg módosítani a vírusvédelmi beállításokat a TechNet-cikkben javasolt módon a [DPM-kiszolgálón futó víruskereső szoftver futtatásával](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Hozzáférési kód módosítása

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| Hozzáférési kód módosítása |A megadott biztonsági PIN-kód helytelen. A művelet végrehajtásához adja meg a helyes biztonsági PIN-kódot. |**Okozhat**<br/> Ez a hiba akkor fordul elő, ha érvénytelen vagy lejárt biztonsági PIN-kódot ad meg a kritikus művelet végrehajtása közben (például a jelszó módosítása). <br/>**Javasolt művelet:**<br/> A művelet végrehajtásához érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód beszerzéséhez jelentkezzen be a Azure Portalba, és nyissa meg a Recovery Services-tárolót. Ezután lépjen a **Beállítások** > **Tulajdonságok** > **biztonsági PIN-kód létrehozásához**. Ezt a PIN-kódot használhatja a jelszó megváltoztatásához. |
| Hozzáférési kód módosítása |Sikertelen volt a művelet. Azonosító: 120002 |**Okozhat**<br/>Ez a hiba akkor fordul elő, ha a biztonsági beállítások engedélyezve vannak, vagy ha nem támogatott verziót használ, amikor megpróbál módosítani egy jelszót.<br/>**Javasolt művelet:**<br/> A jelszó módosításához először frissítenie kell a Backup ügynököt a 2.0.9052 minimális verziójára. Emellett frissítenie kell Azure Backup Server az 1. frissítés minimumára, majd érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód beszerzéséhez jelentkezzen be a Azure Portalba, és nyissa meg a Recovery Services-tárolót. Ezután lépjen a **Beállítások** > **Tulajdonságok** > **biztonsági PIN-kód létrehozásához**. Ezt a PIN-kódot használhatja a jelszó megváltoztatásához. |


## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

| Művelet | Hibaüzenet részletei | Áthidaló megoldás |
| --- | --- | --- |
| E-mail-értesítések beállítása Office 365-fiók használatával |Hiba azonosítója: 2013| **Okozhat**<br> Office 365-fiók használatának megkísérlése <br>**Javasolt művelet:**<ol><li> Az első dolog, hogy a "névtelen továbbító engedélyezése egy fogadási összekötőn" a DPM-kiszolgálóhoz az Exchange-kiszolgálón legyen beállítva. További információ a konfigurálásáról: [Névtelen továbbító engedélyezése fogadási összekötőn](https://technet.microsoft.com/library/bb232021.aspx) a TechNeten.</li> <li> Ha nem használ belső SMTP-továbbítót, és az Office 365-kiszolgáló használatával kell beállítania, beállíthatja, hogy az IIS továbbító legyen. Konfigurálja úgy a DPM-kiszolgálót, hogy [az IIS használatával továbbítsa az SMTP-t a O365](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **FONTOS** Ügyeljen arra, hogy a felhasználói\@domain.com formátumot használja, és *ne* megadásakor<br><br><li>A DPM pont a helyi kiszolgáló nevét használja SMTP-kiszolgálóként, 587-as porton. Ezután mutasson arra a felhasználói e-mailre, amelyről az e-mailek származnak.<li> A DPM SMTP-beállítás lapjának felhasználónevének és jelszavának a DPM tartományában lévő tartományi fiókhoz kell tartoznia. </li><br> **MEGJEGYZÉS**: Ha megváltoztatja az SMTP-kiszolgáló címe beállítást, végezze el a módosítást az új beállításokra, majd kattintson a beállítások mezőre, majd nyissa meg újra, hogy az tükrözze az új értéket.  Előfordulhat, hogy a módosítás és a tesztelés nem mindig okozza az új beállítások érvénybe léptetését, így a tesztelés így a legjobb megoldás.<br><br>A folyamat során bármikor törölheti ezeket a beállításokat a DPM-konzol bezárásával és a következő beállításkulcsok szerkesztésével: **A HKLM\SOFTWARE\Microsoft\Microsoft adatvédelem Manager\Notification\ <br/> törli a SMTPPassword és a SMTPUserName kulcsokat**. Ha újra elindítják, a felhasználói felülethez is hozzáadhatók.


## <a name="common-issues"></a>Gyakori problémák

Ez a szakasz a Azure Backup Server használata során előforduló gyakori hibákat ismerteti.


### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés sikertelen, mert a lemez biztonsági másolati replikája érvénytelen vagy hiányzik. | A probléma megoldásához ellenőrizze az alábbi lépéseket, majd próbálja megismételni a műveletet: <br/> 1. Lemezes helyreállítási pont létrehozása<br/> 2. Konzisztencia-ellenőrzés futtatása az adatforráson <br/> 3. Állítsa le az adatforrás védelmét, majd konfigurálja újra az adatforrás védelmét.

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Hibaüzenet | Javasolt művelet |
-- | --
A forrás kötetének pillanatképe sikertelen volt, mert a replikán található metaadatok érvénytelenek. | Hozzon létre egy lemez-helyreállítási pontot az adatforrásból, és próbálkozzon újra az online biztonsági mentéssel

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Hibaüzenet | Javasolt művelet |
-- | --
A forrásoldali kötet pillanatképe nem sikerült, mert inkonzisztens adatforrás-replika. | Konzisztencia-ellenőrzés futtatása az adatforráson, majd próbálkozzon újra

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Hibaüzenet | Javasolt művelet |
-- | --
A biztonsági mentés nem sikerült, mert a lemez biztonsági másolati replikáját nem lehetett klónozni.| Győződjön meg arról, hogy az összes korábbi lemezes biztonsági másolat másodpéldány-fájlja (. vhdx) le van választva, és nincs folyamatban lemezről lemezre történő biztonsági mentés az online biztonsági mentések során.
