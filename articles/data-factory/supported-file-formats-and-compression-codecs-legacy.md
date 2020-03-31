---
title: Támogatott fájlformátumok az Azure Data Factoryban (örökölt)
description: Ez a témakör ismerteti a fájlalapú összekötők által támogatott fájlalapú összekötők által támogatott fájlformátumok és tömörítési kódok at.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 423706c391e8d8c2c609798d9f50e5a22f5c39bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260682"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Támogatott fájlformátumok és tömörítési kodekek az Azure Data Factoryban (örökölt)

*Ez a cikk a következő összekötőkre vonatkozik: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>A Data Factory új formátumalapú adatkészletmodellt vezetett be, lásd a megfelelő formátumú cikket a részletekkel: <br>- [Avro formátum](format-avro.md)<br>- [Bináris formátum](format-binary.md)<br>- [Tagolt szövegformátum](format-delimited-text.md)<br>- [JSON formátum](format-json.md)<br>- [ORC formátum](format-orc.md)<br>- [Parketta formátum](format-parquet.md)<br>A jelen cikkben említett többi konfigurációk továbbra is támogatottak a visszamenőleges kompatibilitáshoz. Javasoljuk, hogy az új modellt használja a jövőben. 

## <a name="text-format-legacy"></a><a name="text-format"></a>Szövegformátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [Tagolt szövegformátumról.](format-delimited-text.md) A fájlalapú adattár-adatkészlet következő konfigurációi továbbra is támogatottak a visszamenőleges kompatibilitás esetén. Javasoljuk, hogy az új modellt használja a jövőben.

Ha szövegfájlból szeretne olvasni vagy szövegfájlba írni, `type` állítsa `format` az adatkészlet szakaszának tulajdonságát TextFormat ( **szövegformátum**) beállításra. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A TextFormat használatát bemutató példa](#textformat-example) című szakaszt.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| columnDelimiter |A fájlokban az oszlopok elválasztására használt karakter. Érdemes lehet olyan ritka, nem nyomtatható karaktert használni, amely esetleg nem létezik az adatokban. Adja meg például a "\u0001" értéket, amely a Címsor (SOH) kezdetét jelöli. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték a **vessző (,)**. <br/><br/>Unicode karakter használatához olvassa el a [Unicode karakterek című kódot.](https://en.wikipedia.org/wiki/List_of_Unicode_characters) |Nem |
| rowDelimiter |A fájlokban a sorok elválasztására használt karakter. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték olvasáskor a következő értékek bármelyike: **[„\r\n”, „\r”, „\n”]**, illetve **„\r\n”** írás esetén. |Nem |
| escapeChar |Az oszlophatároló feloldására szolgáló speciális karakter a bemeneti fájl tartalmában. <br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: „Helló, világ”), megadhatja a „$” karakter feloldójelként, és a „Helló$, világ” sztringet használhatja a forrásban. |Nem |
| quoteChar |Egy sztringérték idézéséhez használt karakter. Ekkor az idézőjel-karakterek közötti oszlop- és sorhatárolókat a rendszer a sztringérték részeként kezeli. Ez a tulajdonság a bemeneti és a kimeneti adatkészleteken is alkalmazható.<br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: &lt;Helló, világ&gt;), megadhatja a " (angol dupla idézőjel) értéket idézőjel-karakterként, és a "Helló$, világ" sztringet használhatja a forrásban. |Nem |
| nullValue |A null értéket jelölő egy vagy több karakter. |Egy vagy több karakter. Az **alapértelmezett** értékek az **„\N” és „NULL”** olvasás, illetve **„\N”** írás esetén. |Nem |
| encodingName |A kódolási név megadására szolgál. |Egy érvényes kódolási név. Lásd az [Encoding.EncodingName tulajdonságot](https://msdn.microsoft.com/library/system.text.encoding.aspx). Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| firstRowAsHeader |Megadja, hogy az első sort fejlécnek kell-e tekinteni. A bemeneti adatkészletek első sorát a Data Factory fejlécként olvassa be. A kimeneti adatkészletek első sorát a Data Factory fejlécként írja ki. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |True (Igaz)<br/><b>False (alapértelmezett)</b> |Nem |
| skipLineCount |A bemeneti fájlokból történő adatolvasáskor kihagyandó **nem üres** sorok számát jelzi. Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Egész szám |Nem |
| treatEmptyAsNull |Meghatározza, hogy az adatok bemeneti fájlból történő olvasásakor a sztring null vagy üres értékeit null értékként kell-e kezelni. |**True (alapértelmezett)**<br/>False (Hamis) |Nem |

### <a name="textformat-example"></a>A TextFormat használatát bemutató példa

A következő JSON-definícióban egy adatkészlethez néhány választható tulajdonság van megadva.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

`quoteChar` helyett `quoteChar` használatához cserélje le a sort `escapeChar` értékre a következő escapeChar kifejezéssel:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>A firstRowAsHeader és a skipLineCount használatára vonatkozó forgatókönyvek

* Egy nem fájlalapú forrásból másol egy szöveges fájlba, és szeretne hozzáadni egy fejlécsort, amely tartalmazza a séma (például SQL-séma) metaadatait. Ebben a forgatókönyvben adja meg a `firstRowAsHeader` értékét igazként a kimeneti adatkészletben.
* Egy fejlécsort tartalmazó szöveges fájlból másol egy nem fájlalapú fogadóba, és el szeretné hagyni azt a sort. Adja meg a `firstRowAsHeader` értékét igazként a bemeneti adatkészletben.
* Egy szöveges fájlból másol, és szeretne kihagyni néhány sort az elejéről, amelyek nem tartalmaznak adatokat vagy fejléc-információkat. Adja meg a `skipLineCount` értékét a kihagyni kívánt sorok számának jelzéséhez. Ha a fájl hátralévő része fejlécsort tartalmaz, a `firstRowAsHeader` is megadható. Ha a `skipLineCount` és a `firstRowAsHeader` is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból

## <a name="json-format-legacy"></a><a name="json-format"></a>JSON formátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [JSON formátumú](format-json.md) cikkből. A fájlalapú adattár-adatkészlet következő konfigurációi továbbra is támogatottak a visszamenőleges kompatibilitás esetén. Javasoljuk, hogy az új modellt használja a jövőben.

**JSON-fájl importálása/exportálása az Azure Cosmos DB-be/az Azure Cosmos DB-ből**című témakörben olvashat az [Adatok áthelyezése az Azure Cosmos DB-be/az Azure Cosmos DB-ből](connector-azure-cosmos-db.md) című cikkben.

Ha elemezni szeretné a JSON-fájlokat, vagy JSON formátumban `type` szeretné `format` írni az adatokat, állítsa a szakasz tulajdonságát **JsonFormat**értékre. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |
| jsonNodeReference | Ha egy azonos mintával rendelkező tömbmezőben található objektumokat szeretne iterálni, vagy azokból adatokat kinyerni, adja meg a tömb JSON-útvonalát. Ez a tulajdonság csak JSON-fájlokból **történő** másolásesetén támogatott. | Nem |
| jsonPathDefinition | Megadja az egyes oszlopmegfeleltetések JSON-útvonalának kifejezését testre szabott oszlopnevekkel (kezdje kisbetűvel). Ez a tulajdonság csak akkor **from** támogatott, ha JSON-fájlokból másol adatokat, és adatokat nyerhet ki objektumból vagy tömbből. <br/><br/> A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `jsonNodeReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt. | Nem |
| encodingName |A kódolási név megadására szolgál. Az érvényes kódolási nevekkel kapcsolatban lásd az [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) tulajdonságot. Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték: **UTF-8**. |Nem |
| nestingSeparator |A beágyazási szinteket elválasztó karakter. Az alapértelmezett érték a „.” (pont). |Nem |

>[!NOTE]
>A tömbben lévő adatok több sorra történő keresztalkalmazása esetén (1. eset -> 2. minta `jsonNodeReference`a [JsonFormat példákban](#jsonformat-example)) csak a tulajdonság használatával bonthatja ki az egyes tömböket .

### <a name="json-file-patterns"></a>JSON-fájlminták

Másolási tevékenység elemezheti a következő mintákat A JSON fájlok:

- **I. típus: setOfObjects**

    Minden fájl egyetlen objektumot, illetve több, sorokkal határolt/összefűzött objektumot tartalmaz. Ha ezt a lehetőséget választja egy kimeneti adatkészletben, a másolási tevékenység egyetlen JSON-fájlt állít elő, soronként egy objektummal (sorokkal határolt).

    * **példa egy objektumot tartalmazó JSON-fájlra**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **példa sorokkal határolt JSON-fájlra**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **példa összefűzött JSON-fájlra**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **II. típus: arrayOfObjects**

    Minden fájl objektumok egy tömbjét tartalmazza.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>A JsonFormat használatát bemutató példa

**1. eset: Adatok másolása JSON-fájlokból**

**1. példa: adatok kigyűjtése objektumból és tömbből**

Ebben a példában egy JSON-gyökérobjektum képződik le egyetlen rekordba táblázatos nézetben. Ha a JSON-fájl a következőt tartalmazza:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

és az adatok objektumokból és tömbökből való kigyűjtésével szeretné átmásolni egy Azure SQL-táblába az alábbi formátumban:

| ID (Azonosító) | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információt a [Forrásadatkészlet-oszlopok leképezése a céladatkészlet-oszlopokhoz](copy-activity-schema-and-type-mapping.md)című témakörben talál.
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Adatok másolásához a tömbből, használhatja `array[x].property` az adott `xth` tulajdonság értékének kinyerésére az objektumból, vagy megkeresheti `array[*].property` az ilyen tulajdonságot tartalmazó objektumok értékét.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**2. példa: a tömbből származó ugyanazon minta keresztalkalmazása több objektumra**

Ebben a példában egy JSON-gyökérobjektumot alakít át több rekorddá táblázatos nézetben. Ha a JSON-fájl a következőt tartalmazza:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

és szeretné átmásolni egy Azure SQL-táblába az alábbi formátumban, a tömbben lévő adatok egybesimításával, valamint keresztillesztést létrehozni a közös gyökérinformációval:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információt a [Forrásadatkészlet-oszlopok leképezése a céladatkészlet-oszlopokhoz](copy-activity-schema-and-type-mapping.md)című témakörben talál.
- `jsonNodeReference`jelzi, hogy a **tömb** `orderlines`alatt azonos mintával rendelkező objektumokból iterálni és kinyerni az adatokat.
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Ebben a `ordernumber`példában a `orderdate`, , és `city` alatt gyökér `$.`objektum `order_pd` `order_price` JSON elérési kezdő , míg, és a definiált elérési út származik a tömb elem nélkül. `$.`

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Vegye figyelembe a következő szempontokat:**

* Ha a `structure` és a `jsonPathDefinition` nincs meghatározva a Data Factory-adatkészletben, a másolási tevékenység az első objektumból észleli a sémát, és a teljes objektumot egybesimítja.
* Ha a JSON-bemenet egy tömböt tartalmaz, a másolási tevékenység alapértelmezés szerint a tömb teljes értékét egy sztringgé alakítja át. Választhatja, hogy a `jsonNodeReference` és/vagy a `jsonPathDefinition` használatával kívánja kinyerni belőle az adatokat, vagy ki is hagyhatja ezt a műveletet, ha nem adja meg a `jsonPathDefinition` értékét.
* Ha ismétlődő nevek szerepelnek ugyanazon a szinten, a másolási tevékenység az utolsót választja ki.
* A tulajdonságnevek megkülönböztetik a kis- és nagybetűket. A rendszer két azonos nevű, de eltérő kis- és nagybetűket tartalmazó tulajdonságot két különálló tulajdonságként kezel.

**2. eset: Adatok írása JSON-fájlba**

Ha az SQL Database következő táblával rendelkezik:

| ID (Azonosító) | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

és minden rekordhoz a következő formátumban kell json-objektumba írnia:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

A **JsonFormat** típusú kimeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban a `structure` szakasz határozza meg a célfájlban `nestingSeparator` a testreszabott tulajdonságneveket (az alapértelmezett érték ".") a fészekréteg azonosítására szolgál a névből. Ez a szakasz **nem kötelező**, kivéve, ha módosítani szeretné a tulajdonság nevét a forrásoszlop nevéhez képest, vagy egyes tulajdonságokat egymásba szeretne ágyazni.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a>Parketta formátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [Parquet formátumú](format-parquet.md) cikkből. A fájlalapú adattár-adatkészlet következő konfigurációi továbbra is támogatottak a visszamenőleges kompatibilitás esetén. Javasoljuk, hogy az új modellt használja a jövőben.

Ha elemezni szeretné a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **ParquetFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (MAP, LIST).
* Az oszlopnévben lévő szóköz nem támogatott.
* A Parquet-fájlok a következő tömörítéshez kapcsolódó beállításokat használják: NONE, SNAPPY, GZIP és LZO. A Data Factory támogatja a Parquet fájlból származó adatok olvasását ezen tömörített formátumok bármelyikében, kivéve az LZO-t - a metaadatokban lévő tömörítő kodeket használja az adatok olvasásához. A Parquet-fájlokba való írás esetén azonban a Data Factory a SNAPPY tömörítést választja, amely az alapértelmezett a Parquet-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

> [!IMPORTANT]
> A saját üzemeltetésű integrációs futásidejű ek által engedélyezett másoláshoz pl. a helyszíni és a felhőbeli adattárak között, ha nem a Parquet-fájlokat **másolja a jelenlegi formában,** telepítenie kell a **64 bites JRE 8-at (Java Runtime Environment) vagy az OpenJDK-t** az infravörös számítógépre. További részleteket a következő bekezdésben talál.

A parquet fájl szerializálással/deszerializálással rendelkező saját üzemeltetésű infravörös kapcsolaton *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* futó másolás esetén az ADF úgy *`JAVA_HOME`* keresi meg a Java futtatóidőt, hogy először ellenőrzi a JRE rendszerleíró adatbázisát, ha nem található, másrészt ellenőrzi az OpenJDK rendszerváltozóját.

- **A JRE használata**: A 64 bites ir használatához 64 bites JRE szükséges. Megtalálható, hogy [innen](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Az OpenJDK használata:** az infravörös 3.13-as verzió óta támogatott. Csomagolja be a jvm.dll-t az OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű infravörös gépbe, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.

>[!TIP]
>Ha adatokat másol/onnan a Saját üzemeltetésű integrációs futásidejű használatával, és a "Hiba történt java, message: **java.lang.OutOfMemoryError:Java halomterület"** meghívásakor, hozzáadhat egy környezeti változót `_JAVA_OPTIONS` a saját üzemeltetésű integrációs integrációs szolgáltatást tartalmazó számítógépen, amely a JVM min/max heap méretét állítja be az ilyen másolás felhatalmazásához, majd futtassa újra a folyamatot.

![JVM halommemória méretének beállítása saját üzemeltetésű infravörös rendszeren](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa `_JAVA_OPTIONS` be `-Xms256m -Xmx16g`a változót értékkel. A `Xms` jelző a Java virtuális gép (JVM) kezdeti `Xmx` memóriafoglalási készletét adja meg, míg a maximális memóriafoglalási készletet. Ez azt jelenti, hogy a `Xms` `Xmx` JVM memóriával indul el, és legfeljebb ennyi memóriát tud használni. Alapértelmezés szerint az ADF min 64MB és max 1G értéket használ.

### <a name="data-type-mapping-for-parquet-files"></a>A parkettafájlok adattípus-hozzárendelése

| Adatgyár köztes adattípusa | Parketta primitív típusa | Parketta eredeti típusa (deszerializálás) | Eredeti típus parketta (szerializálás) |
|:--- |:--- |:--- |:--- |
| Logikai | Logikai | N/A | N/A |
| SByte között | Int32 | Int8 | Int8 |
| Bájt | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/Bináris | UInt64 | Decimal |
| Egyirányú | Float | N/A | N/A |
| Double | Double | N/A | N/A |
| Decimal | Bináris | Decimal | Decimal |
| Sztring | Bináris | Utf8 között | Utf8 között |
| DateTime | Int96 | N/A | N/A |
| időtartam | Int96 | N/A | N/A |
| DateTimeOffset (Dátumidő-eltolás) | Int96 | N/A | N/A |
| ByteArray | Bináris | N/A | N/A |
| Guid | Bináris | Utf8 között | Utf8 között |
| Char | Bináris | Utf8 között | Utf8 között |
| Karaktertömb | Nem támogatott | N/A | N/A |

## <a name="orc-format-legacy"></a><a name="orc-format"></a>ORC formátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt az [ORC formátumú](format-orc.md) cikkből. A fájlalapú adattár-adatkészlet következő konfigurációi továbbra is támogatottak a visszamenőleges kompatibilitás esetén. Javasoljuk, hogy az új modellt használja a jövőben.

Ha elemezni szeretné a ORC-fájlokat, vagy ORC formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **OrcFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "OrcFormat"
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, MAP, LIST, UNION).
* Az oszlopnévben lévő szóköz nem támogatott.
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

> [!IMPORTANT]
> A saját üzemeltetésű integrációs futásidejű ek által engedélyezett másoláshoz pl. a helyszíni és a felhőbeli adattárak között, ha nem az ORC-fájlokat **másolja a jelenlegi formában,** telepítenie kell a **64 bites JRE 8 -ot (Java Runtime Environment) vagy az OpenJDK-t** az infravörös gépre. További részleteket a következő bekezdésben talál.

Az ORC fájlszerializálással/deszerializálással rendelkező saját üzemeltetésű infravörös kapcsolaton futó *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* másolás esetén az ADF úgy keresi *`JAVA_HOME`* meg a Java futtatóidőt, hogy először ellenőrzi a JRE rendszerleíró adatbázisát, ha nem található, másodsorban az OpenJDK rendszerváltozójának ellenőrzését.

- **A JRE használata**: A 64 bites ir használatához 64 bites JRE szükséges. Megtalálható, hogy [innen](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Az OpenJDK használata:** az infravörös 3.13-as verzió óta támogatott. Csomagolja be a jvm.dll-t az OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű infravörös gépbe, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.

### <a name="data-type-mapping-for-orc-files"></a>OrC-fájlok adattípus-hozzárendelése

| Adatgyár köztes adattípusa | ORC-típusok |
|:--- |:--- |
| Logikai | Logikai |
| SByte között | Bájt |
| Bájt | Rövid |
| Int16 | Rövid |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Hosszú |
| Int64 | Hosszú |
| UInt64 | Sztring |
| Egyirányú | Float |
| Double | Double |
| Decimal | Decimal |
| Sztring | Sztring |
| DateTime | Időbélyeg |
| DateTimeOffset (Dátumidő-eltolás) | Időbélyeg |
| időtartam | Időbélyeg |
| ByteArray | Bináris |
| Guid | Sztring |
| Char | Karakter(1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a>AVRO formátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt az [Avro formátumú](format-avro.md) cikkből. A fájlalapú adattár-adatkészlet következő konfigurációi továbbra is támogatottak a visszamenőleges kompatibilitás esetén. Javasoljuk, hogy az új modellt használja a jövőben.

Ha elemezni szeretné a Avro-fájlokat, vagy Avro formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **AvroFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "AvroFormat",
}
```

Az Avro formátum Hive-táblákban való használatával kapcsolatban lásd az [Apache Hive oktatóanyagát](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Vegye figyelembe a következő szempontokat:

* [Az összetett adattípusok](https://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, felsorakatok, tömbök, térképek, összeunionss és fix).

## <a name="compression-support-legacy"></a><a name="compression-support"></a>Tömörítéstámogatása (örökölt)

Az Azure Data Factory támogatja az adatok tömörítését/kibontását a másolás során. Ha tulajdonságot ad meg `compression` egy bemeneti adatkészletben, a másolási tevékenység beolvassa a forrásból származó tömörített adatokat, és kibontja azt; és amikor megadja a tulajdonságot egy kimeneti adatkészletben, a másolási tevékenység tömöríti, majd adatokat ír a fogadóba. Íme néhány példa forgatókönyv:

* GZIP tömörített adatokat olvashat egy Azure blobból, kibonthatja, és eredményadatokat írhat egy Azure SQL-adatbázisba. A bemeneti Azure Blob-adatkészletet gzip-ként definiálja a `compression` `type` tulajdonsággal.
* Adatok olvasása egy egyszerű szöveges fájlból a helyszíni fájlrendszerből, tömörítheti azt GZip formátumban, és írja a tömörített adatokat egy Azure blobba. Meg adhat egy kimeneti Azure `compression` `type` Blob-adatkészletet a tulajdonsággal GZip néven.
* Olvassa el a .zip fájlt az FTP-kiszolgálóról, bontsa ki a fájlokat, és tegye le azokat az Azure Data Lake Store-ban. A tulajdonsággal rendelkező `compression` `type` bemeneti FTP-adatkészletet ZipDeflate néven definiálhat.
* GZIP-tömörített adatok at egy Azure blob, kibontani, tömöríteni, tömöríteni a BZIP2 használatával, és írja az eredményadatokat egy Azure blobba. A bemeneti Azure Blob-adatkészletet `compression` `type` a GZIP-re `compression` `type` és a BZIP2-re beállított kimeneti adatkészletet definiálja.

Ha egy adatkészlethez tömörítést szeretne megadni, használja a JSON adatkészlet **tömörítési** tulajdonságát az alábbi példában:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

A **tömörítési** szakasznak két tulajdonsága van:

* **Típus:** a tömörítő kodek, amely lehet **GZIP,** **Deflate,** **BZIP2**vagy **ZipDeflate**. Megjegyzés: Ha másolási tevékenységet használ a ZipDeflate fájl(ok) kibontásához és a fájlalapú `<path specified in dataset>/<folder named as source zip file>/`fogadó adattárba való íráshoz, a fájlok kibontása a következő mappába kerül: .
* **Szint:** a tömörítési arány, amely lehet **optimális** vagy **leggyorsabb**.

  * **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban el kell végeznie, még akkor is, ha az eredményül kapott fájl nincs optimálisan tömörítve.
  * **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet hosszabb időt vesz igénybe.

    További információt a [Tömörítési szint témakörben](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) talál.

> [!NOTE]
> Az **AvroFormat**, **OrcFormat**vagy **ParquetFormat**formátumban lévő adatok nem támogatottak a tömörítési beállítások. Ha ilyen formátumú fájlokat olvas, a Data Factory észleli és használja a metaadatokban lévő tömörítési kodeket. Amikor ilyen formátumú fájlokba ír, a Data Factory kiválasztja az adott formátumhoz tartozó alapértelmezett tömörítési kodeket. Például ZLIB az OrcFormat és a SNAPPY a ParquetFormat esetében.

## <a name="unsupported-file-types-and-compression-formats"></a>Nem támogatott fájltípusok és tömörítési formátumok

Az Azure Data Factory bővíthetőségi funkcióival átalakíthatja a nem támogatott fájlokat.
Két lehetőség közé tartozik az Azure Functions és az egyéni feladatok az Azure Batch használatával.

A [kátrányfájl tartalmának kibontásához egy](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)Azure-függvényt használó minta látható. További információ: [Azure Functions activity](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Ezt a funkciót egyéni dotnet-tevékenységgel is létrehozhatja. További információ [itt](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) található

## <a name="next-steps"></a>További lépések

Ismerje meg a legújabb támogatott fájlformátumokat és tömörítéseket [a támogatott fájlformátumokból és tömörítésekből.](supported-file-formats-and-compression-codecs.md)
