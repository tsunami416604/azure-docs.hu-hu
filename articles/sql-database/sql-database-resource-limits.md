---
title: "Az Azure SQL adatbázis erőforrás-korlátozások |} Microsoft Docs"
description: "Ez a lap néhány általános erőforrás-korlátozások az Azure SQL Database ismerteti."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/28/2018
ms.author: carlrab
ms.openlocfilehash: eea4362e33ff2587758601758db463ffa82382b3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-resource-limits"></a>Az Azure SQL Database erőforrás korlátok

> [!IMPORTANT]
> Erőforrás-korlátok, Azure SQL adatbázis felügyelt példánya, lásd: [SQL felügyelt adatbázispéldány](sql-database-managed-instance.md#managed-instance-service-tier).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Önálló adatbázist: tárterületet és teljesítményszintek

Az önálló adatbázisok az alábbi táblázatokban minden szolgáltatás és teljesítményszintet szintjén egyszeri adatbázis rendelkezésre álló erőforrások. A szolgáltatási rétegben, teljesítményszintet és egy önálló adatbázis használatára vonatkozó mennyisége állíthatja be a [Azure-portálon](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), a [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), vagy a [REST API-t](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Önálló adatbázist: tároló méretének módosítása

- A DTU ára egy önálló adatbázis tárolási további költségek nélkül bizonyos mennyiségű tartalmaz. Megnövelt tárhely meghaladó szerepel az a maximális méretkorlát fel és 1 TB 250 GB-os lépésekben, majd a 256 GB felett 1 TB-os lépésekben akár további költségek telepíthető. Belefoglalt tárolási összegeket és a maximális méretkorlát [önálló adatbázist: tárterületet és teljesítményszintek](#single-database-storage-sizes-and-performance-levels).
- Egyetlen-adatbázis – megnövelt tárhely növeli a maximális méret használatával helyezhetők a [Azure-portálon](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure Parancssori felület](/cli/azure/sql/db#az_sql_db_update), vagy a [REST API-t](/rest/api/sql/databases/update).
- Egyetlen-adatbázis – megnövelt tárhely ára érték a megnövelt tárhely megszorozza a szolgáltatási rétegben – megnövelt tárhely egységárát. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Önálló adatbázist: módosítsa a dtu-k

Után először válassza háttérszínnek. a szolgáltatási rétegben, teljesítményszintet és mennyisége, méretezheti egy önálló adatbázis felfelé vagy lefelé a tényleges élmény használatával dinamikusan alapján a [Azure-portálon](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [Azure CLI](/cli/azure/sql/db#az_sql_db_update), vagy a [REST API-t](/rest/api/sql/databases/update). 

A következő videó bemutatja, hogy dinamikusan a teljesítményszint növelése egy önálló adatbázis elérhető dtu-i módosítását.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Az adatbázis szolgáltatásszintjének és/vagy teljesítményszintjének megváltoztatásakor egy replika jön létre az eredeti adatbázisról az új teljesítményszinten, majd a rendszer átáll a replikához való csatlakozásra. A folyamat során nem történik adatvesztés, de a replikára való átálláskor egy pillanatra az adatbázis felé irányuló kapcsolatok le lesznek tiltva, így lehet, hogy néhány folyamatban lévő tranzakció vissza lesz állítva. A kapcsoló-over idő hosszával platformonként változó, de általában a 4 másodperc érték kisebb, mint 30 másodperc 99 %-ában. Ha nagy számát, a pillanatnyilag kapcsolatok útban tranzakciók le vannak tiltva, akkor lehet, hogy a kapcsoló-over időtartama hosszabb. 

A teljes felskálázási folyamat időtartama az adatbázis a módosítás előtti és utáni méretétől és szolgáltatásszintjétől függ. Például változik, a, vagy a szabványos szolgáltatásréteg, 250 GB-os adatbázis hat órán belül kell végrehajtani. Adatbázis méretének teljesítményszintet belül a prémium szolgáltatásszintet változik a méretezett három órán belül kell végrehajtani.

> [!TIP]
> A-e-mailekben műveletek figyeléséről lásd:: [SQL REST API használatával műveleteinek a felügyeletét,](/rest/api/sql/Operations/List), [parancssori felület használatával műveleteinek a felügyeletét,](/cli/azure/sql/db/op), [műveletek T-SQL használatával figyelheti](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) , és ez a két PowerShell-parancsok: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) és [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Ha frissíti a szolgáltatási szint vagy a teljesítmény a magasabb, az adatbázis maximális méretét nem növekszik, hacsak Ön kifejezetten megad egy nagyobb méretű (maxsize).
* Egy adatbázis megállapításában, a használt adatbázis-terület kisebb, mint a megengedett maximális méret a célként megadott szolgáltatás és teljesítményszintet szinten kell lennie. 
* Amikor alacsonyabb verziójúra változtatása a **prémium** számára a **szabványos** réteg, egy extra storage költséget akkor használható, ha mind a cél teljesítményszintjét támogatott (1) az adatbázis maximális méretét, és (2) a maximális mérete meghaladja a a cél teljesítményszintjét belefoglalt tárolási mennyisége. Például ha 500 GB maximális mérettel P1 adatbázis való S3 van downsized, majd egy – megnövelt tárhely költség érvényes óta S3 támogatja 500 GB-os maximális méretét, és a belefoglalt mennyisége csak 250 GB. Igen a megnövelt tárhely mérete 500 GB – 250 GB = 250 GB. Az árképzés további tárhelyet, lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges használt lemezterület mérete kisebb, mint a belefoglalt mennyisége, majd ez kapcsolódik további költség elkerülhető az adatbázis maximális méretét csökkentve a belefoglalt legkevesebb. 
* Ha az adatbázis frissítése [georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, frissítse a másodlagos adatbázisok a kívánt teljesítményt réteghez csak azután frissítse az elsődleges adatbázis (általános útmutatást). Egy másik alkalmazásra történő verziófrissítés a másodlagos adatbázist frissíti először szükség.
* Amikor alacsonyabb verziójúra változtatása adatbázis [georeplikáció](sql-database-geo-replication-portal.md) engedélyezve van, alacsonyabb a kívánt teljesítményt réteghez elsődleges adatbázisainak előtt alacsonyabb verziójúra változtatása a másodlagos adatbázis (általános útmutatást). Visszaminősítése másik kiadásra, az elsődleges adatbázis visszaminősítése először szükség.
* A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatásszintek esetében. Ha alacsonyabb verzióra való visszatérést meg, hogy a **alapvető** réteg, alacsonyabb biztonsági mentés megőrzési idővel van – lásd [Azure SQL adatbázis biztonsági másolatait](sql-database-automated-backups.md).
* Az adatbázis új tulajdonságai csak akkor lesznek alkalmazva, ha a módosítások befejeződtek.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Önálló adatbázist: P11 és P15, ha a maximális méret 1 TB-nál nagyobb korlátozásai

Egy 1 TB-nál nagyobb, a P11 és P15 adatbázis a következő régiókban támogatott maximális mérete: US East2, USA nyugati régiója, Velünk – (kormányzati) Virginia, Nyugat-Európa, Németország központi, Dél Kelet-Ázsiában, kelet-japán, Kelet-Ausztrália, Kanada központi és Kanada keleti. Az alábbi szempontokat és korlátozásokat 1 TB-nál nagyobb maximális mérettel P11 és P15 adatbázisokra érvényesek:

- Ha 1 TB-nál nagyobb méretű egy adatbázist (értéke 4 TB-os vagy 4096 GB) létrehozásához, a Létrehozás parancs hibával meghiúsul, ha az adatbázis egy nem támogatott régióban lett kiépítve.
- A támogatott régiók egyikéhez sem található meglévő P11 és P15 adatbázisokhoz, növelheti a maximális tárhelyet túl az 1 TB-os lépésekben 256 GB akár 4 TB. Ha egy nagyobb méretű támogatott-e az adott régióban, használja a [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) működik, vagy vizsgálja meg az adatbázis mérete az Azure portálon. Egy meglévő P11 vagy P15 frissítése adatbázis csak hajtható végre egy kiszolgálószintű fő bejelentkezéssel, vagy a dbmanager adatbázis-szerepkör tagjai. 
- Ha egy frissítési művelet végrehajtása egy támogatott régióban a konfigurációja azonnal frissül. Az adatbázis online állapotban marad, a frissítési folyamat során. Azonban nem használhatja a teljes mennyisége túl az 1 TB-nyi tárhelyre tárolási csak az aktuális adatbázisfájlok frissítése után az új maximális méretét. A frissítés alatt álló adatbázis méretétől függ szükséges idő hosszát. 
- Létrehozásakor, vagy egy P11 vagy P15 adatbázis frissítése, csak választhat 1 TB méretű és 4 TB méretű maximális méret 256 GB-os lépésekben. Egy P11/P15 létrehozásakor az 1 TB-os alapértelmezett tárolási lehetőség az előre kijelölt. Az adatbázisok a támogatott régiók egyikéhez sem található a tároló maximális 4 TB-os legfeljebb egy új vagy meglévő egyetlen adatbázis növelhető. Minden más területen maximális mérete 1 TB-os fent nem növelhető. Az ár nem változtatja meg, ha 4 TB-nyi tárhelyre belefoglalt választja.
- Ha egy adatbázis maximális méretét értéke nagyobb, mint 1 TB-os, majd azt nem módosítható és 1 TB akkor is, ha a ténylegesen használt tárhely 1 TB alatt. Így akkor nem visszaminősítését egy P11 vagy P15 egy 1 TB-os P11 vagy 1 TB-os P15 vagy alacsonyabb teljesítményt réteg, például a P1-P6 1 TB-nál nagyobb maximális mérettel). Ez a korlátozás vonatkozik a visszaállítási és is-időpontban, például másolás forgatókönyvek georedundáns helyreállítás, hosszú-távú biztonsági mentés-adatmegőrzés és az adatbázis másolása. Amennyiben az adatbázis mérete nagyobb, mint 1 TB-os van beállítva, az adatbázis összes visszaállítási műveletek 1 TB-nál nagyobb maximális mérettel P11/P15 be kell futnia.
- Aktív georeplikáció forgatókönyvek esetén:
   - A georeplikáció kapcsolat beállítása: Ha az elsődleges adatbázis P11 vagy P15, a secondary(ies) is kell P11 vagy P15; alacsonyabb teljesítményt rétegek visszautasítja másodlagos adatbázist, mert azok nem képes a több mint 1 TB.
   - A georeplikáció kapcsolatban az elsődleges adatbázis frissítése: az elsődleges adatbázis több mint 1 TB maximális méretének módosítása váltja ki ezt a változtatást, a másodlagos adatbázison. Két frissítést a változtatás érvénybe léptetéséhez elsődleges sikeresnek kell lennie. A több mint 1 TB-beállítás régió korlátozások érvényesek. Ha a másodlagos régióban, amely nem támogatja a több mint 1 TB-os, az elsődleges nincs frissítve.
- A több mint 1 TB-os P11/P15 adatbázisokkal feltöltését Import/Export szolgáltatás használata nem támogatott. Használja a SqlPackage.exe [importálása](sql-database-import.md) és [exportálása](sql-database-export.md) adatokat.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>A rugalmas készlet: tárterületet és teljesítményszintek

SQL rugalmas adatbáziskészletek az alábbi táblázatok bemutatják a minden egyes szolgáltatást és teljesítményszintet szinten elérhető erőforrások. A szolgáltatási rétegben, a teljesítményszintet és a tárolási összeg használatával állíthatja be a [Azure-portálon](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), a [Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), vagy a [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Az egyes adatbázisokat rugalmas készletek erőforrás korlátai által megszabott megegyeznek a általában a dtu-inak száma és a szolgáltatási réteg alapján készletek kívül önálló adatbázisok. A maximális párhuzamos munkavállalók S2 adatbázis például 120 munkavállalók. Igen a maximális párhuzamos munkavállalók-adatbázis egy Standard adatbáziskészletben is 120 munkavállalók esetén pedig a készletben lévő adatbázisonként maximális DTU 50 dtu-i (amely egyenértékű S2).

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Ha egy rugalmas készlet minden DTU-ja használatban van, akkor a készletben található minden adatbázis ugyanannyi erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet egyenlő erőforrás-megosztása hozzáadódik az egyes adatbázisok számára máshonnan garantált erőforrások mennyiségéhez, ha a minimális DTU/adatbázis érték nem 0-ra van állítva.

### <a name="database-properties-for-pooled-databases"></a>Készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat ismerteti a készletezett adatbázisok tulajdonságait.

| Tulajdonság | Leírás |
|:--- |:--- |
| eDTU-k maximális száma adatbázisonként |A készletben található adatbázisok bármelyike által használható eDTU-k maximális száma (az elérhetőség a készletben található további adatbázisok kihasználtságától függ). Az eDTU adatbázisonkénti maximális száma nem garantálja az erőforrásokat az adatbázisok számára. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU-k adatbázisonkénti maximális számát állítsa elég magasra ahhoz, hogy az adatbázis-kihasználtsági csúcsokkal is elbírjon. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket. Például tegyük fel, hogy az adatbázisonkénti felhasználási csúcs 20 eDTU, és a készletben található 100 adatbázisnak egyszerre csak a 20%-a éri el a csúcsot. Ha az eDTU-k adatbázisonkénti maximális száma 20-ra van állítva, akkor észszerű a készletet ötszörösen túlméretezni, és az eDTU-k készletenkénti számát 400-ra állítani. |
| eDTU-k minimális száma adatbázisonként |A készletben található adatbázisok mindegyike számára garantált eDTU-k minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU adatbázisonkénti minimális száma lehet 0, ami egyben az alapértelmezett érték is. Ezen tulajdonság értékeként egy 0 és az adatbázisonkénti átlagosan használt eDTU-k száma közötti mennyiséget adjon meg. A készletben található adatbázisok számának és az eDTU-k adatbázisonkénti minimális számának szorzata nem lehet magasabb az eDTU-k készletenkénti számánál. Például ha egy készletben 20 adatbázis van, és az eDTU-k adatbázisonkénti minimális száma 10-re van állítva, akkor az eDTU-k készletenkénti száma legalább 200 kell, hogy legyen. |
| Adatbázisonként maximális tárolási |A készletben található adatbázisok maximális tárterülete. A készletezett adatbázisok osztoznak a készlet tárterületein, így az adatbázisok számára a készletek fennmaradó tárolókapacitása vagy az adatbázisonkénti engedélyezett tárterület közül a kisebbiknek megfelelő tárterület jut. Az adatbázisonkénti maximális tárkapacitás az adatfájlok maximális méretére vonatkozik, de nem tartalmazza a naplófájlok által használt területet. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>A rugalmas készlet: tároló méretének módosítása

- A rugalmas készlethez tartozó eDTU ár további költségek nélkül tárolási bizonyos mennyiségű tartalmazza. Megnövelt tárhely meghaladó szerepel az a maximális méretkorlát fel és 1 TB 250 GB-os lépésekben, majd a 256 GB felett 1 TB-os lépésekben akár további költségek telepíthető. Belefoglalt tárolási összegeket és a maximális méretkorlát [rugalmas készlet: tárterületet és teljesítményszintek](#elastic-pool-storage-sizes-and-performance-levels).
- Rugalmas készletek – megnövelt tárhely növeli a maximális méret használatával helyezhetők a [Azure-portálon](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [REST API-n ](/rest/api/sql/elasticpools/update).
- Extra storage rugalmas készletek ára érték a megnövelt tárhely megszorozza a szolgáltatási rétegben – megnövelt tárhely egységárát. Extra tároló árának részletekért lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>A rugalmas készlet: edtu-k módosítása

Akkor növelheti vagy csökkentheti a számára egy rugalmas készlet igények erőforrást alapján elérhető erőforrások a [Azure-portálon](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), vagy a [ REST API-t](/rest/api/sql/elasticpools/update).

- Ha Edtu rescaling, adatbázis-kapcsolatok röviden eldobott. Ez az a kívánt viselkedést eredményező beállítást, akkor fordul elő, amikor rescaling dtu-k (nem a készlet) egyetlen adatbázis. A telepítések időtartamát és kapcsolatok megszakadnak adatbázis rescaling műveletek során hatását a részletekért lásd: [Rescaling dtu-i egy önálló adatbázis](#single-database-change-storage-size). 
- Edtu átméretezése idejét a készletben lévő összes adatbázisok által felhasznált tárterület teljes mennyisége függ. Általában a rescaling várakozási átlagolja 90 percig vagy / 100 GB-nál kisebb. Például ha használja a teljes lemezterület a készletben található összes adatbázis 200 GB-os, akkor a várt késését a készlet rescaling 3 óra vagy annál kisebb. Bizonyos esetekben a szabványos vagy Basic szint belül a rescaling várakozási függetlenül a használt terület mennyisége a öt perc lehet.
- Módosítsa a minimális Edtu / adatbázis vagy a maximális edtu-k adatbázisonkénti időtartamot általában öt perc vagy kisebb.
- Ha Edtu downsizing, használt készlet mérete kisebb, mint a megengedett maximális méret a célként megadott szolgáltatás réteg és a készlet edtu / kell lennie.
- Edtu rescaling, amikor egy extra tárolási költségű érvényes, ha a készlet (1) a tároló maximális mérete támogatja-e a célként megadott készlethez, és (2) a tároló maximális mérete meghaladja a célkészlettel a belefoglalt tárolási. Például ha egy 100 100 GB maximális mérettel szabványos készlet edtu-ra van downsized egy 50 szabványos készlet edtu-k számára, majd egy – megnövelt tárhely költség érvényes mert célkészlettel támogatja a maximális mérete 100 GB-nál, és a belefoglalt mennyisége csak 50 GB. Igen a megnövelt tárhely mérete 100 GB – 50 GB = 50 GB. Az árképzés további tárhelyet, lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges használt lemezterület mérete kisebb, mint a belefoglalt mennyisége, majd ez kapcsolódik további költség elkerülhető az adatbázis maximális méretét csökkentve a belefoglalt legkevesebb. 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Mi az, hogy a kiszolgálók és adatbázisok maximális számát?

| Maximum | Érték |
| :--- | :--- |
| Adatbázis kiszolgálónként | 5000 |
| Régiónként előfizetésenként kiszolgálók száma | 21 |
|||

> [!IMPORTANT]
> Mivel az adatbázisok számának megközelíti a felső határ az egyes kiszolgáló, a következő akkor fordulhat elő:
> <br> • Növelése késés futó lekérdezések a master adatbázisban.  Ez magában foglalja az erőforrás-kihasználtságának statisztikája sys.resource_stats például nézetek.
> <br> • A felügyeleti műveletek késés növelése és a portál nézőpontok, például az adatbázisok a kiszolgálón számbavétele megjelenítése.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Mi történik az adatbázis és a rugalmas készlet erőforrás korlátok elérése után?

### <a name="compute-dtus-and-edtus"></a>Számítási (Dtu és edtu-k)

(Dtu és edtu-k által mért) adatbázis számítási mértéke túlságosan megnő, ha a késés növekedése lekérdezni, és még akkor is, időtúllépés is. Ilyen körülmények lekérdezések előfordulhat, hogy az a szolgáltatás nem helyezi várólistára, és biztosított erőforrások erőforrás-ként történő végrehajtásnak felszabadul.
Magas számítási kihasználtsági észlelt, amikor megoldás lehetőségek a következők:

- Az adatbázis vagy az adatbázis további Dtu és edtu-k így rugalmas készlet teljesítményszintjének növelését. Lásd: [önálló adatbázist: módosítsa a dtu-k](#single-database-change-dtus) és [rugalmas készlet: edtu-k módosítása](#elastic-pool-change-edtus).
- Minden egyes lekérdezés az erőforrás-használat csökkentésére lekérdezések optimalizálása. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Tárolás

Felhasznált lemezterület adatbázis mérete eléri a maximális méretkorlátot, adatbázis beszúrása és frissítések, amelyek az adatok méretének növelése sikertelen és elküld az ügyfélgépeknek egy [hibaüzenet](sql-database-develop-error-messages.md). Adatbázis-választja ki, és törli továbbra is sikeres.

Amikor magas lemezterület-kihasználás, amikor megoldás lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet vagy módosítsa a teljesítményszintet szerezzen be további része tároló maximális méretének növelését. Lásd: [SQL adatbázis erőforrás korlátok](sql-database-resource-limits.md).
- Ha az adatbázis rugalmas készlethez, majd azt is megteheti az adatbázis helyezheti át a készlet kívül, hogy annak tárolóhelyét nem megosztott a többi adatbázissal.

### <a name="sessions-and-workers-requests"></a>Munkamenetek és alkalmazottak (kérelmek) 

Az egyidejű munkamenetek és alkalmazottak maximális száma a szolgáltatási szint és a teljesítmény szint (Dtu és edtu-k) határozzák meg.  Új kérelmek azért lettek elutasítva, amikor munkamenet vagy munkavégző korlát elérésekor, és az ügyfelek hibaüzenetet kap. Során rendelkezésre álló kapcsolatok száma az alkalmazás által szabályozható, a száma párhuzamos munkavállalók gyakran nehezebben becsléséhez, és szabályozhatja. Ez különösen igaz csúcsterhelési időszakokat amikor adatbázis erőforrás gyűjtésének elérésekor és miatt hosszabb futó lekérdezések munkavállalók egymásra, során. 

Munkamenet vagy munkavégző magas kihasználtsága észlelt, amikor megoldás lehetőségek a következők:
- Az adatbázis vagy a rugalmas készlet szolgáltatási szint vagy a teljesítmény szint növelését. Lásd: [egyetlen adatbázis: tároló méretének módosítása](#single-database-change-storage-size), [egyetlen adatbázis: módosítsa a dtu-k](#single-database-change-dtus), [rugalmas készlet: tároló méretének módosítása](#elastic-pool-change-storage-size), és [rugalmas készlet: edtu-k módosítása ](#elastic-pool-change-edtus).
- Optimalizálás lekérdezések minden egyes lekérdezés az erőforrás-használat csökkentésére, ha nagyobb munkavégző kihasználtsági oka miatt a versengés a számítási erőforrásokat. További információkért lásd: [lekérdezés hangolása/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>További lépések

- Szolgáltatásrétegeiben használt funkciókkal kapcsolatos információkért lásd: [szolgáltatásszintek](sql-database-service-tiers.md).
- Egyetlen adatbázisokkal kapcsolatos információkért lásd: [egy adatbázis-erőforrások](sql-database-resource-limits.md).
- Rugalmas készletek kapcsolatos információkért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
- Általános Azure korlátozását kapcsolatos információkért lásd: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md).
- További információ a Dtu és edtu-k: [Dtu és edtu-k](sql-database-what-is-a-dtu.md).
- A tempdb méretkorlátait kapcsolatos információkért lásd: https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
