---
title: Támogatott fájlformátumok az Azure Data Factoryban |} A Microsoft Docs
description: Ez a témakör ismerteti a fájlformátumokat és a fájl alapú összekötők az Azure Data Factory által támogatott tömörítési kódokat.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: jingwang
ms.openlocfilehash: 844440d22bc0a524e9e61bde457ee9f43fd367b2
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444612"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Támogatott fájlformátumok és az Azure Data Factoryban tömörítési kodek

*Ez a témakör az alábbi csatlakozók vonatkozik: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Az azure File Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), és [SFTP](connector-sftp.md).*

Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. Ha azt szeretné, hogy **elemezni, vagy hozzon létre egy adott formátumú fájlok**, az Azure Data Factory formátuma a következő fájltípusokat támogatja:

* [Szöveges formátum](#text-format)
* [JSON-formátumban](#json-format)
* [Az Avro formátum](#avro-format)
* [ORC formátum](#orc-format)
* [Parquet formátum](#parquet-format)

> [!TIP]
> Ismerje meg, hogyan képezi le a másolási tevékenység a forrásadatokat a fogadó [séma hozzárendelése a másolási tevékenység](copy-activity-schema-and-type-mapping.md), beleértve a hogyan a metaadatokat meghatározott a fájlformátum beállításai alapján, és arról, hogy adja meg, mikor tippek a [adatkészlet `structure` ](concepts-datasets-linked-services.md#dataset-structure) szakaszban.

## <a name="text-format"></a>Szöveges formátum

Ha szeretne egy szövegfájlba olvasni vagy írni egy szövegfájlba, állítsa be a `type` tulajdonságot a `format` az adatkészlet szakaszában **TextFormat**. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A TextFormat használatát bemutató példa](#textformat-example) című szakaszt.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| columnDelimiter |A fájlokban az oszlopok elválasztására használt karakter. Érdemes lehet nem létezik az adatokat a ritka nem nyomtatható karaktert használhatja. Adja meg például a "\u0001", indítsa el a fejléc (SOH) jelölő. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték a **vessző (,)**. <br/><br/>Egy Unicode karakterek használatához tekintse meg [Unicode-karaktereket](https://en.wikipedia.org/wiki/List_of_Unicode_characters) lekérése, a megfelelő kódot. |Nem |
| rowDelimiter |A fájlokban a sorok elválasztására használt karakter. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték olvasáskor a következő értékek bármelyike: **[„\r\n”, „\r”, „\n”]**, illetve **„\r\n”** írás esetén. |Nem |
| escapeChar |Az oszlophatároló feloldására szolgáló speciális karakter a bemeneti fájl tartalmában. <br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: „Helló, világ”), megadhatja a „$” karakter feloldójelként, és a „Helló$, világ” sztringet használhatja a forrásban. |Nem |
| quoteChar |Egy sztringérték idézéséhez használt karakter. Ekkor az idézőjel-karakterek közötti oszlop- és sorhatárolókat a rendszer a sztringérték részeként kezeli. Ez a tulajdonság a bemeneti és a kimeneti adatkészleteken is alkalmazható.<br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: &lt;Helló, világ&gt;), megadhatja a " (angol dupla idézőjel) értéket idézőjel-karakterként, és a "Helló$, világ" sztringet használhatja a forrásban. |Nem |
| nullValue |A null értéket jelölő egy vagy több karakter. |Egy vagy több karakter. Az **alapértelmezett** értékek az **„\N” és „NULL”** olvasás, illetve **„\N”** írás esetén. |Nem |
| encodingName |A kódolási név megadására szolgál. |Egy érvényes kódolási név. Lásd az [Encoding.EncodingName tulajdonságot](https://msdn.microsoft.com/library/system.text.encoding.aspx). Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| firstRowAsHeader |Megadja, hogy az első sort fejlécnek kell-e tekinteni. A bemeneti adatkészletek első sorát a Data Factory fejlécként olvassa be. A kimeneti adatkészletek első sorát a Data Factory fejlécként írja ki. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |True (Igaz)<br/><b>False (alapértelmezett)</b> |Nem |
| skipLineCount |Az adatok bemeneti fájlokból való olvasásakor kihagyandó sorok számát jelzi. Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Egész szám |Nem |
| treatEmptyAsNull |Meghatározza, hogy az adatok bemeneti fájlból történő olvasásakor a null vagy üres értékeket null értékként kell-e kezelni. |**True (alapértelmezett)**<br/>False (Hamis) |Nem |

### <a name="textformat-example"></a>A TextFormat használatát bemutató példa

A következő JSON-definíciót egy adatkészletet, az egyes nem kötelező tulajdonságok vannak megadva.

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

## <a name="json-format"></a>JSON-formátumban

A **importálási/exportálási egy JSON-fájlt,-be és- tárolókról az Azure Cosmos DB van**, importálási/exportálási JSON-dokumentumok részében talál [Adatáthelyezést és- tárolókról az Azure Cosmos DB](connector-azure-cosmos-db.md) cikk.

Ha meg szeretné elemezni a JSON-fájlok vagy JSON formátumban szeretne adatokat írni, állítsa be a `type` tulajdonságot a `format` szakasz **JsonFormat**. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |
| jsonNodeReference | Ha egy azonos mintával rendelkező tömbmezőben található objektumokat szeretne iterálni, vagy azokból adatokat kinyerni, adja meg a tömb JSON-útvonalát. Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokat. | Nem |
| jsonPathDefinition | Megadja az egyes oszlopmegfeleltetések JSON-útvonalának kifejezését testre szabott oszlopnevekkel (kezdje kisbetűvel). Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokat, és ki tud nyerni adatokat objektumokból vagy tömbökből. <br/><br/> A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `jsonNodeReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt. | Nem |
| encodingName |A kódolási név megadására szolgál. Az érvényes kódolási nevekkel kapcsolatban lásd az [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) tulajdonságot. Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| nestingSeparator |A beágyazási szinteket elválasztó karakter. Az alapértelmezett érték a „.” (pont). |Nem |

### <a name="json-file-patterns"></a>JSON-fájlminták

Másolási tevékenység a JSON-fájlok következő mintáit tudja elemezni:

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

Tekintse meg a következő két mintát, ha JSON-fájlokból másol adatokat. Vegye figyelembe veendő általános pontokat:

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

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információkért lásd: [forrás adatkészletoszlopok leképezése az adatkészlet céloszlopok](copy-activity-schema-and-type-mapping.md).
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Adatok másolása egy olyan tömbből, használhatja `array[x].property` kibontani a megadott tulajdonság értékét a `xth` objektumot, vagy használhatja az `array[*].property` , keresse meg az értéket az összes ilyen tulajdonságot tartalmazó.

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

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információkért lásd: [forrás adatkészletoszlopok leképezése az adatkészlet céloszlopok](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` azt jelzi, hogy ismételt futtatásával és az adatok kinyerése a alatt azonos mintával rendelkező objektumok **tömb** `orderlines`.
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Ebben a példában `ordernumber`, `orderdate`, és `city` JSON elérési útja kezdve a gyökérobjektum alatt találhatók `$.`, miközben `order_pd` és `order_price` nélkül a tömbelem osztályból származtatott útvonallal vannak meghatározva `$.` .

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

Ha az alábbi táblázat az SQL Database:

| ID (Azonosító) | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

és az egyes rekordokhoz, a következő formátumban JSON-objektum írását várja:

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

A **JsonFormat** típusú kimeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban `structure` szakasz definiálja a testre szabott tulajdonságneveket a célfájlban, `nestingSeparator` (alapértelmezett érték ".") a beágyazott réteg neve azonosítására szolgál. Ez a szakasz **nem kötelező**, kivéve, ha módosítani szeretné a tulajdonság nevét a forrásoszlop nevéhez képest, vagy egyes tulajdonságokat egymásba szeretne ágyazni.

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

* [Összetett adattípusok](http://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, enumerálások, tömbök, leképezések, egyesítések, és rögzített típusok).

## <a name="orc-format"></a>ORC formátum

Ha elemezni szeretné a ORC-fájlokat, vagy ORC formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **OrcFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> A példány által felhatalmazott helyi integrációs modul például a helyszíni és a felhő között adatokat tárolja, ha nem másol ORC-fájlokat **,-van**, az integrációs modul gépen a JRE (Java-futtatókörnyezet) 8 telepítenie kell. Egy 64 bites integrációs modul 64 bites JRE szükséges. Mindkét verziót megtalálja [itt](http://go.microsoft.com/fwlink/?LinkId=808605).
>

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, MAP, LIST, UNION)
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

### <a name="data-type-mapping-for-orc-files"></a>Adattípus-leképezés ORC-fájlokat

| Data factory közbenső adattípus | ORC-típusok |
|:--- |:--- |
| Logikai | Logikai |
| SByte | Bájt |
| Bájt | Rövid |
| Int16 | Rövid |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Hosszú |
| Int64 | Hosszú |
| UInt64 | Sztring |
| Önálló | Lebegőpontos |
| Dupla | Dupla |
| tizedes tört | tizedes tört |
| Sztring | Sztring |
| DateTime | Időbélyeg |
| DateTimeOffset | Időbélyeg |
| Időtartam | Időbélyeg |
| ByteArray | Bináris |
| GUID | Sztring |
| CHAR | Char(1) |

## <a name="parquet-format"></a>Parquet formátum

Ha elemezni szeretné a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **ParquetFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "ParquetFormat"
}
```

> [!IMPORTANT]
> A példány által felhatalmazott helyi integrációs modul például a helyszíni és a felhő között adatokat tárolja, ha nem másol Parquet-fájlokat **,-van**, az integrációs modul gépen a JRE (Java-futtatókörnyezet) 8 telepítenie kell. Egy 64 bites integrációs modul 64 bites JRE szükséges. Mindkét verziót megtalálja [itt](http://go.microsoft.com/fwlink/?LinkId=808605).
>

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (MAP, LIST)
* A Parquet-fájlok a következő tömörítéshez kapcsolódó beállításokat használják: NONE, SNAPPY, GZIP és LZO. A Data Factory támogatja ezeket tömörített formátum bármelyikében a Parquet-fájlokba történő olvasáskor. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. A Parquet-fájlokba való írás esetén azonban a Data Factory a SNAPPY tömörítést választja, amely az alapértelmezett a Parquet-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

### <a name="data-type-mapping-for-parquet-files"></a>Adattípus-leképezés Parquet-fájlokat

| Data factory közbenső adattípus | Parquet primitív típus | Parquet eredeti típusa (deszerializálni) | Parquet eredeti típusa (szerializálni) |
|:--- |:--- |:--- |:--- |
| Logikai | Logikai | – | – |
| SByte | Int32 | Int8 | Int8 |
| Bájt | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/bináris fájl | UInt64 | tizedes tört |
| Önálló | Lebegőpontos | – | – |
| Dupla | Dupla | – | – |
| tizedes tört | Bináris | tizedes tört | tizedes tört |
| Sztring | Bináris | Utf8 | Utf8 |
| DateTime | Int96 | – | – |
| Időtartam | Int96 | – | – |
| DateTimeOffset | Int96 | – | – |
| ByteArray | Bináris | – | – |
| GUID | Bináris | Utf8 | Utf8 |
| CHAR | Bináris | Utf8 | Utf8 |
| CharArray | Nem támogatott | – | – |

## <a name="compression-support"></a>A tömörítés támogatása

Az Azure Data Factory támogatja az adatok tömörítése és kibontása másolása során. Ha `compression` tulajdonság a bemeneti adatkészlethez, a másolási tevékenység a tömörített adatok olvasása a forrásból, és kibontása; és a tulajdonság egy kimeneti adatkészletet ad meg, amikor a másolási tevékenység tömörítése, majd az adatokat írni a fogadó. Az alábbiakban néhány mintaként használható jelen forgatókönyvek:

* Olvasási gzip Formátumban tömörített adatok egy Azure-blobból kibontása azt, és eredmény adatokat írni az Azure SQL-adatbázis. Az Azure Blob bemeneti adatkészlet meghatározhatja a `compression` `type` GZIP tulajdonság.
* Egy egyszerű szöveges fájlt a helyi fájlrendszerből adatokat olvasni, és tömörítése a GZip formátumban tömörített adatokat írni az Azure-blobba. Meghatározhatja az Azure Blob kimeneti adatkészlet a `compression` `type` GZip tulajdonság.
* FTP-kiszolgálóról a .zip fájl olvasása kibontása található fájlokat, és ezeket a fájlokat az Azure Data Lake Store megnyitja azt. Meghatározhatja az FTP bemeneti adatkészlet a `compression` `type` ZipDeflate tulajdonság.
* Olvassa el a GZIP-tömörített adatok egy Azure-blobból, azt kibontani, tömörítése BZIP2 használatával és eredmény adatokat írni az Azure-blobba. Meghatározhatja az Azure Blob bemeneti adatkészlet `compression` `type` állítsa a GZIP és a kimeneti adatkészlet `compression` `type` BZIP2 értékre.

Egy adatkészlet-tömörítése megadásához használja a **tömörítési** tulajdonságot az adatkészlet JSON az alábbi példában látható módon:   

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

A **tömörítési** szakaszban két tulajdonságokkal rendelkezik:

* **Típus:** tömörítési kodeket, amely lehet **GZIP**, **Deflate**, **BZIP2**, vagy **ZipDeflate**.
* **Szint:** a tömörítési arány, amely lehet **Optimal** vagy **leggyorsabb**.

  * **Leggyorsabb:** a tömörítés művelet még akkor is, ha az eredményül kapott fájl nem optimális tömöríti a lehető leggyorsabban be kell.
  * **Optimális**: A tömörítés művelet optimális, hogy tömöríteni kell, akkor is, ha a művelet végrehajtásához hosszabb ideig tart.

    További információkért lásd: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör.

> [!NOTE]
> Tömörítési beállítások nem támogatottak az adatok a **AvroFormat**, **OrcFormat**, vagy **ParquetFormat**. Ezek a formátumok a fájlok olvasásakor a Data Factory észleli, és a metaadatokban szereplő tömörítési kodeket használja. Ezek a formátumok fájlok írásakor a Data Factory úgy dönt, az alapértelmezett tömörítési kodeket azt a formátumot. Ha például ZLIB OrcFormat és ParquetFormat a SNAPPY.

## <a name="next-steps"></a>További lépések

A fájlalapú adatok tárolók az Azure Data Factory által támogatott a következő cikkekben talál:

- [Az Azure Blob Storage-összekötő](connector-azure-blob-storage.md)
- [Az Azure Data Lake Store-összekötő](connector-azure-data-lake-store.md)
- [Az Amazon S3-összekötő](connector-amazon-simple-storage-service.md)
- [Fájlrendszer-összekötő](connector-file-system.md)
- [FTP-összekötő](connector-ftp.md)
- [SFTP-összekötő](connector-sftp.md)
- [HDFS-összekötő](connector-hdfs.md)
- [HTTP-összekötő](connector-http.md)
