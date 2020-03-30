---
title: Vásárlási modellek
description: Ismerje meg az Azure SQL Database számára elérhető vásárlási modelleket.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255989"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Válasszon a virtuális mag és a DTU vásárlási modellek közül

Az Azure SQL Database segítségével egyszerűen vásárolhat egy teljes körűen felügyelt platformot szolgáltatásként (PaaS) adatbázismotorként, amely megfelel az Ön teljesítményének és költségigényeinek. Az Azure SQL Database-hez kiválasztott telepítési modelltől függően kiválaszthatja az Ön számára működő vásárlási modellt:

- [Virtuális mag (vCore)-alapú vásárlási modell](sql-database-service-tiers-vcore.md) (ajánlott). Ez a beszerzési modell egy kiépített számítási szint és egy kiszolgáló nélküli számítási szint közötti választást biztosít. A kiosztott számítási szint, válassza ki a számítási erőforrások pontos mennyiségét, amelyek mindig ki vannak építve a számítási feladatokhoz. A kiszolgáló nélküli számítási szint, adja meg a számítási erőforrások automatikus skálázása egy konfigurálható számítási tartományban. Ezzel a számítási szinttel automatikusan szüneteltetheti és folytathatja az adatbázist a számítási feladatok alapján. A virtuálismag egységára időegységenként alacsonyabb a kiosztott számítási rétegben, mint a kiszolgáló nélküli számítási rétegben.
- [Adatbázistranzakció-egység (DTU)-alapú beszerzési modell](sql-database-service-tiers-dtu.md). Ez a vásárlási modell a közös számítási feladatokhoz kiegyensúlyozott, csomagban tárolt számítási és tárolási csomagokat biztosít.

Különböző beszerzési modellek érhetők el a különböző Azure SQL Database-telepítési modellekhez:

- Az [Azure SQL Database](sql-database-technical-overview.md) egyetlen [adatbázis-](sql-database-single-databases-manage.md) és [rugalmas készlet-telepítési](sql-database-elastic-pool.md) lehetőségei a [DTU-alapú vásárlási modellt](sql-database-service-tiers-dtu.md) és a [virtuálismag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)is kínálják.
- Az Azure SQL Database [felügyelt példánytelepítési](sql-database-managed-instance.md) lehetősége csak a [virtuálismag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)kínálja.
- A [nagy kapacitású szolgáltatási szint](sql-database-service-tier-hyperscale.md) a [virtuálismag-alapú vásárlási modellt](sql-database-service-tiers-vcore.md)használó egyetlen adatbázishoz érhető el.

Az alábbi táblázat és diagram a virtuálismag-alapú és a DTU-alapú vásárlási modelleket hasonlítja össze és hasonlítja össze:

|**Beszerzési modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell számítási, tárolási és I/O-erőforrások egy csomagban történő mérésén alapul. A számítási méretek egyedi adatbázisok dru-iban, rugalmas adatbázistranzakció-egységekben (eDT-k) vannak kifejezve rugalmas készletekhez. A DTUS-okról és az eDT-kről további információt a [DTUS-ok és az eDTO-k című témakörben talál.](sql-database-purchase-models.md#dtu-based-purchasing-model)|A legjobb az egyszerű, előre konfigurált erőforrás-beállításokat használó ügyfelek számára.|
|vCore-alapú modell|Ez a modell lehetővé teszi, hogy önállóan válassza ki a számítási és tárolási erőforrásokat. A virtuálismag-alapú vásárlási modell lehetővé teszi az [Azure Hybrid Benefit for SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) használatát is, hogy költségmegtakarítást eredményezzön.|A legjobb azoknak az ügyfeleknek, akik értékelik a rugalmasságot, az ellenőrzést és az átláthatóságot.|
||||  

![árképzési modell összehasonlítása](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Számítási költségek

### <a name="provisioned-compute-costs"></a>Kiépített számítási költségek

A kiosztott számítási szint a számítási költség tükrözi az alkalmazás hoz kiépített teljes számítási kapacitás.

Az üzleti legkritikusabb szolgáltatási szinten automatikusan lefoglalunk legalább 3 replikát. A számítási erőforrások további lefoglalásának tükrözése érdekében a virtuálismag-alapú vásárlási modell ára körülbelül 2,7-szer magasabb az üzleti legkritikusabb szolgáltatási szinten, mint az általános célú szolgáltatási szinten. Hasonlóképpen a magasabb gb-onkénti tárolási ár az üzleti legkritikusabb szolgáltatási szinten tükrözi a magasabb I/O-korlátok at és az SSD-tároló alacsonyabb késését.

A biztonsági mentési tárolás költsége megegyezik az üzleti legkritikusabb szolgáltatási szint és az általános célú szolgáltatási szint esetében, mivel mindkét réteg szabványos tárhelyet használ a biztonsági mentések számára.

### <a name="serverless-compute-costs"></a>Kiszolgáló nélküli számítási költségek

A számítási kapacitás meghatározásának és a kiszolgáló nélküli számítási szint költségeinek meghatározásáról az SQL Database kiszolgáló nélküli című [témakörben](sql-database-serverless.md)található.

## <a name="storage-costs"></a>Tárolási költségek

A különböző típusú tárhelyek számlázása eltérő. Az adattároláshoz a kiválasztott maximális adatbázis- vagy készletméret alapján kell fizetnie a kiosztott tárterületért. A költségek csak akkor változnak, ha csökkenti vagy növeli ezt a maximumot. A biztonsági mentési tároló a példány automatikus biztonsági másolataihoz van társítva, és dinamikusan van lefoglalva. A biztonsági mentési megőrzési időszak növelése növeli a példány által felhasznált biztonsági mentési tárolót.

Alapértelmezés szerint az adatbázisok 7 napos automatikus biztonsági mentéseit a rendszer egy olvasási hozzáférésű georedundáns storage (RA-GRS) szabványos Blob storage-fiókba másolja. Ezt a tárolót heti teljes biztonsági mentések, napi különbözeti biztonsági mentések és tranzakciónapló-biztonsági mentések használják, amelyeket 5 percenként másol. A tranzakciós naplók mérete az adatbázis változásának ütemétől függ. Az adatbázis méretének 100 százalékával egyenlő minimális tárterület ingyenesen biztosított. A biztonsági mentési tárhely további felhasználása havonta GB-ban van felszámítva.

A tárolási árakról további információt az árképzési oldalon [talál.](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A virtuális mag (vCore) egy logikai processzort jelöl, és lehetőséget kínál a hardvergenerációk és a hardver fizikai jellemzői (például a magok száma, a memória és a tároló mérete) közötti választásra. A virtuálismag-alapú vásárlási modell rugalmasságot, vezérlést, az egyes erőforrás-felhasználások átláthatóságát és a helyszíni számítási feladatok követelményeinek felhőbe fordítását egyszerű módon biztosítja. Ez a modell lehetővé teszi a számítási, memória- és tárolási erőforrások kiválasztását a számítási feladatok igényei alapján.

A virtuálismag-alapú vásárlási modellben választhat az [általános cél](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) és az üzleti [legkritikusabb](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) szolgáltatásszintek között [az egyes adatbázisokhoz,](sql-database-single-database-scale.md) [rugalmas készletekhez](sql-database-elastic-pool.md)és [felügyelt példányokhoz.](sql-database-managed-instance.md) Az egyes adatbázisok esetében kiválaszthatja a [Nagy kapacitású szolgáltatásszintet](sql-database-service-tier-hyperscale.md)is.

A virtuálismag-alapú vásárlási modell lehetővé teszi, hogy önállóan válassza ki a számítási és tárolási erőforrásokat, párosítsa a helyszíni teljesítményt, és optimalizálja az árat. A virtuálismag-alapú vásárlási modellben a következőkért kell fizetnie:

- Számítási erőforrások (a szolgáltatási szint + a virtuális magok száma és a memória mennyisége + a hardver generálása).
- Az adatok típusa és mennyisége, valamint a naplótárolás.
- Tartalék tároló (RA-GRS).

> [!IMPORTANT]
> Számítási erőforrások, I/O, valamint az adatok és a napló tárolása adatbázisonként vagy rugalmas készletenként kerül felszámolásra. A biztonsági mentési tároló minden adatbázisonként felszámolásra kerül. A felügyelt példánydíjakról a [Felügyelt példányok című témakörben](sql-database-managed-instance.md)talál további információt.
> **Régió korlátai:** A támogatott régiók aktuális listáját a régiónként elérhető termékek című témakörben [tájékán tájékasz.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Ha egy olyan régióban szeretne felügyelt példányt létrehozni, amely jelenleg nem támogatott, [küldjön támogatási kérelmet az Azure Portalon keresztül.](quota-increase-request.md)

Ha az egyetlen adatbázis vagy rugalmas készlet több mint 300 DDO-t használ fel, a virtuálismag-alapú vásárlási modellre való konvertálás csökkentheti a költségeket. Konvertálhat a választott API használatával vagy az Azure Portal használatával, állásidő nélkül. A konverzió azonban nem szükséges, és nem történik meg automatikusan. Ha a DTU-alapú vásárlási modell megfelel a teljesítmény- és üzleti követelményeknek, továbbra is használja azt.

Ha a DTU-alapú vásárlási modellről virtuálismag-alapú vásárlási modellre szeretne konvertálni, válassza ki a számítási méretet a következő hüvelykujjszabályok használatával:

- A standard szinten minden 100 DIT legalább 1 virtuális magot igényel az általános célú szolgáltatási szinten.
- A prémium szinten minden 125 DIT legalább 1 virtuális magot igényel az üzleti legkritikusabb szolgáltatási szinten.

> [!NOTE]
> A DTU virtuálismag méretezési irányelvei hozzávetőlegesek, és a céladatbázis-szolgáltatás célkitűzésének kezdeti becsléséhez szolgálnak. A céladatbázis optimális konfigurációja munkaterheléstől függ. 
> 
> Az optimális ár/teljesítmény arány eléréséhez a virtuálismag-modell rugalmasságának kihasználására lehet szükség a virtuális magok, a [hardvergenerálás](sql-database-service-tiers-vcore.md#hardware-generations), a [szolgáltatás-](sql-database-service-tiers-vcore.md#service-tiers) és [számítási](sql-database-service-tiers-vcore.md#compute-tiers) szintek, valamint az adatbázis-konfigurációs paraméterek, például a [párhuzamosság maximális mértékének](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)beállításához.

## <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

Az adatbázis-tranzakciós egység (DTU) a processzor, a memória, az olvasás és az írás kevert mértékét jelöli. A DTU-alapú vásárlási modell egy sor előre konfigurált számítási erőforrások és a beépített tárolási meghajtó különböző szintű alkalmazás teljesítményét kínál. Ha az előre konfigurált csomag és a fix fizetések egyszerűségét részesíti előnyben minden hónapban, a DTU-alapú modell jobban megfelelhet az Igényeinek.

A DTU-alapú vásárlási modellben választhat az alapszintű, a standard és a prémium szintű szolgáltatási szintek között [egy-és](sql-database-single-database-scale.md) [rugalmas készletek hez.](sql-database-elastic-pool.md) A DTU-alapú vásárlási modell nem érhető el a [felügyelt példányok.](sql-database-managed-instance.md)

### <a name="database-transaction-units-dtus"></a>Adatbázis-tranzakcióegységek (DTUs)Database transaction units (DTUs)

Egyetlen adatbázis egy adott számítási méret egy [szolgáltatási szinten](sql-database-single-database-scale.md)belül, a Microsoft garantálja az erőforrások egy bizonyos szintjét az adott adatbázis (függetlenül bármely más adatbázis az Azure-felhőben). Ez a garancia kiszámítható teljesítményszintet biztosít. Az adatbázishoz lefoglalt erőforrások mennyisége a DIT-ek számaként kerül kiszámításra, és a számítási, tárolási és I/O-erőforrások kötegelt mértéke.

Ezek az erőforrások közötti arány eredetileg határozza meg egy [online tranzakciófeldolgozási (OLTP) benchmark számítási feladat](sql-database-benchmark-overview.md) célja, hogy jellemző a valós OLTP számítási feladatok. Ha a számítási feladatok meghaladják az erőforrások bármelyikét, az átviteli teljesítmény szabályozása csökken, ami lassabb teljesítményt és időtúllépések.

A számítási feladatok által használt erőforrások nincsenek hatással az Azure-felhőben más SQL-adatbázisok számára elérhető erőforrásokra. Hasonlóképpen a más számítási feladatok által használt erőforrások nincsenek hatással az SQL-adatbázis számára elérhető erőforrásokra.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

A DIT-ek a leghasznosabbak az Azure SQL-adatbázisokhoz különböző számítási méretekben és szolgáltatásszinteken lefoglalt relatív erőforrások megértéséhez. Példa:

- A DIT-ek megduplázása az adatbázis számítási méretének növelésével megegyezik az adatbázis számára rendelkezésre álló erőforrások készletének megduplázásával.
- Az 1750 DTU-val rendelkező prémium szintű P11-adatbázis 350-szer több DTU számítási teljesítményt biztosít, mint egy 5 DTU-val rendelkező alapszintű szolgáltatásiréteg-adatbázis.  

Ha mélyebb betekintést szeretne nyerni a számítási feladatok erőforrás-felhasználásába, használja a [lekérdezés-teljesítmény elemzéseket](sql-database-query-performance.md) a következőkre:

- Azonosítsa a leggyakoribb lekérdezéseket a CPU/időtartam/végrehajtás száma szerint, amely potenciálisan jobb teljesítményre hangolható. Például egy I/O-igényes lekérdezés tmilyen előnyökkel járhat [a memórián belüli optimalizálási technikák,](sql-database-in-memory.md) hogy jobban kihasználják a rendelkezésre álló memória egy bizonyos szolgáltatási szint és a számítási méret.
- Részletezze a lekérdezés részleteit a szöveg és az erőforrás-használat előzményeinek megtekintéséhez.
- Az [SQL Database Advisor](sql-database-advisor.md)által végrehajtott műveleteket bemutató teljesítményhangolási javaslatok elérése.

### <a name="elastic-database-transaction-units-edtus"></a>Rugalmas adatbázistranzakció-egységek (eDT-k)

A mindig elérhető SQL-adatbázisok esetében ahelyett, hogy olyan dedikált erőforrásokat (DT-ket) biztosítana, amelyekre nem mindig van szükség, ezeket az adatbázisokat [egy rugalmas készletbe](sql-database-elastic-pool.md)helyezheti. A rugalmas készletben lévő adatbázisok egyetlen Azure SQL Database-kiszolgálón találhatók, és erőforrások készletét osztják meg.

A rugalmas készletben lévő megosztott erőforrásokat rugalmas adatbázis-tranzakcióegységek (eDT-k) mérik. A rugalmas készletek egyszerű, költséghatékony megoldást kínálnak több, széles körben változó és előre nem látható használati mintákkal rendelkező adatbázis teljesítménycéljainak kezelésére. Egy rugalmas készlet garantálja, hogy az összes erőforrást nem lehet felhasználni egy adatbázis a készletben, miközben biztosítja, hogy a készlet minden adatbázis a készletben mindig rendelkezik a szükséges erőforrások minimális mennyisége.

A készlet meghatározott számú eDTE-t kap egy meghatározott árhoz. A rugalmas készletben az egyes adatbázisok automatikus skálázása a konfigurált határokon belül. A nagyobb terhelés alatt álló adatbázisok több eDIT-et használnak fel az igények kielégítésére. A könnyebb terhelés alatt álló adatbázisok kevesebb eD-TO-t használnak fel. A terhelés nélküli adatbázisok nem használnak fel eDT-ket. Mivel az erőforrások a teljes készlet, nem pedig adatbázisonként vannak kiépítve, a rugalmas készletek leegyszerűsítik a felügyeleti feladatokat, és kiszámítható költségvetést biztosítanak a készletszámára.

További eDTE-ket adhat hozzá egy meglévő készlethez adatbázis-leállás nélkül, és nincs hatással a készletben lévő adatbázisokra. Hasonlóképpen, ha már nincs szüksége extra eDT-kre, távolítsa el őket egy meglévő készletből bármikor. Adatbázisokat bármikor hozzáadhat vagy kivonhat egy készletből. Az eDDO-k más adatbázisok számára történő lefoglalásához korlátozza az adatbázis által nagy terhelés alatt használható eDTO-k számát. Ha egy adatbázis konzisztensen használja az erőforrásokat, helyezze át a készletből, és konfigurálja egyetlen adatbázisként, amely kiszámítható mennyiségű szükséges erőforrást használ.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>A számítási feladatokhoz szükséges DIT-ek számának meghatározása

Ha egy meglévő helyszíni vagy SQL Server virtuálisgép-munkaterhelést szeretne áttelepíteni az Azure SQL Database-be, a [DTU kalkulátor](https://dtucalculator.azurewebsites.net/) segítségével közelítheti meg a szükséges DTU-k számát. Egy meglévő Azure SQL Database-számítási feladatok, [lekérdezés-teljesítmény insights](sql-database-query-performance.md) segítségével megismerheti az adatbázis-erőforrás-felhasználás (DTO-k) és mélyebb betekintést a számítási feladatok optimalizálása. A [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamikus felügyeleti nézet (DMV) lehetővé teszi az elmúlt óra erőforrás-felhasználásának megtekintését. A [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) katalógusnézet az elmúlt 14 nap erőforrás-felhasználását jeleníti meg, de alacsonyabb, ötperces átlagok kal.

### <a name="determine-dtu-utilization"></a>DTU-kihasználtság meghatározása

A DTU/eDTU kihasználtság átlagos százalékos arányának meghatározásához az adatbázis vagy egy rugalmas készlet DTU/eDTU korlátjához képest, használja a következő képletet:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

A képlet bemeneti értékei a [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)és [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV-kből szerezhetők be. Más szóval, a DTU/eDTU-kihasználtság százalékos arányának meghatározásához egy adatbázis vagy egy rugalmas készlet DTU/eDTU korlátjához képest válassza ki a legnagyobb százalékértéket a következők közül: `avg_cpu_percent`, `avg_data_io_percent`, és `avg_log_write_percent` egy adott időpontban.

> [!NOTE]
> Az adatbázis DTU-korlátját az adatbázis számára rendelkezésre álló PROCESSZOR, olvasás, írás és memória határozza meg. Mivel azonban az SQL Server adatbázis-motorja általában az összes rendelkezésre `avg_memory_usage_percent` álló memóriát használja az adatgyorsítótárhoz a teljesítmény javítása érdekében, az érték általában az aktuális adatbázisterheléstől függetlenül közel 100%. Ezért annak ellenére, hogy a memória közvetve befolyásolja a DTU-korlátot, nem használja a DTU kihasználtsági képletben.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Az erőforrások rugalmas készletéből származó számítási feladatok

A készletek kiválóan alkalmasak az alacsony erőforrás-kihasználtsági átlaggal és viszonylag ritka kihasználtsági csúcsokkal rendelkező adatbázisokhoz. További információ: [Mikor érdemes megfontolni egy SQL Database rugalmas készlet?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Hardvergenerációk a DTU-alapú vásárlási modellben

A DTU-alapú vásárlási modellben az ügyfelek nem választhatják ki az adatbázisaikhoz használt hardvergenerálást. Míg egy adott adatbázis általában hosszú ideig (általában több hónapig) marad egy adott hardvergeneráción, vannak olyan események, amelyek miatt az adatbázis átkerül egy másik hardvergenerációba.

Például egy adatbázis áthelyezhető egy másik hardvergenerációba, ha felfelé vagy lefelé skálázódik egy másik szolgáltatási célkitűzésre, vagy ha egy adatközpont aktuális infrastruktúrája megközelíti a kapacitáskorlátokat, vagy ha a jelenleg használt hardver tanavan élettartama miatt leszerelték.

Ha egy adatbázist áthelyeznek különböző hardverekbe, a számítási feladatok teljesítménye változhat. A DTU-modell garantálja, hogy a [DTU-teljesítményszámítási](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) számítási feladatok átviteli és válaszideje lényegében azonos marad, ahogy az adatbázis egy másik hardvergenerációba kerül, feltéve, hogy a szolgáltatási célkitűzése (a DTU-k száma) ugyanaz marad. 

Az Azure SQL Database-ben futó ügyfél-munkaterhelések széles spektrumában azonban a különböző hardverek használatának hatása ugyanahhoz a szolgáltatási célhoz hangsúlyosabb lehet. A különböző számítási feladatok számára előnyösek lesznek a különböző hardverkonfigurációk és -szolgáltatások. Ezért a DTU-referenciaértéktől eltérő számítási feladatok esetében teljesítménykülönbségek et láthat, ha az adatbázis egyik hardvergenerációról a másikra kerül.

Például egy olyan alkalmazás, amely érzékeny a hálózati késésre, jobb teljesítményt láthat a Gen5 hardveren és a Gen4-en az Accelerated Networking használata miatt a Gen5-ben, de egy intenzív olvasási IO-t használó alkalmazás jobb teljesítményt láthat a Gen4 hardverés a Gen5 a Gen4-en lévő magonkénti memóriaarány.

A hardveres változásokra érzékeny munkaterheléssel rendelkező ügyfelek, illetve az adatbázisuk hardvergenerálásának beállítását szabályozó ügyfelek a [virtuálismag-modell](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) segítségével választhatják meg az elsődleges hardvergenerálást az adatbázis létrehozása és méretezése során. A virtuálismag-modellben az egyes szolgáltatáscélok erőforráskorlátai az egyes hardvergenerálásokon dokumentálva vannak, mind [az egyes adatbázisok,](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) mind a [rugalmas készletek](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)esetében. A virtuálismag-modell hardvergenerációiról a [Hardvergenerációk](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)című témakörben talál további információt.

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYAKORI KÉRDÉSEK)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Offline állapotba kell helyezni az alkalmazásomat a DTU-alapú szolgáltatási szintről a virtuálismag-alapú szolgáltatási szintre való konvertáláshoz?

Nem. Nem kell az alkalmazást offline állapotba helyeznie. Az új szolgáltatási szintek egy egyszerű online konverziós módszert kínálnak, amely hasonló az adatbázisok frissítésének jelenlegi folyamatához a standardról a prémium szolgáltatási szintre, és fordítva. Ezt a konverziót az Azure Portalon, a PowerShellen, az Azure CLI-n, a T-SQL-en vagy a REST API-n keresztül indíthatja el. Lásd: [Egyetlen adatbázisok kezelése](sql-database-single-database-scale.md) és rugalmas készletek [kezelése.](sql-database-elastic-pool.md)

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Konvertálhatok egy adatbázist egy szolgáltatásszintről a virtuálismag-alapú vásárlási modellben egy szolgáltatási szintre a DTU-alapú vásárlási modellben?

Igen, az Azure Portal, a PowerShell, az Azure CLI, a T-SQL vagy a REST API használatával könnyedén konvertálhatja az adatbázist bármely támogatott teljesítménycélkitűzésre. Lásd: [Egyetlen adatbázisok kezelése](sql-database-single-database-scale.md) és rugalmas készletek [kezelése.](sql-database-elastic-pool.md)

## <a name="next-steps"></a>További lépések

- A virtuálismag-alapú vásárlási modellről további információt a [virtuálismag-alapú vásárlási modell ben](sql-database-service-tiers-vcore.md)talál.
- A DTU-alapú vásárlási modellről további információt a [DTU-alapú vásárlási modell ben](sql-database-service-tiers-dtu.md)talál.
