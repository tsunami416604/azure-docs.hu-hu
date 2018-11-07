---
title: Az Azure SQL Database rendkívüli – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések ügyfeleknek adott válaszok és kérdezze meg a nagy kapacitású szolgáltatásszint – más néven nagy kapacitású adatbázis egy Azure SQL-adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/17/2018
ms.openlocfilehash: 661fd36b4451238f488bff1db60a901a8dabd5aa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242160"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Az Azure SQL nagy kapacitású adatbázisokkal kapcsolatos gyakori kérdések

Ez a cikk ismerteti az ügyfelek az Azure SQL Database rendkívüli szolgáltatási rétegben, más néven (jelenleg nyilvános előzetes verzióban) egy nagy kapacitású adatbázis egy adatbázis a mérlegeli gyakran feltett kérdésekre adott válaszokat. Ez a cikk ismerteti, hogy a nagy kapacitású támogatja, és a kereszt-funkció szolgáltatások kompatibilisek az SQL Database rendkívüli általában.

- Ez a GYIK olvasók, akik egy rövid ismertetése a nagy kapacitású szolgáltatásszint és konkrét kérdése van, és a problémákat, választ keres szól.
- Ez a GYIK nem arra készült, egy és vagy egy SQL Database rendkívüli database használata a kérdésre. Azt javasoljuk, tekintse meg a [Azure SQL Database rendkívüli](sql-database-service-tier-hyperscale.md) dokumentációját.

## <a name="general-questions"></a>Általános kérdések

### <a name="what-is-a-hyperscale-database"></a>Mi az, hogy egy nagy kapacitású adatbázist

Egy nagy kapacitású adatbázisa egy Azure SQL database, a nagy kapacitású szolgáltatási rétegben, amely a nagy kapacitású kibővített tárolási technológia használatával. Egy nagy kapacitású adatbázis támogatja a legfeljebb 100 TB adatot, és nagy átviteli sebességet és teljesítményt, valamint a munkaterhelés követelményeinek megfelelően módosítani a gyors méretezést biztosít. Méretezés az átlátható az alkalmazás – kapcsolat, a lekérdezés-feldolgozás, és így tovább, munka, mint más SQL-adatbázis.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Milyen típusú erőforrásokat és beszerzési modell támogatja a nagy kapacitású

A nagy kapacitású szolgáltatási réteg csak a Virtuálismag-alapú vásárlási modell segítségével az Azure SQL Database önálló adatbázisok számára érhető el.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>A nagy kapacitású szolgáltatásszint Miben különbözik az általános célú és a kritikus fontosságú üzleti szolgáltatási szintekről származó

Rétegek elsősorban különbözteti meg a Virtuálismag-alapú szolgáltatás a rendelkezésre állás, a tárolási típust és az iops-érték alapján.

- Az általános célú szolgáltatásszint alkalmas a legtöbb üzleti számítási feladathoz, amelyek egy elosztott terhelésű számítási és tárolási lehetőségek, ha i/o várakozási ideje vagy a feladatátvételi idők nem állnak a prioritás.
- A nagy kapacitású szolgáltatásszint nagyon nagy méretű adatbázisok számítási feladatokhoz van optimalizálva.
- Az üzletileg kritikus szolgáltatási szinten üzleti számítási feladathoz megfelelő prioritást IO-késés esetén.

| | Erőforrás típusa | Általános rendeltetés |  Rugalmas skálázás | Üzletileg kritikus |
|:---|:---:|:---:|:---:|:---:|:---:|
| **A legjobb** |Összes|  A legtöbb üzleti számítási feladathoz. Ajánlatok költségvetés-orientált elosztott terhelésű számítási és tárolási lehetőségek. | Alkalmazások nagy mennyiségű adat kapacitásigények, és lehetővé teszi az automatikus skálázás storage és a méretezési csoport fluidly számítási. | Magas tranzakciós sebességű és legkisebb késés IO OLTP alkalmazások. Biztosítja a legmagasabb hibatűrést használatával több hibákhoz, elkülönített replika.|
|  **Erőforrás típusa** ||Önálló adatbázis / rugalmas készlet / a felügyelt példány | Önálló adatbázis | Önálló adatbázis / rugalmas készlet / a felügyelt példány |
| **Számítási mérete**|Önálló adatbázis / rugalmas készlet * | 1 és 80 virtuális magok | 1 és 80 virtuális magok * | 1 és 80 virtuális magok |
| |Felügyelt példány | 8, 16, 24, 32, 40, 64, 80 virtuális magok | – | 8, 16, 24, 32, 40, 64, 80 virtuális magok |
| **Tárolás típusa** | Összes |Prémium szintű távtároló (példányonként) | Helyi SSD-gyorsítótárban (példányonként) megszüntetése összekapcsolt tárolóval | Adathozzáférésnek köszönhetően gyors helyi SSD-tárolás (példányonként) |
| **Tároló mérete** | Önálló adatbázis / rugalmas készlet | 5 GB – 4 TB-IG | 100 TB-ig | 5 GB – 4 TB-IG |
| | Felügyelt példány  | 32 GB – 8 TB | – | 32 GB – 4 TB-IG |
| **I/o-teljesítmény** | Egyetlen adatbázisonként ** | A 7000-es maximális IOPS / virtuális mag 500 IOPS | Ismeretlen még | 5000 iops-érték 200 000 maximális iops|
| | Felügyelt példány | Fájl méretétől függ. | – | A felügyelt példány: Fájl méretétől függ|
|**Rendelkezésre állás**|Összes|1 replika, olvasási méretű, nem a helyi gyorsítótár | Több replika, legfeljebb 15 olvasási szintű, a részleges helyi gyorsítótár | 3 replika, 1 olvasási szintű, a zónaredundáns magas rendelkezésre ÁLLÁSÚ, teljes helyi gyorsítótár |
|**Biztonsági másolatok**|Összes|RA-GRS, 7 – 35 nap (alapértelmezés szerint 7 nap)| RA-GRS, 7 – 35 nap (alapértelmezés szerint 7 nap), állandó idő pont – in0time recovery (PITR) | RA-GRS, 7 – 35 nap (alapértelmezés szerint 7 nap) |

\* Nem támogatott a nagy kapacitású szolgáltatási szinten a rugalmas készletek

### <a name="who-should-use-the-hyperscale-service-tier"></a>Ki kell használni a nagy kapacitású szolgáltatásszintet

A nagy kapacitású szolgáltatásszint elsősorban ügyfelek esetében, akik nagy méretű helyszíni SQL Server-adatbázisok vannak és azok az alkalmazások modernizálása a felhőbe való áthelyezésével vagy ügyfelek esetében, akik már használják az Azure SQL Database, és jelentősen bontsa ki a kívánt a lehetséges adatbázis növekedésének megfelelően. Nagy kapacitású ügyfelek, akik nagy teljesítményű és a nagy mértékű skálázhatóságot hledání is szól. Nagy kapacitású a következőket kínálja:

- Akár 100 TB-os adatbázisméret támogatása
- Gyors adatbázisok biztonsági mentése, függetlenül az adatbázis mérete (biztonsági mentések pillanatképeket alapulnak)
- Függetlenül az adatbázis mérete (a visszaállítás a pillanatképeket is) visszaállít gyors adatbázis
- Nagyobb átviteli sebesség eredményeihez az adatbázis méretétől függetlenül gyors tranzakció véglegesítési idő
- Olvasási horizontális felskálázási kiszervezheti a olvasási számítási feladat egy vagy több csak olvasható csomópontot, és ritkáról gyakori elérésű standbys.
- Gyors, felskálázott számítási állandó időben, nagyobb teljesítményű a nehéz számítási feladatok kezelésére, és ezután vertikális leskálázás, állandó időben kell. Ez hasonlít egy, a P11 P6 közötti felfelé és lefelé skálázás például azonban sokkal gyorsabb, mint ez nem egy adatművelet mérete nem.

### <a name="what-regions-currently-support-hyperscale"></a>Mely régiók jelenleg támogatja a nagy kapacitású

Nagy kapacitású jelenleg érhető el az önálló adatbázisok a következő régiókban: USA nyugati US1, USA 2. nyugati régiója, USA keleti US1, USA középső RÉGIÓJA, Nyugat-Európa, Észak-Európa, Egyesült Királyság nyugati régiója, Délkelet-Ázsiában, kelet-japán, Korea középső régiója, Délkelet-Ausztrália és Kelet-Ausztrália.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Logikai kiszolgálónként több nagy kapacitású adatbázist hozhat létre

Igen. További információkért és a egy logikai kiszolgálón nagy kapacitású adatbázisok száma korlátlan, lásd: [egy logikai kiszolgálón egyetlen vagy készletezett adatbázisok SQL Database erőforrás-korlátozások](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>Mik azok a nagy kapacitású adatbázis jellemző

Az SQL Database rendkívüli architektúrát biztosít nagy teljesítményű és az átviteli sebesség támogatása adatbázis nagy méretek mellett. A pontos teljesítmény profil és a jellemzők nem áll rendelkezésre a nyilvános előzetes verzióban.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Mit jelent a nagy kapacitású adatbázis méretezhetősége

Az SQL Database rendkívüli biztosít a számítási feladatok igény szerint a gyors méretezhetőséghez.

- **Felfelé és lefelé skálázás**

  Nagy kapacitású erőforrások, például CPU és memória szempontjából az elsődleges számítási méret vertikális és majd vertikális leskálázás, állandó időben. A tároló meg van osztva, mert vertikális felskálázása és leskálázást mérete nem egy adat-művelet.  
- **Bejövő és kimenő méretezése**

  A nagy kapacitású az olvasási kérések kiszolgálására használhatja egy vagy több további számítási csomópontok létrehozására is kap. Ez azt jelenti, hogy segítségével a számítási csomópontok csak olvasható csomópontként a az elsődleges számítási a olvasási számítási feladatok kiszervezése. A csak olvasható, ezek a csomópontok segédanyagként is használható, készenléti a feladatátvétel esetén keresztül az elsődleges kiszolgálóról.

  Az egyes ezek további számítási csomópontok állandó időben teheti meg, és a egy online művelet kiépítése. Ezek csak olvasható számítási csomópontot állításával kapcsolódhat a `ApplicationIntent` argumentum a kapcsolati karakterláncot a `read_only`. Kapcsolatok jelölése `read-only` automatikusan kapcsolódóak pedig az egyik a csak olvasható számítási csomópontot is.

## <a name="deep-dive-questions"></a>A kérdések részletes bemutatása

### <a name="can-i-mix-hyperscale-and-single-databases-a-my-logical-server"></a>Kombinálhatom a nagy kapacitású és az önálló adatbázisok egy saját logikai kiszolgáló

Igen.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Nagy kapacitású igényel saját alkalmazás programozási modell módosítani

Az alkalmazás programozási modell nem, mivel marad. A kapcsolati karakterláncot a szokott módon, és a többi normál üzemmódban használatával kommunikálhat az Azure SQL database.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Milyen tranzakció elkülönítési szintet gazdakiszolgálói lesznek az SQL Database rendkívüli adatbázis alapértelmezett

Az elsődleges csomóponton a tranzakció elkülönítési szint RCSI (olvasás véglegesített pillanatkép-elkülönítés). Az olvasási másodlagos csomópontján pillanatkép készítése a elkülönítési szintet.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Hozzáadhatom a saját helyszíni vagy IaaS SQL Server-licencét, SQL Database rendkívüli

Igen, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) nagy kapacitású érhető el. 1 nagy kapacitású virtuális magra jogosult minden SQL Server Standard core leképezheti. Minden egyes SQL Server Enterprise processzormag-alapú leképezheti 4 rendkívüli virtuális magot kapnak. Másodlagos replika már nincs szüksége egy SQL-licenc. Az Azure Hybrid Benefit ár automatikusan lépnek érvénybe, olvasási méretű a replika (másodlagos).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Milyen jellegű munkaterheléseket helyeznek az SQL Database rendkívüli készült

Az SQL Database rendkívüli támogatja az összes SQL Server számítási feladatok, de OLTP elsősorban van optimalizálva. A hibrid és analitikai (adatpiac) számítási feladatok, valamint is tenné.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Hogyan választhat az Azure SQL Data Warehouse és az SQL Database rendkívüli között

Ha Ön jelenleg az SQL Server használata az adattárházak, interaktív elemzési lekérdezések futtatása az SQL Database rendkívüli remek választás mert viszonylag kis adattárházak (például néhány TB legfeljebb 10's TB-os) is üzemeltethet alacsonyabb költségek és az adatok áttelepíthetők  adatraktár-számítási feladat az SQL Database rendkívüli a T-SQL-kód módosítása nélkül.

Ha az összetett lekérdezések nagy méretű data analytics fut, és a Parallel Data warehouse-ba (PDW), Teradata vagy más nagymértékben párhuzamos processzor (MPP)) adattárházak, az SQL Data warehouse-bA lehet, hogy a legjobb választás.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Az SQL Database rendkívüli számítási kérdések

### <a name="can-i-pause-my-compute-at-any-time"></a>Szüneteltetheti a számítási bármikor

Nem.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Is üzembe helyezhető egy extra RAM memóriával rendelkező számítási a memóriaigényes számítási feladathoz

Nem. Több RAM Memóriát kap, a nagyobb számítási méretre frissíteni kell. Gen4 Gen5 hardver képest több RAM Memóriát biztosít. További információkért lásd: [tárolási és számítási méretek rendkívüli](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-preview).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Több számítási csomópontok különböző méretű helyezhetek üzembe

Nem.

### <a name="how-many-read-scale-replicas-are-supported"></a>Hány olvasási szintű replika támogatott

Nyilvános előzetes verzióban elérhető a nagy kapacitású adatbázisok jönnek létre az egyik olvasási szintű replika (összesen két replika) alapértelmezés szerint. Ha szeretne hozzáadni vagy eltávolítani, olvasási méretű replikák, nyújtson be támogatási kérelmet az Azure portal használatával.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Magas rendelkezésre állás érdekében do I kell üzembe további számítási csomópontokon

A nagy kapacitású adatbázisokat a magas rendelkezésre állású tárolási szinten biztosítja. Csak akkor kell egy replika magas rendelkezésre állást biztosít. A számítási replika nem működik, ha új replikát automatikusan létrejön az adatvesztés.

Azonban ha csak egy replikát, ez eltarthat egy ideig hozhat létre a helyi gyorsítótár az új replikát a feladatátvételt követően. A gyorsítótár-Újraépítés fázis során az adatbázis olvas adatokat közvetlenül a lap kiszolgálók, csökkentett teljesítményű IOPS és lekérdezéseivel kapcsolatos teljesítményt eredményez.

Magas rendelkezésre állást igénylő kritikus fontosságú alkalmazások esetén, beleértve a elsődleges számítási csomópontot (alapértelmezett) legalább 2 számítási csomópontok kell kiépítenie. Ezzel a módszerrel érhető el a készenléti egy feladatátvétel esetén sem.

## <a name="data-size-and-storage-questions"></a>Méret- és tárolási kérdésekre

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Mi az a maximális méretű támogatott az SQL Database rendkívüli

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Mi az a nagy kapacitású a tranzakciónapló mérete

A tranzakciónaplóban nagy kapacitású érték gyakorlatilag végtelen. Nem kell aggódnia elfogyását naplózási hely, amely rendelkezik a napló nagy átviteli sebesség rendszeren. A napló sebességet szabályozott előfordulhat, hogy folyamatos agresszív számítási feladatokhoz. A maximális és átlagos jelentkezzen generációs sebesség még nem ismert (továbbra is előzetes verzióban érhető el).

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Az ideiglenes adatbázis-adatbázisomat növekedésével méretezése

A `tempdb` adatbázis található helyi SSD-tárolóval és a kiépítendő számítási mérete alapján történik. A `tempdb` van optimalizálva, és a maximális teljesítmény előnyök biztosításának céljából leírva. A `tempdb` mérete nem konfigurálható, alárendelt tárolórendszer által van kezelve.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Nem az adatbázis méretének automatikus növelése, vagy tegye rendelkezik-e az adatfájlok méretének kezelése

Az adatbázis mérete automatikusan növekszik, beszúrása vagy betöltési több adatot.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Mi az a legkisebb adatbázis mérete, amely támogatja az SQL Database rendkívüli, vagy kezdődik

5 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Milyen lépésekben does saját adatbázis mérete növelhető

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Az SQL Database rendkívüli a tárterületet, helyi vagy távoli

Nagy kapacitású az adatfájlokat az Azure standard szintű tárolóban vannak tárolva. Adatok a helyi SSD-tárolóval, a számítási csomópontok közeli gépeken erősen gyorsítótárazva. Emellett a számítási csomópont helyi SSD és a memóriában (Pufferkészletben, és így tovább), az adatok lekérése a csomópontok távoli gyakoriságának csökkentése érdekében a gyorsítótár van.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kezelése vagy adja meg a fájlok és fájlcsoportok nagy kapacitású

Nem
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Is üzembe helyezhető egy rögzített korlát az adatmennyiség növekedése a saját adatbázis

Nem

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Hogyan vannak adatfájlok leírva az SQL Database rendkívüli

Az adatfájlok lap kiszolgálók szabályozza. Az adatok méretének növekedésével az adatfájlok és a kapcsolódó lapra csomópontok kerülnek.

### <a name="is-database-shrink-supported"></a>Adatbázis zsugorítás támogatott

Nem

### <a name="is-database-compression-supported"></a>Adatbázis tömörítése támogatott

Igen

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Ha egy hatalmas táblában van, nem tábla adatok lekérése elosztva több adatfájlon

Igen. Az egy adott táblán társított lapok is végül az adatok több fájlokat, amelyek az ugyanazon fájlcsoport minden része. Az SQL Server használja a [arányos fájlfeltöltési stratégia](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) az adatok elosztása az adatfájlokat.

## <a name="data-migration-questions"></a>Adatok áttelepítése kérdések

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>A nagy kapacitású szolgáltatásszinthez lehet váltani a meglévő Azure SQL-adatbázisok

Igen. Nagy kapacitású áthelyezheti a meglévő Azure SQL-adatbázisok. Nyilvános előzetes verzióban érhető el ez az egyirányú áttelepítést. Nagy kapacitású adatbázisok egy másik szolgáltatásszinthez nem válthat. Javasoljuk, hogy másolatot készít az éles adatbázisokat, és a nagy kapacitású át a megvalósíthatósági fogalmak (próbaverziójú).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Nagy kapacitású adatbázisok áthelyezhető a többi kiadás

Nem. Nyilvános előzetes verzióban egy nagy kapacitású adatbázis nem helyezhető át egy másik szolgáltatási szint.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Tegye elveszítem a bármely funkciók és képességek a nagy kapacitású szolgáltatásszint való migrálás után

Igen. Azure SQL Database funkcióit nem támogatottak a nagy kapacitású nyilvános előzetesben, beleértve többek között a TDE és hosszú távú adatmegőrzés. Miután áttelepítette az adatbázisok, nagy kapacitású, ezek a funkciók tovább működni.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Áthelyezhető a helyszíni SQL Server-adatbázis vagy az SQL Server virtuális gép adatbázis nagy kapacitású

Igen. Az összes meglévő migrálási technológiák használatával nagy kapacitású, beleértve a BACPAC-, tranzakciós replikáció, a logikai Adatbetöltési át. Lásd még a [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtown-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Mi az a Seattle belvárosában helyszíni áttelepítés során vagy a virtuális gépek környezetének nagy kapacitású, és hogyan lehet minimalizálása,

Állásidő ugyanaz, mint a leállás akkor, ha az adatbázisokat az Azure SQL Database egy adatbázis is. Használhat [tranzakciós replikáció](replication-to-sql-database.md#data-migration-scenario
) állásidejű migrálás akár néhány TB méretű adatbázisokhoz minimalizálása érdekében. A nagyon nagy méretű adatbázisból (10 + TB), érdemes lehet az ADF, Spark vagy egyéb adatok mozgását technológiák használata az adatok áttelepítéséhez.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Mennyi idő lenne, akkor hajtsa végre a megfelelő X területén az SQL Database rendkívüli adatmennyiség

Még nem ismert (továbbra is az előzetes verzió)

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Képes az adatbeolvasás a blob storage-ban, és gyors load (például a Polybase és az SQL Data Warehouse)

Olvassa el az adatok Azure Storage-ból, és betöltése az adatok betöltése egy nagy kapacitású adatbázisba (ugyanúgy, mint a rendszeres önálló adatbázis megteheti). A Polybase a az Azure SQL Database jelenleg nem támogatott. A Polybase segítségével teheti [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) vagy Spark-feladatok futtatása [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) együtt a [Spark SQL-összekötő](sql-database-spark-connector.md). A Spark-összekötő az SQL tömeges beszúrási támogatja.

Egyszerű helyreállítási vagy a tömeges naplózási modell nem támogatott a nagy kapacitású. A teljes helyreállítási modell szükséges magas rendelkezésre állást biztosít. Azonban a nagy kapacitású még jobban biztosít az adatok betöltését Hibaarány, szemben az új naplófájl architektúra miatt egyetlen Azure SQL-adatbázishoz.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Az SQL Database rendkívüli lehetővé teszi nagy mennyiségű adat feldolgozására több csomópont kiépítése

Nem. Az SQL Database rendkívüli egy állapotáttelepítési pont architektúra és nem egy aszimmetrikus többprocesszoros vagy egy több főkiszolgálós architektúra. Csak olvasási számítási feladatok horizontális több replika hozhat létre.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Mi az a legrégebbi verzió fogja az SQL Database rendkívüli támogatási áttelepítést a SQL Server

SQL Server 2005. További információkért lásd: [egy adatbázist vagy egy készletezett adatbázis áttelepítése](sql-database-cloud-migrate.md#migrate-to-a-single-database-or-a-pooled-database). A kompatibilitási problémákat, tekintse meg a [adatbázis-migrálás kompatibilitási problémáinak megoldása](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Támogatja az SQL Database rendkívüli Sarki fény, MySQL, Oracle, DB2 és más adatbázis platformokon, például más adatforrásokból áttelepítést

Igen. Különböző adatforrásokból származó hamarosan más, az SQL Server logikai áttelepítési igényel. Használhatja a [Azure Database Migration Service](../dms/dms-overview.md) logikai áttelepítés.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Üzleti folytonosság és vészhelyreállítás helyreállítási kérdések

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Milyen SZOLGÁLTATÁSSZINTEKET egy nagy kapacitású adatbázis-okat

Általában az SLA nem áll rendelkezésre a nyilvános előzetes verzióban. Azonban a nagy kapacitású biztosít magas rendelkezésre állás az aktuális SQL DB-ajánlatok azonos szintű. Lásd: [SLA](https://azure.microsoft.com/support/legal/sla/).

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Az adatbázis biztonsági mentéséhez felügyelt velem az Azure SQL Database szolgáltatás

Igen

### <a name="how-often-are-the-database-backups-taken"></a>Milyen gyakran kerül az adatbázis biztonsági mentéséhez

Nincsenek nem hagyományos teljes, differenciális és az SQL Database rendkívüli adatbázisok naplóalapú biztonsági mentések. Ehelyett az adatfájlok rendszeres pillanatképek vannak, és a napló, amely akkor jön létre, egyszerűen őrződnek meg, mivel a megőrzési időszak konfigurált vagy az Ön számára elérhető.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Nem támogatja az SQL Database rendkívüli időponthoz kötött visszaállítás

Igen

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Mi a helyreállítási időkorlátot (RPO) / helyreállítási időre vonatkozó célkitűzés (RTO) a biztonsági mentési és visszaállítási az SQL Database rendkívüli

Az rpo-t 0 perc. Az RTO célja kevesebb mint 10 perc adatbázis méretétől függetlenül. A nyilvános előzetes verzióban tapasztalhat hosszabb helyreállítási idő.

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Befolyásolják a nagy adatbázisok biztonsági másolatait a saját elsődleges számítási teljesítményt

Nem. Biztonsági másolatok a tárolóalrendszer kezeli, és kihasználhatja a pillanatképeket. A felhasználó az elsődleges adatkötetekre nem kerültek.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>A geo-visszaállítás, az SQL Database rendkívüli adatbázis hajthat végre

Nem, nem a nyilvános előzetes verzióban.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Beállíthatok Georeplikációs adatbázis az SQL Database rendkívüli

Nem, nem a nyilvános előzetes verzióban.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Hajtsa végre a másodlagos számítási csomópontok jelenik meg az SQL Database rendkívüli georeplikált

Nem, nem a nyilvános előzetes verzióban.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Biztonsági mentés az SQL Database rendkívüli adatbázis és állítsa vissza a helyszíni kiszolgálón vagy SQL Server-beli virtuális gépen

Nem. A tárolási formátum nagy kapacitású adatbázisok eltér a hagyományos SQL Server, és nem szabályozza a biztonsági mentések, vagy nem tudja elérni őket. Az adatok egy SQL Database rendkívüli adatbázisból, vagy az export service használata, vagy BCP plusz parancsfájlok.

## <a name="cross-feature-questions"></a>Kérdések a szolgáltatás közötti

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Tegye elveszítem a bármely funkciók és képességek a nagy kapacitású szolgáltatásszint való migrálás után

Igen. Azure SQL Database funkcióit nem támogatottak a nagy kapacitású nyilvános előzetesben, beleértve többek között a TDE és hosszú távú adatmegőrzés. Miután áttelepítette az adatbázisok, nagy kapacitású, ezek a funkciók tovább működni.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Az SQL Database rendkívüli lesz a Polybase használata

Nem. Polybase az Azure SQL Database nem támogatott.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Rendelkezik a szükséges számítási R és python támogatását

Nem. R és Python nem támogatottak az Azure SQL Database-ben.

### <a name="are-the-compute-nodes-containerized"></a>A számítási csomópontok konténeralapú vannak

Nem. Az adatbázis egy virtuális gép számítási és tároló nem található.

## <a name="performance-questions"></a>Teljesítmény-kérdések

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Milyen átviteli sebességre képes leküldéses legnagyobb SQL Database rendkívüli számítási

Még nem ismert (továbbra is az előzetes verzió)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Hány IOPS legnagyobb SQL Database rendkívüli számítási kapacitást

Még nem ismert (továbbra is az előzetes verzió)

### <a name="does-my-throughput-get-affected-by-backups"></a>Nem az átviteli sebesség első által érintett biztonsági mentéseket

Nem. Számítási különválik a tárolási réteg számítási gyakorolt hatás elkerülése érdekében.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Nem az átviteli sebesség lekérése is hatással további számítási csomópontok üzembe helyezhető

Mivel a tároló meg van osztva, és nem történik az elsődleges és másodlagos számítási csomópontok, közötti közvetlen fizikai többszöröződnek technikailag az átviteli sebességet elsődleges csomóponton érinti olvasási szintű csomópontok hozzáadásával. Azonban, hogy előfordulhat, hogy szabályozás folyamatos agresszív munkaterhelés való bejelentkezés engedélyezése másodlagos csomópontot, és továbbítja pótlólag lap kiszolgálók a alkalmazni, és elkerülheti a másodlagos csomópont hibás olvasási teljesítmény.

## <a name="scalability-questions"></a>Méretezésre vonatkozó kérdések

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Mennyi ideig szeretné időt vesz igénybe a felfelé és a egy számítási csomóponton a méretezési csoport

Néhány perc alatt

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Saját adatbázis kapcsolat nélküli módban van, amíg folyamatban van a méretezése felfelé és lefelé művelet

Nem. A skálázás felfelé és lefelé elérhető lesz.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Számíthatok kapcsolat dobja el, ha a skálázási művelet folyamatban van

Skálázás felfelé vagy lefelé meglévő kapcsolatokat, ha feladatátvétel történik, a számítási csomópontra, a célméretet az eldobott eredményez. Kapcsolat csepp olvasható replikák hozzáadásával nem okoz.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Vertikális felskálázása és lefelé a számítási csomópontok automatikus vagy végfelhasználói által aktivált művelet

Végfelhasználói. Nem automatikus.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Does saját `tempb` is nő, ahogy a számítási vertikális felskálázása

Igen. Automatikusan vertikálisan ideiglenes db számítási növekedésével.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Több elsődleges számítási erőforrások, például egy több főkiszolgálós rendszert, amelyben több elsődleges számítási aknázni ösztönözheti a magasabb szintű egyidejűség helyezhetek üzembe

Nem. Csak az elsődleges számítási csomópont olvasási/írási kérelmeket fogadó. Másodlagos számítási csomópontok csak olvasási kérelmek fogadásához.

## <a name="read-scale-questions"></a>Olvasási méretezési kérdések

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Hány másodlagos számítási csomópontot is üzembe helyezhető

Nyilvános előzetes verzióban alapértelmezés szerint hozunk létre 2 replika nagy kapacitású adatbázisok számára. Ha azt szeretné, módosíthatja a replikák száma, nyújtson be támogatási kérelmet az Azure portal használatával.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Hogyan létesíthetek kapcsolatot a másodlagos számítási csomópontok felé

Ezek csak olvasható számítási csomópontot állításával kapcsolódhat a `ApplicationIntent` argumentum a kapcsolati karakterláncot a `read_only`. Kapcsolatok jelölése `read-only` automatikusan kapcsolódóak pedig az egyik a csak olvasható számítási csomópontot is.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Hozhat létre egy dedikált végpontot az olvasási szintű replika

Nem. Nyilvános előzetes verzióban csak csatlakozhat olvasási szintű replika megadásával `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>A rendszer feladata olvasási számítási intelligens terheléselosztás

Nem. Előzetes verzióban elérhető a csak olvasási munkaterhelés újra irányított egy véletlenszerű olvasási szintű replikára.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Felfelé és lefelé a másodlagos számítási csomópontok az elsődleges számítási függetlenül is méretezheti

Nem, nem a nyilvános előzetes verzióban.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Kaphatok különböző ideiglenes db méretezése a saját elsődleges számítási és a saját másodlagos számítási csomópontot

Nem. A `tempdb` van konfigurálva, a számítási méret kiépítés alapján a nyilvános előzetes verzióban a másodlagos számítási csomópontok mérete megegyezik az elsődleges számítási rendszer.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Adhatok hozzá indexeket és a saját másodlagos nézetek számítási csomópontok

Nem. Nagy kapacitású adatbázisok megosztott tárolóhelyet, ami azt jelenti, hogy az összes számítási csomópontok, tekintse meg az azonos táblák, indexek és nézeteket. Ha azt szeretné, hogy további indexek olvasási másodlagos – optimalizált hozzá kell adnia őket az elsődleges először.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Mennyi késleltetési van a későbbiekben az elsődleges és másodlagos számítási csomópont között

Az időpont egy tranzakció elkötelezte magát az elsődleges, a napló sebességet, attól függően, lehet azonnali vagy alacsony ezredmásodpercben.

## <a name="next-steps"></a>További lépések

A nagy kapacitású szolgáltatásszint kapcsolatos további információkért lásd: [szolgáltatásszint nagy kapacitású (előzetes verzió)](sql-database-service-tier-hyperscale.md).
