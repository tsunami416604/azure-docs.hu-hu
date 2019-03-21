---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések 2018 szeptember |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863712"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Mi az új Azure SQL Data warehouse? 2018. szeptember
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek a 2018 szeptember.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Az SQL Data Warehouse Gen2 új alsó belépési pont
A 2018 április [Microsoft bejelentette](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL Data Warehouse Gen2, amely a teljesítmény x 5, 5 x a számítási méret, 4 x Az egyidejűség és a korlátlan tárolási biztosít. Leírtaknak megfelelően a [Data Warehouse a felhőben a teljesítményteszt](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) által Gigaom, az SQL Data Warehouse Gen2 **Amazon Redshift outperforms 42 %-kal**.

Gen2 már általánosan elérhető, egy alacsonyabb belépési pont a DWU500c így nyugodtan egy kisebb méretű adatok adatraktár-fejlesztési-tesztelési környezetre szeretné használni az összes szolgáltatás legújabb fejlesztései. Az új belépési pont megőrzi a Gen2 a funkciókat, beleértve a [adaptív gyorsítótárazást](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [világítás gyors adatok újbóli felosztás](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/), és támogatja a [valós idejű adatraktár](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>SQL-sebezhetőségi felmérés
[Az SQL biztonsági rés értékelése (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) könnyen használható szolgáltatás, amely folyamatosan figyeli az adattárházba. Ez segít biztosítani a magas biztonsági szintű mindig, és teljesülnek-e a szervezeti szabályzatok. Biztosítja a gyakorlatban hasznosítható javítási lépések együtt egy átfogó biztonsági jelentés az összes hiba található. Ez a jelentés megkönnyíti, hogy proaktív módon az adatbázis biztonsági helyzetében kezelése és beavatkozást összpontosíthat a legnagyobb hatással műveletek, még akkor is, ha még nem biztonsági szakértői. Hol tárolja a módosításokat a rendszer a gyakori és visszakövetését, hogy a dinamikus környezetekben sebezhetőség-Felmérési felbecsülhetetlen észlelhesse a beállításokat, amelyek a támadásokkal szemben sebezhető hagyhatja az adattárházba.

## <a name="improved-availability-with-query-restartability"></a>Lekérdezés restartability jobb rendelkezésre állás
Lekérdezés-végrehajtás során problémák tetszőleges számú fordulhat elő, ami hibákat okozhat a lekérdezés sikertelen. A hálózati leállások, egy hardverhiba vagy egyéb leválasztásának szolgáltatáskimaradást okozhat. Az SQL Data Warehouse mostantól támogatja a lekérdezési restartability lépés vagy utasítás szintű SELECT lekérdezések. 

A lekérdezés Restartability egy lekérdezést, amely egy hiba miatt megszakad útban automatikusan újraindul. A lépéseket, a lekérdezés az alakzat számától függően vagy, ha a lekérdezés végrehajtása közben leállt, az SQL Data Warehouse-motor vagy indítsa újra a teljes lekérdezést, vagy az utolsó befejezett lekérdezési lépés fog folytatódni. A végfelhasználói szempontból a lekérdezés csak befejeződik. 

## <a name="maintenance-scheduling-preview"></a>Karbantartási ütemezés (előzetes verzió)
Már az Azure SQL Data Warehouse karbantartási ütemezés előzetes verzióban érhető el. Ez az új szolgáltatás zökkenőmentesen integrálható a Service Health tervezett karbantartásával kapcsolatos értesítések erőforrás állapotának ellenőrzése figyelése és az Azure SQL Data Warehouse karbantartási Feladatütemező szolgáltatás. Karbantartási ütemezés lehetővé teszi a egy olyan időkeretet meghatározhatja, mikor célszerű a új funkciók, a frissítések és javítások.

Karbantartási ütemezés kihasználja az Azure Monitor, és lehetővé teszi a felhasználóknak meghatározhatja azok szeretne értesítést kapni a közelgő karbantartási események és amely automatizált folyamatok legyen elindítva állásidő kezeléséhez, és minimalizálja a műveleteket. Az értesítéseket egy e-mailben vagy szöveges tartalmazhatnak. 

## <a name="wide-row-support-in-polybase"></a>A Polybase széles sor támogatása
Adatok betöltése az SQL Data Warehouse-ba, amikor az általános útmutatást használatára-e [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) van párhuzamos Adatbetöltési támogatása. Ebben a kiadásban az 1 MB - lehetővé teszi a széles körű sor mérete a táblák betöltése 32 ezer szélesebb körű oszlopait támogassa. Széles körű sorok támogatása leegyszerűsíti az Adatbetöltési folyamat nagy sorokat tartalmazó tábla.

> [!Note]
> A teljes sor mérete nem haladhatja meg 1 MB méretű.

## <a name="additional-language-support"></a>További nyelvek támogatása
Ebben a kiadásban bevezeti a következő T-SQL nyelvi elemei:

### <a name="stringsplit"></a>STRING_SPLIT
A [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) függvény oszt fel egy karakterlánc, a megadott elválasztó használatával. Adatok betöltése a forgatókönyvek, ahol egy oszlop rendelkezhet több érték elemzése és a egy másik táblába beszúrandó STRING_SPLIT hasznos.

#### <a name="example"></a>Példa
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>COMPRESS/DECOMPRESS Functions
A [TÖMÖRÍTÉSE](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) funkciója lehetővé teszi, hogy tömörítése és kibontása egy karakterláncot adjon meg a GZIP algoritmus használatával.

#### <a name="example"></a>Példa

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>IF EXISTS záradék a nézetek eldobása
Ha létezik záradék hozzáadása a [DROP NÉZET](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) utasítás leegyszerűsíti a T-SQL kód nézet eltávolítása az adatraktárból szükséges. A Ha létezik szintaxist, amikor egy NÉZET DROP utasítást a alkalmazni fogja dobja el a nézetet, ha létezik, vagy az utasítás figyelmen kívül, ha a nézet nem létezik.

#### <a name="example"></a>Példa
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Továbbfejlesztett fordítási idő egyszeres szúr be és a DDL-utasítások 
Egy hagyományos kinyerési, átalakítási és betöltési (ETL) modell követően gyakran az adatok beszúrási vezet, egy singleton-beszúrási fut ([INSERT-értékek](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) az adatbázis egy táblába. Ebben a kiadásban javul a singleton-beszúrási műveletek teljesítményének a fordítási ilyen típusú utasítás végrehajtásához szükséges idő csökkentésével. Bizonyos esetekben a megfigyelt fordítás javítása a legfeljebb 3 x gyorsabb. Ennek a fejlesztésnek csökken, egy singleton-insert utasítás végrehajtásához szükséges időt. 

A javítására is adatokat adattárházakhoz az objektumok hasonlóképpen csökkentésével nagy mennyiségű adatok definíciós nyelv (DDL) utasítások, létrehozása, beleértve a lekérdezés fordítási idő ALTER és törlési műveletek számos előnyt biztosít. 

Végül javítása csökkenti az utasításokat, amelyek a széles táblák - táblákat, amelyek nagy számú oszlopot az elindított általános végrehajtását. A javítására a lekérdezés fordítási lépés, amely csökkenti a teljes végrehajtási idő lekérdezések idő csökkentése.

## <a name="bug-fixes"></a>Hibajavítások

| Cím | Leírás |
|:---|:---|
| **Javítsa ki az egyedi korlátozások disztribúciókon statisztika létrehozásakor** | Ez a javítás címek felhasználók előforduló futó UPDATE STATISTICS csak a tábla megadása esetén ha a tábla volt-e definiálva egyedi korlátozás hiba. |
| **Javítsa ki a külső táblákon végrehajtott lekérdezések összeállítása során** | A javítás, amely fordítási idő lekérdezések külső táblák használata esetén az érintett hibának címeket.|
| **Javítsa ki a nagy típusú utasítás végrehajtása közben** | Oldja meg egyik deklarált paraméterekkel rendelkező előkészített utasítás fordítási hibának a *nagy* típusok (típus: nvarchar(max), varchar(max) vagy varbinary(max)). |
| **Javítsa ki, ha hiba lép fel, a mélyen beágyazott lekérdezések** | Biztosít egy egyértelmű hibaüzenet, ha a mélyen beágyazott lekérdezés túllépi a rendszer korlátot.|
| **Ha egy utasítást tartalmaz a kapcsolódó allekérdezés és a egy végrehajtási idő konstans fordításkor hibák javítása** |Címek fordításkor hiba lekérdezések adott kombinációjával korrelált segédlekérdezések és végrehajtási idejű állandók (például GETDATE()).|
| **Cím időtúllépési PDW objektum zárolások és autostats egyidejűségi tárhely beszerzésének** |Javítás zárolási időtúllépést megakadályozza, hogy autostats kérelmeket a származási kérések blokkolása hosszú ideig hozzáadja.|

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, megtudhatja, hogyan lehet gyorsan [hozzon létre egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
