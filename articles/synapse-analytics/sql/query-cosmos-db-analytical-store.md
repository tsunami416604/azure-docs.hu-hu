---
title: A kiszolgáló nélküli SQL-készlettel Azure Cosmos DB-adatlekérdezés az Azure szinapszis hivatkozásában (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le Azure Cosmos DB az SQL igény szerinti használatával az Azure szinapszis-hivatkozáson (előzetes verzió).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: c5fa326fa05a34ae5b51054b867a766489b85c16
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670697"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Lekérdezés Azure Cosmos DB az Azure-beli kiszolgáló nélküli SQL-készlettel az Azure szinapszis-hivatkozás (előzetes verzió)

A szinapszis kiszolgáló nélküli SQL-készlete (korábban SQL on-demand) lehetővé teszi az olyan Azure Cosmos DB-tárolókban lévő adatok elemzését, amelyek az [Azure szinapszis hivatkozásával](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) közel valós időben engedélyezve vannak, anélkül, hogy ez hatással lenne a tranzakciós számítási feladatok teljesítményére. Jól ismert T-SQL-szintaxist kínál, amely az [analitikus áruházból](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) származó adatok lekérdezését, valamint a bi-és ad-hoc lekérdezési eszközök széles köréhez való integrált csatlakozást biztosít a t-SQL felületen keresztül.

Azure Cosmos DB lekérdezéséhez a [OpenRowset](develop-openrowset.md) függvény a teljes [kijelölés](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) felületét támogatja, beleértve az [SQL-függvények és-operátorok](overview-features.md)többségét. Azt is megteheti, hogy a lekérdezés eredményeit a Azure Cosmos DB az Azure Blob Storage vagy Azure Data Lake Storage a [külső tábla létrehozása lehetőséggel](develop-tables-cetas.md#cetas-in-sql-on-demand)együtt beolvassa az adatokat. A kiszolgáló nélküli SQL-készlet lekérdezési eredményei jelenleg nem tárolhatók a [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand)használatával Azure Cosmos db.

Ebből a cikkből megtudhatja, hogyan írhat lekérdezéseket kiszolgáló nélküli SQL-készlettel, amely az Azure Cosmos DB tárolók adatait fogja lekérdezni, amelyeken engedélyezve van a szinapszis-hivatkozás. Ezután további információt olvashat a kiszolgáló nélküli SQL-készlet nézeteinek létrehozásáról Azure Cosmos DB tárolók között, és összekapcsolhatja őket [az oktatóanyag Power bi](./tutorial-data-analyst.md) modelljeivel. 

> [!IMPORTANT]
> Ez az oktatóanyag olyan [Azure Cosmos db jól definiált sémával](../../cosmos-db/analytical-store-introduction.md#schema-representation) rendelkező tárolót használ, amely a jövőben támogatott lekérdezési élményt nyújt. A kiszolgáló nélküli SQL-készlet lekérdezési élménye [Azure Cosmos db teljes hűségű sémához](#full-fidelity-schema) olyan ideiglenes viselkedés, amely az előzetes visszajelzések alapján módosítva lesz. Ne támaszkodjon arra a sémára, amelyet a `OPENROWSET` függvény a teljes hűségű tárolók számára biztosít a nyilvános előzetes verzió során, mert előfordulhat, hogy a lekérdezés experinece módosítva lett, és jól definiált sémával van igazítva. Visszajelzés kéréséhez lépjen kapcsolatba a [szinapszis link Product csapatával](mailto:cosmosdbsynapselink@microsoft.com) .

## <a name="overview"></a>Áttekintés

Azure Cosmos DB analitikus tárolóban található adatok lekérdezéséhez és elemzéséhez a kiszolgáló nélküli SQL-készlet a következő `OPENROWSET` szintaxist használja:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A Azure Cosmos DB kapcsolódási karakterlánc megadja a Azure Cosmos DB fiók nevét, az adatbázis nevét, az adatbázis-fiók főkulcsát, és egy választható régió nevét `OPENROWSET` . 

> [!IMPORTANT]
> Győződjön meg arról, hogy az aliast az után használja `OPENROWSET` . Létezik egy [ismert probléma](#known-issues) , amelynek hatására a kapcsolódási probléma a kiszolgáló nélküli SQL-végpontra mutat, ha nem adja meg az aliast a `OPENROWSET` függvény után.

A kapcsolatok karakterláncának formátuma a következő:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

A Azure Cosmos DB tároló neve idézőjelek nélkül van megadva a `OPENROWSET` szintaxisban. Ha a tároló neve speciális karaktereket tartalmaz (például kötőjelet (-)), a nevet a `[]` szintaxisban a (szögletes zárójelben) belül kell becsomagolni `OPENROWSET` .

> [!NOTE]
> A kiszolgáló nélküli SQL-készlet nem támogatja Azure Cosmos DB tranzakciós tároló lekérdezését.

## <a name="sample-data-set"></a>Minta adathalmaz

A jelen cikkben szereplő példák az [Európai Betegségmegelőzési és Járványvédelmi (ECDC) COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) és a [COVID-19 nyílt kutatási adatkészlet (Cord-19), a Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)adatokon alapulnak. 

Ezen lapokon láthatja a licenceket és az adatszerkezetet, és letölthető mintaadatok az [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) és a [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) adatkészletekhez.

Ahhoz, hogy követni tudja, hogyan lehet lekérdezni Cosmos DB-adatbázisokat kiszolgáló nélküli SQL-készlettel, győződjön meg arról, hogy a következő erőforrásokat hozza létre:
* Olyan Azure Cosmos DB adatbázis-fiók, amely engedélyezve van a [szinapszis-hivatkozás](../../cosmos-db/configure-synapse-link.md)
* Egy Azure Cosmos DB adatbázis neve `covid`
* Két Azure Cosmos DB tároló `EcdcCases` és `Cord19` a fenti minta adatkészletek betöltve.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Ismerkedjen meg Azure Cosmos DBával az automatikus séma-következtetéssel

Azure Cosmos DBban az adatelemzés legegyszerűbb módja az automatikus séma-következtetési képesség kihasználása. Ha kihagyja a `WITH` záradékot az `OPENROWSET` utasításból, utasíthatja a kiszolgáló nélküli SQL-készletet az Azure Cosmos db tároló analitikai tárolójának sémájának automatikus észlelésére (következtetésre).

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
A fenti példában arra utasítja a kiszolgáló nélküli SQL-készletet, hogy a `covid` `MyCosmosDbAccount` Azure Cosmos db kulcs használatával hitelesített Azure Cosmos db fiókban található adatbázishoz kapcsolódjon. Ezután elérjük a tároló `EcdcCases` analitikus tárolóját a `West US 2` régióban. Mivel a tulajdonságok nem rendelkeznek kivetítéssel, `OPENROWSET` a Function a Azure Cosmos db elemek összes tulajdonságát visszaküldi.

Ha más tárolóból származó adatokkal kell megvizsgálnia ugyanabban a Azure Cosmos DB-adatbázisban, használhatja ugyanazt a kapcsolódási karakterláncot és a hivatkozáshoz szükséges tárolót harmadik paraméterként:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Séma explicit meghatározása

Habár az automatikus séma-következtetési funkció `OPENROWSET` egy egyszerű, könnyen használható querience biztosít, az üzleti forgatókönyvek esetében előfordulhat, hogy explicit módon meg kell adnia a sémát a csak olvasható tulajdonságokhoz a Azure Cosmos db adatokból.

`OPENROWSET` lehetővé teszi explicit módon megadhatja, hogy milyen tulajdonságokat szeretne beolvasni a tárolóban lévő adatokból, és megadhatja az adattípusokat. Tegyük fel, hogy az [ECDC COVID-adatkészletből](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) importáltunk néhány adatát a következő szerkezettel Azure Cosmos DBba:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Ezek a Azure Cosmos DBban található, a szinapszis SQL-ben található, sorok és oszlopok halmaza használható, egyszerű JSON-dokumentumok. `OPENROWSET` a függvény lehetővé teszi, hogy megadhatja az elolvasni kívánt tulajdonságok egy részhalmazát, valamint a záradékban szereplő pontos oszlop típusait `WITH` :

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

A lekérdezés eredménye az alábbihoz hasonló lehet:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Tekintse át a cikk végén található [SQL Type-hozzárendelések szabályait](#azure-cosmos-db-to-sql-type-mappings) a Azure Cosmos db értékhez használandó SQL-típusokkal kapcsolatos további információkért.

## <a name="querying-nested-objects-and-arrays"></a>Beágyazott objektumok és tömbök lekérdezése

A Azure Cosmos DB lehetővé teszi, hogy összetettebb adatmodelleket képviselje beágyazott objektumokként vagy tömbökként. A szinapszis-hivatkozás automatikus szinkronizálási funkciója Azure Cosmos DB kezeli a séma megjelenítését az analitikai tárolóban, amely magában foglalja a beágyazott adattípusok kezelését, amely lehetővé teszi a kiszolgáló nélküli SQL-készletből történő részletes lekérdezéseket.

Például a [kábel-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) adathalmaz JSON-dokumentumokkal rendelkezik a következő szerkezettel:

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

A lekérdezés eredménye az alábbihoz hasonló lehet:

| cím | szerzők | first_autor_name |
| --- | --- | --- |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Alternatív megoldásként megadhatja a beágyazott értékek elérési útját is az objektumokban a záradék használatakor `WITH` :

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

További információ az [összetett adattípusok elemzéséről a szinapszis-hivatkozásokban](../how-to-analyze-complex-schema.md) és [a beágyazott struktúrákban a kiszolgáló nélküli SQL-készletben](query-parquet-nested-types.md).

> [!IMPORTANT]
> Ha a szövegben nem várt karakterek jelennek meg, például `MÃƒÂ©lade` ahelyett, `Mélade` hogy az adatbázis-rendezés nem az [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -rendezésre van beállítva. 
> [Módosítsa az adatbázis rendezését](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) néhány UTF8-rendezésre úgy, hogy néhány SQL-utasítást használ, például: `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Beágyazott tömbök összeolvasztása

Azure Cosmos DB adatok lehetnek beágyazott altömbök, például a szerző tömbje a [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) -adatkészletből:

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

Bizonyos esetekben előfordulhat, hogy a felső elemből (metaadatok) a tömb (szerzők) minden elemével "csatlakoztatni kell" a tulajdonságokat. A kiszolgáló nélküli SQL-készlet lehetővé teszi a beágyazott struktúrák összeolvasztását a `OPENJSON` függvény a beágyazott tömbön való alkalmazásával:

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

A lekérdezés eredménye az alábbihoz hasonló lehet:

| cím | szerzők | első | utolsó | hovatartozás |
| --- | --- | --- | --- | --- |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Kiegészítő információk öko-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Kiegészítő információk öko-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Katalin | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Ha a szövegben nem várt karakterek jelennek meg, például `MÃƒÂ©lade` ahelyett, `Mélade` hogy az adatbázis-rendezés nem az [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -rendezésre van beállítva. 
> [Módosítsa az adatbázis rendezését](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) néhány UTF8-rendezésre úgy, hogy néhány SQL-utasítást használ, például: `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB az SQL-típusok megfeleltetéséhez

Fontos megjegyezni, hogy habár Azure Cosmos DB tranzakciós tároló séma-agnosztikus, az analitikai tároló sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. A szinapszisok automatikus szinkronizálási funkciójával a Azure Cosmos DB kezeli a séma megjelenítését az analitikus tárolóban, amely magában foglalja a beágyazott adattípusok kezelését. Mivel a kiszolgáló nélküli SQL-készlet lekérdezi az analitikai tárolót, fontos tisztában lennie azzal, hogyan képezhetők le Azure Cosmos DB bemeneti adattípusok az SQL-adattípusokhoz.

Azure Cosmos DB SQL (Core) API-fiókok esetében a JSON-tulajdonságok száma, karakterlánc, logikai, null, beágyazott objektum vagy tömb. Olyan SQL-típusokat kell választania, amelyek megfelelnek ezeknek a JSON-típusoknak, ha a `WITH` záradékot használja `OPENROWSET` . Tekintse meg azokat az SQL-oszlopokat, amelyeket a Azure Cosmos DB különböző típusú tulajdonságainál kíván használni.

| Azure Cosmos DB tulajdonság típusa | SQL-oszlop típusa |
| --- | --- |
| Logikai | bit |
| Egész szám | bigint |
| Tizedesjegy | float |
| Sztring | varchar (UTF8-adatbázis rendezése) |
| Dátum és idő (ISO formátumú karakterlánc) | varchar (30) |
| Dátum és idő (Unix timestamp) | bigint |
| Null | `any SQL type` 
| Beágyazott objektum vagy tömb | varchar (max) (UTF8-adatbázis rendezése), JSON-szövegként szerializálva |

## <a name="full-fidelity-schema"></a>Teljes hűségű séma

Azure Cosmos DB a teljes hűségű séma a tároló összes tulajdonságához az értékeket és a legjobb egyezési típusokat rögzíti.
`OPENROWSET` a teljes hűségű sémával rendelkező tárolók függvénye az egyes cellákban mind a típust, mind a tényleges értéket tartalmazza. Tegyük fel, hogy a következő lekérdezés a teljes hűségű sémával rendelkező tároló elemeit olvassa be:

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

Minden értéknél megtekintheti Cosmos DB tároló elemben azonosított típust. A tulajdonság értékeinek többsége `date_rep` `date` értékeket tartalmaz, de egyesek helytelenül vannak tárolva karakterláncként Cosmos db. A teljes hűségű séma a helyesen beírt `date` értékeket és a helytelenül formázott értékeket is visszaküldi `string` .
Az esetek száma az értékként tárolt információ `int32` , de egy érték decimális számként van megadva. Az érték `float64` típusa. Ha vannak olyan értékek, amelyek meghaladják a legnagyobb `int32` számot, a típusként lesznek tárolva `int64` . A `geo_id` példában szereplő összes érték típusaként van tárolva `string` .

> [!IMPORTANT]
> A teljes hűség séma mindkét értéket a várt típusokkal és a helytelenül beírt típusokkal rendelkező értékekkel teszi elérhetővé.
> Az Azure Cosmos DB tárolóban helytelen típusú értékeket kell megtisztítani, hogy a teljes hűségű analitikus tárolóban alkalmazni lehessen a megrendelést. 

A Mongo DB API-fajta Azure Cosmos DB-fiókjainak lekérdezéséhez további információt talál a teljes körű megbízhatósági séma megjelenítéséről az analitikus tárolóban, valamint az [itt](../../cosmos-db/analytical-store-introduction.md#analytical-schema)használandó bővített tulajdonságokat.

A teljes hűségű séma lekérdezése során explicit módon meg kell adnia az SQL-típust és a várt Cosmos DB tulajdonság típusát a `WITH` záradékban. A következő példában feltételezzük, hogy a tulajdonsághoz `string` és a megfelelő típusú tulajdonsághoz megfelelő típus van `geo_id` `int32` `cases` :

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

A más típusokkal rendelkező értékek nem lesznek visszaadva a `geo_id` és `cases` az oszlopokban, és a lekérdezés a `NULL` cellákban lévő értéket adja vissza. Ez a lekérdezés csak a `cases` () kifejezésben megadott típusra hivatkozik `cases.int32` . Ha olyan értékekkel rendelkezik, `cases.int64` `cases.float64` amelyek nem törölhetők Cosmos db tárolóban, akkor explicit módon hivatkozni kell rájuk a `WITH` záradékban, és össze kell állítania az eredményeket. A következő lekérdezés összesíti a (z) `int32` `int64` és az `float64` oszlopban tárolt adatokat `cases` :

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

Ebben a példában az esetek száma a következőképpen van tárolva `int32` `int64` :,, vagy `float64` értékek, és az összes értéket ki kell vonni az esetek országonkénti számának kiszámításához. 

## <a name="known-issues"></a>Ismert problémák

- Az **aliast** a függvény után kell megadni `OPENROWSET` (például: `OPENROWSET (...) AS function_alias` ). Az alias kihagyása okozhatja a csatlakoztatási problémákat, és a kiszolgáló nélküli SQL-végpont átmenetileg nem érhető el. Ezt a problémát november 2020-én oldja fel a rendszer.
- A kiszolgáló nélküli SQL-készlet lekérdezési élménye [Azure Cosmos db teljes hűségű sémához](#full-fidelity-schema) olyan ideiglenes viselkedés, amely az előzetes visszajelzések alapján módosul. Ne támaszkodjon arra a sémára, amelyet a `OPENROWSET` függvény a nyilvános előzetes verzió során biztosít, mivel előfordulhat, hogy a lekérdezési élmény jól definiált sémával van igazítva. Visszajelzés kéréséhez lépjen kapcsolatba a [szinapszis link Product csapatával](mailto:cosmosdbsynapselink@microsoft.com) .

A lehetséges hibák és hibaelhárítási műveletek az alábbi táblázatban láthatók:

| Hiba | Gyökérok |
| --- | --- |
| Szintaktikai hibák:<br/> -Helytelen szintaxis a "OpenRowset" közelében<br/> - `...` a nem egy felismert TÖMEGES OPENROWSET-szolgáltatói beállítás.<br/> -Helytelen szintaxis közel `...` | Lehetséges kiváltó okok<br/> – Nem a "CosmosDB" paramétert használja az első paraméterként.<br/> -Karakterlánc használata az azonosító helyett a harmadik paraméterben,<br/> -Nem adja meg a harmadik paramétert (tároló neve) |
| Hiba történt a CosmosDB-kapcsolatok karakterláncában | -A fiók, az adatbázis, a kulcs nincs megadva <br/> – A rendszer nem ismeri fel a kapcsolatok karakterláncának néhány lehetőségét.<br/> – A pontosvesszőt `;` a rendszer a kapcsolatok karakterláncának végére helyezi |
| A CosmosDB elérési útjának feloldása sikertelen volt, hiba: "helytelen fióknév" vagy "helytelen adatbázisnév" | A megadott fióknév, adatbázisnév vagy tároló nem található, vagy az analitikai tár nincs engedélyezve a megadott gyűjteményben.|
| A CosmosDB elérési útjának feloldása sikertelen volt, hiba: "helytelen titkos érték" vagy "titkos kód null vagy üres" | A fiók kulcsa érvénytelen vagy hiányzik. |
| A `column name` típusú oszlop `type name` nem kompatibilis a külső adattípussal `type name` | A megadott oszlop típusa a `WITH` záradékban nem egyezik meg Cosmos db tárolóban szereplő típussal. Próbálja meg módosítani az oszlop típusát, ahogy az a következő szakaszban szerepel: [Azure Cosmos db SQL Type-hozzárendelések](#azure-cosmos-db-to-sql-type-mappings) vagy- `VARCHAR` típus használata. |
| Az oszlop az `NULL` összes cellában található értékeket tartalmaz. | Valószínűleg helytelen oszlopnév vagy elérésiút-kifejezés a `WITH` záradékban. Az oszlopnév (vagy az oszlop típusa után a Path kifejezés) a `WITH` záradékban meg kell egyeznie a Cosmos db gyűjteményben szereplő egyes tulajdonságnév nevével. Az összehasonlítás megkülönbözteti a **kis-és nagybetűket**  (például `productCode` `ProductCode` különböző tulajdonságokat). |

A javaslatok és a problémák jelentése az [Azure szinapszis visszajelzéseit ismertető oldalon](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)található.

## <a name="next-steps"></a>Következő lépések

További információkat az következő cikkekben talál:

- [Power BI és kiszolgáló nélküli szinapszis SQL-készlet használata az Azure szinapszis hivatkozásával](../../cosmos-db/synapse-link-power-bi.md)
- [Hogyan hozhatók létre és használhatók nézetek az SQL igény szerinti használatával](create-use-views.md) 
- [Oktatóanyag az SQL on-demand nézeteinek létrehozásához Azure Cosmos DB és a DirectQuery-on keresztüli Power BI modellekhez való csatlakoztatásához](./tutorial-data-analyst.md)
