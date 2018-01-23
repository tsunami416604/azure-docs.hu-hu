---
title: Az Azure SQL Data Warehouse lap cheat |} Microsoft Docs
description: "Hivatkozások és az ajánlott eljárásokat a gyors létrehozása az Azure SQL Data Warehouse megoldások keresése."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: a16c2230c26865913285cb8cbd5b0f81426acdd1
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse lap cheat
Ez Adatlap biztosít hasznos tipp és ajánlott eljárások az Azure SQL Data Warehouse megoldások készítéséhez. Mielőtt hozzáfogna, kapcsolatos további részletek lévő egyes lépések olvasásával [Azure SQL Data Warehouse munkaterhelés minták és víruskereső minták](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns), amely ismerteti, hogy mi az SQL Data Warehouse és mit nem.

A következő ábrán látható adatraktár tervezése:

![Vázlat]

## <a name="queries-and-operations-across-tables"></a>Lekérdezések és műveletek táblák között

Amikor tudja, hogy előzetesen a elsődleges műveletek és az adatraktár futtatásához lekérdezések, hogy ezek a műveletek a data warehouse architektúrája rangsorolhatja. Ezeket a lekérdezéseket és műveletek a következők lehetnek:
* Egy vagy két ténytáblák dimenzió táblákkal, a kombinált tábla szűrési és majd kiegészíti az eredményeket egy adatpiacot történő csatlakoztatását.
* Nagy vagy kis frissítések és az a tény értékesítési.
* A táblák csak adatok hozzáfűzése.

Milyen műveleteket előre ismerete segít a táblák kialakításának optimalizálásához.

## <a name="data-migration"></a>Adatmigrálás

Első lépésként betölteni az adatait [Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store) vagy Azure Blob Storage tárolóban. Ezután a PolyBase segítségével az adatok betöltése az SQL Data Warehouse egy átmeneti tárolási tábla. Az alábbi konfigurációt használja:

| Tervezés | Ajánlás |
|:--- |:--- |
| Disztribúció | Ciklikus időszeletelés |
| Indexelés | Halommemória |
| Particionálás | Nincs |
| Erőforrásosztály | largerc vagy xlargerc |

További információ [adatáttelepítés], [az adatok betöltése], és a [kinyerés, betöltés és átalakítás (ELT) folyamat](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Elosztott vagy replikált táblák

Használja a következő stratégiák, attól függően, hogy a táblázat tulajdonságai:

| Típus | Nagyszerű beválik, ha...| Figyelje meg, hogy ha...|
|:--- |:--- |:--- |
| Replikált | • Kis dimenziótáblák a tároló (~ 5 x tömörítés) tömörítés után legalább 2 GB csillagséma |• Nagy írási tranzakciók vannak a tábla (például az insert, upsert, törlés, a frissítés)<br></br>• Módosítsa kiépítés gyakran Data Warehouse egységek (DWU)<br></br>• Csak használhatja 2-3 oszlopok a tábla azonban sok oszlopot tartalmaz.<br></br>A replikált tábla indexeli • |
| Ciklikus multiplexelés (alapértelmezett) | • Ideiglenes/átmeneti tárolási tábla<br></br> • Nem egyértelmű kulcs, vagy nem jó jelölt oszlop csatlakoztatása |• Teljesítmény az adatok mozgása miatt lassú |
| Kivonat | • A ténytáblák<br></br>• Nagy dimenziótáblák |• A terjesztési kulcs nem lehet frissíteni. |

**Tipp:**
* Ciklikus multiplexelés kezdődnie, de aspire előnyeit nagymértékben párhuzamos architektúra kivonatoló terjesztési stratégia felé.
* Győződjön meg arról, hogy közös kivonatoló kulcsok a ugyanazt az adatformátumot.
* Ne terjessze varchar formátumának.
* A közös kivonatoló kulccsal, hogy egy ténytábla gyakori összekapcsolási műveletek a dimenziótáblák elosztott kivonatoló lehet.
* Használjon  *[sys.dm_pdw_nodes_db_partition_stats]*  bármely döntés az adatok elemzéséhez.
* Használjon  *[sys.dm_pdw_request_steps]*  adatok elemzése a lekérdezések mögött típusú áthelyezések szórási figyelje, és véletlen műveletek egy. Ez az a telepítési stratégia megfontolja.

További információ [replikált táblák] és [táblák elosztott].

## <a name="index-your-table"></a>A tábla index

Indexelő bizonyulhat hasznosnak táblák gyorsan olvasásához. Nincs olyan egyedi szolgáló technológiák csoportja használhatja a igények alapján:

| Típus | Nagyszerű beválik, ha... | Figyelje meg, hogy ha...|
|:--- |:--- |:--- |
| Halommemória | • Átmeneti/ideiglenes tábla<br></br>• Kis táblákra kis |• A keresési megvizsgálja a teljes táblázat |
| Fürtözött index | • Táblákon, amelyekhez a legfeljebb 100 millió sort<br></br>• Nagy (több mint 100 millió sort foglalnak) oszloppal rendelkező táblákon csak 1-2 fokozottan használt |A replikált tábla használt •<br></br>Van több illesztési és Group By műveletek összetett lekérdezések •<br></br>• A módosításokat az indexelt oszlopokon: szükséges memória |
| Fürtözött oszlopcentrikus index (közösségi koordináló intézet) (alapértelmezés) | • Nagy táblák (több mint 100 millió sort foglalnak) | A replikált tábla használt •<br></br>• Nagy elvégezte frissítési műveleteket a tábla<br></br>• Meg a tábla overpartition: sorcsoportok nem fedik másik terjesztési csomópontok és a partíciók |

**Tipp:**
* Fölött egy fürtözött index előfordulhat, hogy hozzáadandó fürtözetlen index erősen a szűréshez használt oszlop. 
* Ügyeljen arra, hogyan kezelheti a memória, a közösségi koordináló intézet tartalmazó tábla. Adatok betöltése, ha azt szeretné, nagy erőforrásosztály kihasználják a felhasználó (vagy a lekérdezés). Ügyeljen arra, hogy kerülje a tisztítás és a sok kisméretű tömörített sor eszközcsoportok létrehozása.
* A közösségi koordináló intézet számítási rétegben sziklák optimalizálva.
* A közösségi koordináló intézet lassú teljesítmény gyenge tömörítés a sor csoportok miatt fordulhat elő. Ha ez történik, építse újra, vagy a közösségi koordináló intézet átrendezéséhez. Azt szeretné, hogy a tömörített sorcsoportok legalább 100 000 sorszám. Az ideális 1 millió sort foglalnak el egy sorcsoport.
* A növekményes terhelés gyakoriságát és mérete alapján átrendezéséhez, vagy az indexek újraépítése automatizálni kívánt. Mindig hasznos a rugó tisztítás.
* Lehet a stratégiai, ha szeretné osztani egy sor csoportot. Hogyan nagy vannak a nyitott sorcsoportok? Adatok betöltése a következő napon várhatók?

További információ [indexek].

## <a name="partitioning"></a>Particionálás
Előfordulhat, hogy partíciós a táblát, ha egy nagy ténytábla (1 milliárd sorok nagyobb). Az esetek százalékát 99 a partíciós kulcs dátum alapján. Legyen óvatos nem overpartition, ha kifejezetten a fürtözött oszlopcentrikus index van.

Az átmeneti tárolási táblák, ELT igénylő, révén kihasználhatja a particionálást. Elősegíti a adatok életciklusának kezelésére.
Ügyeljen arra, hogy az adatok, különösen ha a fürtözött oszlopcentrikus index overpartition.

További információ [partíciók].

## <a name="incremental-load"></a>Növekményes betöltése

Ha Növekményesen betölteni az adatokat, győződjön meg arról, hogy az adatok betöltése nagyobb erőforrás osztályok osszon ki-e. A a ELT folyamatok automatizálásához az SQL Data Warehouse PolyBase és ADF V2 használatát javasoljuk.

Nagy kötegelt frissítések előzményadatait először törölje az érintett adatok. Ezután ellenőrizze a tömeges Beszúrás az új adatok. A kétlépéses megoldás, hatékonyabb.

## <a name="maintain-statistics"></a>Statisztikák karbantartása
 Automatikus-statisztikák általában elérhetők, amíg az SQL Data Warehouse igényel manuális karbantartást statisztikai adat. Fontos, statisztikai adatainak frissítése *jelentős* módosítások fordulhat elő, az adatokhoz. Ez segít a lekérdezésterveket optimalizálása. Ha talál meg, hogy az összes, a statisztikai karbantartása túl sokáig tart, több mérlegelni oszlopok statisztika rendelkeznie kell. 

A frissítési gyakoriságának is meghatározhat. Például érdemes frissíteni a dátumoszlopot, ahol új értékek előfordulhat, hogy vehetők fel, napi bontásban. A legtöbb juttatás nyerhet statisztika rendelkező oszlopokon érintett csatlakozik, a WHERE záradékban használt oszlopok és a GROUP BY található oszlopok.

További információ [statisztika].

## <a name="resource-class"></a>Erőforrásosztály
Az SQL Data Warehouse erőforráscsoportokat használ arra, hogy memóriát foglaljon le a lekérdezésekhez. Ha a lekérdezés vagy betöltése sebesség növelése érdekében több memóriára van szüksége, magasabb erőforrás osztályok kell lefoglalni. A tükrözés oldalon nagyobb erőforrás osztályokat párhuzamossági hatással van. Minden felhasználó egy nagy erőforrásosztály helyezése előtt figyelembe kell venni, hogy szeretné.

Ha azt észleli, hogy a lekérdezések túl hosszúak, ellenőrizze, hogy a felhasználók nagy erőforrás osztályok nem működnek. Nagy erőforrás osztályok sok egyidejű üzembe helyezési ponti felhasználását. Más lekérdezések sorba is okozhatják.

Végül a számítási optimalizált réteg használatával minden erőforrásosztály ennek 2,5-szerese több memóriát, mint a rugalmas optimalizált rétegen lekérdezi.

Munkavégzés további [erőforrás osztályok és feldolgozási].

## <a name="lower-your-cost"></a>A költségek csökkentése
Az SQL Data Warehouse alapfunkciója azt a képességet felfüggesztése, ha nem használ, ami leállítja a számlázási számítási erőforrásokat. Egy másik fontos jellemző az erőforrások méretezhetősége. Felfüggesztés és a méretezésről az Azure-portálon, vagy PowerShell-parancsok segítségével végezhető.

Automatikus skálázás most már a időpontban szeretné az Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Az architektúra a teljesítmény optimalizálása

Javasoljuk, hogy az SQL-adatbázis és az Azure Analysis Services hub és küllős architektúra figyelembe véve. Ez a megoldás biztosíthat munkaterhelés különíteni a különböző felhasználói csoportokat is a speciális biztonsági szolgáltatásokat az SQL-adatbázis és az Azure Analysis Services használata során. Azt is adja meg a felhasználóknak korlátlan párhuzamossági módot.

További információ [tipikus architektúrák előnyeit az SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Egy kattintással központi telepítése az SQL-adatraktár SQL-adatbázisok a küllők:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Sketch]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[az adatok betöltése]:./design-elt-data-loading.md
[deeper guidance]: ./guidance-for-loading-data.md
[indexek]:./sql-data-warehouse-tables-index.md
[partíciók]:./sql-data-warehouse-tables-partition.md
[statisztika]:./sql-data-warehouse-tables-statistics.md
[erőforrás osztályok és feldolgozási]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[adatáttelepítés]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[replikált táblák]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[táblák elosztott]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[Azure Data Lake Store]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
