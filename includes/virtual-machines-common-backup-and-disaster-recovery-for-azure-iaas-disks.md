
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Az Azure infrastruktúra-szolgáltatási lemezek a biztonsági mentés és katasztrófa-helyreállítás

Ez a cikk azt ismerteti, hogyan tervezése a biztonsági mentési és vész-helyreállítási IaaS virtuális gépek (VM) és a lemezt az Azure-ban. Ez a dokumentum ismerteti mind a kezelt, mind a nem felügyelt.

Először igazolnia terjed ki a beépített tartalék tolerancia képességek az Azure platformon, amely segít védelmet biztosítanak a helyi hibák. Ezután arról lesz szó a vész-eljárással beépített képességeit nem teljes mértékben. Ez az ebben a dokumentumban tárgyalt a fő témakört. Azt is megjelenítése néhány példa munkaterhelés forgatókönyvek, ahol különböző biztonsági mentési és vész-Helyreállítási szempontok érvényesek lehetnek. A Microsoft tekintse át a vész-Helyreállítási az infrastruktúra-szolgáltatási lemezek lehetséges megoldásokat. 

## <a name="introduction"></a>Bevezetés

Az Azure platformon redundancia és a hibatűrés különböző módszerek használatával honosított hardverhibák felhasználók védelme. Helyi hibák lehetnek egy virtuális lemez számára az adatok egy részét vagy az SSD és HDD sikertelen ezen a kiszolgálón tárolt Azure Storage server gépével kapcsolatos probléma. Ilyen elkülönített hardver összetevőinek meghibásodása esetén fordulhat elő a normál működés során. 

Az Azure platformon úgy tervezték, hogy ezek a hibák rugalmasak lehetnek. Jelentős katasztrófa hibák vagy sok tárolókiszolgálók vagy akár egy teljes datacenter inaccessibility eredményezhetnek. Bár a virtuális gépek és a lemezek megfelelően védettek a helyi hibák, további lépések szükségesek, hogy megvédje a számítási feladatok régió kiterjedő végzetes hibák, például egy jelentős katasztrófa, amelyek hatással lehetnek a virtuális gép és a lemezek.

Platform hibák lehetőségét, mellett alkalmazások vevői vagy adatok hibák léphetnek fel. Például az alkalmazás új verziójának előfordulhat, hogy véletlenül módosítja azt törés az adatokhoz. Ebben az esetben érdemes állítsa vissza az alkalmazás és az adatok előzetes verzióra, amely tartalmazza a legutóbbi ismert helyes állapotban. Ehhez a rendszeres biztonsági mentések karbantartása.

Regionális vész-helyreállítási készítsen biztonsági másolatot az infrastruktúra-szolgáltatási virtuális gépek lemezei egy másik régióban. 

Mielőtt úgy tekintünk, biztonsági mentési és vész-Helyreállítási beállítások, most recap néhány módszer áll rendelkezésre helyi hibák kezelése.

### <a name="azure-iaas-resiliency"></a>Az Azure infrastruktúra-szolgáltatási rugalmasság

*Rugalmasság* normál hardverösszetevők az előforduló hibák tolerancia hivatkozik. Rugalmasságra helyreállni hibák után, és továbbra is működik. Nincs kapcsolatos hibák elkerülése, de válaszol a hibák úgy, hogy elkerülhető az állásidő és adatvesztés. A rugalmasság célja, hogy az alkalmazás egy hibát követően teljesen működőképes állapotba térjen vissza. Az Azure virtuális gépek és a lemezek úgy tervezték, hogy közös hardver hibákra rugalmasak lehetnek. Nézzük meg, hogy az Azure infrastruktúra-szolgáltatási platform ez rugalmasságot biztosít.

A virtuális gépek általában két részből áll: egy számítási kiszolgáló és az állandó lemezek. A hibatűrés egy virtuális gép is érinti.

Ha az Azure compute gazdagép-kiszolgálón, amely a virtuális gép hardverhiba, amely ritka, Azure célja, hogy automatikusan állítsa helyre a virtuális Gépet, egy másik kiszolgálón. Ha ez történik, a számítógép újraindítása, és a virtuális gép ismét elérhető lesz némi várakozás után. Azure automatikusan észleli az ilyen hardverhibák és hajt végre annak biztosítására, hogy az ügyfél virtuális gép minél hamarabb áll rendelkezésre helyreállítások.

Infrastruktúra-szolgáltatási lemezekre vonatkozó adatok tartósságát fontos állandó tároló platformhoz tartozó. Azure-ügyfél infrastruktúra-szolgáltatáson futó üzleti alkalmazások rendelkeznek, és a megőrzése függenek. A IaaS lemezeket, a helyben tárolt adatok három redundáns másolatait Azure tervek védelmét. Adja meg példányainak magas tartósság helyi hibák ellen. Ha egy a hardverösszetevők, amely tárolja a lemez meghibásodik, a virtuális gép nincs hatással, mert a lemez kérelmek támogatásához két további másolatokat. Jól működik, akkor is, ha két különböző hardverösszetevők, amely támogatja a lemez (amely nagyon ritkán) egyszerre sikertelen. 

Győződjön meg arról, hogy mindig karbantartása három replikákat, Azure Storage automatikusan indít a az adatokat egy új példányát, ha a három közül másolja át az nem érhető el. Ezért azt nem kell RAID használható Azure-lemezeket a hibatűrés szükséges. Egy egyszerű RAID 0 konfigurációs elegendő-e a lemezek szétosztott, ha nagyobb kötetek létrehozásához szükséges kell lennie.

Ebben az architektúrában miatt Azure rendelkezik következetesen kézbesíteni vállalati szintű tartósságot, az infrastruktúra-szolgáltatási lemezek, egy iparágvezető a nulla százalék [annualized hibaaránya](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Honosított hardver hibáit a számítási a gazdagép, vagy a tárolási platform is néha a virtuális gép, amelyet jelez elérhetetlenség eredményezheti a [Azure garantált szolgáltatási szintje](https://azure.microsoft.com/support/legal/sla/virtual-machines/) a virtuális gép rendelkezésre állás érdekében. Azure iparágvezető szolgáltatásiszint-szerződésben garantált is biztosít, a prémium szintű Azure Storage lemezeket használó egyetlen Virtuálisgép-példányok.

Állásidő egy lemezt vagy a virtuális gép ideiglenes elérhetetlensége miatt az alkalmazások és szolgáltatások biztosításához az ügyfelek használhatják [rendelkezésre állási készletek](../articles/virtual-machines/windows/manage-availability.md). Két vagy több virtuális gép rendelkezésre állási csoportba az alkalmazás redundanciájának biztosítása. Azure majd hoz létre a virtuális gépek és a lemezek külön tartalék tartományok különböző tápellátáshoz, a hálózati és a kiszolgáló-összetevők. 

Ezeket külön tartalék tartományok miatt honosított hardver meghibásodása általában nem befolyásolják a készlet több virtuális gép egyszerre. Magas rendelkezésre állást, az alkalmazás olyan külön tartalék tartományokat biztosít. Rendelkezésre állási készletek használata, amikor szükség a magas rendelkezésre állású célszerű figyelembe. A következő szakasz ismerteti a vész-helyreállítási szempont.

### <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és katasztrófa utáni helyreállítás

Vész-helyreállítási azt a képességet ritkán fordul elő, de jelentős, az incidensek helyreállíthatók. Ez magában foglalja a nem átmeneti, szinten hibák, például egy teljes terület érintő üzemkimaradás időtartamát. Vész-helyreállítási magában foglalja az adatok biztonsági mentése és az archiválásra, és tartalmazhat manuális beavatkozásra, például az adatbázis visszaállítása egy biztonsági másolatból.

Az Azure platformon beépített védelmet biztosít a helyi hibák előfordulhat, hogy nem teljes védelme érdekében a virtuális gépek/lemezek Ha jelentős katasztrófa miatt a nagyméretű kimaradások esetén. Ez tartalmaz katasztrofális esemény, például ha egy adatközpont talált egy Hurrikán, földrengés, fire, vagy egy nagy méretű hardver egység meghibásodása esetén. Ezenkívül alkalmazás vagy adatokkal kapcsolatos problémák miatt hibák léphetnek.

A kimaradások az IaaS munkaterhelések védelme érdekében redundancia megtervezése és biztonsági mentések helyreállítási rendelkeznie kell. Vész-helyreállítási készítsen biztonsági másolatot elhagyja az elsődleges hely különböző földrajzi helyen. Ez biztosítja a biztonsági másolat nem érinti a virtuális gép vagy lemezek eredetileg érintő ugyanarra az eseményre. További információkért lásd: [Azure-alkalmazások vész-helyreállítási](/azure/architecture/resiliency/disaster-recovery-azure-applications).

A vész-Helyreállítási szempontok lehetnek a következő szempontokat:

- Magas rendelkezésre állású: az alkalmazás futtatásának állapota kifogástalan, jelentős állásidőt nélkül képességét. Által *megfelelő állapotba*, azt jelenti, hogy az alkalmazás működik-e, és a felhasználók az alkalmazás csatlakozhat, és használhatja őket. Egyes kritikus fontosságú alkalmazások és az adatbázisok lehet szükség, mindig elérhető legyen, akkor is, ha sikertelen a platform. A munkaterhelések esetén szükség lehet tervezze meg az alkalmazás, valamint az adatokat a redundancia.

- Adatok tartóssága: bizonyos esetekben a legfontosabb szempont annak ellenőrzése, hogy az adatok megmaradjanak, ha katasztrófa történik. Ezért szükség lehet egy másik helyen az adatok biztonsági másolata. Az ilyen munkaterhelések esetén előfordulhat, nem kell az alkalmazást, de csak rendszeres biztonsági mentést a lemez teljes redundanciája.

## <a name="backup-and-dr-scenarios"></a>Biztonsági mentési és vész-Helyreállítási forgatókönyvek

Nézzük alkalmazás munkaterhelés forgatókönyvek közül néhány gyakori példák és vész-helyreállítási tervezésével kapcsolatos szempontokat.

### <a name="scenario-1-major-database-solutions"></a>1. forgatókönyv: Fő adatbázis megoldások

Vegye figyelembe az éles adatbázis-kiszolgálót, például az SQL Server vagy Oracle, magas rendelkezésre állást támogató. Kritikus termelési alkalmazások és a felhasználók ezt az adatbázist függ. A vész-helyreállítási terv, a rendszer támogatja a következő követelményeket lehet szükség:

- Az adatok védett és helyreállítható kell lennie.
- A kiszolgáló számára elérhetőnek kell lennie.

A vész-helyreállítási terv szükség lehet az adatbázis biztonsági mentéséhez egy másik régióban karbantartása. Attól függően, hogy a kiszolgáló rendelkezésre állása és az adatok helyreállítása a követelményei a megoldás lehet, hogy egy aktív-aktív vagy aktív-passzív replika helyszínére és közöttiek rendszeres offline biztonsági mentést az adatokat. SQL Server és Oracle, például a relációs adatbázisok replikációs különböző lehetőségeket biztosítanak. Az SQL Server használja [SQL Server AlwaysOn rendelkezésre állási csoportok](https://msdn.microsoft.com/library/hh510230.aspx) a magas rendelkezésre állás érdekében.

NoSQL-adatbázisok, például a MongoDB, is támogatja a [replikák](https://docs.mongodb.com/manual/replication/) a redundancia érdekében. A magas rendelkezésre állású replikát használják.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>2. forgatókönyv: Egy fürt redundáns virtuális gépek

Érdemes lehet a munkaterhelés, a fürt virtuális gépeket, a redundanciát és terheléselosztást kezeli. Egy példa egy olyan régióban telepített Cassandra fürt. Az ilyen típusú architektúra már biztosít magas szintű redundancia adott régión belül. Azonban a munkaterhelések védelme a regionális szintű meghibásodása, vegye figyelembe a fürt két régióban is terjednek vagy rendszeres biztonsági mentést tételére egy másik régióban.

### <a name="scenario-3-iaas-application-workload"></a>3. forgatókönyv: Infrastruktúra-szolgáltatási alkalmazás munkaterhelés

Az infrastruktúra-szolgáltatási alkalmazás munkaterhelés vizsgáljuk meg. Ez lehet például egy tipikus környezet munkaterhelés egy Azure virtuális gépen. Előfordulhat, hogy a webkiszolgáló vagy a fájlkiszolgáló a tartalom és a hely más erőforrásokat. Egy egyedi üzleti alkalmazás, amely az adatok, erőforrások és alkalmazásállapot lemezen tárolja a a virtuális gép egy virtuális gépen is lehet. Ebben az esetben fontos ellenőrizze, hogy rendszeresen a biztonsági mentések igénybe vehet. Biztonsági mentési gyakoriság alapján a virtuális gép munkaterhelés jellegét. Például ha az alkalmazás naponta fut, és módosítja az adatokat, majd a biztonsági mentést kell végezni minden órában.

Egy másik példa: egy jelentéskészítő kiszolgálót, amely más forrásból kéri le az adatokat összesített jelentéseket hoz létre. A virtuális gép vagy lemezek megszűnését előfordulhat, hogy a jelentések elvesztését eredményezi. Azonban esetleg futtassa újra a jelentéskészítési folyamat és a kimenet újragenerálása. Ebben az esetben valóban nincs adatvesztést, még akkor is, ha a jelentéskészítő kiszolgáló találat van egy olyan vészhelyzet esetén. Ennek eredményeképpen lehetséges, hogy a jelentéskészítő kiszolgálón az adatok egy részét elvesztése tűréshatár magasabb szintű. Ebben az esetben ritkábban biztonsági mentések egy beállítást a költségek csökkentésének érdekében.

### <a name="scenario-4-iaas-application-data-issues"></a>4. forgatókönyv: Infrastruktúra-szolgáltatási alkalmazás adatokkal kapcsolatos problémák

Infrastruktúra-szolgáltatási alkalmazás adatproblémákat egy másik lehetőség. Érdemes lehet olyan alkalmazás, amely kiszámítja, tart fenn és kritikus kereskedelmi forgalomban beszerezhető adatok, például a díjszabásról szolgál. Az alkalmazás új verziójának egy szoftverfrissítési hiba, helytelenül az árképzés számított, és a meglévő kereskedelmi adatok, a platform által kiszolgált sérült volt. A legjobb megoldás érdekében ez az alkalmazás és az adatok korábbi verziójának állítható vissza. Ehhez igénybe vehet a rendszer rendszeres biztonsági mentést.

## <a name="disaster-recovery-solution-azure-backup"></a>Vész-helyreállítási megoldást: Azure biztonsági mentés 

[Azure biztonsági mentés](https://azure.microsoft.com/services/backup/) biztonsági mentések és a Vészhelyreállítás használható és [által kezelt lemezeken](../articles/virtual-machines/windows/managed-disks-overview.md) , valamint [lemezek nem felügyelt](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). A biztonsági mentési feladatot idő-alapú biztonsági mentések, könnyű VM-helyreállítás és biztonsági másolatok megőrzésének házirendeket hozhat létre. 

Ha [prémium szintű Storage lemezek](../articles/virtual-machines/windows/premium-storage.md), [által kezelt lemezeken](../articles/virtual-machines/windows/managed-disks-overview.md), vagy más lemeztípusok esetén az a [helyileg redundáns tárolás](../articles/storage/common/storage-redundancy.md#locally-redundant-storage) beállítást, különösen fontos annak rendszeres vész-Helyreállítási biztonsági másolatok. Azure biztonsági mentés tárolja az adatokat a hosszú távú megőrzési recovery services-tároló. Válassza ki a [georedundáns tárolás](../articles/storage/common/storage-redundancy.md#geo-redundant-storage) beállítás megadása a biztonsági mentési helyreállítási szolgáltatások tárolóban. Ez a lehetőség biztosítja a biztonsági mentések replikálva vannak a regionális katasztrófák megóvása egy másik Azure-régiót.

A [nem felügyelt lemezek](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), használja a helyileg redundáns tárolás infrastruktúra-szolgáltatási lemezek, de győződjön meg arról, hogy az Azure Backup szolgáltatás engedélyezve van-e a georedundáns tárolás beállítással a recovery services-tároló.

> [!NOTE]
> Ha használja a [georedundáns tárolás](../articles/storage/common/storage-redundancy.md#geo-redundant-storage) vagy [írásvédett georedundáns tárolás](../articles/storage/common/storage-redundancy.md#read-access-geo-redundant-storage) beállítás megadása után még a nem felügyelt lemezek alkalmazáskonzisztens pillanatképek szükséges biztonsági mentési és vész-Helyreállítási. Mindkét [Azure biztonsági mentés](https://azure.microsoft.com/services/backup/) vagy [alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots).

 A következő táblázat az elérhető vész-Helyreállítási megoldások összegzését.

| Forgatókönyv | Automatikus replikáció | Vész-Helyreállítási megoldás |
| --- | --- | --- |
| Prémium szintű Storage-lemezekhez | Helyi ([helyileg redundáns tárolás](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Felügyelt lemezek | Helyi ([helyileg redundáns tárolás](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nem felügyelt helyileg redundáns tárolás lemezek | Helyi ([helyileg redundáns tárolás](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nem felügyelt georedundáns tárolás lemezek | Több régióban ([georedundáns tárolás](../articles/storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots) |
| Nem felügyelt írásvédett georedundáns tárolás lemezek | Több régióban ([írásvédett georedundáns tárolás](../articles/storage/common/storage-redundancy.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots) |

Magas rendelkezésre állású legjobb teljesül által kezelt lemezek használatával a rendelkezésre állási készlet együtt az Azure biztonsági mentés. Nem felügyelt lemezek használatakor a vész-Helyreállítási továbbra is használhatja a Azure Backup szolgáltatásnál. Ha nem használható az Azure Backup szolgáltatásban, majd véve [alkalmazáskonzisztens pillanatképek](#alternative-solution-consistent-snapshots), amint egy későbbi szakasz ismerteti az alternatív megoldás, a biztonsági mentéshez és vész-Helyreállítási.

A lehetőségek a magas rendelkezésre állás, a biztonsági mentés és a vész-Helyreállítási alkalmazást vagy az infrastruktúra szintjén a következőképpen ábrázolható:

| Szint |   Magas rendelkezésre állás   | Biztonsági mentési vagy a vész-Helyreállítási |
| --- | --- | --- |
| Alkalmazás | SQL Server AlwaysOn | Azure Backup |
| Infrastruktúra    | Rendelkezésre állási csoport  | Az alkalmazáskonzisztens pillanatképek georedundáns tárolás |

### <a name="using-azure-backup"></a>Az Azure Backup segítségével 

[Azure biztonsági mentés](../articles/backup/backup-azure-vms-introduction.md) készíthet biztonsági másolatot a virtuális gépeken futó Windows vagy Linux az Azure Recovery services-tároló. Biztonsági mentése és visszaállítása az üzleti szempontból kritikus fontosságú adatok bonyolítja van arra, hogy üzleti szempontból kritikus fontosságú adatok kell készülni, az alkalmazások az adatokat előállító működése közben. 

A probléma megoldására az Azure Backup alkalmazáskonzisztens biztonsági mentést biztosít Microsoft-munkaterhelések. Győződjön meg arról, hogy adatot ír megfelelően tárolási a kötet árnyékmásolata szolgáltatást használja. Linux virtuális gépekhez csak a fájlkonzisztens biztonsági mentések is lehetségesek, mert Linux nincs egyenértékű a kötet árnyékmásolata szolgáltatás funkcióit.

![Az Azure biztonsági mentés folyamata][1]

Azure biztonsági mentési indít el a biztonsági mentési feladatot a megadott időpontban, amikor elindítja a biztonságimásolat-kiterjesztés telepítése pont időponthoz kötött pillanatképet készít a virtuális gépen. A rendszer pillanatképet készít a kötet árnyékmásolata szolgáltatás együtt a lemezek konzisztens pillanatképének beolvasása a virtuális gép nem kell leállítani. A biztonsági mentési bővítményt a virtuális gép összes írása előtt a lemezeket a konzisztens pillanatképének készítése kiürítése. A pillanatkép alapján, az adatátvitel Azure Backup szolgáltatás a biztonsági mentési tárolóba. A biztonsági mentési folyamat hatékonyabbá teheti, hogy a szolgáltatás azonosítja, és átviszi az adatok, amelyek megváltoztak a legutóbbi biztonsági mentés csak a blokkok.

Visszaállításához a rendelkezésre álló biztonsági mentések Azure Backup segítségével tekintheti meg és majd indítsa el a visszaállítást. Hozhat létre és állítsa vissza az Azure biztonsági mentések keresztül a [Azure-portálon](https://portal.azure.com/), az [powershellel](../articles/backup/backup-azure-vms-automation.md), vagy a [Azure CLI](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>A biztonsági mentés engedélyezésének lépései

Az alábbi lépésekkel biztonsági mentéseket a virtuális gépek használatával a [Azure-portálon](https://portal.azure.com/). A pontos forgatókönyvtől függően néhány módosítás van. Tekintse meg a [Azure biztonsági mentés](../articles/backup/backup-azure-vms-introduction.md) teljes dokumentációjában. Azure biztonsági mentés is [támogatja a virtuális gépek felügyelt lemezek](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Hozzon létre egy virtuális Gépet a recovery services-tároló:

    a. Az a [Azure-portálon](https://portal.azure.com/), keresse meg **összes erőforrás** és található **Recovery Services-tárolók**.

    b. Az a **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** és a lépéseket követve létrehozhat egy új tárolót a virtuális gép ugyanabban a régióban. Például ha a virtuális Gépet az USA nyugati régiója régióban, válasszon USA nyugati régiója a tároló.

2.  Ellenőrizze a tárolási replikáció, az újonnan létrehozott tároló. Hozzáférést a tárolóhoz alatt **Recovery Services-tárolók** , és navigáljon **beállítások** > **biztonsági mentési konfigurációhoz**. Győződjön meg arról a **georedundáns tárolás** beállítás alapértelmezés szerint. Ez biztosítja, hogy a rendszer automatikusan replikálja a tároló egy másodlagos adatközpontba. Például a tároló az USA nyugati régiója a rendszer automatikusan replikálja USA keleti régiója.

3.  Konfigurálja a biztonsági mentési házirend, és válassza ki a virtuális gép ugyanabban a szövegben.

4.  Győződjön meg arról, hogy a biztonsági mentési ügynök telepítve van a virtuális Gépen. Ha a virtuális gép létrehozása az Azure katalógusában lemezkép használatával, majd a biztonsági mentési ügynök már telepítve van. Egyéb (Ez azt jelenti, hogy ha egyéni lemezképet használ), kövesse az utasításokat a [a Virtuálisgép-ügynök telepítése egy virtuális gépen](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Győződjön meg arról, hogy a virtuális gép hálózati kapcsolattal a biztonsági mentési szolgáltatás működéséhez. Kövesse az utasításokat [hálózati kapcsolatra](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity).

6.  Az előző lépések elvégzése után a biztonsági mentés futtatásának rendszeres időközönként, a biztonsági mentési házirend. Szükség esetén manuálisan az irányítópultról tárolóban az Azure portálon az első biztonsági mentés indíthat el.

Parancsfájlok segítségével automatizálhatja a Azure biztonsági mentési, tekintse meg [PowerShell-parancsmagok a virtuális gép biztonsági mentése](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>A helyreállítás lépései

Ha tudja javítani, illetve építse újra a virtuális gép van szüksége, visszaállíthatja a virtuális gép bármelyik a biztonsági mentések helyreállítási pontjait a tárolóban lévő állapottal. Többféle helyreállításhoz különböző lehetőségek közül:

-   A biztonsági másolat virtuális Gépet egy időpontban ábrázolása, hozhat létre egy új virtuális Gépet.

-   Visszaállíthatja a lemezeket, és majd használni a virtuális gép testreszabása, és építse újra a visszaállított virtuális Géphez. 

További információkért tekintse meg az utasításokat [az Azure-portál használatával állítsa vissza a virtuális gépek](../articles/backup/backup-azure-arm-restore-vms.md). Ez a dokumentum is ismerteti az adott biztonsági másolat virtuális gépek visszaállítására párosított adatközpontba a georedundáns biztonsági mentési tároló használatával, ha van egy olyan vészhelyzet esetén, az elsődleges adatközpont. Ebben az esetben Azure Backup segítségével a számítási szolgáltatás másodlagos régióban hozza létre a visszaállított virtuális gépet.

Használhatja a PowerShell [visszaállítását egy virtuális gép](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) vagy [lemezeket az új virtuális gép létrehozása vissza](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatív megoldás: Alkalmazáskonzisztens pillanatképek

Ha nem használható az Azure Backup szolgáltatásban, a saját biztonsági mentési mechanizmus Megvalósíthat által készített pillanatfelvételek segítségével történő. Egy virtuális gép által használt lemezein konzisztens pillanatképeinek és majd replikál egy másik régióban ezeket a pillanatképeket bonyolult. Emiatt Azure úgy ítéli meg, mint egy egyéni megoldás kialakításának jobb megoldás a biztonsági mentési szolgáltatás használatával. 

Ha írásvédett georedundáns tárolás/georedundáns tárolás lemezt használ, a rendszer automatikusan replikálja pillanatképek másodlagos adatközpontba. Ha helyileg redundáns tárolás lemezt használ, kell replikálja az adatokat. További információkért lásd: [biztonsági mentése Azure-nem felügyelt méretű lemezek növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md).

Pillanatkép egy időben egy adott időpontban objektum ábrázolása. Pillanatkép azt eredményezi, azok a háromszorosa számlázási adatok növekményes méretének azt tartalmazza. További információkért lásd: [blob pillanatkép létrehozása](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Hozzon létre pillanatképek, a virtuális gép futása közben

Habár bármikor is készítsen pillanatképet, ha a virtuális gép fut, továbbra is fennáll a lemezek részére küldött adatok, és a pillanatképek tartalmazhatnak részleges műveletek, melyeket a felhőszolgáltató közötti átviteléhez. Is ha több lemez használnak, a különböző lemezek pillanatkép-készítési esetlegesen bekövetkeztek különböző időpontokban. Ez azt jelenti, hogy ezeket a pillanatképeket nem feltétlenül koordinált. Ez a különösen akkor jelent problémát, amelynek fájlok előfordulhat, hogy sérült, ha éppen módosítva biztonsági mentés során csíkozott kötetek.

Ebben a helyzetben elkerülése érdekében a biztonsági mentési folyamat implementálnia kell az alábbi lépéseket:

1.  A lemezek rögzítése.

2.  Ürítse ki a függőben lévő írási műveleteket.

3.  [A blob pillanatkép létrehozása](../articles/storage/blobs/storage-blob-snapshots.md) lemezein.

Egyes Windows-alkalmazások, például az SQL Server egy olyan mechanizmust koordinált biztonsági mentési keresztül egy kötet árnyékmásolata szolgáltatás a alkalmazáskonzisztens biztonsági mentések létrehozását. Linux egy eszköz, például fsfreeze használhatja a lemezek összehangolására. Az eszköz megadja a fájl-konzisztens biztonsági másolatok, de nem alkalmazáskonzisztens pillanatképeket. A folyamat be nem összetett, ezért érdemes [Azure biztonsági mentési](../articles/backup/backup-azure-vms-introduction.md) vagy egy külső biztonsági mentési megoldás, amely már megvalósítja ezt az eljárást.

Az előző folyamat koordinált pillanatképek a virtuális gép lemezeket, egy adott időpontban nézet a virtuális gép képviselő gyűjteménye eredményez. Ez a biztonsági mentés visszaállítási pont a virtuális gép számára. Az eljárás megismétlésével rendszeres időközönként a rendszeres biztonsági mentések létrehozását. Lásd: [másolja át a biztonsági mentés egy másik régióban](#copy-the-snapshots-to-another-region) át kell másolnia a pillanatképek egy másik régióban vész-Helyreállítási lépéseket.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Hozzon létre pillanatképek, miközben a virtuális gép offline állapotban

Konzisztens biztonsági másolatok létrehozására egy másik lehetőség, hogy a virtuális gép leállítása és blob pillanatképek készítése egyes lemezek. Blob pillanatképek készítése egyszerűbb, mint a futó virtuális gépek pillanatképek koordinációs, de néhány perc leállás mellett van szükség.

1. Állítsa le a virtuális Gépet.

2. Pillanatkép létrehozása a minden egyes virtuális merevlemez-meghajtóról blob, amely csak a néhány másodpercet vesz igénybe.

    Pillanatkép létrehozása, használhatja a [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), a [Azure Storage REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](/cli/azure/), vagy egy Azure Storage ügyfélkódtáraival például [a A Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Indítsa el a virtuális Gépet, ami befejezi az állásidő. Általában a teljes folyamat néhány percen belül befejeződik.

Ez a folyamat egy gyűjteményhez tartozó összes lemezt, a biztonsági mentés visszaállítási pontot biztosít a virtuális gép alkalmazáskonzisztens pillanatképek adja eredményül.

### <a name="copy-the-snapshots-to-another-region"></a>A pillanatképek másolása egy másik régióban

A pillanatképek önmagában létrehozásának nem feltétlenül elégségesek Dr. Egy másik régióban is a pillanatkép biztonsági mentést kell készítenie.

Ha georedundáns tárolás vagy írásvédett georedundáns tárolás a lemezen, majd a pillanatképek replikálva vannak a másodlagos régióba automatikusan. A lag a replikáció előtt néhány percet is lehet. Ha az elsődleges adatközpont leáll, mielőtt a pillanatképek Befejezés replikálása, akkor nem érik a pillanatképek a másodlagos adatközpontba. Ez a valószínűségét nem nagy.

> [!NOTE] 
> Csak a lemezek georedundáns tárolás vagy írásvédett georedundáns rendelkező tárfiók nyújt védelmet a virtuális gép a vészhelyzetek esetére. Kell koordinált pillanatképeket, vagy az Azure Backupot használni. Ez a konzisztens állapotú virtuális gépek helyreállítása szükséges.

Helyileg redundáns tárolás használatakor a pillanatkép létrehozása után azonnal során eltérő tárfiók kell másolnia a pillanatképeket. A Másolás célja lehet helyileg redundáns tárolás fiók egy másik régióban található, ami azt eredményezi, hogy a példány éppen egy távoli régióban. A pillanatkép-írásvédett georedundáns tárolás fiókhoz ugyanabban a régióban is másolhatja. Ebben az esetben a pillanatkép lazily replikálódik a távoli másodlagos régióba. A biztonsági másolat a másolás után védett az katasztrófák az elsődleges helyen, és a replikálás befejeződik.

A növekményes pillanatképek hatékony vész-Helyreállítási másolása, tekintse át a utasításait [készítsen biztonsági másolatot az Azure nem felügyelt méretű lemezek növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md).

![Készítsen biztonsági másolatot az Azure nem felügyelt méretű lemezek növekményes pillanatképek][2]

### <a name="recovery-from-snapshots"></a>A pillanatképek helyreállítás

Pillanatkép lekéréséhez másolja azt, hogy egy új blob legyen. A pillanatkép másolása az elsődleges fiókból, másolhatja a pillanatkép keresztül a pillanatkép alap blob. Ez a folyamat visszaállítja a lemez a pillanatkép. Ez a folyamat a pillanatkép előléptetni néven ismert. A pillanatkép biztonsági másolatából egy írásvédett georedundáns tárfiókot, ha egy másodlagos fiókból másolása egy elsődleges fiókhoz kell másolnia. A pillanatkép által másolhatja [PowerShell-lel](../articles/storage/common/storage-powershell-guide-full.md) vagy az AzCopy segédprogram segítségével. További információkért lásd: [adatátvitel az AzCopy parancssori segédprogram](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).

Több lemezzel rendelkező virtuális gépekhez át kell másolnia a azonos koordinált visszaállítási pont részét képező összes pillanatképet. Miután a pillanatképek írható VHD-blobok másolta, a blobok segítségével hozza létre újra a virtuális Gépet a sablon használatával a virtuális gép.

## <a name="other-options"></a>Egyéb beállítások

### <a name="sql-server"></a>SQL Server

A virtuális gépen futó SQL Server biztonsági mentése Azure Blob-tároló vagy egy fájlra az SQL Server adatbázis megosztása a saját beépített funkciókkal rendelkezik. Ha a tárfiók georedundáns tárolás vagy írásvédett georedundáns tárolás, érheti el ezeket a biztonsági másolatok a tárfiók másodlagos adatközpontba legyen katasztrófahelyzet esetén, előbb tárgyalt módon azonos korlátozásokkal. További információkért lásd: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépekben](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). A biztonsági mentése és visszaállítása, továbbá [SQL Server AlwaysOn rendelkezésre állási csoportok](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) is fenntartható a másodlagos replikákat az adatbázisok. Ez jelentősen csökkenti a vész-helyreállítási idő.

## <a name="other-considerations"></a>Egyéb szempontok

Ez a cikk rendelkezik tárgyalt, készítsen biztonsági másolatot, vagy a pillanatképek készítése a virtuális gépek és a lemezek katasztrófa utáni helyreállítás támogatásához meg, és használjon-e az adatok helyreállításához. Az Azure Resource Manager modellt sokan használnak a sablonok létrehozása a virtuális gépek és egyéb infrastruktúra az Azure-ban. Egy sablon használatával hozzon létre egy virtuális Gépet, amely minden esetben azonos konfigurációval rendelkezik. Egyéni lemezképek létrehozásához a virtuális gépek használatakor is meg kell győződnie arról, hogy a képek használatával védetté tett egy írásvédett georedundáns tárfiókot tárolja azokat.

Következésképpen a biztonsági mentési folyamat két dolgot kombinációja lehet:

- Az adatok biztonsági mentésének (lemezeket).
- Biztonsági másolatot készteni a konfigurációról, (sablonok és egyéni lemezképek).

Attól függően, hogy a biztonsági mentési lehetőséget választja lehetséges, hogy a biztonsági mentés az adatok és a konfiguráció kezeléséhez, vagy a biztonsági mentési szolgáltatás előfordulhat, hogy kezelni tudja, hogy az Ön.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>A függelék: A adatredundanciát hatásának ismertetése

Az Azure storage-fiókok, érdemes vonatkozó vész-helyreállítási adatredundanciát három típusa van: helyileg redundáns, georedundáns vagy georedundáns olvasási hozzáférést. 

Helyileg redundáns tárolás három másolatot ugyanabban az adatközpontban lévő adatokat megőrzi. Ha a virtuális gép írja az adatokat, minden három másolatot frissülnek, ahhoz, azok egyezését közöljük visszaérkezik a hívóhoz sikeres van. A lemez védett a helyi hibák, mert nem valószínű, hogy minden három másolatot egyszerre is érint. Helyileg redundáns tárolás esetén nincs nincs georedundancia, a lemez nem védett a végzetes hibák, amelyek hatással lehetnek a teljes adatközpont vagy tárolási egység.

Georedundáns tárolás és a írásvédett georedundáns tárolás három másolatot az adatok az elsődleges régióban, Ön által kiválasztott maradnak. Az adatok három további példányait a megfelelő másodlagos régióban, az Azure-ban beállított megmaradnak. Például ha USA nyugati régiója adatokat tárolja, az adatok replikálódnak USA keleti régiója. Másolás megőrzési aszinkron módon történik, és nincs némi késés frissítéseit az elsődleges és másodlagos helyek között. Replikáit a másodlagos hely lemezeit konzisztens lemez alapú (a késés), de a több aktív lemez replikái nem feltétlenül szinkronban vannak egymással. Ahhoz, hogy konzisztens replikák több lemezre, alkalmazáskonzisztens pillanatképek van szükség.

A georedundáns tárolást és az írásvédett georedundáns tárolás közötti fő különbség a, hogy az írásvédett georedundáns tárolás, a másodlagos másolat olvasható bármikor. Ha a probléma, hogy ez a beállítás az elsődleges régióban lévő adatokat nem érhető el, az Azure-csapat mindent hozzáférés visszaállításához. Amíg az elsődleges nem működik, ha írásvédett georedundáns tárolás engedélyezve van, akkor férhet hozzá az adatokhoz, a másodlagos adatközpontba. Ezért ha azt tervezi, hogy olvassa el a replikából, amíg az elsődleges kiszolgáló nem érhető el, majd írásvédett georedundáns tárolás figyelembe kell venni.

Ha egy jelentős leállás kell, előfordulhat, hogy egy földrajzi feladatátvételt indít az az Azure-csapat, és módosítsa az elsődleges DNS-bejegyzéseket, mutasson a másodlagos tárterületre. Ezen a ponton Ha georedundáns tárolás vagy írásvédett georedundáns tárolás engedélyezve van, hozzáférhet a régióban kell lennie a másodlagos használt adatokat. Ez azt jelenti Ha a tárfiók georedundáns tárolás és probléma merül fel, végezheti el a másodlagos tárterületre csak, ha egy földrajzi feladatátvételt.

További információkért lásd: [Mi a teendő, ha egy Azure Storage esetleges leálláskor](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>Microsoft szabályozza, hogy a feladatátvételt hajt végre. Feladatátvételi nem szabályozott egy tárfiókot, így nem, amelyekről az egyes ügyfelek. Meghatározott tárfiókok vagy a virtuális gép lemezeinek katasztrófa utáni helyreállítás végrehajtásához a jelen cikkben korábban ismertetett módszerek kell használnia.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
