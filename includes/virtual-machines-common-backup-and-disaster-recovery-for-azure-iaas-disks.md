---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa7ddb75017a532b436b9a5cfc71d1a7c2832cb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77179133"
---
Ez a cikk bemutatja, hogyan tervezhet biztonsági mentési és vészhelyreállítást (DR) az IaaS virtuális gépek (VM-ek) és az Azure-beli lemezek. Ez a dokumentum a felügyelt és a nem felügyelt lemezeket egyaránt ismerteti.

Először is, az Azure platform beépített hibatűrési képességeit fedezzük le, amely segít a helyi hibák elleni védelemben. Ezután megvitatjuk a katasztrófa-forgatókönyvek nem teljesen fedezi a beépített képességek. Számos példát is bemutatunk a számítási feladatok forgatókönyvei, ahol különböző biztonsági mentési és vész-fejlesztési szempontok vonatkozhatnak. Ezután áttekintjük az IaaS-lemezek DR-jének lehetséges megoldásait.

## <a name="introduction"></a>Bevezetés

Az Azure platform különböző módszereket használ a redundancia és a hibatűrés, hogy megvédje az ügyfeleket a honosított hardverhibák. A helyi hibák közé tartozhatnak az Azure Storage-kiszolgálógéppel kapcsolatos problémák, amelyek egy virtuális lemez adatainak egy részét, vagy egy SSD vagy HDD hibáit tárolják az adott kiszolgálón. Ilyen elszigetelt hardverösszetevő-hibák fordulhatnak elő a normál működés során.

Az Azure platform úgy van kialakítva, hogy rugalmas legyen ezekkel a hibákkal szemben. Súlyos katasztrófák vezethet hibák vagy a elérhetetlenség sok tárolókiszolgálók, vagy akár egy egész adatközpont. Bár a virtuális gépek és a lemezek általában védett honosított hibák, további lépésekre van szükség, hogy megvédje a számítási feladatok a régióra kiterjedő katasztrofális hibák, például egy súlyos katasztrófa, amely hatással lehet a virtuális gép és a lemezek.

A platformhibák lehetősége mellett problémák léphetnek fel az ügyfélalkalmazással vagy adatokkal kapcsolatban is. Előfordulhat például, hogy az alkalmazás egy új verziója véletlenül módosítja az adatokat, ami miatt megszakad. Ebben az esetben előfordulhat, hogy vissza szeretné állítani az alkalmazást és az adatokat egy korábbi verzióra, amely az utolsó ismert jó állapotot tartalmazza. Ehhez rendszeres biztonsági mentésre van szükség.

A regionális vész-helyreállítási, az IaaS virtuálisgép-lemezek egy másik régióba kell biztonsági másolatot készítenie.

Mielőtt megnéznénk a biztonsági mentést és a VÉSZ-beállításokat, foglaljuk össze a honosított hibák kezelésére rendelkezésre álló módszereket.

### <a name="azure-iaas-resiliency"></a>Az Azure IaaS rugalmassága

*A rugalmasság* a hardverösszetevőkben előforduló normál hibák tűréshatárát nevezi meg. A rugalmasság az a képesség, hogy felépüljön a hibák, és továbbra is működik. Nem a hibák elkerülését jelenti, hanem a hibákra adott olyan válaszokat, amelyek kiküszöbölik az állásidőt és az adatveszteséget. A rugalmasság célja, hogy az alkalmazás egy hibát követően teljesen működőképes állapotba térjen vissza. Az Azure virtuális gépek és lemezek úgy vannak kialakítva, hogy rugalmasak legyenek a gyakori hardverhibákkal szemben. Nézzük meg, hogy az Azure IaaS platform biztosítja ezt a rugalmasságot.

A virtuális gép főként két részből áll: egy számítási kiszolgálóból és az állandó lemezekből. Mindkettő befolyásolja a virtuális gép hibatűrését.

Ha az Azure számítási gazdakiszolgáló, amely a virtuális gép a virtuális gép egy hardverhiba, ami ritka, az Azure célja, hogy automatikusan visszaállítsa a virtuális gép egy másik kiszolgálón. Ebben a forgatókönyvben a számítógép újraindul, és a virtuális gép egy idő után jön vissza. Az Azure automatikusan észleli az ilyen hardverhibákat, és végrehajtja a helyreállításokat annak érdekében, hogy az ügyfél virtuális gépe a lehető leghamarabb elérhető legyen.

Az IaaS-lemezek tekintetében az adatok tartóssága kritikus fontosságú az állandó tárolási platform esetében. Az Azure-ügyfelek fontos üzleti alkalmazások futnak az IaaS-en, és az adatok megőrzésétől függenek. Az Azure védelmet tervez ezekhez az IaaS-lemezekhez, a helyileg tárolt adatok három redundáns másolatával. Ezek a másolatok nagy tartósságot biztosítanak a helyi hibák ellen. Ha a lemezt tartalmazó hardverösszetevők egyike meghibásodik, a virtuális gép nem változik, mert két további példány van a lemezkérelmek támogatásához. Ez szerkezet finom, egyenletes ha kettő különböző vasárú összetevő amit támogat egy korong megbukik ugyanakkor (melyik van ritka). 

Annak érdekében, hogy mindig három replikát tartson fenn, az Azure Storage automatikusan létrehoz egy új másolatot az adatok a háttérben, ha a három példány egyike elérhetetlenné válik. Ezért nem szükséges raid használata azure-lemezekkel a hibatűrés. Egy egyszerű RAID 0 konfigurációnak elegendőnek kell lennie a lemezek csíkozásához, ha szükséges, nagyobb kötetek létrehozásához.

Ennek az architektúrának az Azure következetesen nagyvállalati szintű tartósságot biztosít az IaaS-lemezekhez, iparágvezető nulla százalékos [évesített hibaaránnyal.](https://en.wikipedia.org/wiki/Annualized_failure_rate)

Honosított hardverhibák a számítási gazdagépen vagy a storage platformon néha azt eredményezheti, hogy a virtuális gép ideiglenes elérhetetlensége, amely az [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) virtuális gép rendelkezésre állása. Az Azure egy iparágvezető SLA-t is biztosít az Azure prémium szintű SSD-ket használó egyvirtuális automatákhoz.

Az alkalmazás-munkaterhelések védelme a lemez vagy virtuális gép ideiglenes elérhetetlensége miatt az állásidőtől, az ügyfelek [használhatják a rendelkezésre állási csoportokat.](../articles/virtual-machines/windows/manage-availability.md) Egy rendelkezésre állási csoportkét vagy több virtuális gép redundanciát biztosít az alkalmazás számára. Az Azure ezután létrehozza ezeket a virtuális gépeket és lemezeket különböző tartalék tartományokban, különböző energia-, hálózati és kiszolgáló-összetevőkkel.

Ezek miatt a különálló tartalék tartományok miatt a honosított hardverhibák általában nem befolyásolják a készlet több virtuális gépét egyszerre. A különálló tartalék tartományok magas rendelkezésre állást biztosít az alkalmazás számára. Célszerű a rendelkezésre állási csoportok használata, ha magas rendelkezésre állás szükséges. A következő szakasz a vész-helyreállítási szempont.

### <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

Katasztrófa utáni helyreállítás a képesség, hogy felépüljön a ritka, de nagyobb, események. Ezek az incidensek közé tartoznak a nem átmeneti, széles körű hibák, például a szolgáltatás kimaradása, amely egy teljes régiót érint. A vészhelyreállítás magában foglalja az adatok biztonsági mentését és archiválását, és magában foglalhatja a manuális beavatkozást, például az adatbázis biztonsági mentésből történő visszaállítását.

Előfordulhat, hogy az Azure platform helyi hibák elleni beépített védelme nem védi teljes mértékben a virtuális gépeket/lemezeket, ha egy súlyos katasztrófa nagymértékű kimaradásokat okoz. Ezek a nagyméretű kimaradások közé tartoznak a katasztrofális események, például ha egy adatközpontot hurrikán, földrengés, tűz vagy nagyméretű hardveregység-hiba ér el. Emellett előfordulhat, hogy alkalmazás- vagy adatproblémák miatt hibák léphetnek fel.

Az IaaS-munkaterhelések kimaradások elleni védelme érdekében meg kell terveznie a redundanciát, és biztonsági mentéseket kell készítenie a helyreállítás engedélyezéséhez. A vészhelyreállításhoz az elsődleges helytől eltérő földrajzi helyen kell biztonsági másolatot kapnia. Ez a megközelítés segít biztosítani, hogy a biztonsági mentés nem érinti ugyanazt az eseményt, amely eredetileg érintette a virtuális gép vagy a lemezek. További információ: [Katasztrófa utáni helyreállítás az Azure-alkalmazásokhoz.](/azure/architecture/resiliency/disaster-recovery-azure-applications)

A vészbiztonsági szolgáltatásokkal kapcsolatos szempontok a következő szempontokat tartalmazhatják:

- Magas rendelkezésre állás: Az alkalmazás képes továbbra is fut egy kifogástalan állapotban, jelentős állásidő nélkül. *Kifogástalan állapotban*ez az állapot azt jelenti, hogy az alkalmazás reszponzív, és a felhasználók csatlakozhatnak az alkalmazáshoz, és kölcsönhatásba léphet vele. Bizonyos kritikus fontosságú alkalmazásokra és adatbázisokra szükség lehet, hogy mindig elérhetők legyenek, még akkor is, ha a platformon hibák vannak. Ezeka számítási feladatok esetén szükség lehet az alkalmazás és az adatok redundanciájának megtervezésére.

- Adattartósság: Bizonyos esetekben a fő szempont annak biztosítása, hogy az adatok megmaradjanak, ha katasztrófa történik. Ezért előfordulhat, hogy egy másik helyen kell biztonsági másolatot készítenie az adatokról. Az ilyen számítási feladatok hoz, előfordulhat, hogy nem kell a teljes redundancia az alkalmazás, de csak a lemezek rendszeres biztonsági mentését.

## <a name="backup-and-dr-scenarios"></a>Biztonsági mentési és VÉSZ-forgatókönyvek

Nézzünk meg néhány tipikus példát az alkalmazás számítási feladatok forgatókönyvek és a tervezési szempontok a vész-helyreállítási.

### <a name="scenario-1-major-database-solutions"></a>1. forgatókönyv: Főbb adatbázis-megoldások

Fontolja meg egy éles adatbázis-kiszolgáló, például az SQL Server vagy az Oracle, amely támogatja a magas rendelkezésre állást. A kritikus fontosságú éles alkalmazások és felhasználók ettől az adatbázistól függenek. Előfordulhat, hogy a rendszer vész-helyreállítási tervének a következő követelményeket kell támogatnia:

- Az adatokat védeni kell, és helyreállíthatónak kell lenniük.
- A kiszolgálónak elérhetőnek kell lennie.

A vész-helyreállítási terv szükség lehet az adatbázis replikájának karbantartására egy másik régióban biztonsági másolatként. A kiszolgáló rendelkezésre állásának és az adatok helyreállításának követelményeitől függően a megoldás az aktív-aktív vagy aktív-passzív replikahelytől az adatok rendszeres offline biztonsági mentéséhez terjedhet. A relációs adatbázisok, például az SQL Server és az Oracle különböző replikációs lehetőségeket biztosítanak. Az SQL Server esetén használja az [SQL Server AlwaysOn rendelkezésre állási csoportjait](https://msdn.microsoft.com/library/hh510230.aspx) a magas rendelkezésre állás érdekében.

NoSQL-adatbázisok, például a MongoDB, is támogatja a [replikák](https://docs.mongodb.com/manual/replication/) redundancia. A replikák a magas rendelkezésre állás ú vannak használatban.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>2. forgatókönyv: Redundáns virtuális gépek fürtje

Fontolja meg a redundanciát és terheléselosztást biztosító virtuális gépek fürtje által kezelt munkaterhelést. Egy példa egy régióban telepített Cassandra-fürt. Ez a fajta architektúra már magas szintű redundanciát biztosít az adott régióban. Azonban a számítási feladatok védelme a regionális szintű hiba, érdemes a fürt két régióközötti szétosztása, vagy rendszeres biztonsági mentések egy másik régióba.

### <a name="scenario-3-iaas-application-workload"></a>3. forgatókönyv: IaaS-alkalmazások munkaterhelése

Nézzük meg az IaaS-alkalmazás munkaterhelését. Például ez az alkalmazás lehet egy tipikus éles számítási feladat fut egy Azure virtuális gép. Lehet, hogy egy webszerver vagy fájlszerver, amely a webhely tartalmát és egyéb erőforrásait tartja. Előfordulhat, hogy egy egyéni üzleti alkalmazás fut egy virtuális gép, amely tárolja az adatokat, erőforrásokat és az alkalmazás állapotát a virtuális gép lemezeken. Ebben az esetben fontos, hogy rendszeresen készítsen biztonsági mentéseket. A biztonsági mentés gyakoriságának a virtuális gép számítási feladatának jellegén kell alapulnia. Például ha az alkalmazás fut minden nap, és módosítja az adatokat, majd a biztonsági mentést kell venni óránként.

Egy másik példa egy jelentéskészítő kiszolgáló, amely más forrásokból származó adatokat szed le, és összesített jelentéseket hoz létre. A virtuális gép vagy a lemezek elvesztése a jelentések elvesztését eredményezheti. Előfordulhat azonban, hogy újra futtathatja a jelentéskészítési folyamatot, és újra generálhatja a kimenetet. Ebben az esetben nem igazán adatvesztés, még akkor is, ha a jelentéskészítő kiszolgálót katasztrófa éri. Ennek eredményeképpen előfordulhat, hogy magasabb szintű tűréshatára van a jelentéskészítő kiszolgálón lévő adatok egy részének elvesztésével szemben. Ebben az esetben a kevésbé gyakori biztonsági mentések a költségek csökkentésére is lehetőséget jelentenek.

### <a name="scenario-4-iaas-application-data-issues"></a>4. forgatókönyv: IaaS-alkalmazásadatokkal kapcsolatos problémák

IaaS alkalmazás adatok kérdések egy másik lehetőség. Fontolja meg egy olyan alkalmazás, amely kiszámítja, karbantartja és kiszolgálja a kritikus kereskedelmi adatokat, például az árképzési információkat. Az alkalmazás új verziójának szoftverhibája volt, amely helytelenül számította ki az árképzést, és megrongálta a platform által kiszolgált meglévő kereskedelmi adatokat. Itt a legjobb megoldás az alkalmazás és az adatok korábbi verziójának visszaállítása. Ennek engedélyezéséhez rendszeres biztonsági mentést készítsen a rendszerről.

## <a name="disaster-recovery-solution-azure-backup"></a>Vész-helyreállítási megoldás: Azure Backup 

[Az Azure Backup](https://azure.microsoft.com/services/backup/) biztonsági mentések és a DR, és működik a [felügyelt lemezek,](../articles/virtual-machines/windows/managed-disks-overview.md) valamint a nem felügyelt lemezek. Létrehozhat egy biztonsági mentési feladatot időalapú biztonsági mentésekkel, egyszerű virtuális gép-visszaállítással és biztonsági mentési adatmegőrzési házirendekkel.

Ha [prémium szintű SSD-ket,](../articles/virtual-machines/windows/disks-types.md) [felügyelt lemezeket](../articles/virtual-machines/windows/managed-disks-overview.md)vagy más lemeztípusokat használ a [helyileg redundáns tárolási](../articles/storage/common/storage-redundancy-lrs.md) lehetőséggel, különösen fontos, hogy rendszeres ens-biztonsági mentéseket készítsen. Az Azure Backup tárolja az adatokat a helyreállítási szolgáltatások tárolójában a hosszú távú megőrzés. Válassza ki a [georedundáns tárolási](../articles/storage/common/storage-redundancy-grs.md) lehetőséget a biztonsági mentési helyreállítási szolgáltatások tároló. Ez a beállítás biztosítja, hogy a biztonsági mentések replikálása egy másik Azure-régióba a regionális katasztrófák elleni védelem érdekében.

Nem felügyelt lemezek esetén használhatja a helyileg redundáns tárolási típus iaaS-lemezek, de győződjön meg arról, hogy az Azure Backup engedélyezve van a georedundáns tárolási lehetőség a helyreállítási szolgáltatások tárolójában.

> [!NOTE]
> Ha a [georedundáns tárolási](../articles/storage/common/storage-redundancy-grs.md) vagy [olvasási hozzáférésű georedundáns tárolási](../articles/storage/common/storage-redundancy.md) lehetőséget használja a nem felügyelt lemezekhez, akkor is szüksége van konzisztens pillanatképekre a biztonsági mentéshez és a VÉSZ-kezeléshez. Használja az [Azure Backup](https://azure.microsoft.com/services/backup/) vagy [a konzisztens pillanatképeket.](#alternative-solution-consistent-snapshots)

 Az alábbi táblázat a DR-hez rendelkezésre álló megoldások összefoglalását foglalja el.

| Forgatókönyv | Automatikus replikáció | DR megoldás |
| --- | --- | --- |
| Premium SSD-lemezek | Helyi[(helyileg redundáns tárolás)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Felügyelt lemezek | Helyi[(helyileg redundáns tárolás)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nem felügyelt, helyileg redundáns tárolólemezek | Helyi[(helyileg redundáns tárolás)](../articles/storage/common/storage-redundancy-lrs.md) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nem felügyelt georedundáns tárolólemezek | Régiók közötti ([georedundáns tárolás](../articles/storage/common/storage-redundancy-grs.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzisztens pillanatképek](#alternative-solution-consistent-snapshots) |
| Nem felügyelt olvasási hozzáférésű georedundáns tárolólemezek | Régiók közötti ([olvasási hozzáférésű georedundáns tárolás](../articles/storage/common/storage-redundancy.md)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzisztens pillanatképek](#alternative-solution-consistent-snapshots) |

A magas rendelkezésre állás az Azure Backup szolgáltatással együtt elérhető helyen lévő felügyelt lemezek használatával érhető el a legjobban. Ha nem felügyelt lemezeket használ, továbbra is használhatja az Azure Backup for DR.If you use un managed disks, you still use Azure Backup for DR. Ha nem tudja használni az Azure Backup, majd [konzisztens pillanatképek](#alternative-solution-consistent-snapshots), akésőbbi szakaszban leírtak szerint egy alternatív megoldás a biztonsági mentés és a DR.

A magas rendelkezésre állás, a biztonsági mentés és a VÉSZ-beállítások alkalmazás- vagy infrastruktúraszinteken a következőképpen jelennek meg:

| Szint |   Magas rendelkezésre állás   | Biztonsági mentés vagy DR |
| --- | --- | --- |
| Alkalmazás | SQL Server AlwaysOn | Azure Backup |
| Infrastruktúra    | Rendelkezésre állási csoport  | Georedundáns tárolás konzisztens pillanatképekkel |

### <a name="using-azure-backup"></a>Az Azure Backup használata 

[Az Azure Backup](../articles/backup/backup-azure-vms-introduction.md) biztonsági másolatot készíthet a Windows vagy Linux futó virtuális gépekről az Azure helyreállítási szolgáltatások tárolójában. Az üzleti legkritikusabb adatok biztonsági mentését és visszaállítását bonyolítja az a tény, hogy az üzleti legkritikusabb adatokról biztonsági másolatot kell készíteni, miközben az adatokat előállító alkalmazások futnak. 

A probléma megoldásához az Azure Backup alkalmazáskonzisztens biztonsági mentéseket biztosít a Microsoft számítási feladatokhoz. A kötet árnyékszolgáltatását használja annak biztosítására, hogy az adatok helyesen legyenek írva a tárolóba. Linuxos virtuális gépek esetén az alapértelmezett biztonsági mentési konzisztenciamód a fájlkonzisztens biztonsági mentések, mivel a Linux nem rendelkezik a kötet árnyékszolgáltatásával egyenértékű funkcionalitással, mint a Windows esetében. Linux-gépek esetén lásd: [Az Azure Linux virtuális gépek alkalmazáskonzisztens biztonsági mentése.](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

![Azure biztonsági mentési folyamat][1]

Amikor az Azure Backup az ütemezett időpontban biztonsági mentési feladatot indít el, elindítja a virtuális gépbe telepített biztonsági mentési bővítményt, hogy időponthoz érkezzen pillanatkép. A pillanatkép a kötet árnyékszolgáltatásával együttműködve készül a virtuális gép lemezeinek konzisztens pillanatképének leállítása érdekében. A biztonsági mentési bővítmény a virtuális gép kiüríti az összes írási, mielőtt egy konzisztens pillanatképet az összes lemez. A pillanatkép végrehajtása után az adatokat az Azure Backup átviszi a biztonsági mentési tárolóba. A biztonsági mentési folyamat hatékonyabbá tétele érdekében a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek a legutóbbi biztonsági mentés után megváltoztak.

A visszaállításhoz megtekintheti a rendelkezésre álló biztonsági mentéseket az Azure Backup szolgáltatáson keresztül, majd kezdeményezheti a visszaállítást. Az [Azure-biztonsági](https://portal.azure.com/)mentések az Azure Portalon keresztül, a [PowerShell használatával](../articles/backup/backup-azure-vms-automation.md)vagy az [Azure CLI](/cli/azure/)használatával hozhat létre és állíthat vissza.

### <a name="steps-to-enable-a-backup"></a>A biztonsági mentés engedélyezésének lépései

Az alábbi lépésekkel engedélyezheti a virtuális gépek biztonsági mentését az [Azure Portalon](https://portal.azure.com/)keresztül. Van némi eltérés attól függően, hogy a pontos forgatókönyv. A részleteket az [Azure Backup dokumentációjában](../articles/backup/backup-azure-vms-introduction.md) találja. Az Azure Backup [a felügyelt lemezekkel rendelkező virtuális gépeket](https://azure.microsoft.com/blog/azure-managed-disk-backup/)is támogatja.

1.  Hozzon létre egy helyreállítási szolgáltatások tárolója egy virtuális gép:

    a. Az [Azure Portalon](https://portal.azure.com/)keresse meg az **Összes erőforrást,** és keresse meg a **Helyreállítási szolgáltatások tárolóit.**

    b. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás gombra,** és kövesse a lépéseket egy új tároló létrehozásához ugyanabban a régióban, mint a virtuális gép. Ha például a virtuális gép az USA nyugati régiójában található, válassza az USA nyugati régióját a tárolóhoz.

1.  Ellenőrizze az újonnan létrehozott tároló tárolóreplikációját. Nyissa meg a tárolót a **Helyreállítási szolgáltatások tárolói** alatt, és lépjen a **Tulajdonságok** > **biztonsági másolatának konfigurációs** > **frissítésére.** Győződjön meg arról, hogy a **georedundáns tárolási** beállítás alapértelmezés szerint be van jelölve. Ez a beállítás biztosítja, hogy a tároló automatikusan replikálódik egy másodlagos adatközpontba. Például a boltozat az USA nyugati részén automatikusan replikálódik az USA keleti része.

1.  Konfigurálja a biztonsági mentési házirendet, és válassza ki a virtuális gép ugyanabból a felhasználói felületről.

1.  Győződjön meg arról, hogy a biztonsági másolat ügynök telepítve van a virtuális gép. Ha a virtuális gép egy Azure-katalógus képsegítségével jön létre, majd a biztonsági másolat ügynök már telepítve van. Ellenkező esetben (azaz ha egyéni lemezképet használ), az utasításokat használja [a virtuális gép ügynökének virtuális gépre való telepítéséhez.](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent)

1.  Az előző lépések befejezése után a biztonsági mentés rendszeres időközönként fut a biztonsági mentési házirendben megadottmódon. Szükség esetén az első biztonsági mentést manuálisan aktiválhatja az Azure Portalon a tároló irányítópultjáról.

Az Azure Backup parancsfájlok használatával történő automatizálásához tekintse meg [a Virtuálisgép-biztonsági mentés PowerShell-parancsmagjait.](../articles/backup/backup-azure-vms-automation.md)

### <a name="steps-for-recovery"></a>A helyreállítás lépései

Ha meg kell javítania vagy újra kell építenie egy virtuális gép, visszaállíthatja a virtuális gép a tároló bármelyik biztonsági mentési helyreállítási pontjairól. Van néhány különböző lehetőségek elvégzésére a hasznosítás:

-   Létrehozhat egy új virtuális gép, mint egy pont-in-time ábrázolása a biztonsági másolatot készíteni virtuális gép.

-   Visszaállíthatja a lemezeket, majd a virtuális gép sablonjával testreszabhatja és újraépítheti a visszaállított virtuális gép.

További információt az Azure [Portal használatával a virtuális gépek visszaállítására](../articles/backup/backup-azure-arm-restore-vms.md)vonatkozó utasításokban talál. Ez a dokumentum azt is ismerteti, hogy a biztonsági másolatban szereplő virtuális gépek visszaállítása egy párosított adatközpontba a georedundáns biztonsági mentési tároló használatával, ha katasztrófa van az elsődleges adatközpontban. Ebben az esetben az Azure Backup a másodlagos régió számítási szolgáltatását használja a visszaállított virtuális gép létrehozásához.

A PowerShell segítségével [is létrehozhat egy új virtuális gép a visszaállított lemezek.](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## <a name="alternative-solution-consistent-snapshots"></a>Alternatív megoldás: Konzisztens pillanatképek

Ha nem tudja használni az Azure Backup, megvalósíthatja a saját biztonsági mentési mechanizmus pillanatképek használatával. Konzisztens pillanatképek létrehozása a virtuális gép által használt összes lemezhez, majd a pillanatképek replikálása egy másik régióba bonyolult. Ebből az okból az Azure úgy véli, hogy a biztonsági mentési szolgáltatás használata jobb megoldás, mint egy egyéni megoldás létrehozása.

Ha olvasási hozzáférésű georedundáns tárolót/georedundáns tárolást használ a lemezekhez, a pillanatképek automatikusan replikálódnak egy másodlagos adatközpontba. Ha helyileg redundáns tárolót használ a lemezekhez, saját kezűleg kell replikálnia az adatokat. További információ: [Az Azure-ban nem felügyelt virtuálisgép-lemezek biztonsági másolatot készíteni növekményes pillanatképekkel.](../articles/virtual-machines/windows/incremental-snapshots.md)

A pillanatkép egy objektum egy adott időpontban való ábrázolása. A pillanatkép számlázási a növekményes mérete az általa birtokolt adatok. További információ: [Blob snapshot létrehozása.](../articles/storage/blobs/storage-blob-snapshots.md)

### <a name="create-snapshots-while-the-vm-is-running"></a>Pillanatképek létrehozása a virtuális gép futása közben

Bár bármikor készíthet pillanatképet, ha a virtuális gép fut, továbbra is vannak adatok streamelése a lemezekre. A pillanatképek tartalmazhatnak részleges műveleteket, amelyek a repülés. Továbbá, ha több lemez is érintett, a különböző lemezek pillanatképei különböző időpontokban fordulhattak elő. Ezek a forgatókönyvek a pillanatképek koordinálatlanját okozhatják. A koordináció hiánya különösen problémás azon csíkozott kötetek esetében, amelyek fájljai megsérülhetnek, ha a biztonsági mentés során módosításokat végeznek.

A helyzet elkerülése érdekében a biztonsági mentési folyamatnak a következő lépéseket kell végrehajtania:

1.  Fagyassza le az összes lemezt.

1.  Ürítse ki az összes függőben lévő írást.

1.  [Hozzon létre egy blob pillanatképet](../articles/storage/blobs/storage-blob-snapshots.md) az összes lemezhez.

Egyes Windows-alkalmazások, például az SQL Server, egy kötetárnyék-szolgáltatáson keresztül összehangolt biztonsági mentési mechanizmust biztosítanak az alkalmazáskonzisztens biztonsági mentések létrehozásához. Linuxon egy olyan eszközt használhat, mint *az fsfreeze* a lemezek koordinálásához. Ez az eszköz fájlkonzisztens biztonsági mentéseket biztosít, de alkalmazáskonzisztens pillanatképeket nem. Ez a folyamat összetett, ezért érdemes az [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) vagy egy külső biztonsági mentési megoldás, amely már megvalósítja ezt az eljárást.

Az előző folyamat az összes virtuálisgép-lemez összehangolt pillanatképeinek gyűjteményét eredményezi, amelyek a virtuális gép adott időpont-időpont nézetét képviselik. Ez egy biztonsági mentési visszaállítási pont a virtuális gép számára. A folyamatot ütemezett időközönként megismételheti, hogy rendszeres biztonsági mentéseket hozzon létre. Tekintse [meg a biztonsági mentések másolása egy másik régióba](#copy-the-snapshots-to-another-region) lépéseket a pillanatképek másolása egy másik régióba a DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Pillanatképek létrehozása, miközben a virtuális gép offline állapotban van

Egy másik lehetőség, hogy hozzon létre konzisztens biztonsági mentések leállítása a virtuális gép, és blob pillanatképek az egyes lemezek. Blob pillanatképek készítése könnyebb, mint egy futó virtuális gép pillanatképeinek koordinálása, de néhány perc állásidőt igényel.

1. Állítsa le a virtuális gép.

1. Hozzon létre egy pillanatképet az egyes virtuális merevlemez-blobok, amely csak néhány másodpercet vesz igénybe.

    Pillanatkép létrehozásához használhatja a [PowerShellt](../articles/storage/common/storage-powershell-guide-full.md), az [Azure Storage REST API-t,](https://msdn.microsoft.com/library/azure/ee691971.aspx)az [Azure CLI-t](/cli/azure/)vagy az Azure Storage ügyfélkódtárak egyikét, például [a .NET Storage ügyfélkódtárját.](https://msdn.microsoft.com/library/azure/hh488361.aspx)

1. Indítsa el a virtuális gép, amely befejezi az állásidőt. Általában a teljes folyamat néhány percen belül befejeződik.

Ez a folyamat az összes lemez konzisztens pillanatképeinek gyűjteményét eredményezi, és biztonsági mentési visszaállítási pontot biztosít a virtuális gép számára.

### <a name="copy-the-snapshots-to-another-region"></a>A pillanatképek másolása egy másik régióba

A pillanatképek létrehozása önmagában nem feltétlenül elegendő a DR. A pillanatkép biztonsági másolatait is replikálnia kell egy másik régióba.

Ha georedundáns tárolást vagy olvasási hozzáférésű georedundáns tárolót használ a lemezekhez, majd a pillanatképek automatikusan replikálódnak a másodlagos régióba. A replikáció előtt néhány perc késés is lehet. Ha az elsődleges adatközpont leáll, mielőtt a pillanatképek replikálása befejeződik, nem érheti el a pillanatképeket a másodlagos adatközpontból. Ennek a valószínűsége kicsi.

> [!NOTE]
> Csak a lemezek egy georedundáns tároló vagy olvasási hozzáférésű georedundáns tárfiók nem védi meg a virtuális gép a katasztrófáktól. Koordinált pillanatképeket is létre kell hoznia, vagy az Azure Backup szolgáltatást kell használnia. Erre a virtuális gép konzisztens állapotba való helyreállításához van szükség.

Ha helyileg redundáns tárolást használ, a pillanatképeket közvetlenül a pillanatkép létrehozása után egy másik tárfiókba kell másolnia. A másolási cél lehet egy helyileg redundáns tárfiók egy másik régióban, ami azt eredményezi, hogy a másolat egy távoli régióban. A pillanatképet is másolhatja egy olvasási hozzáférésű georedundáns tárfiók ugyanabban a régióban. Ebben az esetben a pillanatkép lustán replikálódik a távoli másodlagos régióba. A biztonsági mentés az elsődleges helyen a másolás és a replikáció befejezése után védett.

A dr-re vonatkozó növekményes pillanatképek másolásához tekintse át az [Azure nem felügyelt virtuálisgép-lemezein növekményes pillanatképeket tartalmazó](../articles/virtual-machines/windows/incremental-snapshots.md)utasításokat.

![Az Azure nem felügyelt virtuálisgép-lemezeinek biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági, növekményes pillanatképek][2]

### <a name="recovery-from-snapshots"></a>Helyreállítás pillanatképekből

Pillanatkép lekéréséhez másolja azt egy új blob. Ha a pillanatképet az elsődleges fiókból másolja, átmásolhatja a pillanatképet a pillanatkép alapblobjába. Ez a folyamat visszaállítja a lemezt a pillanatképbe. Ezt a folyamatot úgy ismerik, hogy elősegíti a pillanatképet. Ha a pillanatkép biztonsági mentését egy másodlagos fiókból másolja, olvasási hozzáférésű georedundáns tárfiók esetén egy elsődleges fiókba kell másolnia. A pillanatképek másolása [a PowerShell vagy](../articles/storage/common/storage-powershell-guide-full.md) az AzCopy segédprogram használatával. További információt az [Adatok átvitele az AzCopy parancssori segédprogrammal című témakörben](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)talál.

Több lemezzel rendelkező virtuális gépek esetén az összes olyan pillanatképet át kell másolnia, amelyek ugyanannak a koordinált visszaállítási pontnak a részét képezik. Miután másolja a pillanatképek írható virtuális merevlemez-blobok, használhatja a blobok a virtuális gép újbóli létrehozásához a virtuális gép a virtuális gép sablon használatával.

## <a name="other-options"></a>Egyéb lehetőségek

### <a name="sql-server"></a>SQL Server

A virtuális gépen futó SQL Server saját beépített képességekkel rendelkezik az SQL Server-adatbázis Azure Blob-tárolóba vagy fájlmegosztásra történő biztonsági mentésére. Ha a tárfiók georedundáns tárolási vagy olvasási hozzáférésű georedundáns tároló, ezek a biztonsági mentések a tárfiók másodlagos adatközpontjában katasztrófa esetén, ugyanazokat a korlátozásokat, mint korábban tárgyalt. További információt az [SQL Server biztonsági és visszaállítása az Azure virtuális gépeken című témakörben talál.](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md) A biztonsági másolat és visszaállítás mellett az [SQL Server AlwaysOn rendelkezésre állási csoportjai](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) az adatbázisok másodlagos replikáit is karbantarthatják. Ez a képesség nagymértékben csökkenti a vész-helyreállítási idő.

## <a name="other-considerations"></a>Egyéb szempontok

Ez a cikk ismerteti, hogyan biztonsági másolatot készíteni, vagy pillanatképeket készíteni a virtuális gépek és a lemezek a vész-helyreállítási támogatás, és hogyan használhatja ezeket a biztonsági másolatokat vagy pillanatképeket az adatok helyreállításához. Az Azure Resource Manager modell, sokan használják a sablonokat, hogy hozzon létre a virtuális gépek és más infrastruktúrák az Azure-ban. Egy sablon segítségével létrehozhat egy virtuális gép, amely minden alkalommal azonos konfigurációval rendelkezik. Ha egyéni rendszerképeket használ a virtuális gépek létrehozásához, győződjön meg arról is, hogy a rendszerképek védett ek egy olvasási hozzáférésű georedundáns tárfiók tárolása.

Ennek következtében a biztonsági mentési folyamat két dolog kombinációja lehet:

- Biztonsági másolatot készíteni az adatokról (lemezek).
- Készítsen biztonsági másolatot a konfigurációról (sablonok és egyéni lemezképek).

A választott biztonsági mentési beállítástól függően előfordulhat, hogy az adatok és a konfiguráció biztonsági mentését is kezelnie kell, vagy a biztonsági másolat szolgáltatás az összeset kezeli.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Függelék: Az adatredundancia hatásának megértése

Az Azure-beli tárfiókok esetében háromféle adatredundanciát kell figyelembe vennie a vész-helyreállítással kapcsolatban: helyileg redundáns, georedundáns vagy georedundáns olvasási hozzáféréssel. 

A helyileg redundáns tárolás az adatok három példányát őrzi meg ugyanabban az adatközpontban. Amikor a virtuális gép írja az adatokat, mind a három példány frissül, mielőtt a sikeres vissza a hívó, így tudja, hogy azonosak. A lemez védett a helyi hibáktól, mert nem valószínű, hogy mindhárom példány egyszerre érintett lenne. Helyileg redundáns tárolás esetén nincs georedundancia, így a lemez nem védett a katasztrofális hibáktól, amelyek hatással lehetnek a teljes adatközpontra vagy a tárolóegységre.

A georedundáns tárolás és az olvasási hozzáférésű georedundáns tárolás, az adatok három példányban őrzi meg az Ön által kiválasztott elsődleges régióban. Az adatok további három példányát az Azure által beállított megfelelő másodlagos régióban őrizzük meg. Ha például az USA nyugati részén tárol adatokat, az adatok replikálódnak az USA keleti részére. A másolásmegőrzés aszinkron módon történik, és az elsődleges és másodlagos helyek frissítései között kis késés van. A másodlagos helyen lévő lemezek replikái lemezenként konzisztensek (a késleltetéssel), de előfordulhat, hogy több aktív lemez replikái nincsenek szinkronban egymással. Ahhoz, hogy több lemezkon konzisztens replikák legyenek, konzisztens pillanatképekre van szükség.

A georedundáns tárolás és az olvasási hozzáférésű georedundáns tárolás közötti fő különbség az, hogy az olvasási hozzáférésű georedundáns tárolással bármikor elolvashatja a másodlagos példányt. Ha olyan probléma merül fel, amely elérhetetlenné teszi az adatokat az elsődleges régióban, az Azure-csapat mindent megtesz a hozzáférés visszaállítása érdekében. Amíg az elsődleges nem, ha olvasási hozzáférésű georedundáns tárolás engedélyezve van, hozzáférhet az adatokhoz a másodlagos adatközpontban. Ezért ha azt tervezi, hogy olvassa el a replika, míg az elsődleges nem érhető el, majd olvasási hozzáférés georedundáns tárolás figyelembe kell venni.

Ha kiderül, hogy jelentős kimaradás, az Azure-csapat elindíthatja a geo-feladatátvételt, és módosítsa az elsődleges DNS-bejegyzéseket, hogy másodlagos tárolóra mutassanak. Ezen a ponton, ha georedundáns tárolás vagy olvasási hozzáférés georedundáns tárolás engedélyezve van, hozzáférhet az adatokat a régióban, amely korábban a másodlagos. Más szóval, ha a tárfiók georedundáns tárolás, és probléma merül fel, csak akkor érheti el a másodlagos tárolót, ha van egy geo-feladatátvétel.

További információk: [Mi a Mi a teendő az Azure Storage leállása esetén](../articles/storage/common/storage-disaster-recovery-guidance.md).

>[!NOTE] 
>A Microsoft szabályozza, hogy történik-e feladatátvétel. Feladatátvétel nem szabályozott tárfiókonként, így nem határozza meg az egyes ügyfelek. Az adott tárfiókok vagy virtuálisgép-lemezek vész-helyreállítási megvalósításához a jelen cikkben ismertetett technikákat kell használnia.

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
