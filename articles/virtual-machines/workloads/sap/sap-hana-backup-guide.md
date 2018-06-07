---
title: Azure virtuális gépeken SAP HANA a biztonsági mentési útmutatója |} Microsoft Docs
description: Biztonsági mentési SAP Hana az útmutató két fő biztonsági mentési lehetőségek SAP Hana az Azure virtuális gépeken
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9346ed6b26b4fa4a7875354153b6847938253d1e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657009"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Útmutató az Azure-beli virtuális gépeken futó SAP HANA biztonsági mentéséhez

## <a name="getting-started"></a>Első lépések

A biztonsági mentési útmutató az Azure virtuális gépeken futó SAP Hana csak Azure-specifikus témakörök ismerteti. Általános SAP HANA biztonsági mentési kapcsolódó elemek, a dokumentációjában SAP HANA (lásd: _biztonsági mentési SAP HANA-dokumentáció_ Ez a cikk későbbi).

Ez a cikk a két fő biztonsági mentési lehetőségek SAP Hana Azure virtuális gépeken futó célja:

- A fájlrendszer a Linux virtuális gépeinek HANA biztonsági mentés (lásd: [SAP HANA Azure biztonsági mentési fájl szinten](sap-hana-backup-file-level.md))
- A storage-pillanatfelvételekkel a szolgáltatással az Azure storage blob pillanatkép manuálisan vagy az Azure Backup szolgáltatás alapján HANA biztonsági mentés (lásd: [SAP HANA biztonsági másolat alapján storage-pillanatfelvételekkel](sap-hana-backup-storage-snapshots.md))

SAP HANA API, amely lehetővé teszi, hogy a külső biztonsági mentési eszközök közvetlenül az SAP HANA integrálása biztonsági kínál. (Ez nem ez az útmutató hatókörén belül.) Nincs közvetlen integráció az Azure Backup szolgáltatásban elérhető jobbra a alapján ez az API SAP HANA van.

SAP HANA hivatalosan támogatott Azure Virtuálisgép-típussá GS5 egyetlen példányt egy további korlátozás OLAP munkaterhelések (lásd: [hitelesített IaaS platformok](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) az SAP-webhelyen). Ez a cikk új ajánlatok SAP Hana Azure elérhető legyen, mint fog frissülni.

Nem érhető el egy SAP HANA hibrid megoldás Azure SAP HANA futtató nem virtualizált fizikai kiszolgálókon. Azonban az SAP HANA Azure biztonsági mentési útmutató hozzá van rendelve egy SAP HANA futtató egy Azure virtuális gép, a SAP HANA-nem fut a tiszta Azure-környezetéhez &quot;nagy példányok.&quot; Lásd: [SAP HANA (nagy példányok) – áttekintés és Azure architektúra](hana-overview-architecture.md) tájékozódhat a biztonsági mentési megoldás a &quot;nagy példányok&quot; storage-pillanatfelvételekkel alapján.

Általános információk a támogatott Azure SAP termékek található [SAP Megjegyzés 1928533](https://launchpad.support.sap.com/#/notes/1928533).

A következő három adat áttekintést a jelenleg használt natív Azure-képességek SAP HANA biztonsági mentési lehetőségeket, és három lehetséges jövőbeli biztonsági mentési helyzetet is megjelenítése. A kapcsolódó cikkek [SAP HANA Azure biztonsági mentési fájl szinten](sap-hana-backup-file-level.md) és [SAP HANA biztonsági másolat alapján storage-pillanatfelvételekkel](sap-hana-backup-storage-snapshots.md) részletesen, beleértve az SAP mérete és a teljesítménnyel kapcsolatos ezeket a lehetőségeket ismerteti HANA biztonsági mentések, amelyek multi-terabájt méretű.

![Az ábrán látható, két lehetőség közül választhat, az aktuális virtuális gép állapotának mentése](media/sap-hana-backup-guide/image001.png)

Az ábrán látható, az aktuális virtuális gép állapotának mentése, vagy az Azure Backup szolgáltatás vagy a virtuális gépek lemezei manuális pillanatképe lehetőségét. Ezt a módszert, egy nem&#39;nincs SAP HANA-biztonsági mentések kezelése. A lemez pillanatkép forgatókönyv kihívás fájlrendszer-konzisztenciával, de az alkalmazáskonzisztens lemez állapota. A konzisztencia-témakör a szakaszban tárgyalt _SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel_ című cikkben. Képességek és az Azure Backup szolgáltatás SAP HANA másolatokra történő visszaállításával kapcsolatos korlátozások is ismertetése a cikk későbbi részében.

![Az ábrán látható, hogy egy SAP HANA fogadására beállítások fájlszintű biztonsági a virtuális Gépen belül](media/sap-hana-backup-guide/image002.png)

Ez a szám egy SAP HANA fájl biztonsági másolat a virtuális Gépen belül, és majd tárolásukról HANA biztonságimásolat-fájlok valahol ellenkező esetben a különböző eszközök használatával lehetőségeket mutatja be. HANA biztonsági másolat egy pillanatkép-alapú biztonsági mentési megoldás több időt igényel, azonban mindenképpen sértetlenségét és konzisztencia előnyeit. További részletek találhatók a cikk későbbi részében.

![Ezen az ábrán egy lehetséges jövőbeli SAP HANA biztonsági mentési forgatókönyvet mutat](media/sap-hana-backup-guide/image003.png)

Ezen az ábrán egy lehetséges jövőbeli SAP HANA biztonsági mentési forgatókönyvet mutat. SAP HANA engedélyezett másodlagos replikációból biztonsági másolatok fogadására, ha azt szeretné adja hozzá a biztonsági mentési stratégia további beállításokat. Jelenleg nem lehet a SAP HANA wiki post megfelelően:

_&quot;Az biztonsági másolatok készítése a másodlagos oldalon is?_

_Nem, jelenleg csak adatokat és az elsődleges oldal a biztonsági mentések jelentkezzen. Ha automatikus napló biztonsági mentési engedélyezve van, a másodlagos oldalának felvásárlási után a Naplók biztonsági másolatainak automatikusan írandó van.&quot;_

## <a name="sap-resources-for-hana-backup"></a>SAP HANA biztonsági mentés erőforrásait

### <a name="sap-hana-backup-documentation"></a>Biztonsági mentési SAP HANA-dokumentáció

- [SAP HANA felügyeleti bemutatása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [A biztonsági mentési és helyreállítási stratégia tervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Biztonsági mentés HANA ABAP DBACOCKPIT ütemezése](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Ütemezési adatok biztonsági mentése (SAP HANA Vezérlőpult)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- SAP HANA gyakori kérdések a biztonsági mentés [SAP Megjegyzés 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Gyakori kérdések az adatbázis és a tárolási pillanatképek SAP HANA [SAP Megjegyzés 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nem alkalmas hálózati fájlrendszerek biztonsági másolat és helyreállítás a [SAP Megjegyzés 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Miért SAP HANA biztonsági mentést?

Az Azure storage kínál rendelkezésre állásának és megbízhatóságának nem (lásd: [Microsoft Azure Storage bemutatása](../../../storage/common/storage-introduction.md) további információ az Azure storage).

A minimális &quot;biztonsági mentési&quot; annak az Azure garantált szolgáltatási szintek, megtartja a SAP HANA adatainak és naplókönyvtárainak fájlokat az Azure virtuális merevlemezek a virtuális gép SAP HANA-kiszolgálóhoz csatlakoztatott támaszkodnak. Ez a megközelítés VM hibák, de a SAP HANA-adatok és a naplófájlokat vagy a logikai hibák például törlés, adatok vagy a fájlok véletlenül nem lehetséges kár ismerteti. Biztonsági mentés is szükség, megfelelőségi vagy jogi okokból. Rövid mindig szükség van a SAP HANA biztonsági mentésekhez.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA biztonsági mentés helyességét ellenőrzése
Egy teszt visszaállítást egy másik rendszeren futó tárolási pillanatképek használata esetén ajánlott. Ez a megközelítés biztosítja az gondoskodjon arról, hogy a biztonsági mentés helyes-e, és a belső folyamatok, a biztonsági mentéshez és a munka visszaállítani, mert a várt. Ez nem egy jelentős a küszöbértéket a helyszínen, sokkal egyszerűbb elvégzéséhez szükséges erőforrások átmenetileg biztosítva erre a célra, a felhőben.

Tartsa szem előtt, amely során egy egyszerű visszaállítás és ellenőrizni, hogy HANA működik-e és fut nem elegendő. Ideális esetben egy futhat győződjön meg arról, hogy rendben-e a visszaállított adatbázis tábla konzisztencia-ellenőrzést. SAP HANA kínál a konzisztencia-ellenőrzések leírt különböző típusú [SAP Megjegyzés 1977584](https://launchpad.support.sap.com/#/notes/1977584).

A tábla konzisztencia-ellenőrzés kapcsolatos információkat is megtalálható az SAP webhellyel [tábla és a katalógus konzisztencia-ellenőrzések](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

A szokásos fájlszintű biztonsági a teszt visszaállítás nincs szükség. Két SAP HANA-eszközzel, ellenőrizze, hogy mely biztonsági másolat nem használható visszaállítási: hdbbackupdiag és hdbbackupcheck. Lásd: [manuális ellenőrzése hogy a helyreállítás az lehetséges](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) ezekkel az eszközökkel kapcsolatos további információk.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Előnyei és hátrányai HANA biztonsági mentés és tárolás pillanatkép

SAP nem&#39;t vagy HANA biztonsági másolat tárolási pillanatkép és előnyben részesítik. Felsorolja az előnyei és hátrányai, egy alapján határozza meg attól függően, hogy a helyzet, és a rendelkezésre álló tár technológia használandó (lásd: [tervezési a biztonsági mentési és helyreállítási stratégia](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Az Azure, vegye figyelembe azt a tényt, hogy az Azure-blobot pillanatkép-e a szolgáltatás nem&#39;t garancia fájlrendszer-konzisztenciával (lásd: [blob pillanatképei Using PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A következő szakaszban _SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel_, ez a szolgáltatás kapcsolatos szempontokat ismerteti.

Emellett egy rendelkezik a számlázási vonatkozó következmények ismertetése, amikor olyan gyakran blob pillanatképek ebben a cikkben leírtak szerint: [ismertetése hogyan pillanatképek keletkeznek költségek](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– azt nem&#39;t, nyilvánvaló, Azure virtuális használatával lemezek.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA adatkonzisztencia véve a storage-pillanatfelvételekkel

A fájlok rendszer és az alkalmazás konzisztenciájának egy összetett probléma esetén tárolási pillanatképek készítése. A legegyszerűbb módja a problémák elkerülése érdekében az SAP HANA leállt, vagy lehet, hogy akkor is igaz, az egész virtuális gép lenne. Előfordulhat, hogy a leállás doable egy bemutató vagy prototípus vagy akár egy fejlesztői rendszernek, de nincs lehetőség az éles rendszerre.

Az Azure, vegye figyelembe, hogy az Azure-blobot pillanatkép-e a szolgáltatás nem senkinek&#39;t garancia fájlrendszer-konzisztenciával. Jól működik azonban használatával az SAP HANA pillanatkép-funkció, mindaddig, amíg nincs érintett csak egyetlen virtuális lemez. De egyetlen lemezen, még akkor is a további elemek azokat fel kell venni. [Megjegyzés: 2039883 SAP](https://launchpad.support.sap.com/#/notes/2039883) SAP HANA-biztonsági mentések storage-pillanatfelvételekkel keresztül kapcsolatos fontos információkat tartalmaz. Például, hogy megjelenik-e, hogy a XFS fájlrendszerrel futtatásához szükséges **xfs\_rögzítése** konzisztencia biztosításához tárolási pillanatkép megkezdése előtt (lásd: [xfs\_freeze(8) - Linux man lap ](https://linux.die.net/man/8/xfs_freeze) talál részletes információt **xfs\_rögzítése**).

A témakör a következetesség válik, abban az esetben, ha egy operációs rendszer által felölelt több lemez/kötet még több kihívást. Például úgy, hogy mdadm vagy LVM használ, és szétosztott. A fenti állapotok SAP Megjegyzés:

_&quot;De vegye figyelembe, hogy a tárolórendszer rendelkezik egy SAP HANA-adatok kötetenként tárolási pillanatkép létrehozása során i/o-konzisztencia biztosításához, azaz egy SAP HANA-szolgáltatással kapcsolatos adatok kötet pillanatfelvételeinek kell lennie egy atomi művelet.&quot;_

Feltételezve, hogy az átfedés négy Azure virtuális lemezek XFS fájlrendszer, az alábbi lépések nyújtanak a HANA adatterület jelölő konzisztens pillanatkép:

- HANA pillanatkép előkészítése
- A fájlrendszer rögzítése (például **xfs\_rögzítése**)
- Minden szükséges blob pillanatkép létrehozása az Azure
- A fájlrendszer feloldása
- Erősítse meg a HANA pillanatkép

Javasoljuk, hogy segítségével a fent leírt lépéseket minden olyan esetben kell a biztonság kedvéért, függetlenül attól, milyen fájlrendszer. Vagy ha ez egy egyetlen lemez vagy, mdadm vagy LVM több lemezre kiterjedő csíkozást.

Fontos a HANA pillanatkép megerősítéséhez. Miatt a &quot;másolási módosításkor,&quot; SAP HANA előfordulhat, hogy nincs szükség további lemezterületet a ez mód pillanatkép-előkészítéséhez. Az&#39;s is nem lehetséges, hogy új biztonsági mentést elindítani, amíg a rendszer meggyőződött róla, a SAP HANA-pillanatkép.

Az Azure Backup szolgáltatás Azure Virtuálisgép-bővítmények használatával a fájlrendszer-konzisztenciával kezeli. A Virtuálisgép-bővítmények önálló használatra nem érhetők el. Egy SAP HANA konzisztencia kezeléséhez továbbra is rendelkezik. Olvassa el a kapcsolódó [SAP HANA Azure Backup a fájlszintű](sap-hana-backup-file-level.md) további információt.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA-ütemezési biztonsági mentési stratégia

A SAP HANA-cikk [tervezési a biztonsági mentési és helyreállítási stratégia](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) el a biztonsági mentéseket alapszintű terv szerint:

- (Naponta) pillanatkép-tárolás
- Teljes biztonsági mentését a fájl vagy bacint formátumban (hetente egyszer)
- Automatikus naplóalapú biztonsági mentések

Szükség esetén egy sikerült nyissa meg teljesen nélkül storage-pillanatfelvételekkel; sikerült cserélhetősége HANA különbözeti biztonsági mentések, például növekményes vagy különbözeti biztonsági másolatok (lásd: [különbözeti biztonsági mentések](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

A HANA felügyeleti útmutató egy példa listája. Azt javasolja, hogy egy helyreállítás SAP HANA az adott időben a biztonsági mentések az alábbi lépések segítségével:

1. Teljes biztonságimásolat-készítő
2. Különbözeti biztonsági mentése
3. A növekményes biztonsági mentés 1
4. A növekményes biztonsági mentés 2. régiója
5. Napló-biztonságimásolatokat

Vonatkozó pontos ütemezés mikor és milyen gyakran történjen egy adott típust, nincs lehetőség általános iránymutatás adhat – ez túl specifikus, és attól függ, a rendszer hány adatváltozásokat fordul elő. Egy alapszintű ajánlás SAP oldaláról, amelyek általános útmutatásként látható, az, hogy ellenőrizze egy teljes HANA biztonsági mentés hetente egyszer.
Napló-biztonságimásolatokat vonatkozó, az SAP HANA dokumentációjában [Naplóalapú biztonsági mentések](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP is azt javasolja, hogy ezzel biztosítható, hogy a végtelen ennél a biztonsági mentési katalógus néhány housekeeping (lásd: [biztonságimásolat-katalógus és a biztonsági másolatok tárolásának háztartási](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA-konfigurációs fájlok

Ahogy az a – gyakori kérdések a [SAP Megjegyzés 1642148](https://launchpad.support.sap.com/#/notes/1642148), a SAP HANA konfigurációs fájljai nem egy szabványos HANA biztonsági másolat része. Nem nélkülözhetetlenek visszaállította a rendszert. Manuálisan a HANA konfigurációs sikerült módosítani a visszaállítás után. Abban az esetben, ha egy szeretné a visszaállítási folyamat során ugyanazt az egyéni konfigurációt beolvasandó, fontos külön-külön biztonsági mentése a HANA konfigurációs fájlokat.

Ha szabványos HANA biztonsági mentések egy dedikált HANA biztonságimásolat-fájl rendszerre, egy sikerült a konfigurációs fájlokat átmásolhatja a ugyanazt biztonsági mentési filesystem, és másolja minden együtt a végső célhelyet ritkán hasonlóan a blob-tároló.

### <a name="sap-hana-cockpit"></a>SAP HANA-Vezérlőpult

SAP HANA-Vezérlőpult figyelésének és kezelésének SAP HANA böngészővel lehetőséget biztosít. Azt is lehetővé teszi, hogy a SAP HANA-biztonsági mentések kezelése, és ezért SAP HANA Studio és ABAP DBACOCKPIT alternatívájaként használható (lásd: [SAP HANA-Vezérlőpult](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) olvashat).

![Az ábrán látható, a SAP HANA Vezérlőpult adatbázis felügyeleti képernyő](media/sap-hana-backup-guide/image004.png)

Az ábrán látható, a SAP HANA Vezérlőpult adatbázis felügyeleti képernyő, és a biztonsági mentési csempe a bal oldalon. Megtekintheti a biztonsági mentési csempe szükség van a megfelelő felhasználói jogosultságok bejelentkezési fiók.

![Biztonsági mentések SAP HANA-Vezérlőpult tudja felügyelni, amíg azok nem folyamatos](media/sap-hana-backup-guide/image005.png)

Biztonsági mentések figyelhető SAP HANA Vezérlőpulton, amíg a folyamatban lévő, és amikor elkészült, a biztonsági mentési részletek érhetők el.

![Egy példa, egy Azure SLES 12 virtuális gépen a Gnome desktop Firefox használatával](media/sap-hana-backup-guide/image006.png)

Az előző képernyőfelvételeken történtek egy Windows Azure virtuális gépről. Erre látható egy példa egy Azure SLES 12 virtuális gépen a Gnome desktop Firefox segítségével. Azt illusztrálja, SAP HANA Vezérlőpulton található SAP HANA biztonsági mentési ütemterv megadása lehetőséget. Egy is láthatja, mivel azt sugallja, dátum/idő előtagjaként a biztonságimásolat-fájlok. Az SAP HANA Studióban, az alapértelmezett előtag van &quot;COMPLETE\_adatok\_biztonsági MENTÉSI&quot; teljes biztonsági mentés során. Egy egyedi előtag használata javasolt.

### <a name="sap-hana-backup-encryption"></a>SAP HANA a biztonsági mentés titkosítása

SAP HANA adatainak és naplókönyvtárainak titkosításának kínál. Ha SAP HANA-adatainak és naplókönyvtárainak nincs titkosítva, majd a biztonsági mentéseket is nincs titkosítva. Az ügyfél az SAP HANA-biztonsági mentések titkosításához valamilyen harmadik féltől származó megoldás használata esetén. Lásd: [adatok és a napló Kötettitkosítást](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) további tudnivalók az SAP HANA-titkosítás.

A Microsoft Azure-ügyfél titkosításához használhatja az infrastruktúra-szolgáltatási virtuális gép titkosítási szolgáltatást. Használhatja például egy dedikált adatlemezt csatolni a virtuális gép szolgáló SAP HANA-biztonsági mentések tárolására, akkor ezek a lemezek példányát.

Az Azure Backup szolgáltatás kezelni tud a titkosított virtuális gép/lemez (lásd: [biztonsági mentése és visszaállítása a virtuális gépek az Azure Backup szolgáltatással titkosított](../../../backup/backup-azure-vms-encryption.md)).

Egy másik lehetőség az lenne a SAP HANA-virtuális gép és a titkosítás nélkül lemezei karbantartása, és tárolja a biztonsági mentési SAP HANA-fájlokat egy tárfiókot, amelyre engedélyezték a titkosítást (lásd: [Azure Storage szolgáltatás titkosítási inaktív adatok](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Teszt beállítása

### <a name="test-virtual-machine-on-azure"></a>Teszt virtuális gép az Azure-on

Egy Azure GS5 virtuális gép egy SAP HANA-telepítés a következő biztonsági mentés/visszaállítás tesztek lett megadva.

![Az ábrán látható része a HANA teszt virtuális gép az Azure portál áttekintése](media/sap-hana-backup-guide/image007.png)

Az ábrán látható, az Azure portál áttekintése a HANA teszt virtuális gép részét.

### <a name="test-backup-size"></a>Biztonsági másolat mérete tesztelése

![Ez a szám HANA Studio a biztonsági mentési konzoljáról készült, és megmutatja a HANA index kiszolgáló 229 GB biztonságimásolat-fájl méretét](media/sap-hana-backup-guide/image008.png)

Egy üres táblázatot adatokat lekérni a teljes adatok biztonsági másolatának mérete több mint 200 GB-os reális teljesítményadatokat kapcsolattípusokból töltötte. Az ábra HANA Studio a biztonsági mentési konzoljáról készült, és megmutatja a HANA index kiszolgáló 229 GB biztonságimásolat-fájl méretét. A vizsgálatok az alapértelmezett biztonsági mentési előtag SAP HANA Studio "COMPLETE_DATA_BACKUP" lett megadva. A valós éles rendszerek hasznosabb előtag meg kell határozni. SAP HANA-Vezérlőpult Dátum és idő alapján.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Fájlok másolása közvetlenül az Azure storage eszköz tesztelése

Átvitele SAP HANA biztonságimásolat-fájlok közvetlenül az Azure blob Storage tárolóban, vagy az Azure fájlmegosztások, a blobxfer eszköz lett megadva, mert mindkét célok támogatja, és könnyen integrálható az automatizálási parancsfájlokat a parancssori felület miatt. A blobxfer eszköz [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Tesztelje a biztonsági mentés méretének becslése

Fontos SAP HANA biztonsági mentés méretének becslése. Ez a becslés segít a teljesítmény javítása a biztonságimásolat-fájl maximális mérete biztonságimásolat-fájlok, fájlmásolás közben párhuzamosság miatt számos meghatározásával. (Adatai magyarázatát a cikk későbbi részében.) Egy kell is döntenie, hogy egy teljes biztonsági mentését vagy különbözeti biztonsági mentés (növekményes vagy különbözeti).

Szerencsére van egy egyszerű SQL-utasítást, amely a biztonsági mentési fájlok méretének becslése: **válasszon \* m\_biztonsági MENTÉSI\_mérete\_BECSLÉSEKET** (lásd: [becslése a biztonsági mentést a fájlrendszer szükséges hely](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Az SQL-utasítás kimenete megegyezik a teljes biztonsági mentését a lemezen szinte teljesen a tényleges mérete](media/sap-hana-backup-guide/image009.png)

A tesztrendszerhez az SQL-utasítás kimenete megegyezik szinte teljesen valós méretét a teljes biztonsági mentését a lemezen.

### <a name="test-hana-backup-file-size"></a>Teszt HANA biztonságimásolat-fájl mérete

![A biztonsági mentési HANA Studio konzol lehetővé teszi egy HANA biztonságimásolat-fájlok maximális méretének korlátozása](media/sap-hana-backup-guide/image010.png)

A biztonsági mentési HANA Studio konzol lehetővé teszi egy HANA biztonságimásolat-fájlok maximális méretének korlátozására. A minta környezetben, a szolgáltatás lehetővé teszi helyett egy 230 GB-os biztonságimásolat-fájl több kisebb biztonsági mentési fájlok eléréséhez. Kisebb fájlméretet jelentős hatással van a teljesítményre (a kapcsolódó cikke [SAP HANA Azure Backup a fájlszintű](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Összegzés

Az alábbi táblázatok bemutatják és megoldások Azure virtuális gépeken futó egy SAP HANA-adatbázis biztonsági mentése teszt eredménye alapján.

**A fájlrendszer SAP HANA mentésére, és másolja a biztonságimásolat-fájlt utána az utolsó biztonsági mentési célhelyre**

|Megoldás                                           |Informatikai szakemberek                                 |Hátrányok                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|A virtuális gépek lemezei HANA biztonsági mentések megtartása                      |Nincsenek további felügyeleti próbálkozások     |Helyi virtuális gép lemezterületet eats           |
|Másolja a biztonságimásolat-fájlt a blob storage Blobxfer eszköz |Több fájl másolása párhuzamossági ritkán használt adatok a blob storage használatához | További eszköz karbantartási és az egyéni parancsfájlok | 
|A BLOB másolási Powershell vagy a parancssori felület használatával                    |Nincs szükség esetén további eszköz valósítható meg Azure Powershell vagy a parancssori felület használatával |a manuális eljáráshoz, az ügyfélnek a parancsfájlkezelést, és a visszaállítási másolt blobok felügyeleti irányuló van.|
|NFS-megosztás másolása                                  |Utófeldolgozási biztonsági mentési fájlok és más virtuális gép a HANA kiszolgáló gyakorolt hatás nélkül|Lassú másolást|
|Azure Fájlszolgáltatás Blobxfer másolás                |Nem keleti-afrikai fel lemezterületet a helyi virtuális gépek lemezei|Nincs közvetlen támogatási írja HANA biztonsági másolat, fájlméret korlátozása fájlmegosztás jelenleg 5 TB:|
|Az Azure Backup szolgáltatás ügynöke                                 | Előnyben részesített megoldás lenne         | Linux rendszeren jelenleg nem érhető el    |



**Storage-pillanatfelvételekkel alapuló biztonsági mentési SAP HANA**

|Megoldás                                           |Informatikai szakemberek                                 |Hátrányok                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Lehetővé teszi a virtuális gép biztonsági mentése a blob pillanatképek alapján | Használatakor nem szintű fájlvisszaállításra igényel az új virtuális gép létrehozása a visszaállítási folyamat, amely majd azt az új SAP HANA-licenc kulcs szükséges a|
|A pillanatképek kézi blob                              | Hozzon létre, és állítsa vissza az adott virtuális gépek lemezei a virtuális gép egyedi azonosítója módosítása nélkül adhassanak|Az összes manuális tevékenység, amelyet az ügyfél által végezhető|

## <a name="next-steps"></a>További lépések
* [SAP HANA Azure biztonsági mentési fájl szinten](sap-hana-backup-file-level.md) ismerteti a fájl alapú biztonsági mentési beállítás.
* [A storage-pillanatfelvételekkel alapján SAP HANA biztonsági mentés](sap-hana-backup-storage-snapshots.md) ismerteti a tárolási pillanatkép-alapú biztonsági mentési beállítás.
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md).
