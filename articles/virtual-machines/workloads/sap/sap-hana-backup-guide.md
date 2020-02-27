---
title: Az Azure Virtual Machines SAP HANA biztonsági mentési útmutatója | Microsoft Docs
description: A SAP HANA biztonsági mentési útmutatója két fő biztonsági mentési lehetőséget biztosít az Azure-beli virtuális gépek SAP HANA
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: 8de83cbb7060e6ca5390720a4a241be71bb9dc92
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617430"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Az Azure-beli SAP HANA biztonsági mentési útmutatója Virtual Machines

## <a name="getting-started"></a>Első lépések

Az Azure Virtual Machines szolgáltatásban futó SAP HANA biztonsági mentési útmutatója csak az Azure-specifikus témaköröket fogja leírni. Az általános SAP HANA biztonsági mentéshez kapcsolódó elemek esetében tekintse meg a SAP HANA dokumentációját (lásd a cikk későbbi, _SAP HANA biztonsági mentési dokumentációját_ ).

Ennek a cikknek a középpontjában az Azure Virtual Machines szolgáltatásban SAP HANA két fő biztonsági mentési lehetősége van:

- HANA biztonsági másolat készítése egy Azure-beli linuxos virtuális gépen található fájlrendszerre (lásd: [SAP HANA Azure Backup a fájl szintjén](sap-hana-backup-file-level.md))
- A HANA biztonsági mentése a tárolási Pillanatképek alapján az Azure Storage blob Snapshot szolgáltatással manuálisan vagy Azure Backup szolgáltatással (lásd: [SAP HANA biztonsági mentés a tárolási Pillanatképek alapján](sap-hana-backup-storage-snapshots.md))

A SAP HANA biztonsági mentési API-t biztosít, amely lehetővé teszi, hogy a külső gyártótól származó biztonsági mentési eszközök közvetlenül a SAP HANA integrálva legyenek. (Ez nem az útmutató hatókörébe esik.) Jelenleg nem érhető el közvetlen integráció a SAP HANA Azure Backup szolgáltatással, amely ebben az API-ban elérhető.

A SAP HANA a különböző Azure-beli virtuálisgép-típusok, például az Azure M sorozatok esetében hivatalosan támogatott. A SAP HANA Certified Azure-beli virtuális gépek teljes listájáért tekintse meg a [Certified IaaS platformokat](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ez a cikk akkor frissül, amikor az Azure-beli SAP HANA új ajánlatai elérhetővé válnak.

Létezik egy SAP HANA hibrid megoldás is az Azure-on, ahol a SAP HANA nem virtualizált fizikai kiszolgálókon fut. Ez a SAP HANA Azure Backup-útmutató azonban olyan tiszta Azure-környezetet foglal magába, ahol a SAP HANA egy Azure-beli virtuális gépen fut, nem SAP HANA &quot;nagyméretű példányokon.&quot; tekintse meg a [SAP HANA (nagyméretű példányok) áttekintését és architektúráját az Azure-on](hana-overview-architecture.md) , ha további információt szeretne erről a biztonsági mentési megoldásról &quot;nagyméretű példányokról&quot; tárolási Pillanatképek alapján.

Az Azure-ban támogatott SAP-termékekkel kapcsolatos általános információk a 1928533-es [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1928533)találhatók.

Az alábbi három ábra áttekintést nyújt a SAP HANA biztonsági mentési lehetőségeiről a natív Azure-képességek használatával, és három lehetséges jövőbeli biztonsági mentési forgatókönyvet is megjelenít. A [fájlok szintjén Azure Backup SAP HANA](sap-hana-backup-file-level.md) kapcsolódó cikkek, valamint a [SAP HANA biztonsági mentés a tárolási Pillanatképek alapján](sap-hana-backup-storage-snapshots.md) című cikk részletesen leírja ezeket a beállításokat, beleértve a méretet és a teljesítménnyel kapcsolatos szempontokat SAP HANA több terabájt méretű biztonsági másolatok esetében is.

![Ez az ábra két lehetőséget mutat a virtuális gép állapotának mentésére](media/sap-hana-backup-guide/image001.png)

Ez az ábra az aktuális virtuálisgép-állapot mentésének lehetőségét mutatja Azure Backup szolgáltatáson vagy a virtuálisgép-lemezek manuális pillanatképén keresztül. Ezzel a módszerrel&#39;nem kell SAP HANA biztonsági másolatokat kezelnie. A lemez pillanatkép-forgatókönyvének kihívása a fájlrendszer konzisztenciája, valamint az alkalmazások konzisztens lemezének állapota. A konzisztencia témakört a jelen cikk későbbi, a _tárolási pillanatképek készítése során SAP HANA adatkonzisztencia_ című szakasza tárgyalja. A SAP HANA biztonsági másolatokkal kapcsolatos Azure Backup szolgáltatások funkcióit és korlátozásait a cikk későbbi részében is tárgyaljuk.

![Ez az ábra azokat a beállításokat mutatja be, amelyekkel a SAP HANA fájlok biztonsági mentése a virtuális gépen belül](media/sap-hana-backup-guide/image002.png)

Ez az ábra azokat a lehetőségeket mutatja be, amelyekkel egy SAP HANA fájl biztonsági mentését elvégezheti a virtuális gépen, majd más eszközök használatával tárolhatja azt a HANA-beli biztonsági mentési fájlokat. A HANA biztonsági mentés több időt vesz igénybe, mint a pillanatkép-alapú biztonsági mentési megoldás, de az integritással és az egységességgel kapcsolatos előnyökkel jár. További részleteket a cikk későbbi részében talál.

![Ez az ábra egy lehetséges jövőbeli SAP HANA biztonsági mentési forgatókönyvet mutat be](media/sap-hana-backup-guide/image003.png)

Ez az ábra egy lehetséges jövőbeli SAP HANA biztonsági mentési forgatókönyvet mutat be. Ha SAP HANA engedélyezi, hogy egy másodlagos replikálásról készítsen biztonsági másolatot, további lehetőségeket is hozzáadhat a biztonsági mentési stratégiákhoz. Jelenleg nem lehetséges a SAP HANA wiki bejegyzése szerint:

_&quot;a másodlagos oldalról is lehetséges biztonsági mentést készíteni?_

_Nem, jelenleg csak az adatokat és a naplók biztonsági mentését lehet az elsődleges oldalon. Ha engedélyezve van az automatikus napló biztonsági mentése, a másodlagos oldalra való átvételt követően a rendszer automatikusan megírja a napló biztonsági másolatait.&quot;_

## <a name="sap-resources-for-hana-backup"></a>A HANA biztonsági mentésének SAP-erőforrásai

### <a name="sap-hana-backup-documentation"></a>SAP HANA biztonsági mentési dokumentáció

- [Bevezetés a SAP HANA felügyeletbe](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [A biztonsági mentési és helyreállítási stratégia megtervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [A HANA biztonsági mentésének ütemterve ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Az adatbiztonsági másolatok ütemezése (SAP HANA pilótafülke)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Gyakori kérdések SAP HANA biztonsági mentésről az [SAP-megjegyzés 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Gyakori kérdések SAP HANA adatbázis-és tárolási pillanatképekről az [SAP-megjegyzés 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nem megfelelő hálózati fájlrendszerek biztonsági mentéshez és helyreállításhoz a 1820529-as [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Miért SAP HANA biztonsági mentést?

Az Azure Storage szolgáltatás rendelkezésre állást és megbízhatóságot biztosít az Azure Storage-ban (lásd: a [Microsoft Azure Storage bemutatása](../../../storage/common/storage-introduction.md)

&quot;biztonsági mentési&quot; számára a minimális érték az Azure SLA-ra támaszkodik, a SAP HANA-adatok és a naplófájlok tárolása az SAP HANA-kiszolgáló virtuális géphez csatolt Azure VHD-fájlokban. Ez a megközelítés a virtuális gépek meghibásodásait fedi le, de nem jelent kárt a SAP HANA-és naplófájlok esetében, illetve olyan logikai hibákat, mint például az adattörlés vagy a fájlok véletlen törlése. A biztonsági mentések megfelelőségi vagy jogi okokból is szükségesek. Röviden, mindig szükség van SAP HANA biztonsági másolatokra.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA biztonsági mentés helyességének ellenőrzése
A tárolási Pillanatképek használata esetén a teszt visszaállításának futtatása egy másik rendszeren javasolt. Ezzel a módszerrel biztosítható, hogy a biztonsági mentés helyes legyen, és a biztonsági mentés és a visszaállítás belső folyamatai a várt módon működjenek. Habár ez jelentős akadályt jelent a helyszíni működésben, sokkal egyszerűbb a felhőben elvégezni a szükséges erőforrások ideiglenes biztosítását erre a célra.

Ne feledje, hogy egyszerű visszaállítást és ellenőrzést végez, ha a HANA nem működik megfelelően. Ideális esetben az egyiknek egy tábla konzisztencia-ellenőrzését kell futtatnia, hogy a visszaállított adatbázis rendben legyen. SAP HANA számos konzisztencia-ellenőrzést kínál a 1977584-es [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1977584)ismertetett módon.

A tábla konzisztencia-ellenőrzésével kapcsolatos információk az SAP webhelyén [, a Table és a Catalog konzisztencia-ellenőrzéseken](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)is megtalálhatók.

A standard fájlok biztonsági mentése esetén nincs szükség teszt-visszaállításra. Két SAP HANA eszköz áll rendelkezésre, amelyek segítségével ellenőrizhető, hogy mely biztonsági másolat használható a visszaállításhoz: hdbbackupdiag és hdbbackupcheck. További információ az eszközökről: [manuális ellenőrzés, hogy lehetséges-e a helyreállítás](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) .

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>A HANA Backup és a Storage pillanatkép előnyei és hátrányai

Az SAP&#39;-t nem lehet a HANA Backup és a Storage pillanatkép használatával előnyben részesíteni. Felsorolja azok előnyeit és hátrányait, így az a helyzettől és a rendelkezésre álló tárolási technológiától függően eldöntheti, hogy melyiket érdemes használni (lásd [a biztonsági mentési és helyreállítási stratégia megtervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)című témakört).

Az Azure-ban vegye figyelembe, hogy az Azure Blob pillanatkép-funkciója nem&#39;t garantálja a fájlrendszer egységességét (lásd: [blob-Pillanatképek használata a PowerShell használatával](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A következő szakasz, _SAP HANA adatkonzisztencia a tárolási Pillanatképek készítésekor_, a szolgáltatással kapcsolatos szempontokat tárgyalja.

Emellett az egyiknek meg kell ismernie a számlázási szempontokat, amikor gyakran dolgozik a blob-pillanatképekkel a következő cikkben leírtak szerint: a [Pillanatképek felmerülésének ismertetése](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)–&#39;az Azure-beli virtuális lemezek használatakor kézenfekvő

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Adatkonzisztencia SAP HANA a tárolási Pillanatképek készítésekor

A fájlrendszer és az alkalmazások konzisztenciája összetett probléma a tárolási pillanatképek készítése során. A problémák elkerülésének legegyszerűbb módja a SAP HANA leállítása, vagy akár a teljes virtuális gép is. A Leállítás lehet egy bemutató vagy prototípus, vagy akár egy fejlesztői rendszer is, de az éles rendszerek esetében nem lehetséges.

Az Azure-ban az egyiknek szem előtt kell tartania, hogy az Azure&#39;blob pillanatkép-funkciója nem t garantálja a fájlrendszer egységességét. A SAP HANA Snapshot szolgáltatással jól működik, ha csak egyetlen virtuális lemez van benne. De még egyetlen lemezzel is, további elemeket kell ellenőrizni. Az 2039883-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2039883) fontos információkat tartalmaz a SAP HANA biztonsági másolatokról a tárolási pillanatképeken keresztül. Például megemlíti, hogy a XFS fájlrendszerben a **XFS\_rögzítése** szükséges, mielőtt elkezdené a tárolási pillanatképet a konzisztencia garantálása érdekében (lásd: [XFS\_Freeze (8)-Linux man oldal](https://linux.die.net/man/8/xfs_freeze) a **XFS\_befagyasztásával**kapcsolatos részletekért.

A konzisztencia témája még nagyobb kihívást jelent abban az esetben, ha egyetlen fájlrendszer több lemezre vagy kötetre is kiterjed. Például a mdadm vagy az LVM és a csíkozás használatával. Az SAP-Megjegyzés fent említett állapota:

_&quot;azonban ne feledje, hogy a tárolási rendszernek meg kell felelnie az I/O-konzisztencia számára SAP HANA adatmennyiség tárolási pillanatképének létrehozásakor, azaz egy SAP HANA szolgáltatás-specifikus adatkötet snapshotting atomi műveletnek kell lennie.&quot;_

Feltételezve, hogy a XFS fájlrendszer négy Azure virtuális lemezzel rendelkezik, a következő lépések egységes pillanatképet biztosítanak, amely a HANA-adatterületet jelöli:

- HANA-pillanatkép előkészítése
- A fájlrendszer rögzítése (például **XFS használata\_Freeze**)
- Az összes szükséges blob-pillanatkép létrehozása az Azure-ban
- A fájlrendszer rögzítésének feloldása
- A HANA-pillanatkép megerősítése

Javasoljuk, hogy a fenti eljárást minden esetben a biztonságos oldalon kell használni, függetlenül attól, hogy melyik fájlrendszert használja. Vagy ha ez egy lemez vagy csíkozás, mdadm vagy LVM használatával több lemezen.

Fontos, hogy erősítse meg a HANA-pillanatképet. A &quot;-írásos másolás miatt előfordulhat, hogy&quot; SAP HANA nem igényel további lemezterületet a pillanatkép-előkészítési módban. Az&#39;új biztonsági mentéseket nem lehet elindítani, amíg meg nem történik a SAP HANA pillanatkép megerősítése.

A Azure Backup szolgáltatás Azure virtuálisgép-bővítmények használatával gondoskodik a fájlrendszer konzisztenciájáról. Ezek a virtuálisgép-bővítmények nem érhetők el különálló használatra. Az egyiknek továbbra is kezelnie kell SAP HANA konzisztenciát. További információért tekintse meg a kapcsolódó cikket [SAP HANA Azure Backup a fájl szintjén](sap-hana-backup-file-level.md) .

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA biztonsági mentési ütemezési stratégia

A [biztonsági mentést és helyreállítási stratégiát Megtervező](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) SAP HANA cikk a biztonsági mentések alapszintű tervét állítja be:

- Tárolási pillanatkép (naponta)
- Az adatbiztonsági mentés befejezése fájl-vagy bacint-formátum használatával (hetente egyszer)
- Automatikus naplók biztonsági mentései

Opcionálisan a Storage-Pillanatképek nélkül is mehet. Előfordulhat, hogy a HANA-különbözeti biztonsági másolatok, például a növekményes vagy különbözeti biztonsági másolatok (lásd: [különbözeti biztonsági másolatok](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)) helyébe léphetnek.

A HANA felügyeleti útmutató egy példa listát tartalmaz. Azt javasolja, hogy egy adott időpontra SAP HANA a biztonsági mentések következő sorrendjének használatával:

1. Teljes adat biztonsági mentése
2. Különbségi biztonsági mentés
3. Növekményes biztonsági mentés 1
4. Növekményes biztonsági mentés 2
5. Biztonsági másolatok naplózása

A pontos ütemtervtel kapcsolatban, hogy mikor és milyen gyakran fordul elő egy adott biztonsági mentési típus, nem adhat meg általános útmutatást – túl ügyfél-specifikus, és attól függ, hogy hány adatváltozás történik a rendszeren. Az SAP oldal egyik alapszintű ajánlása, amely általános útmutatásként tekinthető, hogy hetente egyszer teljes HANA-biztonsági mentést készítsen.
A naplók biztonsági mentésével kapcsolatban lásd: SAP HANA dokumentációs [napló biztonsági mentései](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

Az SAP emellett azt is javasolja, hogy a biztonsági mentési katalógusban ne legyenek kitakarítások, így folyamatosan növekszik (lásd: [takarítás a biztonsági mentési katalógus és a biztonsági mentési tár esetében](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA konfigurációs fájlok

Ahogy az a 1642148-es [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1642148)is szerepel, a SAP HANA konfigurációs fájlok nem részei a szabványos HANA biztonsági mentésnek. Nem elengedhetetlenek a rendszer helyreállításához. A HANA-konfiguráció a visszaállítás után manuálisan módosítható. Abban az esetben, ha a visszaállítási folyamat során egy egyéni konfigurációt szeretne kapni, külön kell biztonsági másolatot készíteni a HANA konfigurációs fájljairól.

Ha a standard HANA-alapú biztonsági mentések dedikált HANA biztonsági mentési fájlrendszerbe kerülnek, akkor a konfigurációs fájlokat ugyanarra a biztonsági másolati fájlrendszerre is másolhatja, majd a végső tárterületet, például a ritka blob Storage-ot is másolhatja.

### <a name="sap-hana-cockpit"></a>SAP HANA pilótafülke

SAP HANA pilótafülke lehetővé teszi a SAP HANA figyelését és felügyeletét böngészőn keresztül. Lehetővé teszi SAP HANA biztonsági mentések kezelését is, így a SAP HANA Studio és a ABAP DBACOCKPIT alternatívájaként is használható (további információért lásd: [SAP HANA pilótafülke](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) ).

![Ez az ábra a SAP HANA pilótafülke adatbázis-felügyeleti képernyőjét mutatja](media/sap-hana-backup-guide/image004.png)

Ez az ábra a SAP HANA pilótafülke adatbázis-felügyeleti képernyőjét és a bal oldali biztonsági mentés csempéjét mutatja. A biztonsági mentési csempének megfelelő felhasználói jogosultságokat kell látnia a bejelentkezési fiókhoz.

![A biztonsági mentések monitorozása SAP HANA pilótafülkében folyamatban van](media/sap-hana-backup-guide/image005.png)

A biztonsági mentések a SAP HANA pilótafülkében figyelhetők meg, amíg folyamatban vannak, és ha elkészült, a biztonsági mentés összes adata elérhetővé válik.

![Példa a Firefox használatára egy Azure SLES 12 virtuális gépen a GNOME Desktop használatával](media/sap-hana-backup-guide/image006.png)

Az előző képernyőképek egy Azure Windows rendszerű virtuális gépről készültek. Ez egy példa a Firefox használatára egy Azure SLES 12 virtuális gépen a GNOME Desktop használatával. Ez a beállítás azt mutatja be, hogy SAP HANA biztonsági mentési ütemterveket SAP HANA pilótafülkében. Ahogy az egyik is láthatja, a dátum/idő a biztonságimásolat-fájlok előtagjaként is javaslatot tesz. SAP HANA Studióban az alapértelmezett előtag &quot;a teljes biztonsági mentés során\_adat\_biztonsági mentés&quot;. Egyedi előtag használata ajánlott.

### <a name="sap-hana-backup-encryption"></a>SAP HANA biztonsági másolat titkosítása

SAP HANA az adattitkosítást és a naplót is biztosítja. Ha SAP HANA adatokat és naplókat nem titkosítják, akkor a biztonsági másolatok is titkosítva vannak. Az ügyfél a SAP HANA biztonsági mentések titkosításához a harmadik féltől származó megoldás valamilyen formáját használja. További információ a SAP HANA titkosításról: az [adatmennyiség és a naplózási kötetek titkosítása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) .

Microsoft Azure esetén az ügyfél a IaaS VM titkosítási funkciójával titkosíthatja a titkosítást. Használhat például egy olyan dedikált adatlemezt, amely a virtuális géphez van csatolva, amely SAP HANA biztonsági másolatok tárolására szolgál, majd másolatot készít ezekről a lemezekről.

Azure Backup szolgáltatás képes kezelni a titkosított virtuális gépeket/lemezeket (lásd: [a titkosított virtuális gépek biztonsági mentése és visszaállítása a Azure Backup](../../../backup/backup-azure-vms-encryption.md)használatával).

Egy másik lehetőség a SAP HANA virtuális gép és a lemez titkosítás nélküli fenntartása, valamint a SAP HANA biztonsági mentési fájlok tárolása egy olyan Storage-fiókban, amelynél engedélyezve van a titkosítás (lásd: [Azure-Storage Service encryption a REST-alapú adatokhoz](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Teszt beállítása

### <a name="test-virtual-machine-on-azure"></a>Virtuális gép tesztelése az Azure-ban

A tesztek elvégzéséhez a következő biztonsági mentési/visszaállítási tesztek során egy Azure GS5-beli virtuális gép SAP HANA telepítését használták. Az alapelvek ugyanazok, mint az M-sorozatú virtuális gépek esetében.

![Ez az ábra a HANA-teszt virtuális gép Azure Portal áttekintésének részét mutatja be](media/sap-hana-backup-guide/image007.png)

Ez az ábra a HANA test VM Azure Portal áttekintésének részét képezi.

### <a name="test-backup-size"></a>Biztonsági mentés méretének tesztelése

![Ez az ábra a HANA Studio biztonsági mentési konzolján történt, és a HANA-index kiszolgáló 229 GB-os biztonsági mentési fájlméretét jeleníti meg.](media/sap-hana-backup-guide/image008.png)

A rendszer kitöltötte az adattal, hogy az adatbiztonsági másolatok teljes mérete meghaladja a 200 GB-ot, így reális teljesítményadatokat érhet el. Az ábrát a HANA Studio biztonsági mentési konzolján vették át, és a HANA index-kiszolgáló 229 GB-os biztonsági mentési fájlméretét jeleníti meg. A tesztek esetében a SAP HANA Studióban található "COMPLETE_DATA_BACKUP" alapértelmezett biztonsági mentési előtag lett használva. A valós éles rendszerek esetében egy hasznos előtagot kell meghatározni. SAP HANA a pilótafülke a dátum-és időpontot javasolja.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Tesztelési eszköz a fájlok közvetlenül az Azure Storage-ba való másolásához

SAP HANA biztonsági másolat fájljainak közvetlenül az Azure Blob Storage-ba vagy az Azure-fájlmegosztásba való átviteléhez a blobxfer eszközt használták, mert mindkét célt támogatja, és a parancssori felülete miatt könnyen integrálható az Automation-parancsfájlokba. A blobxfer eszköz a [githubon](https://github.com/Azure/blobxfer)érhető el.

### <a name="test-backup-size-estimation"></a>A biztonsági másolatok méretének becslése

Fontos megbecsülni SAP HANA biztonsági mentésének méretét. Ez a becslés segít a teljesítmény javításában azáltal, hogy meghatározza a biztonságimásolat-fájl maximális méretét több biztonságimásolat-fájl esetében, a párhuzamosságok miatt a fájlok másolásakor. (Ezeket a részleteket a cikk későbbi részében ismertetjük.) Azt is el kell döntenie, hogy teljes biztonsági mentést vagy különbözeti biztonsági mentést (növekményes vagy különbözeti) kell-e végeznie.

Szerencsére van egy egyszerű SQL-utasítás, amely a biztonságimásolat-fájlok méretét becsüli meg: **válassza a \* az M\_backup\_a méret\_becslések** (lásd: [a fájlrendszerben szükséges lemezterület becslése az adatok biztonsági mentéséhez](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Az SQL-utasítás kimenete majdnem pontosan megfelel a lemezen lévő teljes adatbiztonsági mentés valós méretének.](media/sap-hana-backup-guide/image009.png)

A tesztelési rendszer esetében ennek az SQL-utasításnak a kimenete majdnem pontosan megegyezik a lemez teljes biztonsági mentésének valós méretével.

### <a name="test-hana-backup-file-size"></a>A HANA biztonságimásolat-fájl méretének tesztelése

![A HANA Studio biztonsági mentési konzol lehetővé teszi, hogy az egyik a HANA Backup-fájlok maximális fájlméretének korlátozására legyen korlátozva.](media/sap-hana-backup-guide/image010.png)

A HANA Studio biztonsági mentési konzol lehetővé teszi, hogy az egyik korlátozza a HANA biztonságimásolat-fájlok maximális fájlméretét. A mintavételi környezetben Ez a funkció lehetővé teszi, hogy a 1 230 GB-os biztonságimásolat-fájl helyett több kisebb biztonsági mentési fájlt kapjon. A kisebb fájlméret jelentős hatással van a teljesítményre (lásd a kapcsolódó cikket [SAP HANA Azure Backup a fájl szintjén](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Összegzés

A teszt eredményei alapján a következő táblázatok az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentésére szolgáló megoldások előnyeit és hátrányait mutatják be.

**SAP HANA biztonsági mentése a fájlrendszerbe és a biztonságimásolat-fájlok másolása a végső biztonsági mentési célhelyre**

|Megoldás                                           |Szakemberek számára                                 |Hátrányok                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|A HANA-alapú biztonsági másolatok megőrzése a VM-lemezeken                      |Nincsenek további felügyeleti erőfeszítések     |Megeszi a helyi virtuális gép lemezterületét           |
|Blobxfer eszköz a biztonságimásolat-fájlok blob Storage-ba való másolásához |Párhuzamosság a több fájl másolásához, választható a ritka blob Storage használata | További eszközök karbantartása és egyéni parancsfájlok | 
|BLOB másolása a PowerShell vagy a CLI használatával                    |Nincs szükség további eszközre az Azure PowerShell vagy a parancssori felület használatával |manuális folyamat, az ügyfélnek gondoskodnia kell a másolt Blobok parancsfájlkezeléséről és kezeléséről a visszaállításhoz|
|Másolás NFS-megosztásba                                  |Más virtuális gépen lévő biztonságimásolat-fájlok feldolgozása a HANA-kiszolgálón való hatás nélkül|Lassú másolási folyamat|
|Blobxfer másolása az Azure file Service-be                |Nem eszik helyet a helyi VM-lemezeken|A HANA Backup nem támogatja a közvetlen írási támogatást, a fájlmegosztás méretének korlátozása jelenleg 5 TB|
|Azure biztonságimásolat-készítő ügynök                                 | Előnyben részesített megoldás lenne         | Jelenleg nem érhető el Linux rendszeren    |



**Biztonsági mentési SAP HANA tárolási Pillanatképek alapján**

|Megoldás                                           |Szakemberek számára                                 |Hátrányok                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Lehetővé teszi a virtuális gépek biztonsági mentését blob-Pillanatképek alapján | Ha nem használja a fájlok szintjének visszaállítását, egy új virtuális gépet kell létrehoznia a visszaállítási folyamathoz, ami azt jelenti, hogy új SAP HANA licenckulcs szükséges.|
|Manuális blob-Pillanatképek                              | Rugalmasan meghatározott virtuálisgép-lemezek létrehozása és visszaállítása a virtuális gép egyedi AZONOSÍTÓjának módosítása nélkül|Minden manuális munka, amelyet az ügyfélnek kell elvégeznie|

## <a name="next-steps"></a>Következő lépések
* [SAP HANA Azure Backup a fájl szintjén](sap-hana-backup-file-level.md) leírja a fájl alapú biztonsági mentési lehetőséget.
* A [tárolási Pillanatképek alapján SAP HANA biztonsági mentés](sap-hana-backup-storage-snapshots.md) a Storage pillanatkép-alapú biztonsági mentési lehetőséget ismerteti.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
