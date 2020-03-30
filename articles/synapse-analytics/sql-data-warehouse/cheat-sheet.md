---
title: Cheat lap az Azure Synapse Analytics (korábban SQL DW)
description: Hivatkozásokat és gyakorlati tanácsokat találhat az Azure Synapse Analytics (korábbi SQL DW) megoldásainak gyors létrehozásához.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f2019f8538b6997d8fe802a1e90069e88274eb0c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349123"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Cheat lap az Azure Synapse Analytics (korábban SQL DW)

Ez a cheat sheet hasznos tippeket és gyakorlati tanácsok azure synapse megoldások létrehozásához nyújt hasznos tippeket és gyakorlati tanácsokat. 

A következő ábra egy adattárház tervezésének folyamatát mutatja be:

![Vázlat](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Táblák közötti lekérdezések és műveletek

Ha előre tudja, milyen elsődleges műveleteket és lekérdezéseket futtat majd az adattárházban, figyelembe veheti ezeket az adattárház architektúrájának kialakításakor. Ilyen lekérdezések és műveletek lehetnek többek között a következők:
* Egy vagy két ténytábla egyesítése dimenziótáblákkal, a kombinált tábla szűrése, majd az eredmények összefűzése egy data martba.
* Nagyobb vagy kisebb frissítések elvégzése a tényértékesítésekben.
* Csak adatok hozzáfűzése a táblákhoz.

A művelettípusok előzetes ismerete segít optimalizálni a táblák kialakítását.

## <a name="data-migration"></a>Adatok migrálása

Először töltse be adatait az [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md) vagy az Azure Blob Storage. Ezután a PolyBase segítségével töltse be az adatokat átmeneti táblákba. Használja a következő konfigurációt:

| Tervezés | Ajánlás |
|:--- |:--- |
| Disztribúció | Ciklikus időszeletelés |
| Indexelés | Halommemória |
| Particionálás | None |
| Erőforrásosztály | largerc vagy xlargerc |

Itt további információkat tudhat meg az [adatok migrálásáról](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), az [adatok betöltéséről](design-elt-data-loading.md) és a [kinyerési, betöltési és átalakítási (ELT) folyamatról](design-elt-data-loading.md). 

## <a name="distributed-or-replicated-tables"></a>Elosztott vagy replikált táblák

A tábla tulajdonságaitól függően a következő stratégiákat használja:

| Típus | Kiválóan alkalmas a következőhöz:| Ügyeljen a következő esetekben:|
|:--- |:--- |:--- |
| Replikált | * Kis méretű táblák egy csillag séma kevesebb, mint 2 GB tároló tömörítés után (~ 5x tömörítés) |* Sok írási tranzakciók a táblázatban (mint például a insert, upsert, delete, update)<br></br>* Gyakran módosítja az adattárház egységek (DWU) kiépítését<br></br>* Csak 2-3 oszlopot használ, de a táblázat sok oszlopot tartalmaz<br></br>* Ön index egy replikált tábla |
| Ciklikus időszeletelés (alapértelmezett) | * Ideiglenes / átmeneti tábla<br></br> * Nem nyilvánvaló összekötő kulcs vagy jó jelölt oszlop |* A teljesítmény lassú az adatok mozgása miatt |
| Kivonat | * Tény táblázatok<br></br>* Nagy méretű táblázatok |* A terjesztési kulcs nem frissíthető |

**Tippek:**
* Kezdje ciklikus időszeleteléssel, de haladjon a kivonatoló terjesztési stratégia felé, hogy kihasználhassa a nagymértékben párhuzamos architektúrát.
* Ügyeljen arra, hogy a közös kivonatkulcsoknak ugyanaz legyen az adatformátuma.
* Varchar formátumban ne terjesszen.
* A gyakori csatlakozási műveletekkel rendelkező ténytáblákhoz közös kivonatkulccsal rendelkező dimenziótáblákhoz kivonatterjesztés használható.
* A *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)* segítségével elemezheti az adatokban lévő eltéréseket.
* A *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)* segítségével elemezheti a lekérdezések mögötti adatmozgásokat, monitorozhatja az időszórásokat és módosíthatja a műveletek sorrendjét. Ez a terjesztési stratégia áttekintéséhez hasznos.

További tudnivalók a [replikált táblákról](design-guidance-for-replicated-tables.md) és az [elosztott táblákról](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>A tábla indexelése

Az indexelés a táblák gyors olvasásához hasznos. Egyedi technológiákat alkalmazhat az igényei szerint:

| Típus | Kiválóan alkalmas a következőhöz: | Ügyeljen a következő esetekben:|
|:--- |:--- |:--- |
| Halommemória | * Átmeneti / ideiglenes tábla<br></br>* Kis asztalok kis keres |* Minden keresés ellenőrzi a teljes táblázat |
| Fürtözött index | * Táblázatok akár 100 millió sor<br></br>* Nagy táblázatok (több mint 100 millió sor) csak 1-2 oszlop erősen használt |* Használt egy replikált tábla<br></br>* Van komplex lekérdezések bevonásával több illesztés és csoport műveletek<br></br>* Ön, hogy a frissítéseket az indexelt oszlopok: tart memória |
| Fürtözött oszlopcentrikus index (CCI) (alapértelmezett) | * Nagy asztalok (több mint 100 millió sor) | * Használt egy replikált tábla<br></br>* Ön, hogy hatalmas frissítési műveletek az asztalon<br></br>* Ön túlparticionálja a táblázatot: sorcsoportok nem span különböző terjesztési csomópontok és partíciók |

**Tippek:**
* A fürtözött indexek mellett érdemes lehet nem fürtözött indexet is hozzáadni a szűréshez gyakran használt oszlopokhoz. 
* Ügyeljen arra, hogyan kezeli a memóriát a CCI-vel rendelkező táblákban. Adatok betöltésekor az a cél, hogy a felhasználó (vagy a lekérdezés) nagyméretű erőforrásosztályt használhasson. Kerülje a vágást és sok kis tömörített sorcsoport létrehozását.
* A Gen2-n a teljesítmény maximalizálása érdekében a CCI-táblák gyorsítótárazása a számítási csomópontokon helyben történik.
* CCI esetén a sorcsoportok nem megfelelő tömörítése miatt gyenge lehet a teljesítmény. Ilyen esetben állítsa össze újra vagy rendezze át a CCI-t. Tömörített sorcsoportonként legalább 100 000 sorra van szükség. Az ideális a sorcsoportonként 1 millió sor.
* A növekményes terhelési gyakoriság és méret alapján érdemes lehet automatizálni az indexek átrendezésekor vagy újraépítésekor. A tavaszi nagytakarítás mindig hasznos.
* A sorcsoportokat stratégiai szempontok szerint vágja. Mekkorák a nyitott sorcsoportok? Mennyi adatot tervez betölteni a következő napokban?

További információk az [indexekről](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Particionálás
Particionálhatja a táblát nagyméretű ténytábla esetén (1 milliárd sornál nagyobb). Az esetek 99 százalékában a partíciókulcsnak dátumon kell alapulnia. Kerülje a túlparticionálást fürtözött oszlopcentrikus index esetén.

ELT-t igénylő előkészítési táblák esetén hasznos lehet a particionálás. Ez megkönnyíti az adatok életciklus-felügyeletét.
Kerülje az adatok túlparticionálását, különösen fürtözött oszlopcentrikus indexeken.

További információk a [partíciókról](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Növekményes betöltés

Ha növekményesen fogja betölteni az adatokat, először győződjön meg arról, hogy nagyobb méretű erőforrásosztályokat foglalt le az adatok betöltéséhez.  Ez különösen fontos fürtözött oszlopcentrikus indexekkel rendelkező táblákba való betöltésekor.  További részleteket [az erőforrásosztályokban](resource-classes-for-workload-management.md) talál.  

Javasoljuk, hogy a PolyBase és az ADF V2 segítségével automatizálja az ELT-folyamatokat az adatraktárba.

Az előzményadatok nagy mennyiségű frissítéséhez fontolja meg egy [CTAS](sql-data-warehouse-develop-ctas.md) használatát a táblázatban tartani kívánt adatok írásához az INSERT, UPDATE és DELETE használata helyett.

## <a name="maintain-statistics"></a>Statisztikák karbantartása
 Amíg az automatikus statisztikák nem állnak rendelkezésre, a statisztikák manuális karbantartása szükséges. Fontos a statisztikák frissítése, mivel az adatokban *jelentős* változások történhetnek. Ez segít optimalizálni a lekérdezésterveket. Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, körültekintőbben válassza ki, mely oszlopok rendelkezzenek statisztikákkal. 

A frissítések gyakoriságát is megadhatja. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. A legnagyobb előnnyel az jár, ha a csatlakozások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit készíti el.

További információk a [statisztikákról](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Erőforrásosztály
Az erőforráscsoportok segítségével memóriát foglalnak le a lekérdezésekhez. Ha a lekérdezés vagy a betöltés sebességének növelése érdekében több memóriára van szüksége, magasabb erőforrásosztályokat kell lefoglalnia. A nagyobb erőforrásosztályok használata azonban hatással van a párhuzamos működésre. Ezt érdemes figyelembe venni, mielőtt az összes felhasználót nagyméretű erőforrásosztályba helyezné át.

Ha úgy látja, hogy a lekérdezések túl sokáig tartanak, ellenőrizze, hogy a felhasználók nem nagyméretű erőforrásosztályokban futnak-e. A nagyméretű erőforrás osztályok számos egyidejű helyet foglalnak le, ezért más lekérdezések várólistára helyezését okozhatják.

Végül az [SQL-készlet](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)Gen2 használatával minden erőforrásosztály 2,5-szer több memóriát kap, mint a Gen1.

További információk az [erőforrásosztályokról és a párhuzamos működésről](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Csökkentheti költségeit
Az Azure Synapse egyik legfontosabb jellemzője a [számítási erőforrások kezelése.](sql-data-warehouse-manage-compute-overview.md) Szüneteltetheti az SQL-készletet, ha nem használja, amely leállítja a számítási erőforrások számlázását. Az erőforrásokat a teljesítményigényeinek megfelelően skálázhatja. A szüneteltetést az [Azure Portalon](../../synapse-analytics/sql-data-warehouse/pause-and-resume-compute-portal.md) vagy a [PowerShell-lel](../../synapse-analytics/sql-data-warehouse/pause-and-resume-compute-powershell.md) végezheti el. A skálázáshoz használhatja az [Azure Portalt](quickstart-scale-compute-portal.md), a [PowerShellt](quickstart-scale-compute-powershell.md), a [T-SQL-t](quickstart-scale-compute-tsql.md) vagy egy [REST API-t](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).


Az Azure Synapse egyik legfontosabb jellemzője a [számítási erőforrások kezelése.](sql-data-warehouse-manage-compute-overview.md) Szüneteltetheti az SQL-készletet, ha nem használja, amely leállítja a számítási erőforrások számlázását. Az erőforrásokat a teljesítményigényeinek megfelelően skálázhatja. A szüneteltetést az [Azure Portalon](../../sql-data-warehouse/pause-and-resume-compute-portal.md) vagy a [PowerShell-lel](../../sql-data-warehouse/pause-and-resume-compute-powershell.md) végezheti el. A skálázáshoz használhatja az [Azure Portalt](quickstart-scale-compute-portal.md), a [PowerShellt](quickstart-scale-compute-powershell.md), a [T-SQL-t](quickstart-scale-compute-tsql.md) vagy egy [REST API-t](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Az Azure Functions használatával mostantól bármikor használhatja az automatikus skálázást:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Teljesítményre optimalizálhatja az architektúrát

Küllős architektúra esetén az SQL Database és az Azure Analysis Services használatát javasoljuk. Ez a megoldás a munkaterhelések elkülönítését biztosítja a különböző felhasználói csoportok között, és az SQL Database és az Azure Analysis Services speciális biztonsági funkcióinak használatát teszi lehetővé. Emellett ezzel a módszerrel korlátlan párhuzamos működést biztosíthat a felhasználók számára.

További információ az [Azure Synapse előnyeit kihasználó tipikus architektúrákról.](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/)

Egyetlen kattintással telepítheti a küllőket az SQL-adatbázisokban az SQL-készletből:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>