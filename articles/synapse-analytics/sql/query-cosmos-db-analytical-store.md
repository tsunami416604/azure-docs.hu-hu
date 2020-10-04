---
title: Az SQL Server nélküli Azure szinapszis-kapcsolaton keresztüli lekérdezés Azure Cosmos DB
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le Azure Cosmos DB az SQL igény szerinti használatával az Azure szinapszis-hivatkozáson (előzetes verzió).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9d36cee1ebf9f58991db6fd40fd6aa9422546b61
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710753"
---
# <a name="query-azure-cosmos-db-data-using-sql-serverless-in-azure-synapse-link-preview"></a>Az SQL Server nélküli Azure szinapszis-kapcsolaton keresztüli lekérdezés Azure Cosmos DB

A szinapszis SQL Server nélküli (korábban SQL on-demand) lehetővé teszi az olyan Azure Cosmos DB-tárolókban lévő adatok elemzését, amelyek az [Azure szinapszis-hivatkozással](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) közel valós időben engedélyezve vannak, anélkül, hogy ez hatással lenne a tranzakciós számítási feladatok teljesítményére. Jól ismert T-SQL-szintaxist kínál, amely az [analitikus áruházból](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) származó adatok lekérdezését, valamint a bi-és ad-hoc lekérdezési eszközök széles köréhez való integrált csatlakozást biztosít a t-SQL felületen keresztül.

> [!NOTE]
> Az SQL Server nélküli Azure Cosmos DB analitikus tároló lekérdezésének támogatása jelenleg a GateD Preview-ban érhető el. 

Azure Cosmos DB lekérdezéséhez a [OpenRowset](develop-openrowset.md) függvény a teljes [kijelölés](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) felületét támogatja, beleértve az [SQL-függvények és-operátorok](overview-features.md)többségét. Azt is megteheti, hogy a lekérdezés eredményeit a Azure Cosmos DB az Azure Blob Storage vagy Azure Data Lake Storage a [külső tábla létrehozása lehetőséggel](develop-tables-cetas.md#cetas-in-sql-on-demand)együtt beolvassa az adatokat. Az SQL Server nélküli lekérdezési eredményeket jelenleg nem tárolhatja a [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand)használatával Azure Cosmos db.

Ebből a cikkből megtudhatja, hogyan írhat egy lekérdezést az SQL Server nélküli lekérdezéssel, amely lekérdezi az Azure Cosmos DB tárolók adatait, amelyeken engedélyezve van a szinapszis-hivatkozás. Ezután további információt kaphat az SQL Server nélküli nézetek Azure Cosmos DB tárolókkal való létrehozásáról, valamint [az oktatóanyagban](./tutorial-data-analyst.md) található Power bi modellekhez való csatlakozásról. 

## <a name="overview"></a>Áttekintés

Azure Cosmos DB analitikus tárolóban található adatok lekérdezéséhez és elemzéséhez az SQL Server a következő `OPENROWSET` szintaxist használja:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

A Azure Cosmos DB kapcsolódási karakterlánc megadja a Azure Cosmos DB fiók nevét, az adatbázis nevét, az adatbázis-fiók főkulcsát, és egy választható régió nevét `OPENROWSET` . A kapcsolatok karakterláncának formátuma a következő:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

A Azure Cosmos DB tároló neve idézőjelek nélkül van megadva a `OPENROWSET` szintaxisban. Ha a tároló neve speciális karaktereket tartalmaz (például kötőjelet (-)), a nevet a `[]` szintaxisban a (szögletes zárójelben) belül kell becsomagolni `OPENROWSET` .

> [!NOTE]
> Az SQL Server nélküli nem támogatja Azure Cosmos DB tranzakciós tároló lekérdezését.

## <a name="sample-data-set"></a>Minta adathalmaz

A jelen cikkben szereplő példák az [Európai Betegségmegelőzési és Járványvédelmi (ECDC) COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) és a [COVID-19 nyílt kutatási adatkészlet (Cord-19), a Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)adatokon alapulnak. 

Ezen lapokon láthatja a licenceket és az adatszerkezetet, és letölthető mintaadatok az [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) és a [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) adatkészletekhez.

Ahhoz, hogy követni tudja, hogyan lehet lekérdezni Cosmos DB SQL Server nélküli lekérdezéseket, győződjön meg arról, hogy a következő erőforrásokat hozza létre:
* Olyan Azure Cosmos DB adatbázis-fiók, amely engedélyezve van a [szinapszis-hivatkozás](../../cosmos-db/configure-synapse-link.md)
* Egy Azure Cosmos DB adatbázis neve `covid`
* Két Azure Cosmos DB tároló `EcdcCases` és `Cord19` a fenti minta adatkészletek betöltve.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Ismerkedjen meg Azure Cosmos DBával az automatikus séma-következtetéssel

Azure Cosmos DBban az adatelemzés legegyszerűbb módja az automatikus séma-következtetési képesség kihasználása. Ha kihagyja a `WITH` záradékot az `OPENROWSET` utasításból, megadhatja az SQL-kiszolgálótól a Azure Cosmos db tároló analitikai tárolójának sémájának automatikus észlelését (következtetését).

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
A fenti példában arra utasítja az SQL Servert, hogy az adatbázishoz kapcsolódjon `covid` Azure Cosmos db fiókjában, amelyet `MyCosmosDbAccount` a Azure Cosmos db kulcs használatával hitelesített (a fenti példában szereplő dummy). Ezután elérjük a tároló `EcdcCases` analitikus tárolóját a `West US 2` régióban. Mivel a tulajdonságok nem rendelkeznek kivetítéssel, `OPENROWSET` a Function a Azure Cosmos db elemek összes tulajdonságát visszaküldi.

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

A Azure Cosmos DB lehetővé teszi, hogy összetettebb adatmodelleket képviselje beágyazott objektumokként vagy tömbökként. A szinapszis-hivatkozás automatikus szinkronizálási funkciója Azure Cosmos DB kezeli a séma megjelenítését az analitikai tárolóban, amely magában foglalja a beágyazott adattípusok kezelését, amelyek lehetővé teszik az SQL Server nélküli lekérdezéseket.

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

További információ az [összetett adattípusok elemzéséről a szinapszis-hivatkozásokban](../how-to-analyze-complex-schema.md) és [az SQL Server nélküli beágyazott struktúrákban](query-parquet-nested-types.md).

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

Bizonyos esetekben előfordulhat, hogy a felső elemből (metaadatok) a tömb (szerzők) minden elemével "csatlakoztatni kell" a tulajdonságokat. Az SQL Server nélküli lehetővé teszi, hogy beágyazott struktúrákat alkalmazzon a `OPENJSON` beágyazott tömbben lévő függvény alkalmazásával:

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

Fontos megjegyezni, hogy habár Azure Cosmos DB tranzakciós tároló séma-agnosztikus, az analitikai tároló sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. A szinapszisok automatikus szinkronizálási funkciójával a Azure Cosmos DB kezeli a séma megjelenítését az analitikus tárolóban, amely magában foglalja a beágyazott adattípusok kezelését. Mivel az SQL Server nélküli lekérdezi az analitikai tárolót, fontos tisztában lennie azzal, hogyan képezhetők le Azure Cosmos DB bemeneti adattípusok az SQL-adattípusokhoz.

Azure Cosmos DB SQL (Core) API-fiókok esetében a JSON-tulajdonságok száma, karakterlánc, logikai, null, beágyazott objektum vagy tömb. Olyan SQL-típusokat kell választania, amelyek megfelelnek ezeknek a JSON-típusoknak, ha a `WITH` záradékot használja `OPENROWSET` . Tekintse meg azokat az SQL-oszlopokat, amelyeket a Azure Cosmos DB különböző típusú tulajdonságainál kíván használni.

| Azure Cosmos DB tulajdonság típusa | SQL-oszlop típusa |
| --- | --- |
| Logikai érték | bit |
| Egész szám | bigint |
| Tizedesjegy | float |
| Sztring | varchar (UTF8-adatbázis rendezése) |
| Dátum és idő (ISO formátumú karakterlánc) | varchar (30) |
| Dátum és idő (Unix timestamp) | bigint |
| Null | `any SQL type` 
| Beágyazott objektum vagy tömb | varchar (max) (UTF8-adatbázis rendezése), JSON-szövegként szerializálva |

A Mongo DB API-fajta Azure Cosmos DB-fiókjainak lekérdezéséhez további információt talál a teljes körű megbízhatósági séma megjelenítéséről az analitikus tárolóban, valamint az [itt](../../cosmos-db/analytical-store-introduction.md#analytical-schema)használandó bővített tulajdonságokat.

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- [Hogyan hozhatók létre és használhatók nézetek az SQL igény szerinti használatával](create-use-views.md) 
- [Oktatóanyag az SQL on-demand nézeteinek létrehozásához Azure Cosmos DB és a DirectQuery-on keresztüli Power BI modellekhez való csatlakoztatásához](./tutorial-data-analyst.md)
