---
title: Ajánlott eljárások az SQL igény szerinti használatra (előzetes verzió)
description: Javaslatok és ajánlott eljárások az SQL on-demand (előzetes verzió) használata esetén.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 79318ab67ec58ed10520365a366785ea0de41666
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836328"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Ajánlott eljárások az SQL igény szerinti használatára (előzetes verzió) az Azure szinapszis Analytics szolgáltatásban

Ebben a cikkben megtalálja az ajánlott eljárások gyűjteményét az SQL on-demand (előzetes verzió) használatához. Az SQL on-demand az Azure szinapszis Analytics egyik erőforrása.

## <a name="general-considerations"></a>Általános megfontolások

Az SQL on-demand lehetővé teszi fájlok lekérdezését az Azure Storage-fiókokban. Nem rendelkezik helyi tárolási vagy betöltési képességekkel. Tehát az összes olyan fájl, amelyet a lekérdezés a célként használt SQL-eszközökön kívülre mutat. A fájlok tárterületről való olvasásával kapcsolatos minden művelet hatással lehet a lekérdezés teljesítményére.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Az Azure Storage-fiók és az SQL igény szerinti elhelyezése

A késés csökkentése érdekében helyezze el az Azure Storage-fiókját és az SQL igény szerinti végpontját. A munkaterület létrehozása során kiépített Storage-fiókok és-végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha más Storage-fiókokhoz is hozzáfér az SQL on-demand szolgáltatással, győződjön meg róla, hogy ugyanabban a régióban van. Ha nem ugyanabban a régióban találhatók, az adatok hálózati átvitele nagyobb késéssel jár a távoli régió és a végpont régiója között.

## <a name="azure-storage-throttling"></a>Azure Storage-szabályozás

Előfordulhat, hogy több alkalmazás és szolgáltatás fér hozzá a Storage-fiókhoz. A tárolási szabályozás akkor fordul elő, ha az alkalmazások, szolgáltatások és az SQL igény szerinti munkaterhelése által generált kombinált IOPS vagy átviteli sebesség meghaladja a Storage-fiók korlátait. Ennek eredményeképpen jelentős negatív hatást tapasztal a lekérdezési teljesítményre.

A szabályozás észlelése esetén az SQL on-demand beépített kezeléssel rendelkezik a megoldásához. Az SQL igény szerint lassabban, a szabályozás feloldása után kéri a tárterületet.

> [!TIP]
> Az optimális lekérdezés-végrehajtás érdekében a lekérdezés végrehajtása során ne hangsúlyozzák a Storage-fiókot más munkaterhelésekkel.

## <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezéshez

Ha lehetséges, készíthet fájlokat a jobb teljesítmény érdekében:

- A CSV és a JSON konvertálása a parketta formátumba. A parketta oszlopos formátumú. Mivel tömörítve van, a fájlméretük kisebb, mint a CSV-vagy JSON-fájlok, amelyek ugyanazokat az adatmennyiségeket tartalmazzák. Az SQL on-demand kevesebb időt és kevesebb tárolási kérést igényel a beolvasáshoz.
- Ha egy lekérdezés egyetlen nagyméretű fájlt céloz meg, akkor a több kisebb fájlra is kihasználhatja.
- Próbálja meg a CSV-fájl méretét 10 GB alatt tartani.
- Jobb, ha azonos méretű fájlokat szeretne egy OPENROWSET elérési úthoz vagy egy külső tábla HELYéhez.
- Particionálja az adatait úgy, hogy a partíciókat különböző mappákba vagy fájlnevekre tárolja. Lásd: [fájlnév és filepath függvények használata adott partíciók célzásához](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Helyettesítő karakterek leküldése az elérési út alacsonyabb szintjeire

Az elérési úton helyettesítő karaktereket használhat [több fájl és mappa lekérdezéséhez](develop-storage-files-overview.md#query-multiple-files-or-folders). Az SQL on-demand listázza a Storage-fiókban lévő fájlokat, az elsőtől kezdve a Storage API használatával. Kiküszöböli a megadott elérési úttal nem egyező fájlokat. A fájlok kezdeti listájának csökkentése növelheti a teljesítményt, ha sok olyan fájl található, amely megfelel a megadott elérési útnak az első helyettesítő karakternek.

## <a name="use-appropriate-data-types"></a>Megfelelő adattípusok használata

A lekérdezésben használt adattípusok hatással vannak a teljesítményre. Ha követi a következő irányelveket, jobb teljesítményt érhet el: 

- Használja a legkisebb adatméretet, amely a lehető legnagyobb értéket fogja kielégíteni.
  - Ha a karakteres érték legfeljebb 30 karakter hosszúságú, akkor a karakter adattípusa 30.
  - Ha az összes karakteres oszlop értéke rögzített méretű, használja a **char** vagy a **NCHAR**. Ellenkező esetben használja a **varchar** vagy a **nvarchar**.
  - Ha a maximális egész oszlop értéke 500, használja a **smallint** , mert ez a legkisebb adattípus, amely képes erre az értékre. [Ebben a cikkben](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)az egész adattípus-tartományokat is megtalálhatja.
- Ha lehetséges, használja a **varchar** és a **char** helyett a **nvarchar** és a **NCHAR**értéket.
- Ha lehetséges, használja az egész szám alapú adattípust. A RENDEZÉSi, ILLESZTÉSi és csoportosítási műveletek a karakteres adatoknál gyorsabban, egész számokon vannak végrehajtva.
- Ha séma-következtetést használ, ellenőrizze a [késleltetett adattípusokat](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Késleltetett adattípusok keresése

A [séma-következtetések](query-parquet-files.md#automatic-schema-inference) segítségével gyorsan írhat lekérdezéseket, és megvizsgálhatja az adatfájl-sémák ismerete nélkül. Ennek a kényelemnek a díja, hogy a kikövetkeztetett adattípusok nagyobbak, mint a tényleges adattípusok. Ez akkor fordulhat elő, ha nincs elegendő információ a forrásfájlok számára a megfelelő adattípus használata érdekében. A Parquet-fájlok például nem tartalmaznak metaadatokat a karakteres oszlopok maximális hosszával kapcsolatban. Így az SQL igény szerint varchar (8000) értékűre következtet.

A lekérdezés eredményül kapott adattípusait a [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) használatával is megtekintheti.

Az alábbi példa bemutatja, hogyan optimalizálhatja a késleltetett adattípusokat. Ez az eljárás a késleltetett adattípusok megjelenítésére szolgál: 
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

Itt látható az eredményhalmaz:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Ha ismeri a lekérdezés késleltetett adattípusait, megadhatja a megfelelő adattípusokat:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Fájlnevek és filepath függvények használata adott partíciók célzásához

Az adathalmazok gyakran partíciókban vannak rendszerezve. Az SQL igény szerint kérhető az adott mappák és fájlok lekérdezésére. Ez csökkenti a fájlok számát és a lekérdezés által beolvasott és feldolgozandó adatmennyiséget. A hozzáadott bónusz az, hogy jobb teljesítményt érhet el.

További információért olvassa el a [filename](develop-storage-files-overview.md#filename-function) és a [filepath](develop-storage-files-overview.md#filepath-function) függvényt, és tekintse meg az [adott fájlok lekérdezésének](query-specific-files.md)példáit.

> [!TIP]
> A filepath és a filename függvények eredményét mindig a megfelelő adattípusokra konvertálja. Ha karakteres adattípusokat használ, ügyeljen arra, hogy a megfelelő hosszúságot használja.

> [!NOTE]
> A partíció-eltávolításhoz, a filepath és a filenamehez használt függvények jelenleg nem támogatottak a külső táblák esetében, az Apache Spark for Azure szinapszis Analytics szolgáltatásban létrehozott minden egyes tábla esetében automatikusan létrehozva.

Ha a tárolt adatai nincsenek particionálva, érdemes particionálni. Ezen függvények használatával optimalizálhatja azokat a lekérdezéseket, amelyek a fájlokat célozzák meg. Ha [lekérdezi az Azure-beli szinapszis-táblák particionált Apache Sparkeit](develop-storage-files-spark-tables.md) az SQL igény szerint, a lekérdezés automatikusan csak a szükséges fájlokat fogja megcélozni.

## <a name="use-parser_version-20-to-query-csv-files"></a>CSV-fájlok lekérdezése PARSER_VERSION 2,0 használatával

A CSV-fájlok lekérdezése teljesítményre optimalizált elemző használatával végezhető el. Részletekért lásd: [PARSER_VERSION](develop-openrowset.md).

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezések teljesítményének és illesztésének növeléséhez

A [CETAS](develop-tables-cetas.md) az SQL igény szerint elérhető legfontosabb funkcióinak egyike. A CETAS egy párhuzamos művelet, amely létrehozza a külső tábla metaadatait, és exportálja a SELECT lekérdezési eredményeket a Storage-fiókban lévő fájlok készletére.

A CETAS használatával a lekérdezések gyakran használt részeit (például az összekapcsolt hivatkozási táblákat) a fájlok új készletéhez is tárolhatja. Ezután ehhez az egyetlen külső táblához csatlakozhat, és nem kell ismétlődő közös illesztéseket használnia több lekérdezésben.

Ahogy a CETAS a parketta-fájlokat hozza létre, a statisztikák automatikusan létrejönnek, amikor az első lekérdezés ezt a külső táblázatot célozza meg, ami jobb teljesítményt eredményez.

## <a name="azure-ad-pass-through-performance"></a>Azure AD-alapú átmenő teljesítmény

Az SQL on-demand lehetővé teszi a tárolóban lévő fájlok elérését Azure Active Directory (Azure AD) áteresztő vagy SAS hitelesítő adatok használatával. Előfordulhat, hogy lassabb teljesítményt tapasztal az Azure AD-n keresztül, mint az SAS.

Ha jobb teljesítményre van szüksége, próbálja meg SAS hitelesítő adatok használatával hozzáférni a tárolóhoz, amíg az Azure AD átmenő teljesítmény nem javul.

## <a name="next-steps"></a>További lépések

Tekintse át a gyakori problémák megoldására vonatkozó [hibaelhárítási](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) cikket. Ha SQL-készletek helyett SQL-készleteket használ, tekintse meg az [ajánlott eljárásokat az SQL-készletekhez](best-practices-sql-pool.md) az adott útmutatáshoz.
