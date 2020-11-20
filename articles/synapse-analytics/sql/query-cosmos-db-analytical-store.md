---
title: Az Azure szinapszis link Preview-ban található kiszolgáló nélküli SQL-készlettel Azure Cosmos DB-adatlekérdezés
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le Azure Cosmos DB egy kiszolgáló nélküli SQL-készlet használatával az Azure szinapszis link Preview-ban.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: eda05cbdf2f5b077fd6cf217a00cc58b1c6eda27
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986640"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link-preview"></a>Az Azure szinapszis link Preview-ban található kiszolgáló nélküli SQL-készlettel rendelkező lekérdezés Azure Cosmos DB

A kiszolgáló nélküli SQL-készletek lehetővé teszik az [Azure szinapszis-hivatkozással](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) rendelkező Azure Cosmos db tárolókban lévő adatok elemzését, a tranzakciós feladatok teljesítményének befolyásolása nélkül. Jól ismert T-SQL-szintaxist biztosít az [analitikus áruházból](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) származó adatok lekérdezéséhez és az üzleti intelligenciával (BI) és az alkalmi lekérdezési eszközökhöz való integrált kapcsolódáshoz a t-SQL felületen keresztül.

Azure Cosmos DB lekérdezéséhez a [OpenRowset](develop-openrowset.md) függvény a teljes [kijelölés](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) felületét támogatja, amely az [SQL-függvények és operátorok](overview-features.md)többségét tartalmazza. A lekérdezés eredményeit olyan módon is tárolhatja, amely Azure Cosmos DB adatokat olvas be az Azure Blob Storage vagy Azure Data Lake Storage használatával, [külső tábla létrehozása Select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) néven. A kiszolgáló nélküli SQL-készlet lekérdezési eredményei jelenleg nem tárolhatók a CETAS használatával Azure Cosmos DB.

Ebből a cikkből megtudhatja, hogyan írhat lekérdezést olyan kiszolgáló nélküli SQL-készlettel, amely az Azure szinapszis-hivatkozással engedélyezett Azure Cosmos DB tárolóból származó adatok lekérdezésére szolgál. Ezután további információt olvashat a kiszolgáló nélküli SQL-készlet nézeteinek létrehozásáról Azure Cosmos DB tárolók között, és összekapcsolhatja őket az [oktatóanyag](./tutorial-data-analyst.md)Power bi modelljeivel.

> [!IMPORTANT]
> Ez az oktatóanyag egy [Azure Cosmos db jól definiált sémával](../../cosmos-db/analytical-store-introduction.md#schema-representation)rendelkező tárolót használ. A kiszolgáló nélküli SQL-készlet lekérdezési élménye, amely egy [Azure Cosmos db teljes hűségű séma](#full-fidelity-schema) számára biztosít ideiglenes viselkedést, amely az előzetes visszajelzés alapján változik. Ne használja a függvény eredményhalmaz sémáját anélkül, `OPENROWSET` `WITH` hogy a záradék egy teljes megbízhatósági sémával beolvassa az adatokat egy tárolóból, mert a lekérdezési élmény összhangban van a és a jól definiált séma alapján. Visszajelzését az [Azure szinapszis Analytics visszajelzési fórumában](https://feedback.azure.com/forums/307516-azure-synapse-analytics)teheti közzé. A visszajelzések megadásához az [Azure szinapszis link Product csapatával](mailto:cosmosdbsynapselink@microsoft.com) is kapcsolatba léphet.

## <a name="overview"></a>Áttekintés

Egy Azure Cosmos DB analitikus tárolóban lévő adatok lekérdezéséhez és elemzéséhez a kiszolgáló nélküli SQL-készlet a következő `OPENROWSET` szintaxist használja:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A Azure Cosmos DB kapcsolódási karakterlánc megadja az Azure Cosmos DB fiók nevét, az adatbázis nevét, az adatbázis-fiók főkulcsát, valamint a függvény választható régiójának nevét `OPENROWSET` .

> [!IMPORTANT]
> Győződjön meg arról, hogy UTF-8 adatbázis-rendezést használ, például mert a `Latin1_General_100_CI_AS_SC_UTF8` Azure Cosmos db analitikus tárolóban lévő karakterlánc-értékek UTF-8 szövegként vannak kódolva.
> A fájl és a rendezés szöveges kódolása nem egyezik meg a szöveges konvertálás során fellépő hibák miatt.
> A T-SQL-utasítás használatával egyszerűen módosíthatja az aktuális adatbázis alapértelmezett rendezését `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

A kapcsolatok karakterláncának formátuma a következő:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

A Azure Cosmos DB tároló neve idézőjelek nélkül van megadva a `OPENROWSET` szintaxisban. Ha a tároló neve speciális karaktereket tartalmaz, például egy kötőjelet (-), a nevet szögletes zárójelbe () kell becsomagolni `[]` a `OPENROWSET` szintaxisban.

> [!NOTE]
> Egy kiszolgáló nélküli SQL-készlet nem támogatja Azure Cosmos DB tranzakciós tárolók lekérdezését.

## <a name="sample-dataset"></a>Minta adatkészlet

A cikkben szereplő példák az [Európai Betegségmegelőzési és Járványvédelmi Központ (ECDC) COVID-19 esetek](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) és a [COVID-19 nyílt kutatási adatkészlet (Cord-19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)adatokon alapulnak.

Ezen lapokon láthatja a licencet és az adatszerkezetet. Az [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) és a [Cord-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) adatkészletek mintaadatok is letölthetők.

Ahhoz, hogy követni tudja, hogyan lehet lekérdezni Azure Cosmos DB-adatbázisokat egy kiszolgáló nélküli SQL-készlettel, győződjön meg arról, hogy a következő erőforrásokat hozza létre:

* Egy Azure Cosmos DB adatbázis-fiók, amelyen [engedélyezve van az Azure szinapszis-hivatkozás](../../cosmos-db/configure-synapse-link.md).
* Egy nevű Azure Cosmos DB adatbázis `covid` .
* Két Azure Cosmos DB tároló, `EcdcCases` `Cord19` amely az előző minta adatkészletekkel együtt lett megnevezve és betöltve.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Ismerkedjen meg Azure Cosmos DBával az automatikus séma-következtetéssel

A Azure Cosmos DBban lévő adatvizsgálatok legegyszerűbb módja az automatikus séma-következtetési lehetőség használata. Ha kihagyja a `WITH` záradékot az `OPENROWSET` utasításból, utasíthatja a kiszolgáló nélküli SQL-készletet a Azure Cosmos db tároló analitikai tárolójának sémájának automatikus észlelésére (következtetésre).

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
Az előző példában arra utasította a kiszolgáló nélküli SQL-készletet, hogy a `covid` Azure Cosmos db kulccsal hitelesített Azure Cosmos db fiókban található adatbázishoz kapcsolódjon `MyCosmosDbAccount` (az előző példában szereplő dummy). Ezután elérjük a `EcdcCases` tároló analitikus tárolóját a `West US 2` régióban. Mivel a tulajdonságok nem rendelkeznek kivetítéssel, a `OPENROWSET` függvény a Azure Cosmos db elemek összes tulajdonságát visszaküldi.

Feltételezve, hogy a Azure Cosmos DB tárolóban található elemek a `date_rep` `cases` , a és a `geo_id` tulajdonsággal rendelkeznek, a lekérdezés eredményei a következő táblázatban láthatók:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Ha más tárolóból származó adatokkal kell megvizsgálnia ugyanabban a Azure Cosmos DB-adatbázisban, használhatja ugyanazt a kapcsolódási karakterláncot, és a szükséges tárolóra hivatkozhat harmadik paraméterként:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Séma explicit meghatározása

Habár az automatikus séma-következtetési funkció `OPENROWSET` egyszerű és könnyen használható querience biztosít, az üzleti forgatókönyvek esetében előfordulhat, hogy explicit módon meg kell adnia a sémát a csak olvasható tulajdonságok közül a Azure Cosmos db adatokból.

A `OPENROWSET` függvény segítségével explicit módon megadhatja, hogy milyen tulajdonságokat szeretne beolvasni a tárolóban lévő adatokból, és megadhatja az adattípusokat.

Tegyük fel, hogy az [ECDC COVID adatkészletből](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) importáltunk néhány adatát a következő szerkezettel Azure Cosmos DBba:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Ezek a Azure Cosmos DBban található, a szinapszis SQL-ben található, sorok és oszlopok halmaza használható, egyszerű JSON-dokumentumok. A `OPENROWSET` függvény lehetővé teszi, hogy megadhatja az elolvasni kívánt tulajdonságok egy részhalmazát, valamint a záradékban szereplő pontos oszlop típusait `WITH` :

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

A lekérdezés eredménye a következő táblázathoz hasonló lehet:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

A Azure Cosmos DB értékekhez használandó SQL-típusokkal kapcsolatos további információkért tekintse meg a cikk végén található [SQL Type-hozzárendelések szabályait](#azure-cosmos-db-to-sql-type-mappings) .

## <a name="query-nested-objects-and-arrays"></a>Beágyazott objektumok és tömbök lekérdezése

A Azure Cosmos DB segítségével összetettebb adatmodelleket is megadhat, ha beágyazott objektumként vagy tömbökként készíti el őket. Az Azure szinapszis Azure Cosmos DB-hivatkozás automatikus szinkronizálási funkciója kezeli a séma megjelenítését az analitikai tárolóban, amely magában foglalja a beágyazott adattípusok kezelését, amelyek lehetővé teszik a kiszolgáló nélküli SQL-készletből való széleskörű lekérdezést.

A [kábel-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) ADATKÉSZLET például JSON-dokumentumokkal rendelkezik, amelyek ezt a struktúrát követik:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

A Azure Cosmos DB beágyazott objektumai és tömbök JSON-karakterláncként jelennek meg a lekérdezés eredményében, amikor a `OPENROWSET` függvény beolvassa őket. Az alábbi összetett típusok értékeinek az SQL-oszlopokban való beolvasására szolgáló lehetőség az SQL JSON-függvények használata:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

A lekérdezés eredménye a következő táblázathoz hasonló lehet:

| cím | szerzők | first_autor_name |
| --- | --- | --- |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Alternatív megoldásként megadhatja az objektumok beágyazott értékeinek elérési útját is, ha a `WITH` záradékot használja:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

További információ az [összetett adattípusok elemzéséről az Azure szinapszis-hivatkozásokban](../how-to-analyze-complex-schema.md) és a [beágyazott struktúrákban egy kiszolgáló nélküli SQL-készletben](query-parquet-nested-types.md).

> [!IMPORTANT]
> Ha a szövegben nem várt karakterek jelennek meg, például a `MÃƒÂ©lade` helyett `Mélade` , akkor az adatbázis-rendezés nem [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) rendezésre van beállítva.
> [Módosítsa az adatbázis rendezését](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) UTF-8 rendezésre egy olyan SQL-utasítás használatával, mint például: `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Beágyazott tömbök összeolvasztása

Azure Cosmos DB adatok lehetnek beágyazott altömbök, például a szerző tömbje egy [kábel-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) adatkészletből:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Bizonyos esetekben előfordulhat, hogy a felső elemből (metaadatok) a tömb (szerzők) minden elemével "csatlakoztatni kell" a tulajdonságokat. A kiszolgáló nélküli SQL-készletek lehetővé teszik a beágyazott struktúrák összeolvasztását a `OPENJSON` függvény a beágyazott tömbön való alkalmazásával:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

A lekérdezés eredménye a következő táblázathoz hasonló lehet:

| cím | szerzők | első | utolsó | hovatartozás |
| --- | --- | --- | --- | --- |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Kiegészítő információk öko-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Kiegészítő információk öko-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Katalin | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Ha a szövegben nem várt karakterek jelennek meg, például a `MÃƒÂ©lade` helyett `Mélade` , akkor az adatbázis-rendezés nem [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) rendezésre van beállítva. [Módosítsa az adatbázis rendezését](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) UTF-8 rendezésre egy olyan SQL-utasítás használatával, mint például: `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB az SQL-típusok megfeleltetéséhez

Habár Azure Cosmos DB tranzakciós tároló a séma-agnosztikus, az analitikai tároló sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. Az Azure szinapszis-hivatkozás automatikus szinkronizálási funkciójával a Azure Cosmos DB felügyeli a séma megjelenítését az analitikai tárolóban, amely magában foglalja a beágyazott adattípusok kezelését. Mivel a kiszolgáló nélküli SQL-készlet lekérdezi az analitikai tárolót, fontos tisztában lennie azzal, hogyan képezhetők le Azure Cosmos DB bemeneti adattípusok az SQL-adattípusokhoz.

Azure Cosmos DB SQL (Core) API-fiókok esetében a JSON-tulajdonságok száma, karakterlánc, logikai, null, beágyazott objektum vagy tömb. Olyan SQL-típusokat kell választania, amelyek megfelelnek ezeknek a JSON-típusoknak, ha a záradékát használja a alkalmazásban `WITH` `OPENROWSET` . A következő táblázat azokat az SQL-oszlopokat tartalmazza, amelyeket a Azure Cosmos DB különböző típusú tulajdonságaihoz kell használni.

| Azure Cosmos DB tulajdonság típusa | SQL-oszlop típusa |
| --- | --- |
| Logikai | bit |
| Egész szám | bigint |
| Tizedesjegy | float |
| Sztring | varchar (UTF-8 adatbázis-rendezés) |
| Dátum és idő (ISO-formázott karakterlánc) | varchar (30) |
| Dátum és idő (UNIX timestamp) | bigint |
| Null | `any SQL type` 
| Beágyazott objektum vagy tömb | varchar (max) (UTF-8 adatbázis-rendezés), JSON-szövegként szerializálva |

## <a name="full-fidelity-schema"></a>Teljes hűségű séma

Azure Cosmos DB a teljes hűségű séma a tároló összes tulajdonságához az értékeket és a legjobb egyezési típusokat rögzíti. A `OPENROWSET` teljes hűségű sémát tartalmazó tároló függvénye a típust és a tényleges értéket is megadja az egyes cellákban. Tegyük fel, hogy a következő lekérdezés a teljes hűségű sémával rendelkező tároló elemeit olvassa be:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

A lekérdezés eredménye JSON-szövegként formázott típusokat és értékeket ad vissza:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"Int32": "254"} | {"string": "RS"} |
| {"date": "2020-08-12"} | {"Int32": "235"}| {"string": "RS"} |
| {"date": "2020-08-11"} | {"Int32": "316"} | {"string": "RS"} |
| {"date": "2020-08-10"} | {"Int32": "281"} | {"string": "RS"} |
| {"date": "2020-08-09"} | {"Int32": "295"} | {"string": "RS"} |
| {"string": "2020/08/08"} | {"Int32": "312"} | {"string": "RS"} |
| {"date": "2020-08-07"} | {"float64":"339.0"} | {"string": "RS"} |

Minden értéknél megtekintheti az Azure Cosmos DB tároló elemben azonosított típust. A tulajdonsághoz tartozó értékek többsége `date_rep` `date` értékeket tartalmaz, de egyesek helytelenül vannak tárolva karakterláncként Azure Cosmos db. A teljes hűségű séma a helyesen beírt `date` értékeket és a helytelenül formázott értékeket is visszaküldi `string` .
Az esetek száma az `int32` értékként tárolt információ, de egy érték decimális számként van megadva. Az érték típusa a `float64` következő:. Ha vannak olyan értékek, amelyek meghaladják a legnagyobb `int32` számot, a rendszer a `int64` típusként tárolja őket. A `geo_id` példában szereplő összes érték típusaként van tárolva `string` .

> [!IMPORTANT]
> A `OPENROWSET` záradék nélküli függvény `WITH` mindkét értéket a várt típusokkal és a helytelenül beírt típusokkal rendelkező értékekkel teszi elérhetővé. Ez a függvény adatfeltárásra szolgál, nem pedig jelentéskészítéshez. Ne elemezze a függvény által visszaadott JSON-értékeket a jelentések létrehozásához. Jelentések létrehozásához használjon explicit [with záradékot](#query-items-with-full-fidelity-schema) . Az Azure Cosmos DB tárolóban helytelen típusú értékeket kell megtisztítani a teljes hűségű analitikus tárolóban a javítások alkalmazásához.

Ha le kell kérdezni Azure Cosmos DB fiókokat a Mongo DB API-típusból, többet tudhat meg az analitikai tárolóban lévő teljes hűségű séma-ábrázolásról és a [mi Azure Cosmos db analitikus tárolóban (előzetes verzió)](../../cosmos-db/analytical-store-introduction.md#analytical-schema)használni kívánt bővített tulajdonságnév használatáról?.

### <a name="query-items-with-full-fidelity-schema"></a>Teljes megbízhatósági sémával rendelkező elemek lekérdezése

A teljes hűségű séma lekérdezése során explicit módon meg kell adnia az SQL-típust és a várt Azure Cosmos DB tulajdonság típusát a `WITH` záradékban. Ne használjon `OPENROWSET` `WITH` záradékot a jelentésekben, mert az eredményhalmaz formátuma a visszajelzések alapján módosítható az előzetes verzióban.

Az alábbi példában feltételezzük, hogy a `string` tulajdonság megfelelő típusa, `geo_id` és a tulajdonság típusa a következő `int32` `cases` :

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

A `geo_id` és `cases` más típusokkal rendelkező értékek értékként lesznek visszaadva `NULL` . Ez a lekérdezés csak a `cases` () kifejezésben megadott típusra hivatkozik `cases.int32` .

Ha olyan értékekkel rendelkezik, `cases.int64` `cases.float64` amelyek nem tisztíthatók Azure Cosmos db tárolóban, explicit módon hivatkozni kell rájuk egy `WITH` záradékban, és össze kell kapcsolniuk az eredményeket. A következő lekérdezés összesíti a (z) `int32` `int64` és az `float64` oszlopban tárolt adatokat `cases` :

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Ebben a példában az esetek száma a következőképpen van tárolva: `int32` , `int64` vagy `float64` érték. Az egyes országok esetében az esetek számának kiszámításához minden értéket ki kell vonni.

## <a name="known-issues"></a>Ismert problémák

- A kiszolgáló nélküli SQL-készlet lekérdezési élménye [Azure Cosmos db teljes hűségű sémához](#full-fidelity-schema) olyan ideiglenes viselkedés, amely az előzetes visszajelzések alapján módosul. Ne használja azt a sémát, amelyet a `OPENROWSET` záradék nélküli függvény biztosít a nyilvános előzetes verzióban, `WITH` mert a lekérdezési élmény a felhasználói visszajelzések alapján jól definiált sémával van igazítva. A visszajelzések megadásához forduljon az [Azure szinapszis link Product csapatához](mailto:cosmosdbsynapselink@microsoft.com).
- A kiszolgáló nélküli SQL-készlet nem ad vissza fordítási idejű hibát, ha az `OPENROWSET` oszlop rendezése nem rendelkezik UTF-8 kódolással. A `OPENROWSET` T-SQL-utasítás használatával egyszerűen módosíthatja az aktuális adatbázisban futó összes függvény alapértelmezett rendezését `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

A lehetséges hibák és hibaelhárítási műveletek az alábbi táblázatban láthatók.

| Hiba | Gyökérok |
| --- | --- |
| Szintaktikai hibák:<br/> -Helytelen szintaxis a "OpenRowset" közelében<br/> - `...` a nem egy felismert TÖMEGES OPENROWSET-szolgáltatói beállítás.<br/> -Helytelen szintaxis közel `...` | Lehetséges kiváltó okok:<br/> – A CosmosDB nem használja az első paraméterként.<br/> – A harmadik paraméterben szereplő azonosító helyett literális karakterláncot használjon.<br/> -Nem adja meg a harmadik paramétert (tároló neve). |
| Hiba történt a CosmosDB-kapcsolatok karakterláncában. | – Nincs megadva a fiók, az adatbázis vagy a kulcs. <br/> – A rendszer nem ismeri fel a kapcsolatok sztringjét.<br/> – A rendszer egy pontosvesszőt ( `;` ) helyez el a kapcsolatok karakterláncának végére. |
| A CosmosDB elérési útjának feloldása meghiúsult a következő hibával: "helytelen fióknév" vagy "helytelen adatbázisnév". | Nem található a megadott fióknév, adatbázisnév vagy tároló, vagy az analitikai tár nem lett engedélyezve a megadott gyűjteményben.|
| A CosmosDB elérési útjának feloldása meghiúsult a "helytelen titkos érték" vagy a "Secret null értékű vagy üres" hiba miatt. | A fiók kulcsa érvénytelen vagy hiányzik. |
| `column name`A típus oszlopa `type name` nem kompatibilis a külső adattípussal `type name` . | A záradékban megadott oszlop típusa `WITH` nem egyezik a Azure Cosmos db tárolóban szereplő típussal. Próbálja meg módosítani az oszlop típusát, ahogy az az [SQL-típusú hozzárendelések Azure Cosmos db](#azure-cosmos-db-to-sql-type-mappings)szakaszában szerepel, vagy használja a `VARCHAR` típust. |
| Az oszlop az `NULL` összes cellában található értékeket tartalmaz. | Valószínűleg helytelen oszlopnév vagy elérésiút-kifejezés szerepel a `WITH` záradékban. A záradékban szereplő oszlopnév (vagy az oszlop típusa után a Path kifejezés) `WITH` meg kell egyeznie a Azure Cosmos db gyűjtemény egyes tulajdonságainak nevével. Az összehasonlítás *megkülönbözteti a kis*-és nagybetűket. Például a `productCode` és `ProductCode` a különböző tulajdonságok. |

A javaslatok és a problémák jelentését az [Azure szinapszis Analytics visszajelzési oldalán](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)teheti meg.

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- [Power BI és kiszolgáló nélküli SQL-készlet használata az Azure szinapszis hivatkozásával](../../cosmos-db/synapse-link-power-bi.md)
- [Nézetek létrehozása és használata kiszolgáló nélküli SQL-készletben](create-use-views.md)
- [Oktatóanyag kiszolgáló nélküli SQL Pool-nézetek létrehozásához Azure Cosmos DB és a DirectQuery-en keresztüli Power BI modellekhez való csatlakoztatásához](./tutorial-data-analyst.md)
