---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: luywang
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: luywang
ms.custom: include file
ms.openlocfilehash: 5c7c9938b6a0b3d2e6050940154a8dc3f114341e
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53638787"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Biztonsági mentési és vész-helyreállítási Azure IaaS-lemezek

Ez a cikk a biztonsági mentési és vész-helyreállítási iaas-beli virtuális gépeken (VM) és az Azure-lemezek tervezését ismerteti. Ez a dokumentum ismerteti a felügyelt és a nem felügyelt lemezeket.

Először is ismerteti a beépített hibatűrési képességeket az Azure platformon, amely segít megvédeni a helyi hibák ellen. Ezután bemutatjuk a vészhelyreállítási forgatókönyveket, a beépített funkciók nem teljesen hatálya alá. Azt is megmutatjuk, néhány példa munkaterhelés-forgatókönyvek, ahol különböző biztonsági mentési és Vészhelyreállítási szempontok is alkalmazhatja. Hogy tekintse át a DR az IaaS-lemezek lehetséges megoldásokat. 

## <a name="introduction"></a>Bevezetés

Az Azure platform különböző módszereket a redundancia és a hibatűrést használ a helyi hardverhibák felhasználók védelme érdekében. Helyi hibák lehetnek a problémák az Azure Storage server-gép, amely tárolja az adatokat egy virtuális lemez része, vagy egy SSD vagy HDD hibák az adott kiszolgálón. Az ilyen elkülönített összetevő hardverhibák fordulhat elő, a normál működés során. 

Az Azure platform is ellenáll ezek a hibák tervezték. Súlyos vészhelyzetek esetére hibák vagy a inaccessibility számos tárolási kiszolgálók vagy akár egész adatközpontok eredményezhet. Bár a virtuális gépek és a lemezek megfelelően védettek a hibák, további lépések szükségesek, hogy megvédje a számítási feladatok régióra kiterjedő végzetes hibák, például egy vészhelyzetek, amelyek hatással lehetnek a virtuális gép és a lemezek.

A platform hibák lehetőségét, mellett egy ügyfél alkalmazáshoz vagy az adatok problémák merülhetnek fel. Például az alkalmazás új verziójának előfordulhat, hogy véletlenül lehet módosítani az adatokat, amelyek miatt a érvényteleníteni. Ebben az esetben előfordulhat, hogy szeretné visszaállítani az alkalmazás- és a egy korábbi verzióra, amely tartalmazza az utolsó ismert jó állapotot. Ehhez a rendszeres biztonsági mentések karbantartása.

A regionális vészhelyreállítás biztonsági másolatot kell IaaS VM-lemezeit, egy másik régióba. 

Biztonsági mentési és Vészhelyreállítási lehetőségek megnézzük, mielőtt foglaljuk honosított hibák elérhető néhány módszert.

### <a name="azure-iaas-resiliency"></a>Az Azure IaaS rugalmassága

*Rugalmasság* hivatkozik a tolerancia normál hardverösszetevők az előforduló hibákat. Rugalmasság helyreálljon a hibák után, és folytassa a működést. Akkor sem kapcsolatos hibák elkerülése, de válaszol a hibák oly módon, amelyek kiküszöbölik az állásidőt és az adatveszteséget. A rugalmasság célja, hogy az alkalmazás egy hibát követően teljesen működőképes állapotba térjen vissza. Az Azure virtual machines és a lemezek úgy tervezték, hogy legyen ellenállni a közös hardverhiba esetén. Nézzük meg, hogy a az Azure IaaS-platformon ez rugalmasságot biztosít.

A virtuális gépek nagyrészt két részből áll: egy számítási kiszolgáló és az állandó lemezt. A hibatűrés érdekében a virtuális gép is érinti.

Az Azure-beli számítási gazdakiszolgáló a virtuális gép-környezetet tároló egy hardverhiba, amely ritkán fordul elő, ha Azure célja automatikusan visszaállítani a virtuális gép egy másik kiszolgálón. Ha ebben a forgatókönyvben a számítógép-újraindítások és a virtuális gép betölt vissza egy kis idő múlva. Az Azure automatikusan észleli az ilyen hardveres hibák esetén, és annak biztosítására, hogy az ügyfél virtuális gép érhető el minél hamarabb helyreállítást hajt végre.

IaaS-lemezek kapcsolatban a tárolt adatok fontos adattárolásra platform. Az Azure-ügyfelek kell IaaS-on futó fontos üzleti alkalmazások, és az adatok megőrzése függenek. IaaS lemezek, a helyben tárolt adatok redundáns másolatait három Azure tervek védelmét. Ezek a másolatok biztosít nagyfokú tartósság helyi hibák ellen. Ha egy hardverösszetevő, amely tartalmazza a lemez meghibásodik, a virtuális gép nincs hatással, mert a lemez kérelmek támogatásához két további példányt. Jól működik, akkor is, ha két különböző hardverösszetevők, amelyek támogatják az egy lemezt egy időben (amely ritka) sikertelen. 

Győződjön meg arról, hogy három replika mindig megmaradjanak, az Azure Storage automatikusan indít a háttérben az adatok másolatát, ha a három közül másolja át az elérhetetlenné válik. Ezért nem lehet az Azure disks hibatűrése RAID használata szükséges. Egy egyszerű RAID 0-beállításokat kell megadni a lemezek szétosztott, ha szükséges, nagyobb kötetek létrehozásához elegendő.

Miatt ebben az architektúrában az Azure rendelkezik következetesen nyújtott nagyvállalati szintű tartósságot iaas lemezeken, az iparágvezető nulla %-os [érvényes évesített Hibaarány](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Honosított hardverhiba esetén számítási üzemeltetéséhez, vagy a tárolási platform képes néha az átmeneti elérhetetlensége, valamint a virtuális gép, amely érvényes lesz az eredménye a [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) a virtuális gépek rendelkezésre állása. Az Azure-szolgáltatás piacvezető SLA-t is egyetlen Virtuálisgép-példányok Azure prémium szintű SSD-lemezeket használó biztosít.

A leállás miatt az átmeneti elérhetetlensége, valamint egy lemez vagy a virtuális Gépet az alkalmazások és szolgáltatások védelme érdekében, az ügyfelek használhatják [rendelkezésre állási csoportok](../articles/virtual-machines/windows/manage-availability.md). Két vagy több virtuális gépet egy rendelkezésre állási csoportban az alkalmazás redundanciájának biztosítsa érdekében. Az Azure létrehozza a virtuális gépek és a lemezek majd külön tartalék tartományokban, a másik, a hálózat és a kiszolgáló-összetevők. 

Ezeket külön tartalék tartományokban miatt a helyi hardverhibák általában nem befolyásolják a készlet több virtuális gép egyszerre. Az alkalmazás magas rendelkezésre állást külön tartalék tartományokban kellene biztosít. Használjon rendelkezésre állási csoportokat, amikor szükség a magas rendelkezésre állású célszerű figyelembe vette. Ez a szakasz ismerteti a vész-helyreállítási aspektus.

### <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

Vész-helyreállítási ezáltal helyreállíthatja az adatokat a ritka, de jelentős, incidensek. Ezek az incidensek például nem átmeneti, széleskörű hibák, például a szolgáltatáskimaradás, amely egy teljes régióra hatással van. Vész-helyreállítási magában foglalja az adatok biztonsági mentése és archiválása, és tartalmazhat manuális beavatkozásra, például egy adatbázist egy biztonsági másolatból történő visszaállítását.

Az Azure platform beépített védelmi hibák ellen előfordulhat, hogy nem teljesen nyújt védelmet a virtuális gépek vagy-tárolólemezek Ha jelentős katasztrófa miatt a nagy méretű valamilyen okból kimaradás lép. Ezek nagy méretű valamilyen okból kimaradás lép többek között a katasztrofális események, például ha egy Hurrikán, földrengés, fire talált egy adatközpontban, vagy egy nagy méretű egység hardverhiba esetén. Emellett előfordulhat, hogy kapcsolatban tapasztalt hibák alkalmazás vagy az adatokkal kapcsolatos problémák miatt.

A leállások az IaaS munkaterhelések védelme érdekében kell tervezése a redundancia és helyreállítás engedélyezéséhez biztonsági mentések. Vész-helyreállítási készítsen biztonsági másolatot erről az elsődleges hely különböző földrajzi helyen. Ez a megközelítés biztosítja a biztonsági mentés nincs hatással, amelyek eredetileg befolyásolja a virtuális gép vagy lemez ugyanahhoz az eseményhez. További információkért lásd: [vészhelyreállítása az Azure-alkalmazások](/azure/architecture/resiliency/disaster-recovery-azure-applications).

A Vészhelyreállítás szempontjai a következők lehetnek a következő szempontokat:

- Magas rendelkezésre állás: Azon képessége, az alkalmazás állapota kifogástalan, jelentős állásidő nélkül folytatódik. Által *megfelelő állapotba*, ez az állapot azt jelenti, hogy az alkalmazás válaszol, és a felhasználók kapcsolódnak az alkalmazáshoz és kezelheti azt. Bizonyos alapvető fontosságú alkalmazások és adatbázisok lehet szükség, mindig elérhető legyen, akkor is, ha a platform fordulnak elő hibák. Ezeket a feladatokat szükség lehet az alkalmazás, valamint az adatok redundanciájának megtervezése.

- Adatok tartóssága: Bizonyos esetekben a legfontosabb szempont annak ellenőrzése, hogy az adatok megmaradjanak-e, ha katasztrófa történik. Ezért szükség lehet egy másik helyet az adatok biztonsági másolata. Ilyen számítási feladatok esetén nincs szükség lehet az alkalmazás, de csak egy rendszeres biztonsági mentés a lemezek teljes redundancia.

## <a name="backup-and-dr-scenarios"></a>Biztonsági mentési és Vészhelyreállítási forgatókönyvek

Tekintsünk meg néhány jellemző példa az alkalmazás munkaterhelés-forgatókönyvek és a vész-helyreállítási tervezési szempontokat.

### <a name="scenario-1-major-database-solutions"></a>1. forgatókönyv: Fő adatbázis-megoldások

Fontolja meg egy éles adatbázis-kiszolgáló, mint például az SQL Server vagy az Oracle által támogatott magas rendelkezésre állású. Kritikus fontosságú éles alkalmazások és a felhasználók ehhez az adatbázishoz függenek. A Vészhelyreállítási terv esetében a rendszer támogatja az alábbiakat lehet szükség:

- Az adatokat a védett és helyreállítható kell lennie.
- A kiszolgáló használható kell lennie.

A vész-helyreállítási terv szükség lehet az adatbázis biztonsági mentéséhez egy másik régióban lévő replika karbantartása. Kiszolgáló rendelkezésre állása és adat-helyreállítás követelményeitől függően a megoldás lehet, hogy egy aktív – aktív vagy aktív – passzív replika helyszínére és közötti az adatok rendszeres offline biztonsági mentés. Relációs adatbázisok, például az SQL Server és az Oracle, a replikáció különböző lehetőségeket biztosítanak. Az SQL Server esetében használjon [SQL Server AlwaysOn rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) magas rendelkezésre állás érdekében.

NoSQL-adatbázisok, például a mongodb-hez, emellett támogatja a [replikák](https://docs.mongodb.com/manual/replication/) a redundancia biztosítása érdekében. A magas rendelkezésre állást a replikák szolgálnak.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>2. forgatókönyv: Redundáns virtuális gépek

Érdemes lehet a munkaterhelés kezeli a virtuális gépek, amelyek a redundancia és a terheléselosztás. Egy példa, egy régióban üzembe helyezve, Cassandra-fürtjére. Az ilyen típusú architektúra már biztosít egy magas szintű redundancia a régión belül. Azonban a számítási feladatok védelmét a regionális szintű meghibásodása, érdemes szét a fürt két régióban, vagy egy másik régióba rendszeres biztonsági mentések készítése.

### <a name="scenario-3-iaas-application-workload"></a>3. forgatókönyv: IaaS-alkalmazás számítási feladatait

Tekintsük át az IaaS-alkalmazás számítási feladatait. Ez az alkalmazás Előfordulhat például, egy Azure-beli virtuális gépen egy jellemző éles üzemi számítási feladatot. A webalkalmazás-kiszolgáló vagy a fájlkiszolgáló a tartalom és más erőforrások, a hely lehet. A személyre szabott üzleti alkalmazások, a virtuális gépen, amely tárolja az adatokat, erőforrások és alkalmazásállapot a Virtuálisgép-lemezek is lehet. Ebben az esetben fontos, hogy rendszeresen a biztonsági mentések igénybe vehet. Biztonsági mentés gyakorisága a virtuális gép számítási jellege kell alapulnia. Például ha az alkalmazás naponta fut, és módosítja az adatokat, majd a biztonsági mentést kell fordítani óránként.

Egy másik példa a jelentéskészítő kiszolgáló, amely más forrásokból származó adatokat kér le, és összesített jelentéseket hoz létre. A virtuális gép vagy lemez elvesztését elvesztését, a jelentések vezethet. Azonban esetleg futtassa újra a jelentéskészítési folyamat és a kimenet újbóli létrehozása. Ebben az esetben nem igazán rendelkezik adatvesztést, akkor is, ha a jelentéskészítő kiszolgáló elérte a vészhelyzet. Ennek eredményeképpen előfordulhat, hogy rendelkezik a magasabb szintű részét a jelentéskészítő kiszolgálón az adatok elvesztése. Ebben az esetben a kevésbé gyakori biztonsági mentései költségek csökkentése érdekében lehetőség.

### <a name="scenario-4-iaas-application-data-issues"></a>4. forgatókönyv: IaaS-alkalmazás adatokkal kapcsolatos problémák

IaaS alkalmazásproblémák adatokat egy másik lehetőség. Érdemes lehet olyan alkalmazás, amely kiszámítja, tárolja és szolgálja ki a kritikus fontosságú kereskedelmi forgalomban beszerezhető adatok, például a díjszabásról. Az alkalmazás új verziójának kellett egy szoftverfrissítési programhiba, amelynek helytelenül számított díjszabását, és a meglévő, a platform által kiszolgált kereskedelmi adatok sérültek. Itt a legjobb megoldás érdekében, hogy az alkalmazás- és a korábbi verziójának visszaállítása. Engedélyezéséhez hajtsa végre a rendszer rendszeres biztonsági mentést.

## <a name="disaster-recovery-solution-azure-backup"></a>Vész-helyreállítási megoldást: Azure Backup 

[Az Azure Backup](https://azure.microsoft.com/services/backup/) használatos biztonsági mentések és a Vészhelyreállítás, és együttműködik az [felügyelt lemezek](../articles/virtual-machines/windows/managed-disks-overview.md) , valamint [nem felügyelt lemezek](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). Biztonsági mentési feladat idő-alapú biztonsági mentések, könnyű VM-helyreállítás és a biztonsági másolatok megőrzési házirendeket is létrehozhat. 

Ha [prémium szintű SSD-lemezeket](../articles/virtual-machines/windows/premium-storage.md), [felügyelt lemezek](../articles/virtual-machines/windows/managed-disks-overview.md), vagy más lemeztípusok a a [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md) beállítást, különösen fontos, hogy a DR rendszeres biztonsági mentések. Az Azure Backup tárolja az adatokat hosszú távú megőrzésének a recovery services-tárolót. Válassza ki a [georedundáns tárolás](../articles/storage/common/storage-redundancy-grs.md) lehetőséget a biztonsági mentési helyreállítási tár. Ez a beállítás biztosítja, hogy biztonsági mentések replikálja a rendszer a gondoskodik a regionális csapásokkal szemben a más Azure-régióban.

A [nem felügyelt lemezek](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), a helyileg redundáns tárolási típust használja az IaaS-lemezeknek, de győződjön meg arról, hogy az Azure Backup engedélyezve van-e a recovery services-tároló, a georedundáns tárolási lehetőséggel.

> [!NOTE]
> Használatakor a [georedundáns tárolás](../articles/storage/common/storage-redundancy-grs.md) vagy [írásvédett georedundáns tárolás](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) beállítás a nem felügyelt lemezek, hogy továbbra is kell alkalmazáskonzisztens pillanatképek a biztonsági mentés és Vészhelyreállítás. Mindkét [Azure Backup](https://azure.microsoft.com/services/backup/) vagy [alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots).

 A következő táblázatban található egy összefoglaló az DR elérhető megoldásokat.

| Forgatókönyv | Az automatikus replikáció | DR-megoldásként |
| --- | --- | --- |
| Prémium szintű SSD-lemez | Helyi ([helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Felügyelt lemezek | Helyi ([helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Helyileg redundáns tárolás nem felügyelt lemezek | Helyi ([helyileg redundáns tárolás](../articles/storage/common/storage-redundancy-lrs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Georedundáns tárolás nem felügyelt lemezek | Régiók közötti ([georedundáns tárolás](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots) |
| Nem felügyelt olvasási hozzáférésű georedundáns tárolás lemezek | Régiók közötti ([írásvédett georedundáns tárolás](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots) |

Magas rendelkezésre állású legjobban teljesül egy rendelkezésre állási csoportot az Azure Backup és a felügyelt lemezek használatával. Ha nem felügyelt lemezeket használ, továbbra is használhatja az Azure Backup a Vészhelyreállításhoz. Ha nem tudja használni az Azure Backup, majd véve [alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots)egy későbbi szakaszban leírtak szerint alternatív megoldást kínál a biztonsági mentés és Vészhelyreállítás.

A magas rendelkezésre állás, a backup és az alkalmazás vagy infrastruktúra-szintű Vészhelyreállítás egyik lehetőséget a következőképpen jelölhető:

| Szint |   Magas rendelkezésre állás   | Biztonsági mentési vagy a DR |
| --- | --- | --- |
| Alkalmazás | SQL Server AlwaysOn | Azure Backup |
| Infrastruktúra    | Rendelkezésre állási csoport  | Alkalmazáskonzisztens pillanatképek a georedundáns tárolás |

### <a name="using-azure-backup"></a>Az Azure Backup használatával 

[Az Azure Backup](../articles/backup/backup-azure-vms-introduction.md) készíthet biztonsági másolatot a Windows rendszerű virtuális gépek vagy a Linux az Azure Recovery services-tároló. Biztonsági mentése és visszaállítása az üzleti szempontból kritikus fontosságú adatok bonyolítja van arra, hogy üzleti szempontból kritikus fontosságú adatokat kell készíteni, amely az adatokat az alkalmazások működése közben. 

A probléma megoldása érdekében az Azure Backup alkalmazáskonzisztens biztonsági mentést biztosít Microsoft-munkaterhelések. A kötet árnyékmásolata szolgáltatást használ, győződjön meg arról, hogy adatokat írt megfelelő tárolási. Linux rendszerű virtuális gépekhez csak a fájlkonzisztens biztonsági mentés is lehetségesek, mert Linux nem rendelkezik egyenértékű, a kötet árnyékmásolata szolgáltatás funkcióit.

![Az Azure Backup-folyamat][1]

Azure Backup kezdeményezi a biztonsági mentési feladat a megadott időpontban, amikor elindítja a biztonsági mentési bővítmény időponthoz pillanatkép készítése a virtuális gépen telepítve van. Egy pillanatkép készül a kötet árnyékmásolata szolgáltatás együtt egy egységes pillanatképet, a lemezek beolvasása a anélkül, hogy állítsa le a virtuális gépet. A biztonsági mentési bővítményt a virtuális gép kiüríti az összes írási művelet az összes lemez konzisztens pillanatképének elkészítése előtt. Miután elvégezte a pillanatkép, az adatok átkerülnek az Azure Backup szolgáltatás a biztonsági mentési tárba. A biztonsági mentési folyamat hatékonyabbá teheti, hogy a szolgáltatás azonosítja, és csak azokat az adatblokkokat, amelyek megváltoztak a legutóbbi biztonsági mentés továbbítja.

Szeretne visszaállítani, az elérhető biztonsági másolatok az Azure Backup segítségével megtekintheti és majd indítsa el a visszaállítást. Hozhat létre, és állítsa vissza az Azure biztonsági mentések keresztül a [az Azure portal](https://portal.azure.com/), [PowerShell-lel](../articles/backup/backup-azure-vms-automation.md), vagy a [Azure CLI-vel](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>A biztonsági mentés engedélyezésének lépései

Kövesse az alábbi lépéseket a virtuális gépek biztonsági másolatainak engedélyezése használatával a [az Azure portal](https://portal.azure.com/). Van néhány eltérés pontos a forgatókönyvtől függően. Tekintse meg a [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) a teljes dokumentációt. Az Azure Backup is [támogatja a felügyelt lemezekkel rendelkező virtuális gépeket](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Hozzon létre egy recovery services-tárolót egy virtuális géphez:

    a. Az a [az Azure portal](https://portal.azure.com/), Tallózás **összes erőforrás** , és keresse meg **Recovery Services-tárolók**.

    b. A a **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** kövesse a lépéseket egy új tárolót ugyanabban a régióban, mint a virtuális gép létrehozásához. Például ha a virtuális gép az USA nyugati régiójában, válasszon USA nyugati RÉGIÓJA a tárolóhoz.

1.  Ellenőrizze a tárreplikáció az újonnan létrehozott tároló. Hozzáférést a tárolóhoz a **Recovery Services-tárolók** , majd **beállítások** > **biztonsági mentés konfigurációja**. Győződjön meg, hogy a **georedundáns tárolás** beállítás alapértelmezés szerint. Ez a beállítás biztosítja, hogy a rendszer automatikusan replikálja a tároló egy másodlagos adatközpontba. A tárolót az USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA például automatikusan replikálja.

1.  A biztonsági mentési szabályzat konfigurálása, és válassza ki a virtuális gép ugyanazon a felhasználói felületről.

1.  Győződjön meg arról, hogy a Backup szolgáltatás ügynöke telepítve van a virtuális gépen. Ha a virtuális gép létrehozása az Azure-katalógus-lemezkép használatával, majd a Backup-ügynök már telepítve van. Egyéb (azaz ha egyéni rendszerkép használatával), kövesse az utasításokat a [Virtuálisgép-ügynök telepítése a virtuális gépen](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Győződjön meg arról, hogy a virtuális gép lehetővé teszi, hogy a biztonsági mentési szolgáltatás függvény hálózati kapcsolattal. Kövesse az utasításokat [hálózati kapcsolat](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

1.  Miután végrehajtotta az előző lépéseket, a biztonsági mentés fut, a biztonsági mentési szabályzatban megadott rendszeres időközönként. Ha szükséges, az első biztonsági mentés manuálisan a tároló irányítópultjáról az Azure Portalon is indíthat.

Parancsfájlok használatával az Azure biztonsági mentés automatizálása, tekintse meg [virtuális gép biztonsági mentése PowerShell-parancsmagok](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>A helyreállítás lépései

Ha kell javítania vagy építse újra a virtuális gép, visszaállíthatja a virtuális gép bármely, a tárolóban lévő biztonsági mentési helyreállítási pontok. Van több helyreállításhoz a másik lehetőség közül választhat:

-   Létrehozhat egy új virtuális Gépet, a biztonsági másolatban szereplő virtuális gép időponthoz reprezentációját.

-   Állítsa vissza a lemezeket, és ezután használhatja a sablont a virtuális gép testreszabása és újbóli létrehozása a visszaállított virtuális Gépet. 

További információkért tekintse meg az utasításokat követve [virtuális gépek visszaállítása az Azure portal használatával](../articles/backup/backup-azure-arm-restore-vms.md). Ez a dokumentum ismerteti a biztonsági másolatban szereplő virtuális gépek visszaállítása egy párosított adatközpontban, a georedundáns biztonsági mentési tár használatával, ha az elsődleges adatközpont katasztrófa lépései is. Ebben az esetben az Azure Backup a számítási szolgáltatást használja a másodlagos régióból a visszaállított virtuális gép létrehozásához.

Is használhatja a PowerShell- [visszaállítása egy virtuális gép](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) vagy [az új virtuális gép létrehozása a lemezek visszaállítása](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatív megoldás: Alkalmazáskonzisztens pillanatképek

Ha nem tudja használni az Azure Backup, a saját biztonsági mentési mechanizmus pillanatképek használatával is alkalmazható. Bonyolult az alkalmazáskonzisztens pillanatképek a virtuális gép által használt összes lemez létrehozása, és ezután másik régióban való replikálásához a pillanatképeket. Ebből kifolyólag Azure figyelembe veszi, mint egy jobb megoldás, mint az egyéni megoldás létrehozása a Backup szolgáltatás használatával. 

Ha írásvédett georedundáns tárolás vagy georedundáns tárolást használ a lemezek, a pillanatképek automatikusan replikált egy másodlagos adatközpontba. Helyileg redundáns tárolás lemezek használatakor meg kell replikálja az adatokat. További információkért lásd: [biztonsági mentése növekményes pillanatképekkel virtuális gép Azure-unmanaged disks](../articles/virtual-machines/windows/incremental-snapshots.md).

Pillanatkép egy objektum egy adott időpontban időben reprezentációját. Pillanatkép a számlázás az adatok növekményes méretének, visszatartással tekintetében. További információkért lásd: [blob-pillanatkép létrehozása](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Hozzon létre pillanatképek, a virtuális gép futása közben

Bár is igénybe vehet egy pillanatkép bármikor, ha a virtuális gép fut, van még a lemezek részére küldött adatokat. A pillanatképek részleges műveletek útban is tartalmazhatnak. Is ha nincsenek érintett több lemezt, a másik lemezek pillanatképei esetlegesen bekövetkeztek különböző időpontokban. Ezekben az esetekben kell összehangolatlan pillanatképeiével okozhat. Koordinációs hiánya esetén különösen problémás, amelynek fájl sérült, ha módosítások történtek folyamatban van a biztonsági mentés során csíkozott kötetek.

Ezen helyzet elkerülése érdekében a biztonsági mentési folyamat meg kell valósítani az alábbi lépéseket:

1.  Minden lemez rögzítése.

1.  Kiüríti az összes függőben lévő írási művelet.

1.  [Blob-pillanatkép létrehozása](../articles/storage/blobs/storage-blob-snapshots.md) minden lemez esetében.

Egyes Windows-alkalmazások, például az SQL Server egy olyan mechanizmust koordinált biztonsági másolat alkalmazáskonzisztens biztonsági mentését kötet árnyékmásolata szolgáltatáson keresztül. Linux rendszeren használható egy eszköz, például *fsfreeze* összehangolására a lemezeket. Ez az eszköz fájlkonzisztens biztonsági másolatokat, de nem alkalmazáskonzisztens pillanatképeket tartalmaz. Ez a folyamat összetett, ezért akkor érdemes megfontolni [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) vagy egy külső biztonsági mentési megoldás, amely már megvalósítja ezt az eljárást.

Az előző folyamat során egy gyűjtemény összes egy adott időpontban a nézet a virtuális gép képviselő Virtuálisgép-lemezek koordinált pillanatképek eredményez. Ez a virtuális gép biztonsági másolat visszaállítási pont. Az eljárás megismétlésével rendszeres időközönként a rendszeres biztonsági mentések létrehozását. Lásd: [a biztonsági másolatok másolása egy másik régióba](#copy-the-snapshots-to-another-region) lépéseit a pillanatképek másolása egy másik régióba a Vészhelyreállításhoz.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Pillanatképek létrehozása közben a virtuális gép offline állapotban.

Alkalmazáskonzisztens biztonsági másolatok létrehozásához egy másik lehetőség, hogy állítsa le a virtuális gép és a blob-pillanatfelvételek, az egyes lemezekről. A blob-pillanatképeket egyszerűbb, mint a futó virtuális gépek pillanatképeivel koordinációt, de néhány perc állásidő szükséges.

1. A virtuális gép leállítására.

1. Hozzon létre egy pillanatképet minden egyes virtuális merevlemez blob, amely csak a pár másodpercet vesz igénybe.

    Pillanatkép létrehozása, használhatja [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), a [Azure Storage REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI-vel](/cli/azure/), vagy az Azure Storage ügyfélkódtáraival valamelyik például [a A Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/hh488361.aspx).

1. Indítsa el a virtuális gép, amely az állásidő vége. A teljes folyamat általában néhány percen belül befejeződik.

Ez a folyamat minden lemez egy biztonsági mentés visszaállítási pontot biztosít a virtuális gép alkalmazáskonzisztens pillanatképeket gyűjteménye eredményez.

### <a name="copy-the-snapshots-to-another-region"></a>A pillanatképek másolása egy másik régióba

Az önálló pillanatképek létrehozása nem elegendő a Vészhelyreállításhoz. A pillanatkép biztonsági mentései egy másik régióba kell replikálni.

Ha a georedundáns tárolás vagy írásvédett georedundáns tárolás esetében használja a lemezeket, majd a pillanatképek automatikusan a másodlagos régióba replikálja. Néhány percet, mielőtt a replikációs késés lehet. Ha az elsődleges adatközpont leáll, a pillanatképek Befejezés replikálása előtt, a másodlagos adatközpontba a nem érhetők el a pillanatképeknek. Ez a valószínűségét kis.

> [!NOTE] 
> Csak kellene a lemezeket, a georedundáns tárolás vagy írásvédett georedundáns tárfiók nem nyújt védelmet a virtuális gép csapásokkal szemben. Kell koordinált pillanatképeket létrehozni, vagy az Azure Backup használata. Ez egy konzisztens állapotú virtuális gép helyreállítása a szükséges.

Ha helyileg redundáns tárolást használ, a pillanatkép létrehozása után azonnal egy másik tárfiókot kell másolnia a pillanatképeket. A Másolás célja lehet egy helyileg redundáns tárolás fiókot egy másik régióban lévő Másolás folyamatban egy távoli régióban eredményez. A pillanatképek írásvédett georedundáns tárolás fiókra ugyanabban a régióban is másolhatja. Ebben az esetben a pillanatkép ráérősen replikálódnak a távoli másodlagos régióba. A biztonsági mentés védett a katasztrófák az elsődleges hely a másolása után, és a replikálás befejeződik.

Másolja a növekményes pillanatképek vészhelyreállítás hatékony, tekintse át a következő témakör utasításait [biztonsági mentése az Azure nem felügyelt Virtuálisgép-lemezek növekményes pillanatképekkel](../articles/virtual-machines/windows/incremental-snapshots.md).

![Készítsen biztonsági másolatot az Azure nem felügyelt Virtuálisgép-lemezek növekményes pillanatképekkel][2]

### <a name="recovery-from-snapshots"></a>A pillanatképek helyreállítása

Pillanatkép lekéréséhez, hogy egy új blob másolja. A pillanatkép másolása az elsődleges fiókból, a pillanatkép alap blobba a pillanatkép másolhatja át. Ez a folyamat visszaállítja a pillanatképek a lemez. Ez a folyamat a pillanatkép előléptetése néven ismert. A pillanatkép biztonsági másolatából másolása egy másodlagos írásvédett georedundáns tárolás számla esetében-fiók elsődleges fiókra kell másolnia. Másolhatja a pillanatkép [PowerShell-lel](../articles/storage/common/storage-powershell-guide-full.md) vagy az AzCopy segédprogram használatával. További információkért lásd: [adatátvitel az AzCopy parancssori segédprogram](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Több lemezzel rendelkező virtuális gépek esetén kell másolnia a azonos koordinált visszaállítási pont részét képező összes pillanatképet. Másolja a pillanatképek írható Virtuálismerevlemez-blobokat, miután a blobok használatával hozza létre újra a virtuális Géphez a virtuális gép a sablon használatával.

## <a name="other-options"></a>Egyéb beállítások

### <a name="sql-server"></a>SQL Server

Egy virtuális Gépen futó SQL Server biztonsági mentése az Azure Blob storage és a egy fájlt az SQL Server-adatbázis a saját beépített funkciókkal rendelkezik. Ha a tárfiók georedundáns tárolás vagy írásvédett georedundáns tárolás, amelyet elérhet ezeket a biztonsági mentéseket a tárfiók másodlagos adatközpontban katasztrófa esetén ugyanazok a korlátozások vonatkoznak előbb tárgyalt módon. További információkért lásd: [biztonsági mentése és visszaállítása az SQL Server Azure-beli virtuális gépeken](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Emellett biztonsági mentéséhez és visszaállításához [SQL Server AlwaysOn rendelkezésre állási csoportok](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) képes fenntartani az adatbázis másodlagos replikákon. Ez jelentősen csökkenti a vész-helyreállítási idő.

## <a name="other-considerations"></a>Egyéb szempontok

Ebben a cikkben tárgyalt rendelkezik biztonsági mentése vagy pillanatképeket a vész-helyreállítási, és hogyan használhatja ezeket a biztonsági mentéseket a virtuális gépek és azok a lemezek vagy pillanatképek az adatok helyreállításához. Az Azure Resource Manager-modell sokan sablonok segítségével a virtuális gépek és egyéb infrastruktúra létrehozása az Azure-ban. Egy sablon használatával hozzon létre egy virtuális Gépet, amelynek minden alkalommal ugyanazt a konfigurációt. Ha egyéni lemezképeket használ a virtuális gépek létrehozásához, meg kell győződjön meg arról is, hogy a képek védett írásvédett georedundáns tárfiók tárolja azokat.

Ennek következtében a biztonsági mentési folyamat két dolgokat kombinációját is lehet:

- Adatok biztonsági mentéséhez (lemezeket).
- Készítsen biztonsági másolatot a konfigurációt (sablonok és egyéni rendszerképek).

A biztonsági mentési lehetőséget is választja, attól függően lehetséges, hogy a biztonsági mentés az adatok és a konfiguráció kezeléséhez, vagy a biztonsági mentési szolgáltatás előfordulhat, hogy kezelni tudja, hogy az Ön számára.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>A függelék: Adatredundancia hatásának ismertetése

Az Azure storage-fiókok, adatredundancia vész-helyreállítási kapcsolatban megfontolandó három típusa van: helyileg redundáns, georedundáns vagy georedundáns írásvédett. 

Helyileg redundáns tárolás ugyanabban az adatközpontban lévő adatok három példányban őrzi meg. Amikor a virtuális gép az adatokat ír, az összes három példányt frissíti, mielőtt sikeres küld vissza a hívónak, hogy tudja, hogy megegyeznek. A lemez védett helyi hibák esetén, mert nem valószínű, hogy az összes három példányt egy időben érintett. Helyileg redundáns tárolás esetén nincs geo-redundancia, így a lemez nem védett a katasztrofális hibák, amelyek hatással lehetnek a teljes adatközpont vagy tárolási egységet.

A georedundáns tárolás és az írásvédett georedundáns tárolás az adatok három másolatát az elsődleges régióban, Ön által kiválasztott jelennek meg. Az adatok további három példányban egy megfelelő másodlagos régióban, az Azure-ban beállított megmaradnak. Például, ha az adatok tárolása USA nyugati RÉGIÓJA, az adatok replikálódnak az USA keleti RÉGIÓJA. Másolat megőrzése aszinkron módon történik, és némi késés az elsődleges és másodlagos helyek frissítései között van. (Az a késleltetés) lemez alapon összhangban-e a másodlagos hely a lemezen a replikákat, de replikák több aktív lemezekkel nem feltétlenül szinkronban egymással. Ahhoz, hogy egységes replikák több lemezre kiterjedő, az alkalmazáskonzisztens pillanatképek szükségesek.

A georedundáns tárolás és az írásvédett georedundáns tárolás közötti fő különbség a, hogy az írásvédett georedundáns tárolás, a másodlagos másolat olvasható bármikor. Ha a probléma, hogy ez a beállítás az adatokat az elsődleges régióban nem érhető el, az Azure csapata mindent hozzáférés visszaállítása. Amíg az elsődleges nem működik, ha írásvédett georedundáns tárolás engedélyezve van, elérheti az adatokat a másodlagos adatközponthoz. Ezért ha azt tervezi, olvassa el a replikából, amíg az elsődleges nem érhető el, majd az írásvédett georedundáns tárolás kell tekinteni.

Azt tapasztaltuk, jelentős szolgáltatáskimaradás lehet, ha az Azure-csapat előfordulhat, hogy földrajzi feladatátvétel és az elsődleges DNS-bejegyzéseket, mutasson a másodlagos tárterületre módosítása. Ezen a ponton Ha georedundáns tárolás vagy írásvédett georedundáns tárolás engedélyezve van, elérheti az adatokat, amelyet használni kell a másodlagos régióban. Más szóval ha a tárfiók georedundáns tárolás és a probléma, hozzáférhet a másodlagos tároló csak akkor, ha nincs földrajzi feladatátvételt.

További információkért lásd: [Mi a teendő az Azure Storage leállása esetén](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>A Microsoft azt szabályozza, hogy jelentkezik-e a feladatátvételt. Feladatátvétel nem szabályozott tárfiókonként, így nem, amelyekről az egyes ügyfelek. Adott storage-fiókok vagy virtuálisgép-lemezek vészhelyreállítása implementálásához az ebben a cikkben korábban leírt technikákkal kell használnia.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
