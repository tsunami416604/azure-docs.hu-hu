---
title: Az Azure Data Factory támogatott fájlformátumok |} Microsoft Docs
description: Ez a témakör ismerteti a fájlformátumokat és a támogatottak az Azure Data Factory összekötők fájlalapú tömörítés kódokat.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 05/09/2018
ms.author: jingwang
ms.openlocfilehash: fdfee4e06994de1b9a63996203b1a1b9fed9b768
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Támogatott fájlformátumok és az Azure Data Factory tömörítési kodek

*Ez a témakör a következő összekötőkre vonatkozik: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Store](connector-azure-data-lake-store.md), [Azure File Storage](connector-azure-file-storage.md), [ Fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), és [SFTP](connector-sftp.md).*

Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. Ha azt szeretné, hogy **elemezni, vagy egy adott formátumú fájlok generálása**, Azure Data Factory formátuma a következő fájltípusokat támogatja:

* [Szöveges formátum](#text-format)
* [JSON formátumban](#json-format)
* [Az Avro formátum](#avro-format)
* [ORC formátum](#orc-format)
* [Parquet formátum](#parquet-format)

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [formátumú és tömörítést támogatja a Data Factory version1](v1//data-factory-supported-file-and-compression-formats.md).

> [!TIP]
> Ismerje meg, hogyan másolási tevékenység leképezi a gyűjtése a forrásadatok [séma-hozzárendelése a másolási tevékenység](copy-activity-schema-and-type-mapping.md), beleértve a módját a metaadatok határoz meg a fájl formátuma beállítások alapján, és arról, hogy adja meg, mikor tippek a [dataset `structure` ](concepts-datasets-linked-services.md#dataset-structure) szakasz.

## <a name="text-format"></a>Szöveges formátum

Ha szeretne egy szövegfájlt olvasni vagy írni egy szövegfájlba, állítsa be a `type` tulajdonságot a `format` a adatkészlet szakasza **szöveges**. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A TextFormat használatát bemutató példa](#textformat-example) című szakaszt.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| columnDelimiter |A fájlokban az oszlopok elválasztására használt karakter. Érdemes lehet használni az adatok nem létezik ritka nem nyomtatható karaktert. Adja meg például a "\u0001", ami indítsa el a fejléc (SOH) jelöli. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték a **vessző (,)**. <br/><br/>A Unicode karakterek használatához tekintse meg [Unicode-karaktereket](https://en.wikipedia.org/wiki/List_of_Unicode_characters) a megfelelő kód lekérése. |Nem |
| rowDelimiter |A fájlokban a sorok elválasztására használt karakter. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték olvasáskor a következő értékek bármelyike: **[„\r\n”, „\r”, „\n”]**, illetve **„\r\n”** írás esetén. |Nem |
| escapeChar |Az oszlophatároló feloldására szolgáló speciális karakter a bemeneti fájl tartalmában. <br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: „Helló, világ”), megadhatja a „$” karakter feloldójelként, és a „Helló$, világ” karakterláncot használhatja a forrásban. |Nem |
| quoteChar |Egy karakterláncérték idézéséhez használt karakter. Ekkor az idézőjel-karakterek közötti oszlop- és sorhatárolókat a rendszer a karakterláncérték részeként kezeli. Ez a tulajdonság a bemeneti és a kimeneti adatkészleteken is alkalmazható.<br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: <Helló, világ>), megadhatja a " (angol dupla idézőjel) értéket idézőjel-karakterként, és a "Helló$, világ" karakterláncot használhatja a forrásban. |Nem |
| nullValue |A null értéket jelölő egy vagy több karakter. |Egy vagy több karakter. Az **alapértelmezett** értékek az **„\N” és „NULL”** olvasás, illetve **„\N”** írás esetén. |Nem |
| encodingName |A kódolási név megadására szolgál. |Egy érvényes kódolási név. Lásd az [Encoding.EncodingName tulajdonságot](https://msdn.microsoft.com/library/system.text.encoding.aspx). Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| firstRowAsHeader |Megadja, hogy az első sort fejlécnek kell-e tekinteni. A bemeneti adatkészletek első sorát a Data Factory fejlécként olvassa be. A kimeneti adatkészletek első sorát a Data Factory fejlécként írja ki. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |True (Igaz)<br/><b>False (alapértelmezett)</b> |Nem |
| skipLineCount |Az adatok bemeneti fájlokból való olvasásakor kihagyandó sorok számát jelzi. Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Egész szám |Nem |
| treatEmptyAsNull |Meghatározza, hogy az adatok bemeneti fájlból történő olvasásakor a null vagy üres értékeket null értékként kell-e kezelni. |**True (alapértelmezett)**<br/>False (Hamis) |Nem |

### <a name="textformat-example"></a>A TextFormat használatát bemutató példa

A következő JSON-definícióban egy adatkészlet egyes nem kötelező tulajdonságok vannak megadva.

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

## <a name="json-format"></a>JSON formátumban

A **importálási/exportálási egy JSON-fájl,-be/Azure Cosmos DB van**, importálási/exportálási JSON-dokumentumok szakaszában talál [helyezi át az adatokat az Azure Cosmos DB](connector-azure-cosmos-db.md) cikk.

Ha szeretne elemezni a JSON-fájlokat, vagy az adatok írása JSON formátumban, állítsa be a `type` tulajdonságot a `format` szakaszban **JsonFormat**. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |
| jsonNodeReference | Ha egy azonos mintával rendelkező tömbmezőben található objektumokat szeretne iterálni, vagy azokból adatokat kinyerni, adja meg a tömb JSON-útvonalát. Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokat. | Nem |
| jsonPathDefinition | Megadja az egyes oszlopmegfeleltetések JSON-útvonalának kifejezését testre szabott oszlopnevekkel (kezdje kisbetűvel). Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokat, és ki tud nyerni adatokat objektumokból vagy tömbökből. <br/><br/> A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `jsonNodeReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt. | Nem |
| encodingName |A kódolási név megadására szolgál. Az érvényes kódolási nevekkel kapcsolatban lásd az [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) tulajdonságot. Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| nestingSeparator |A beágyazási szinteket elválasztó karakter. Az alapértelmezett érték a „.” (pont). |Nem |

### <a name="json-file-patterns"></a>JSON-fájlminták

Másolási tevékenység során tudja értelmezni a JSON-fájlok a következő minták:

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

Tekintse meg a következő két minta, amikor az adatok másolása JSON-fájlokat. Vegye figyelembe az általános szempontok:

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
                    "ResourceManagmentProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
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

| ID (Azonosító) | deviceType | targetResourceType | resourceManagmentProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információkért lásd: [dataset Forrásoszlopok leképezése cél adatkészlet oszlopok](copy-activity-schema-and-type-mapping.md).
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Adatok másolása tömb, használhatja a `array[x].property` kibontása a megadott tulajdonság értékének a `xth` objektum, vagy használhatja az `array[*].property` az összes ilyen tulajdonságot tartalmazó objektumtól értéket keresi.

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
            "name": "resourceManagmentProcessRunId",
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
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagmentProcessRunId": "$.context.custom.dimensions[1].ResourceManagmentProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
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

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információkért lásd: [dataset Forrásoszlopok leképezése cél adatkészlet oszlopok](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` határozza meg, hogy a következőnek és az adatok kinyerése a ugyanilyen mintájú objektumok **tömb** `orderlines`.
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Ebben a példában `ordernumber`, `orderdate`, és `city` alatt gyökérszintű objektum, amelynek JSON elérési út a `$.`, amíg `order_pd` és `order_price` a tömbelem nélkül származó elérési úttal rendelkező meghatározott `$.` .

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
* Ha a JSON-bemenet egy tömböt tartalmaz, a másolási tevékenység alapértelmezés szerint a tömb teljes értékét egy karakterlánccá alakítja át. Választhatja, hogy a `jsonNodeReference` és/vagy a `jsonPathDefinition` használatával kívánja kinyerni belőle az adatokat, vagy ki is hagyhatja ezt a műveletet, ha nem adja meg a `jsonPathDefinition` értékét.
* Ha ismétlődő nevek szerepelnek ugyanazon a szinten, a másolási tevékenység az utolsót választja ki.
* A tulajdonságnevek megkülönböztetik a kis- és nagybetűket. A rendszer két azonos nevű, de eltérő kis- és nagybetűket tartalmazó tulajdonságot két különálló tulajdonságként kezel.

**2. eset: Adatok írása JSON-fájlba**

Ha az SQL-adatbázis a következő táblázatban:

| ID (Azonosító) | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

és az egyes rekordokhoz, várt írni egy JSON-objektum a következő formátumban:

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

A **JsonFormat** típusú kimeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban `structure` szakaszban definiálja a testreszabott tulajdonságnevek célfájlt, `nestingSeparator` (alapértelmezett érték ".") a neve a nest réteg azonosítására szolgálnak. Ez a szakasz **nem kötelező**, kivéve, ha módosítani szeretné a tulajdonság nevét a forrásoszlop nevéhez képest, vagy egyes tulajdonságokat egymásba szeretne ágyazni.

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

## <a name="avro-format"></a>Az AVRO formátum

Ha elemezni szeretné a Avro-fájlokat, vagy Avro formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **AvroFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "AvroFormat",
}
```

Az Avro formátum Hive-táblákban való használatával kapcsolatban lásd az [Apache Hive oktatóanyagát](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Vegye figyelembe a következő szempontokat:

* [Összetett adattípusú](http://avro.apache.org/docs/current/spec.html#schema_complex) használata nem támogatott (rögzíti, enumerálások, tömbök, térképeket, unióját képezi, és rögzített).

## <a name="orc-format"></a>ORC formátum

Ha elemezni szeretné a ORC-fájlokat, vagy ORC formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **OrcFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> A másolási által felhatalmazott Self-hosted integrációs futásidejű pl. a helyszíni és a felhő között adatokat tárolja, ha nem másol ORC fájlokat **,-van**, telepítenie kell a JRE 8 (Java Runtime Environment) a IR-számítógépre. Egy 64 bites IR 64 bites JRE igényel. Mindkét verziót megtalálja [itt](http://go.microsoft.com/fwlink/?LinkId=808605).
>

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, MAP, LIST, UNION)
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

### <a name="data-type-mapping-for-orc-files"></a>Adattípus-leképezést ORC fájlokat

| Data factory ideiglenes adattípus | ORC típusok |
|:--- |:--- |
| Logikai | Logikai |
| SByte | Bájt |
| Bájt | Rövid |
| Int16 | Rövid |
| Csak UInt16 | Int |
| Int32 | Int |
| UInt32 | Hosszú |
| Int64 | Hosszú |
| UInt64 | Karakterlánc |
| Önálló | Lebegőpontos |
| Dupla | Dupla |
| Decimális | Decimális |
| Karakterlánc | Karakterlánc |
| DateTime | Időbélyeg |
| DateTimeOffset | Időbélyeg |
| A TimeSpan | Időbélyeg |
| ByteArray | Bináris |
| GUID | Karakterlánc |
| Karakter | Char(1) |

## <a name="parquet-format"></a>Parquet formátum

Ha elemezni szeretné a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **ParquetFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "ParquetFormat"
}
```

> [!IMPORTANT]
> A másolási által felhatalmazott Self-hosted integrációs futásidejű pl. a helyszíni és a felhő között adatokat tárolja, ha nem másol Parquet fájlok **,-van**, telepítenie kell a JRE 8 (Java Runtime Environment) a IR-számítógépre. Egy 64 bites IR 64 bites JRE igényel. Mindkét verziót megtalálja [itt](http://go.microsoft.com/fwlink/?LinkId=808605).
>

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (MAP, LIST)
* A Parquet-fájlok a következő tömörítéshez kapcsolódó beállításokat használják: NONE, SNAPPY, GZIP és LZO. Adat-előállító támogatja az adatok olvasása a tömörített formátumban Parquet fájlból. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. A Parquet-fájlokba való írás esetén azonban a Data Factory a SNAPPY tömörítést választja, amely az alapértelmezett a Parquet-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

### <a name="data-type-mapping-for-parquet-files"></a>Adattípus-leképezést Parquet fájlok

| Data factory ideiglenes adattípus | Egyszerű típus parquet | Eredeti típus parquet (deszerializálni) | Eredeti típus parquet (szerializálni) |
|:--- |:--- |:--- |:--- |
| Logikai | Logikai | – | – |
| SByte | Int32 | int8 | int8 |
| Bájt | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| Csak UInt16 | Int32 | Csak UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/bináris | UInt64 | Decimális |
| Önálló | Lebegőpontos | – | – |
| Dupla | Dupla | – | – |
| Decimális | Bináris | Decimális | Decimális |
| Karakterlánc | Bináris | Utf8 | Utf8 |
| DateTime | Int96 | – | – |
| A TimeSpan | Int96 | – | – |
| DateTimeOffset | Int96 | – | – |
| ByteArray | Bináris | – | – |
| GUID | Bináris | Utf8 | Utf8 |
| Karakter | Bináris | Utf8 | Utf8 |
| CharArray | Nem támogatott | – | – |

## <a name="compression-support"></a>Tömörítés támogatása

Az Azure Data Factory adatok tömörítése/kibontani a másolása során támogatja. Ha a `compression` tulajdonság egy bemeneti adatkészlet, a másolási tevékenység során a tömörített adatok olvasása a forrás és kibontása; és a tulajdonság egy kimeneti adathalmazt ad meg, amikor a másolási tevékenység tömöríti, majd adatokat írni a fogadó. Itt van néhány szituáció:

* Tömörített olvasási GZIP adatait az Azure blob kibontani azt, és eredmény adatok írása az Azure SQL-adatbázis. A bemeneti Azure-Blob adatkészlet megadhatja a `compression` `type` GZIP tulajdonság.
* Adatokat olvasni egy egyszerű szöveges fájlt a helyi fájlrendszer, a tömörítés a GZip formátumban, és a tömörített adatok írása az Azure-blobot. Egy kimeneti Azure Blob adatkészlet megadhatja a `compression` `type` GZip tulajdonság.
* Az FTP-kiszolgáló, .zip fájl olvasása azt belül a fájlokat, és azokat a fájlokat az Azure Data Lake Store ártó kibontani. Egy bemeneti FTP adatkészlet megadhatja a `compression` `type` ZipDeflate tulajdonság.
* A GZIP-tömörített adatokat olvasni az Azure blob, azt kibontani, BZIP2 használatával tömörítése és eredmény adatokat írni az Azure blob. Megadhatja, hogy a bemeneti Azure-Blob adatkészlet `compression` `type` GZIP és a kimeneti adatkészlet `compression` `type` BZIP2 értékre.

A DataSet adatkészlet tömörítése megadásához használja a **tömörítés** tulajdonság az adatkészlet JSON az alábbi példában látható módon:   

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

A **tömörítés** szakasz két tulajdonságokkal rendelkezik:

* **Típus:** a tömörítési kodek, amely lehet **GZIP**, **Deflate**, **BZIP2**, vagy **ZipDeflate**.
* **Szint:** a tömörítési arány, amely lehet **Optimal** vagy **leggyorsabb**.

  * **Leggyorsabb:** a tömörítési művelet még akkor is, ha a fájl nem optimális tömörített, minél gyorsabban be kell.
  * **Optimális**: A tömörítési műveletet kell lehet optimális tömörített, akkor is, ha a művelet befejezéséhez hosszabb időt vesz igénybe.

    További információkért lásd: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör.

> [!NOTE]
> Tömörítési beállítások nem támogatottak az adatok a **AvroFormat**, **OrcFormat**, vagy **ParquetFormat**. Az alábbi formátumokban fájlok olvasásakor a Data Factory észlel, és a tömörítési kodek használ a metaadatokban. Ezek a formátumok a fájlokat írásakor adat-előállító úgy dönt, az alapértelmezett tömörítési kodek azt a formátumot. Például ZLIB OrcFormat és a ParquetFormat SNAPPY.

## <a name="next-steps"></a>További lépések

Tekintse meg a fájlalapú adatok tárolóinak Azure Data Factory támogatja a következő cikkeket:

- [Az Azure Blob Storage-összekötő](connector-azure-blob-storage.md)
- [Azure Data Lake Store-összekötő](connector-azure-data-lake-store.md)
- [Amazon S3 összekötő](connector-amazon-simple-storage-service.md)
- [Fájl System-összekötő](connector-file-system.md)
- [FTP-összekötő](connector-ftp.md)
- [SFTP-összekötő](connector-sftp.md)
- [HDFS-összekötő](connector-hdfs.md)
- [HTTP-összekötő](connector-http.md)