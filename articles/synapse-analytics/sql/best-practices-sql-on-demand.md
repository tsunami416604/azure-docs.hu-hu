---
title: Ajánlott eljárások az SQL igény szerinti használatára (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban
description: Javaslatok és ajánlott eljárások az SQL igény szerinti használata esetén (előzetes verzió).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0792dd98bb554b7b430676472c27274f3312d381
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701367"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Ajánlott eljárások az SQL igény szerinti használatára (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban

Ebben a cikkben megtalálja az ajánlott eljárások gyűjteményét az SQL on-demand (előzetes verzió) használatához. Az SQL on-demand egy további erőforrás az Azure szinapszis Analyticsben.

## <a name="general-considerations"></a>Általános megfontolások

Az SQL on-demand lehetővé teszi fájlok lekérdezését az Azure Storage-fiókokban. Nem rendelkezik helyi tárolási vagy betöltési képességekkel. Ennek megfelelően az összes olyan fájl, amely a lekérdezés célja, az SQL igény szerint kívül van. A fájlok tárterületről való olvasásával kapcsolatos minden művelet hatással lehet a lekérdezés teljesítményére.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Az Azure Storage-fiók és az SQL igény szerinti elhelyezése

A késés csökkentése érdekében helyezze el az Azure Storage-fiókját és az SQL igény szerinti végpontját. A munkaterület létrehozása során kiépített Storage-fiókok és-végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha más Storage-fiókokhoz is hozzáfér az SQL igény szerint, ügyeljen arra, hogy ugyanabban a régióban legyenek. Ha nem ugyanabban a régióban találhatók, az adatok hálózati átvitele nagyobb késéssel jár a távoli és a végpont régiói között.

## <a name="azure-storage-throttling"></a>Azure Storage-szabályozás

Több alkalmazás és szolgáltatás is hozzáférhet a Storage-fiókhoz. A tárolási szabályozás akkor fordul elő, ha az alkalmazások, szolgáltatások és az SQL igény szerinti munkaterhelése által generált kombinált IOPS vagy átviteli sebesség meghaladja a Storage-fiók korlátait. Ennek eredményeképpen jelentős negatív hatást tapasztal a lekérdezési teljesítményre.

A szabályozás észlelése után az SQL on-demand beépített kezeléssel rendelkezik ebben a forgatókönyvben. Az SQL igény szerint lassabban, a szabályozás feloldása után kéri a tárterületet.

> [!TIP]
> Az optimális lekérdezési műveletek végrehajtásához a Storage-fiókot más számítási feladatokkal kell kihangsúlyozni a lekérdezés végrehajtása során.

## <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezéshez

Ha lehetséges, készíthet fájlokat a jobb teljesítmény érdekében:

- CSV-és JSON-fájl konvertálása a Parquet-parketta oszlopos formátumú. Mivel tömörítve van, a fájlméretek kisebbek, mint a CSV-vagy JSON-fájlok ugyanazzal az adattal. Az SQL on-demand kevesebb időt és tárolási kérést igényel a beolvasáshoz.
- Ha egy lekérdezés egyetlen nagyméretű fájlt céloz meg, akkor a több kisebb fájlra is kihasználhatja.
- Próbálja meg a CSV-fájl méretét 10 GB alatt tartani.
- Jobb, ha azonos méretű fájlokat szeretne egy OPENROWSET elérési úthoz vagy egy külső tábla HELYéhez.
- Particionálja az adatait úgy, hogy a partíciókat különböző mappákba vagy fájlnevekre tárolja, [majd a fájlnév és a filepath függvények használatával adja meg az adott partíciókat](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Helyettesítő karakterek leküldése a megadott elérési úthoz alacsonyabb szintre

Az elérési úton helyettesítő karaktereket használhat [több fájl és mappa lekérdezéséhez](develop-storage-files-overview.md#query-multiple-files-or-folders). Az SQL on-demand listázza a Storage-fiókban lévő fájlokat, amelyek a Storage API használatával kezdődnek, és kiküszöböli a megadott elérési úttal nem egyező fájlokat. A fájlok kezdeti listájának csökkentése növelheti a teljesítményt, ha sok olyan fájl található, amely megfelel a megadott elérési útnak az első helyettesítő karakternek.

## <a name="use-appropriate-data-types"></a>Megfelelő adattípusok használata

A lekérdezésben használt adattípusok hatással vannak a teljesítményre. Jobb teljesítményt érhet el, ha: 

- Használja a legkisebb adatméretet, amely a lehető legnagyobb értéket fogja kielégíteni.
  - Ha a karakteres érték legfeljebb 30 karakter hosszúságú, akkor a karakter adattípust kell használnia a 30 értéknél.
  - Ha az összes karakteres oszlop értéke rögzített méretű, használja a char vagy a NCHAR. Ellenkező esetben használja a varchar vagy a nvarchar.
  - Ha a maximális egész oszlop értéke 500, használja a smallint, mert a legkisebb adattípus, amely képes alkalmazkodni ennek az értéknek. [Itt](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)megtalálhatja az egész adattípusok tartományait.
- Ha lehetséges, használja a varchar és a char helyett a nvarchar és a NCHAR értéket.
- Ha lehetséges, használja az egész szám alapú adattípust. A rendezési, a illesztési és a csoportosítási műveletek végrehajtása a karakteres adatoknál gyorsabban történik.
- Ha séma-következtetést használ, ellenőrizze a [késleltetett adattípust](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Késleltetett adattípusok keresése

A [séma-következtetés](query-parquet-files.md#automatic-schema-inference) segít gyorsan írni a lekérdezéseket, és az adatelemzést a sémafájl ismerete nélkül. Ez a kényelem a kikövetkeztetett adattípusok esetében a ténylegesnél nagyobb költségekkel jár. Akkor következik be, ha nincs elegendő információ a forrásfájlok számára a megfelelő adattípus használatáról. A Parquet-fájlok például nem tartalmaznak metaadatokat a karakteres oszlopok maximális hosszával kapcsolatban, és az SQL igény szerint varchar (8000) értékűre következtetnek. 

A lekérdezés eredményül kapott adattípusait [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15)használatával tekintheti meg.

Az alábbi példa bemutatja, hogyan optimalizálhatja a késleltetett adattípusokat. Az eljárás a késleltetett adattípusok megjelenítésére szolgál. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Itt látható az eredményhalmaz.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Ha a lekérdezéshez következtetett adattípusokat ismerünk, megadhatjuk a megfelelő adattípusokat:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Fileinfo és filepath függvények használata adott partíciók célzásához

Az adathalmazok gyakran partíciókban vannak rendszerezve. Az SQL igény szerint kérhető az adott mappák és fájlok lekérdezésére. Ez a függvény csökkenti a fájlok számát és az adatmennyiséget, amelyet a lekérdezésnek olvasni és feldolgoznia kell. A hozzáadott bónusz az, hogy jobb teljesítményt érhet el.

További információért olvassa el a [filename](develop-storage-files-overview.md#filename-function) és a [filepath](develop-storage-files-overview.md#filepath-function) függvények és példák című témakört a [megadott fájlok lekérdezéséhez](query-specific-files.md).

> [!TIP]
> A filepath és a fileinfo függvények a megfelelő adattípusokhoz való továbbításának eredménye mindig. Ha karakteres adattípusokat használ, ügyeljen arra, hogy a megfelelő hossz legyen használatban.

> [!NOTE]
> A partíció-eltávolításhoz, a filepath és a fileinfo használt függvények jelenleg nem támogatottak az Azure szinapszis Analytics szolgáltatásban Apache Spark által létrehozott táblákon kívüli, az egyes táblákhoz automatikusan létrehozott külső táblák esetében.

Ha a tárolt adatok nincsenek particionálva, érdemes particionálni, hogy ezek a függvények a fájlokra irányuló lekérdezések optimalizálására legyenek optimalizálva. Ha az [Azure szinapszis-táblák particionált Apache Spark lekérdezését](develop-storage-files-spark-tables.md) az SQL igény szerint, a lekérdezés automatikusan csak a szükséges fájlokat fogja megcélozni.

## <a name="use-parser_version-20-for-querying-csv-files"></a>PARSER_VERSION 2,0 használata CSV-fájlok lekérdezéséhez

A CSV-fájlok lekérdezése során teljesítményre optimalizált elemzőt használhat. Részletekért olvassa el a [PARSER_VERSION](develop-openrowset.md) .

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezések teljesítményének és illesztésének növeléséhez

A [CETAS](develop-tables-cetas.md) az SQL igény szerint elérhető legfontosabb funkcióinak egyike. A CETAS egy párhuzamos művelet, amely létrehozza a külső tábla metaadatait, és exportálja a SELECT lekérdezési eredményeket a Storage-fiókban lévő fájlok készletére.

A CETAS használatával a lekérdezések gyakran használt részeit (például az összekapcsolt hivatkozási táblákat) a fájlok új készletéhez is tárolhatja. Ezután csatlakozhat ehhez az egyetlen külső táblához, és nem kell ismétlődő közös illesztéseket használnia több lekérdezésben.

Ahogy a CETAS a parketta-fájlokat hozza létre, a statisztikák automatikusan létrejönnek, amikor az első lekérdezés ezt a külső táblázatot célozza meg, ami jobb teljesítményt eredményez.

## <a name="aad-pass-through-performance"></a>HRE átmenő teljesítmény

Az SQL on-demand lehetővé teszi a tárolóban lévő fájlok elérését a HRE átmenő vagy SAS hitelesítő adatok használatával. Az SAS-vel való összehasonlítás során lassabb teljesítményt tapasztalhat a HRE. 

Ha jobb teljesítményre van szüksége, próbálja ki az SAS hitelesítő adatait a tároló eléréséhez, amíg a HRE-teljesítmény nem javul.

## <a name="next-steps"></a>További lépések

Tekintse át a gyakori problémákkal és megoldásokkal kapcsolatos [hibaelhárítási](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) cikket. Ha SQL-készlet helyett SQL-készlettel dolgozik, tekintse meg az [ajánlott eljárásokat az SQL-készlethez](best-practices-sql-pool.md) című cikket a konkrét útmutatásért.
