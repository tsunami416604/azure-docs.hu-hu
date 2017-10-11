---
title: "A storage-pillanatfelvételekkel alapján SAP HANA Azure biztonsági mentés |} Microsoft Docs"
description: "Két fő biztonsági mentési lehetőség SAP Hana az Azure virtuális gépeken, ez a cikk ismerteti a storage-pillanatfelvételekkel alapján SAP HANA biztonsági mentése"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: f332b8ac091b75a23489ac27f15ad1fd10d24ec6
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Tárolási pillanatképeken alapuló biztonsági mentés SAP HANA-hoz

## <a name="introduction"></a>Bevezetés

Ez egy SAP HANA biztonsági másolaton kapcsolódó cikkek háromrészes sorozatát része. [Biztonsági mentési útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md) áttekintése és információkat nyújt a kezdeti lépések, és [SAP HANA Azure biztonsági mentési fájl szinten](sap-hana-backup-file-level.md) tartalmazza a fájl alapú biztonsági mentési beállítás.

Egypéldányos mindent egy bemutató rendszer esetén egy virtuális gép biztonsági mentési funkció használata esetén egy vegye figyelembe ennek során a virtuális gép biztonsági mentése HANA biztonsági mentés az operációs rendszer szintjén kezelése helyett. A másik lehetőség az egyes virtuális lemezek, a virtuális gép csatlakozik, másolatait létrehozása az Azure blob pillanatképek és a HANA adatfájlok. De a kritikus pont app konzisztencia, amikor egy virtuális gép biztonsági mentési vagy a lemez pillanatkép létrehozásához, miközben a rendszer működik-e és fut. Lásd: _SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel_ a kapcsolódó cikkben [biztonsági útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md). SAP HANA rendelkezik egy szolgáltatás, amely támogatja az ilyen típusú storage-pillanatfelvételekkel.

## <a name="sap-hana-snapshots"></a>SAP HANA-pillanatképek

Egy szolgáltatás található SAP HANA, amely támogatja a tárolási pillanatkép létrehozása van folyamatban. Azonban 2016. December, nincs korlátozás single-tároló rendszereken. Több-bérlős tároló konfigurációk nem támogatják ilyen típusú adatbázis-pillanatkép (lásd: [(SAP HANA Studio) tárolási pillanatkép létrehozása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Azt a következőképpen működik:

- Az SAP HANA-pillanatkép kezdeményezése készíti elő tárolási pillanatkép
- Futtassa a tárolási pillanatkép (az Azure blob pillanatkép, például)
- Erősítse meg a SAP HANA-pillanatkép

![Ezen a képernyőfelvételen látható, hogy egy SAP HANA-adatok pillanatképének elkészítéséhez SQL-utasítás használatával hozhatók létre](media/sap-hana-backup-storage-snapshots/image011.png)

Ezen a képernyőfelvételen látható jeleníti meg, hogy egy SAP HANA-adatok pillanatképének elkészítéséhez SQL-utasítás használatával hozhatók létre.

![A pillanatkép majd is megjelennek a biztonsági mentési katalógusban SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

A pillanatkép majd is megjelenik a biztonságimásolat-katalógus SAP HANA Studio.

![A lemezen a pillanatkép megjelennek az SAP HANA-adatainak könyvtára](media/sap-hana-backup-storage-snapshots/image013.png)

A lemezen a pillanatkép megjelennek a SAP HANA adatkönyvtárat.

Egy rendelkezik győződjön meg arról, hogy a fájlrendszer-konzisztenciával is garantáltan előtt, a tárolási pillanatkép, miközben SAP HANA a pillanatkép elkészítése módban van. Lásd: _SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel_ a kapcsolódó cikkben [biztonsági útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md).

Ha végzett a tárolási pillanatkép, fontos, a SAP HANA-pillanatkép megerősítéséhez. Nincs a megfelelő SQL-utasítás futtatása: biztonsági MENTÉSI adatok Bezárás PILLANATKÉP (lásd: [biztonsági MENTÉSI adatok Bezárás SNAPSHOT utasítás (biztonsági mentés és helyreállítás)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Erősítse meg a HANA pillanatkép. Miatt &quot;másolási módosításkor,&quot; SAP HANA előfordulhat, hogy további lemezterületet a pillanatkép-előkészítése módot, és nincs lehetőség, hogy új biztonsági mentést elindítani, amíg a rendszer meggyőződött róla, a SAP HANA-pillanatkép.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA virtuális gép biztonsági mentése Azure Backup szolgáltatáson keresztül

Az Azure biztonsági mentési szolgáltatás biztonsági mentési ügynök nincs Linux virtuális gépekhez elérhető 2016. December. Hogy a fájl vagy könyvtár szinten Azure biztonsági mentés, az egyik akkor másolja a SAP HANA biztonságimásolat-fájlt egy Windows virtuális gépre, majd a biztonságimásolat-készítő ügynök. Ellenkező esetben csak a teljes Linux virtuális gép biztonsági mentés az Azure Backup szolgáltatáson keresztül lehetséges. Lásd: [Azure Backup funkcióinak áttekintése](../../../backup/backup-introduction-to-azure-backup.md) további.

Az Azure Backup szolgáltatás biztonsági mentése és visszaállítása egy virtuális Gépet egy lehetőséget is kínál. Ezt a szolgáltatást, és annak működéséről további információt a cikkben található [tervezze meg a virtuális gép biztonsági mentési infrastruktúra az Azure-ban](../../../backup/backup-azure-vms-introduction.md).

Nincsenek megfelelően, ha a cikkben két fontos szempontokat:

_&quot;Linux virtuális gépek esetében csak a fájlkonzisztens biztonsági mentések is előfordulhatnak, Linux nem tartozik egy egyenértékű platform VSS&quot;_

_&quot;Alkalmazások kell megvalósítani a saját &quot;javítása&quot; mechanizmus a helyreállított adatokra.&quot;_

Ezért egy is ellenőrizze, hogy SAP HANA lemezen konzisztens állapotban van, amikor a biztonsági mentés elindul. Lásd: _SAP HANA-pillanatképek_ a dokumentum a korábban ismertetett. De van egy potenciális problémát, ha a SAP HANA marad a pillanatkép-előkészítés módja. Lásd: [(SAP HANA Studio) tárolási pillanatkép létrehozása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) további információt.

Jelzi, hogy a cikk:

_&quot;Erősítse meg, vagy tárolási pillanatkép minél hamarabb abandon a létrehozásukat követően erősen ajánlott. A tárolási pillanatkép közben előkészítve, vagy létrehozott, a pillanatkép-vonatkozó adatokat zárolva van. A pillanatkép-vonatkozó adatokat zárolt állapotban marad, amíg még módosíthatók az adatbázisban. Ezek a változások nem okoz módosítani a rögzített pillanatkép-vonatkozó adatokat. Ehelyett a módosításokat pozíciók az adatterületen, amelyek külön, a tárolási pillanatképből írja. Módosításokat is írja a naplót. Azonban minél hosszabb a pillanatkép-vonatkozó adatokat tartják zárolt, annál adatmennyiség növelhető.&quot;_

Az Azure Backup gondoskodik a fájlrendszer-konzisztenciával Azure Virtuálisgép-bővítmények keresztül. Ezek a bővítmények nem érhető el önálló, és csak az Azure Backup szolgáltatás együtt működik. Ettől függetlenül is továbbra is kezelheti egy alkalmazás konzisztencia biztosításához SAP HANA-pillanatkép követelmény.

Azure biztonsági mentés két fő fázisa van:

- Pillanatkép készítése
- Átviteli adatokat tároló

Az SAP HANA-pillanatkép ezért az egyik megerősítené, amikor befejeződött a pillanatkép létrehozása van folyamatban az Azure Backup szolgáltatás fázisában. Tekintse meg az Azure portálon több percig is eltarthat.

![Az ábrán látható, a biztonsági mentési feladat listáját az Azure Backup szolgáltatás része](media/sap-hana-backup-storage-snapshots/image014.png)

Az ábrán látható, az Azure Backup szolgáltatás, amelynek használatával készítsen biztonsági másolatot a HANA tesztelése a virtuális gép biztonsági mentési feladat listája részét.

![A feladat részleteinek megjelenítéséhez kattintson a biztonsági mentési feladat, az Azure-portálon](media/sap-hana-backup-storage-snapshots/image015.png)

A feladat részleteinek megjelenítéséhez kattintson a biztonsági mentési feladat, az Azure portálon. Itt egy tekintheti meg a két fázisban történik. Eltarthat néhány percig, amíg azt mutatja, hogy a pillanatkép fázis Befejezett állapotúvá válik. Az esetek többségében az adatok átvitel fázis telik.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Biztonsági mentési automation HANA VM Azure Backup szolgáltatáson keresztül

Az SAP HANA-pillanatkép manuálisan egy megerősítené, után az Azure biztonsági mentési pillanatkép fázis befejeződött, a fentebb leírt módon, de hasznos lehet automation fontolja meg, mert a rendszergazda nem lehet, hogy figyeli a biztonsági mentési feladat listáján, az Azure-portálon.

Ez magyarázattal hogyan azt megvalósítható Azure PowerShell-parancsmagok használatával.

![Egy Azure Backup szolgáltatás létrejött hana-backup-tárolóban neve](media/sap-hana-backup-storage-snapshots/image016.png)

Egy Azure Backup szolgáltatás létrejött nevű &quot;hana-backup-tárolóba.&quot; A PS parancs **Get-AzureRmRecoveryServicesVault-Name hana-backup-tárolóba** kéri le a megfelelő objektum. Ez az objektum majd segítségével állítsa be a biztonsági környezet az alábbi ábrán látható módon.

![Egy ellenőrizheti a biztonsági mentési feladat jelenleg folyamatban van](media/sap-hana-backup-storage-snapshots/image017.png)

Miután beállította a megfelelő környezetben, egy is ellenőrizze a biztonsági mentési feladat jelenleg folyamatban van, és keresse meg a feladat részleteit. A részfeladatnál annak regisztrálása listán látható, ha a pillanatkép fázis az Azure biztonsági mentési feladat már kész:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Kérdezze le a hurok található értékkel, amíg kész értékre változik](media/sap-hana-backup-storage-snapshots/image018.png)

Ha a feladat részleteit egy változó tárolja, egyszerűen PS szintaxis első tömb bejegyzést, és az állapot értéke. Az automatizálási parancsfájl befejezéséhez kérdezze le az ismétlődő értéket, amíg ez &quot;befejezve.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA licenckulcs és a virtuális gép visszaállítása Azure Backup szolgáltatáson keresztül

Az Azure Backup szolgáltatás célja, hogy hozzon létre egy új virtuális Gépet a visszaállítás során. Nincs a terv most tegye egy &quot;helyben&quot; egy meglévő Azure virtuális gép visszaállítását.

![Az ábrán látható, a helyreállítás az Azure szolgáltatás az Azure-portálon](media/sap-hana-backup-storage-snapshots/image019.png)

Az ábrán látható, a helyreállítás az Azure szolgáltatás az Azure portálon. Egyet választhat egy virtuális gép létrehozása a visszaállítás során vagy a lemezek visszaállítása. A lemezek visszaállítása, után fontos továbbra is hozzon létre egy új virtuális Gépet utasítást. Amikor új virtuális gép jön létre az Azure-on a egyedi változik-e a virtuális gép azonosítója (lásd: [elérése és használata Azure virtuális gép egyedi azonosítója](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Az ábrán látható, az Azure virtuális gép egyedi Azonosítót előtt és után a visszaállítás Azure Backup szolgáltatáson keresztül](media/sap-hana-backup-storage-snapshots/image020.png)

Az ábrán látható az Azure virtuális gép egyedi azonosítója, előtt és után a visszaállítás Azure Backup szolgáltatáson keresztül. Az SAP hardver kulcs, használja a licencelési SAP, használja a virtuális gép azonosítóját Következésképpen új SAP licencet telepítve van a virtuális gépek visszaállítása után.

Ez az útmutató biztonsági másolat létrehozásakor egy új Azure biztonsági mentési szolgáltatás előzetes üzemmódban adtak meg. Lehetővé teszi a virtuális gép pillanatkép készült a virtuális gép biztonsági mentési alapján szintű fájlvisszaállításra. Ezzel elkerülhető, hogy telepítse egy új virtuális Gépet, hogy szükség van, ezért a virtuális gép egyedi azonosítója ugyanaz marad, és nem új SAP HANA-licenc kulcsot meg kell adni. Ez a szolgáltatás további dokumentációiért nyújtanak után lett teljes körűen tesztelve.

Azure biztonsági mentés lehetővé teszi a idővel biztonsági másolatot az egyes Azure virtuális lemezek és fájlok és könyvtárak innen: a virtuális Gépen belül. Egy Azure Backup fő előnye az összes biztonsági mentés, az ügyfél ne kelljen mentése kezelését. Ha a visszaállítás szükségessé válik, Azure biztonsági mentési fogják kiválasztani a helyes biztonsági másolat.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA virtuális gép biztonsági mentése a lemezek manuális pillanatkép keresztül

Az Azure Backup szolgáltatás helyett egy sikerült konfigurálása egy egyedi biztonsági mentési megoldás blob pillanatképeinek Azure virtuális merevlemezek manuálisan a PowerShell segítségével. Lásd: [blob pillanatképei Using PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) lépések leírását.

Nagyobb rugalmasságot nyújt, de nem oldja meg a problémákat a jelen dokumentum korábbi alapján:

- Egy továbbra is kell ügyeljen arra, hogy SAP HANA konzisztens állapotban
- Az operációsrendszer-lemez nem írható felül, még akkor is, ha a virtuális gép felszabadítása figyelmezteti a felhasználókat arra, hogy létezik-e a címbérlet-hiba miatt. Ez csak akkor működik a virtuális Gépet, amelyek virtuális gép új egyedi azonosító és a telepítése egy új SAP-licenc szükséges törlését követően.

![Lehetséges, csak az Azure virtuális gép adatlemezek visszaállítása](media/sap-hana-backup-storage-snapshots/image021.png)

Csak egy Azure virtuális Gépre, az első virtuális gép új egyedi azonosító a probléma elkerülése adatlemezek visszaállíthat, és ezért érvényteleníti az SAP-licenc:

- A teszteket két Azure adatlemezek volt a virtuális Géphez csatlakozik, és szoftveres RAID platformképességeivel lett definiálva 
- Ez volt megerősítette, hogy SAP HANA konzisztens állapotban SAP HANA-pillanatkép szolgáltatás
- Fájlrendszer rögzítése (lásd: _SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel_ a kapcsolódó cikkben [biztonsági útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md))
- A BLOB pillanatképek vették mindkét adatlemezek
- Fájlrendszer rögzítésének feloldása
- SAP HANA-pillanatkép megerősítése
- Az adatlemezek visszaállításához a virtuális gép le volt állítva, és mindkét lemez leválasztása
- Leválasztása a lemezeket, miután azok felülírva a korábbi blob pillanatképekkel
- Ezután a visszaállított virtuális lemezek volt újra a virtuális Géphez csatlakozik
- A virtuális gép elindítása, után minden, a szoftverfrissítési RAID működött, és vissza lett állítva a blob pillanatkép idő:
- HANA vissza lett-e állítva a HANA pillanatkép:

Ha sikerült a blob pillanatképek előtt állítsa le a SAP HANA, az eljárás kevésbé összetett lesz. Ebben az esetben egy sikerült hagyja ki a HANA pillanatkép és, ha nincs más zajlik a rendszer is hagyhatja a fájl rendszer rögzíteni. A képbe hozzáadott összetettsége származik, közben minden online pillanatképek ehhez szükség esetén. Lásd: _SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel_ a kapcsolódó cikkben [biztonsági útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Következő lépések
* [Biztonsági mentési útmutató SAP Hana Azure virtuális gépeken](sap-hana-backup-guide.md) áttekintése és bevezető információkat biztosít.
* [SAP HANA biztonsági másolat alapján fájlszintű](sap-hana-backup-file-level.md) tartalmazza a fájl alapú biztonsági mentési beállítás.
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md).
