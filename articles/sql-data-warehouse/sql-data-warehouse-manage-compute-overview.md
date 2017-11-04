---
title: "Az Azure SQL Data Warehouse (áttekintés) számítási teljesítményt kezelése |} Microsoft Docs"
description: "Az Azure SQL Data Warehouse képességek kibővítési teljesítményét. Horizontális felskálázás dwu-k módosításával vagy és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 3/23/2017
ms.author: elbutter
ms.openlocfilehash: 0d0d3b94fb50155ce0579d32e8ff78a47b9e3589
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Kezelheti a számítási teljesítményt az Azure SQL Data Warehouse (áttekintés)
> [!div class="op_single_selector"]
> * [Áttekintés](sql-data-warehouse-manage-compute-overview.md)
> * [Portál](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

Az SQL Data Warehouse architektúrája elválasztja a tárolást és számítást, hogy egymástól független méretezését. Ennek eredményeképpen számítási is méretezhető teljesítményigények kielégítése függetlenül az adatok mennyiségét. Ez az architektúra természetes következménye, hogy [számlázási] [ billed] számítási és tárolási elkülönül. 

Ez az áttekintés bemutatja hogyan működik az SQL Data Warehouse és hogyan használják a szüneteltetési kiterjesztése folytatása, és méretezhető SQL Data Warehouse képességeit. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Milyen számítási felügyeleti műveletek a vártnak az SQL Data Warehouse
Az SQL Data Warehouse architektúrája áll a vezérlő csomópont, a számítási csomópontok és a tárolási réteg 60 terjesztéseket elosztva. 

Egy normál aktív munkamenet során az SQL Data Warehouse a rendszer átjárócsomópont kezeli a metaadatok, és az elosztott lekérdezésoptimalizáló tartalmazza. A head csomópont alatt a számítási csomópontok és a tárolási rétegből állnak. A DWU 400 a rendszer egy átjárócsomópont, négy számítási csomópontok és a tárolási rétegből álló 60 terjesztéseket rendelkezik. 

A skála változni vagy művelet felfüggeszti, a rendszer először használhatatlanná teszi az összes bejövő lekérdezés, és visszavon tranzakciók konzisztens állapotú biztosításához. A skálázási műveletek, a méretezés akkor történik, a tranzakciós visszaállítás befejezése után. Méretezett művelet a rendszer kiosztja a felesleges szükséges száma számítási csomópontokat, és megkezdi a újracsatlakoztatása a számítási csomópontok a tárolási réteg. Lefelé méretezéshez művelet a felesleges csomópontok kiadott, majd a többi számítási csomópontot újra csatlakoztatja, magukat a megfelelő számú terjesztési. A szüneteltetési művelete minden számítási csomópontok feloldásáig blokkolva lesz, és a rendszer metaadat-művelet a végső rendszer stabil állapotban, hogy számos változni fog.

| DWU  | \#a számítási csomópontok | \#az azokat a terjesztéseket, csomópontonként |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

A három elsődleges funkciója számítási kezeléséhez a következők:

1. Szünet
2. Folytatás
3. Méretezés

Mindkét művelet több percet is igénybe vehet. Ha skálázás/felfüggesztése vagy folytatása automatikusan, érdemes lehet megvalósítani logika győződjön meg arról, hogy bizonyos műveletek befejeződtek-e egy másik művelet folytatása előtt. 

Az adatbázis állapotát a különböző végpontok ellenőrzése lehetővé teszik megfelelően megvalósítását az ilyen műveletek automatizálását. A portál értesítést küldenek befejezett egy műveletet, és az adatbázisok aktuális állapotát, de nem engedélyezi a állapot ellenőrzéséhez szoftveres. 

>  [!NOTE]
>
>  Számítási felügyeleti funkció végpontjai között nem létezik.
>
>  

|              | Felfüggesztése vagy folytatása | Méretezés | Ellenőrizze az adatbázis állapota |
| ------------ | ------------ | ----- | -------------------- |
| Azure Portal | Igen          | Igen   | **Nem**               |
| PowerShell   | Igen          | Igen   | Igen                  |
| REST API     | Igen          | Igen   | Igen                  |
| T-SQL        | **Nem**       | Igen   | Igen                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Skála számítási

Az SQL Data Warehouse teljesítményének mérése [adattárházegységek (dwu-k)] [az adatraktár-(dwu)] Ez egy számítási erőforrásokat, például a Processzor, memória és I/O sávszélesség abstracted mértékét. A felhasználó, aki a rendszer teljesítményét méretezési kívánja ehhez különböző eszközökkel, például a portálon, a T-SQL és a REST API-k segítségével. 

### <a name="how-do-i-scale-compute"></a>Hogyan méretezni a számítási?
Számítási power van kezelve az SQL Data Warehouse DWU beállítás módosításával. Teljesítmény lineárisan növeli a további DWU bizonyos műveletek való hozzáadása során.  Kínálunk DWU ajánlatokat, amelyek biztosítják, hogy a teljesítmény változik feltétlenül felfelé vagy lefelé amikor méretezni a rendszer. 

Úgy, hogy dwu-k, ezek az egyes módszerek bármelyikét használhatja.

* [Skála számítási teljesítményt az Azure-portálon][Scale compute power with Azure portal]
* [Skála számítási teljesítményt a PowerShell használatával][Scale compute power with PowerShell]
* [Skála számítási teljesítményt REST API-khoz][Scale compute power with REST APIs]
* [Skála számítási teljesítményt a TSQL használatával][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>Hány dwu-k érdemes használni?

Az ideális DWU érték megtalálásához próbáljon meg vertikálisan le- és felskálázni, az adatok betöltése után pedig futtasson néhány lekérdezést. Mivel a skálázás nem időigényes, próbálja meg különböző teljesítményszintek vagy kevesebb mint egy óra alatt. 

> [!Note] 
> Az SQL Data Warehouse nagy mennyiségű adat feldolgozására szolgál. Igaz platformképességei méretezéshez, különösen a nagyobb dwu-k, hogy használni kívánt a nagy adatkészletet, ami megközelíti vagy meghaladja az 1 TB.

Javaslatok a munkaterhelés számára a legjobb DWU kereséséhez:

1. Az adatok a fejlesztési először egy kisebb DWU teljesítményszintet kiválasztása.  Jó kiindulópont DW400 vagy DW200.
2. Az alkalmazás teljesítményének figyelése, a teljesítmény azt láthatja a dwu-k száma a kijelölt megfigyelő képest.
3. Határozza meg, mennyi gyorsabb vagy alacsonyabb teljesítményt kell lennie ahhoz, hogy a lineáris skála feltételezve elérni az optimális teljesítményszint szükséges a követelmények teljesítéséhez.
4. Növelheti vagy csökkentheti a dwu-k számát arányában hogyan sokkal gyorsabban vagy lassabban szeretné-e a számítási feladatok végrehajtásához. 
5. Folytatható, amíg el nem éri az optimális teljesítmény szintű üzleti igényeinek a szükséges módosításokat.

> [!NOTE]
>
> Lekérdezési teljesítmény csak további párhuzamos folyamatkezelést biztosítja a növekszik, ha a munkahelyi oszthatók számítási csomópontjai között. Ha úgy találja, hogy skálázás nem változik a teljesítményt, adjon tekintse meg a teljesítményhangolás annak ellenőrzéséhez, hogy az adatok nem egyenlően van elosztva, vagy ha bevezették az adatátvitelt jelölik a nagy mennyiségű. 

### <a name="when-should-i-scale-dwus"></a>Mikor érdemes méretezni a dwu-k?
A következő fontos forgatókönyveit dwu-k skálázás módosítja:

1. A rendszer a vizsgálatokat, összesítések és CTAS utasítások lineárisan módosítása
2. A polybase-zel betöltésekor olvasók és írók számának növelése
3. Egyidejű lekérdezések és feldolgozási üzembe helyezési ponti maximális száma

Mikor érdemes méretezni a dwu-k javaslatokat:

1. Mielőtt a nagy mennyiségű adat betöltenie vagy átalakítási műveletet hajt végre, növelheti dwu-k, hogy az adatok elérhető gyorsabban.
2. Csúcsidőszak munkaidőben méretezhető egyidejű lekérdezések nagy mennyiségű befogadásához. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Felfüggesztés számítási
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Egy adatbázis felfüggesztése, használja az alábbi egyes módszereket.

* [Felfüggesztés számítási az Azure-portálon][Pause compute with Azure portal]
* [Felfüggesztés számítási a PowerShell használatával][Pause compute with PowerShell]
* [Felfüggesztés számítási REST API-khoz][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Folytatás számítási
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Egy adatbázis folytatásához használja az alábbi egyes módszereket.

* [Folytatás számítási az Azure-portálon][Resume compute with Azure portal]
* [Folytatás számítási a PowerShell használatával][Resume compute with PowerShell]
* [Folytatás számítási REST API-khoz][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Ellenőrizze az adatbázis állapota 

Egy adatbázis folytatásához használja az alábbi egyes módszereket.

- [A T-SQL adatbázis állapotának ellenőrzése][Check database state with T-SQL]
- [Ellenőrizze az adatbázis állapotát a PowerShell használatával][Check database state with PowerShell]
- [Ellenőrizze az adatbázis állapotát a REST API-k][Check database state with REST APIs]

## <a name="permissions"></a>Engedélyek

Az adatbázis skálázás az engedélyekkel kell rendelkeznie a leírt [ALTER DATABASE][ALTER DATABASE].  Szüneteltetése és folytatása szükséges a [SQL DB Contributor] [ SQL DB Contributor] engedéllyel, akkor a kifejezetten Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikkek segítenek megismerni néhány további legfontosabb teljesítményi fogalmak:

* [Munkaterhelés és feldolgozási kezelése][Workload and concurrency management]
* [Tábla kialakítás áttekintése][Table design overview]
* [Elosztása][Table distribution]
* [Tábla indexelő][Table indexing]
* [Táblaparticionálást][Table partitioning]
* [Tábla statisztikai adatainak][Table statistics]
* [Gyakorlati tanácsok][Best practices]

<!--Image reference-->

<!--Article references-->
[billed]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
