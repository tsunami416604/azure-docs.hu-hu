---
title: Hasznos tanácsok az Azure SQL Data Warehouse-hoz | Microsoft Docs
description: Itt hivatkozásokat és ajánlott eljárásokat találhat az Azure SQL Data Warehouse-megoldások gyors összeállításához.
services: sql-data-warehouse
author: acomet
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: a22aadff2d58ace60a980a138035e30a638b08fa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Hasznos tanácsok az Azure SQL Data Warehouse-hoz
Ez a témakör az Azure SQL Data Warehouse-megoldások összeállításával kapcsolatos hasznos tippeket és ajánlott eljárásokat tartalmaz. Mielőtt belekezdene, részletesen ismerje meg az egyes lépéseket az [Azure SQL Data Warehouse-munkaterhelési mintákat és kizárási mintákat](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns) ismertető témakör elolvasásával, amely leírja, mi az az SQL Data Warehouse.

A következő ábra adattárház tervezésének folyamatát mutatja be:

![Vázlat]

## <a name="queries-and-operations-across-tables"></a>Táblák közötti lekérdezések és műveletek

Ha előre tudja, milyen elsődleges műveleteket és lekérdezéseket futtathat az adattárházban, ezekhez a műveletekhez priorizálhatja az adattárházat. Ilyen lekérdezések és műveletek lehetnek többek között a következők:
* Egy vagy két ténytábla egyesítése dimenziótáblákkal, a kombinált tábla szűrése, majd az eredmények összefűzése egy data martba.
* Nagyobb vagy kisebb frissítések elvégzése a tényértékesítésekben.
* Csak adatok hozzáfűzése a táblákhoz.

A művelettípusok előzetes ismerete segít optimalizálni a táblák kialakítását.

## <a name="data-migration"></a>Adatok migrálása

Először töltse be az adatokat az [Azure Data Lake Store](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)-ba vagy az Azure Blob Storage-ba. Ezután a PolyBase segítségével töltse be az adatokat az SQL Data Warehouse-ba egy előkészítési táblában. Használja a következő konfigurációt:

| Tervezés | Ajánlás |
|:--- |:--- |
| Disztribúció | Ciklikus időszeletelés |
| Indexelés | Halommemória |
| Particionálás | None |
| Erőforrásosztály | largerc vagy xlargerc |

Itt további információkat tudhat meg az [adatok migrálásáról], az [adatok betöltéséről] és a [kinyerési, betöltési és átalakítási (ELT) folyamatról](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Elosztott vagy replikált táblák

A tábla tulajdonságaitól függően a következő stratégiákat használja:

| Típus | Kiválóan alkalmas a következőhöz:| Ügyeljen a következő esetekben:|
|:--- |:--- |:--- |
| Replikált | • Kis dimenziótáblák csillag sémában, tömörítés után kevesebb mint 2 GB tárhellyel (~5-szörös tömörítés) |• Számos írási tranzakció található a táblában (például beszúrás, upsert, törlés, frissítés)<br></br>• Gyakran módosítja az adattárházegységek (DWU-k) kiépítését<br></br>• Csak 2-3 oszlopot használ, de a táblában számos oszlop található<br></br>• Replikált táblát indexel |
| Ciklikus időszeletelés (alapértelmezett) | • Ideiglenes/előkészítési tábla<br></br> • Nincs egyértelmű csatlakozási kulcs vagy alkalmas oszlop |• A teljesítmény az adatmozgás miatt lassú |
| Kivonat | • Ténytáblák<br></br>• Nagyméretű dimenziótáblák |• Az elosztási kulcs nem frissíthető |

**Tippek:**
* Kezdje ciklikus időszeleteléssel, de haladjon a kivonatoló terjesztési stratégia felé, hogy kihasználhassa a nagymértékben párhuzamos architektúrát.
* Ügyeljen arra, hogy a közös kivonatkulcsoknak ugyanaz legyen az adatformátuma.
* Ne terjesszen varchar formátumban.
* A gyakori csatlakozási műveletekkel rendelkező ténytáblákhoz közös kivonatkulccsal rendelkező dimenziótáblákhoz kivonatterjesztés használható.
* A *[sys.dm_pdw_nodes_db_partition_stats]* segítségével elemezheti az adatokban lévő eltéréseket.
* A *[sys.dm_pdw_request_steps]* segítségével elemezheti a lekérdezések mögötti adatmozgásokat, monitorozhatja az időszórásokat és módosíthatja a műveletek sorrendjét. Ez a terjesztési stratégia áttekintéséhez hasznos.

További tudnivalók a [replikált táblákról] és az [elosztott táblákról].

## <a name="index-your-table"></a>A tábla indexelése

Az indexelés a táblák gyors olvasásához hasznos. Egyedi technológiákat alkalmazhat az igényei szerint:

| Típus | Kiválóan alkalmas a következőhöz: | Ügyeljen a következő esetekben:|
|:--- |:--- |:--- |
| Halommemória | • Előkészítési/ideiglenes tábla<br></br>• Kisméretű táblák kisméretű keresésekkel |• Minden keresés a teljes táblában keres |
| Fürtözött index | • Legfeljebb 100 millió sorral rendelkező táblák<br></br>• Nagyméretű táblák (100 millió sornál nagyobbak) mindössze 1-2 gyakran használt oszloppal |• Replikált táblán vannak használva<br></br>• Több csatlakozási és csoportosítási műveletet tartalmazó, összetett lekérdezéseket használ<br></br>• Frissítéseket végez az indexelt oszlopokon: ez memóriát használ |
| Fürtözött oszlopcentrikus index (CCI) (alapértelmezett) | • Nagyméretű táblák (100 millió sornál nagyobbak) | • Replikált táblán vannak használva<br></br>• Nagyméretű frissítési műveleteket végez a táblán<br></br>• Túlparticionálja a táblát: a sorcsoportok nem fedik a különböző terjesztési csomópontokat és partíciókat |

**Tippek:**
* A fürtözött indexek mellett érdemes lehet nem fürtözött indexet is hozzáadni a szűréshez gyakran használt oszlopokhoz. 
* Ügyeljen arra, hogyan kezeli a memóriát a CCI-vel rendelkező táblákban. Adatok betöltésekor az a cél, hogy a felhasználó (vagy a lekérdezés) nagyméretű erőforrásosztályt használhasson. Kerülje a vágást és sok kis tömörített sorcsoport létrehozását.
* A Gen2-n a teljesítmény maximalizálása érdekében a CCI-táblák gyorsítótárazása a számítási csomópontokon helyben történik.
* CCI esetén a sorcsoportok nem megfelelő tömörítése miatt gyenge lehet a teljesítmény. Ilyen esetben állítsa össze újra vagy rendezze át a CCI-t. Tömörített sorcsoportonként legalább 100 000 sorra van szükség. Az ideális a sorcsoportonként 1 millió sor.
* A növekményes terhelési gyakoriság és méret alapján érdemes lehet automatizálni az indexek átrendezésekor vagy újraépítésekor. A tavaszi nagytakarítás mindig hasznos.
* A sorcsoportokat stratégiai szempontok szerint vágja. Mekkorák a nyitott sorcsoportok? Mennyi adatot tervez betölteni a következő napokban?

További információk az [indexekről].

## <a name="partitioning"></a>Particionálás
Particionálhatja a táblát nagyméretű ténytábla esetén (1 milliárd sornál nagyobb). Az esetek 99 százalékában a partíciókulcsnak dátumon kell alapulnia. Kerülje a túlparticionálást fürtözött oszlopcentrikus index esetén.

ELT-t igénylő előkészítési táblák esetén hasznos lehet a particionálás. Ez megkönnyíti az adatok életciklus-felügyeletét.
Kerülje az adatok túlparticionálását, különösen fürtözött oszlopcentrikus indexeken.

További információk a [partíciókról].

## <a name="incremental-load"></a>Növekményes betöltés

Ha növekményesen fogja betölteni az adatokat, először győződjön meg arról, hogy nagyobb méretű erőforrásosztályokat foglalt le az adatok betöltéséhez. A PolyBase és az ADF V2 használatát javasoljuk az ELT folyamatok SQL Data Warehouse-ban való automatizálásához.

Az előzményadatokban lévő nagyméretű tömeges frissítéshez először törölje az érintett adatokat. Ezután végezze el az új adatok tömeges beszúrását. Ez a kétlépéses módszer hatékonyabb.

## <a name="maintain-statistics"></a>Statisztikák karbantartása
 Az automatikus statisztikák általános elérhetővé válásáig az SQL Data Warehouse a statisztikák manuális karbantartását igényli. Fontos a statisztikák frissítése, mivel az adatokban *jelentős* változások történhetnek. Ez segít optimalizálni a lekérdezésterveket. Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, körültekintőbben válassza ki, mely oszlopok rendelkezzenek statisztikákkal. 

A frissítések gyakoriságát is megadhatja. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. A legnagyobb előnnyel az jár, ha a csatlakozások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit készíti el.

További információk a [statisztikákról].

## <a name="resource-class"></a>Erőforrásosztály
Az SQL Data Warehouse erőforráscsoportokat használ arra, hogy memóriát foglaljon le a lekérdezésekhez. Ha a lekérdezés vagy a betöltés sebességének növelése érdekében több memóriára van szüksége, magasabb erőforrásosztályokat kell lefoglalnia. A nagyobb erőforrásosztályok használata azonban hatással van a párhuzamos működésre. Ezt érdemes figyelembe venni, mielőtt az összes felhasználót nagyméretű erőforrásosztályba helyezné át.

Ha úgy látja, hogy a lekérdezések túl sokáig tartanak, ellenőrizze, hogy a felhasználók nem nagyméretű erőforrásosztályokban futnak-e. A nagyméretű erőforrás osztályok számos egyidejű helyet foglalnak le, ezért más lekérdezések várólistára helyezését okozhatják.

Végezetül az SQL Data Warehouse Gen2 verziójának használatával minden erőforrásosztály 2,5-szer több memóriát kap, mint a Gen1-el.

További információk az [erőforrásosztályokról és a párhuzamos működésről].

## <a name="lower-your-cost"></a>Csökkentheti költségeit
Az SQL Data Warehouse egyik legfőbb jellemzője, hogy képes a [számítási erőforrások felügyeletére](sql-data-warehouse-manage-compute-overview.md). Amikor nem használja az adattárházat, szüneteltetheti, ami leállítja a számítási erőforrások számlázását. Az erőforrásokat a teljesítményigényeinek megfelelően skálázhatja. A szüneteltetést az [Azure Portalon](pause-and-resume-compute-portal.md) vagy a [PowerShell-lel](pause-and-resume-compute-powershell.md) végezheti el. A skálázáshoz használhatja az [Azure Portalt](quickstart-scale-compute-portal.md), a [PowerShellt](quickstart-scale-compute-powershell.md), a [T-SQL-t](quickstart-scale-compute-tsql.md) vagy egy [REST API-t](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Az Azure Functions használatával mostantól bármikor használhatja az automatikus skálázást:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Teljesítményre optimalizálhatja az architektúrát

Küllős architektúra esetén az SQL Database és az Azure Analysis Services használatát javasoljuk. Ez a megoldás a munkaterhelések elkülönítését biztosítja a különböző felhasználói csoportok között, és az SQL Database és az Azure Analysis Services speciális biztonsági funkcióinak használatát teszi lehetővé. Emellett ezzel a módszerrel korlátlan párhuzamos működést biztosíthat a felhasználók számára.

További információk az [SQL Data Warehouse előnyeit kihasználó tipikus architektúrákról](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Egyetlen kattintással helyezhet üzembe küllőket az SQL-adatbázisokban az SQL Data Warehouse-ból:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Vázlat]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[adatok betöltéséről]:design-elt-data-loading.md
[deeper guidance]:guidance-for-loading-data.md
[indexekről]:sql-data-warehouse-tables-index.md
[partíciókról]:sql-data-warehouse-tables-partition.md
[statisztikákról]:sql-data-warehouse-tables-statistics.md
[erőforrásosztályokról és a párhuzamos működésről]:resource-classes-for-workload-management.md
[replikált táblákról]:design-guidance-for-replicated-tables.md
[elosztott táblákról]:sql-data-warehouse-tables-distribute.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[adatok migrálásáról]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

[Azure Data Lake Store]: ../data-factory/connector-azure-data-lake-store.md
[sys.dm_pdw_nodes_db_partition_stats]: /sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
