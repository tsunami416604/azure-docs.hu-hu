---
title: Támogatott fájlformátumok az Azure Data Factoryban |} A Microsoft Docs
description: Ez a témakör ismerteti a fájlformátumokat és a fájl alapú összekötők az Azure Data Factory által támogatott tömörítési kódokat.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: f2ffd88b21d8cf331435a030199b562e6b5b979f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840267"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Támogatott fájlformátumok és az Azure Data Factoryban tömörítési kodek

*Ez a cikk a következő összekötőket érinti: [Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).*

Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. Ha azt szeretné, hogy **elemezni, vagy hozzon létre egy adott formátumú fájlok**, az Azure Data Factory formátuma a következő fájltípusokat támogatja:

* [Szöveges formátum](#text-format)
* [JSON-formátumban](#json-format)
* [Parquet formátum](#parquet-format)
* [ORC formátum](#orc-format)
* [Az Avro formátum](#avro-format)
* [Bináris formátum](#binary-format)

> [!TIP]
> Ebből a témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrásadatokat a [másolási tevékenységben lévő séma-hozzárendelés](copy-activity-schema-and-type-mapping.md)

## <a name="text-format"></a>Szöveges formátum

>[!NOTE]
>Data Factory új, tagolt szöveges formátumú datset vezetett be, a részleteket a [tagolt szöveg formátuma](format-delimited-text.md) című cikkben találja. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

Ha szeretne egy szövegfájlba olvasni vagy írni egy szövegfájlba, állítsa be a `type` tulajdonságot a `format` az adatkészlet szakaszában **TextFormat**. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A TextFormat használatát bemutató példa](#textformat-example) című szakaszt.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| columnDelimiter |A fájlokban az oszlopok elválasztására használt karakter. Érdemes lehet nem létezik az adatokat a ritka nem nyomtatható karaktert használhatja. Adja meg például a "\u0001", indítsa el a fejléc (SOH) jelölő. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték a **vessző (,)** . <br/><br/>Egy Unicode karakterek használatához tekintse meg [Unicode-karaktereket](https://en.wikipedia.org/wiki/List_of_Unicode_characters) lekérése, a megfelelő kódot. |Nem |
| rowDelimiter |A fájlokban a sorok elválasztására használt karakter. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték olvasáskor a következő értékek bármelyike: **[„\r\n”, „\r”, „\n”]** , illetve **„\r\n”** írás esetén. |Nem |
| escapeChar |Az oszlophatároló feloldására szolgáló speciális karakter a bemeneti fájl tartalmában. <br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Példa: Ha a vesszőt (",") az oszlop elválasztójának kell lennie, de a szövegben vessző karaktert szeretne használni (például: "Helló, világ"), megadhatja a "$" karaktert Escape-karakterként, és a "Hello $, World" karakterláncot használhatja a forrásban. |Nem |
| quoteChar |Egy sztringérték idézéséhez használt karakter. Ekkor az idézőjel-karakterek közötti oszlop- és sorhatárolókat a rendszer a sztringérték részeként kezeli. Ez a tulajdonság a bemeneti és a kimeneti adatkészleteken is alkalmazható.<br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: &lt;Helló, világ&gt;), megadhatja a " (angol dupla idézőjel) értéket idézőjel-karakterként, és a "Helló$, világ" sztringet használhatja a forrásban. |Nem |
| nullValue |A null értéket jelölő egy vagy több karakter. |Egy vagy több karakter. Az **alapértelmezett** értékek az **„\N” és „NULL”** olvasás, illetve **„\N”** írás esetén. |Nem |
| encodingName |A kódolási név megadására szolgál. |Egy érvényes kódolási név. Lásd az [Encoding.EncodingName tulajdonságot](https://msdn.microsoft.com/library/system.text.encoding.aspx). Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| firstRowAsHeader |Megadja, hogy az első sort fejlécnek kell-e tekinteni. A bemeneti adatkészletek első sorát a Data Factory fejlécként olvassa be. A kimeneti adatkészletek első sorát a Data Factory fejlécként írja ki. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |True (Igaz)<br/><b>False (alapértelmezett)</b> |Nem |
| skipLineCount |Azt jelzi, hogy hány **nem üres** az adatok bemeneti fájlokból való olvasásakor kihagyandó sorok. Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Egész szám |Nem |
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

* Nem fájlból másolja a fájlt egy szövegfájlba, és hozzá szeretne adni egy, a séma metaadatait tartalmazó fejlécet (például: SQL-séma). Ebben a forgatókönyvben adja meg a `firstRowAsHeader` értékét igazként a kimeneti adatkészletben.
* Egy fejlécsort tartalmazó szöveges fájlból másol egy nem fájlalapú fogadóba, és el szeretné hagyni azt a sort. Adja meg a `firstRowAsHeader` értékét igazként a bemeneti adatkészletben.
* Egy szöveges fájlból másol, és szeretne kihagyni néhány sort az elejéről, amelyek nem tartalmaznak adatokat vagy fejléc-információkat. Adja meg a `skipLineCount` értékét a kihagyni kívánt sorok számának jelzéséhez. Ha a fájl hátralévő része fejlécsort tartalmaz, a `firstRowAsHeader` is megadható. Ha a `skipLineCount` és a `firstRowAsHeader` is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból

## <a name="json-format"></a>JSON-formátumban

A **importálási/exportálási egy JSON-fájlt,-be és- tárolókról az Azure Cosmos DB van**, importálási/exportálási JSON-dokumentumok részében talál [Adatáthelyezést és- tárolókról az Azure Cosmos DB](connector-azure-cosmos-db.md) cikk.

Ha meg szeretné elemezni a JSON-fájlok vagy JSON formátumban szeretne adatokat írni, állítsa be a `type` tulajdonságot a `format` szakasz **JsonFormat**. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |
| jsonNodeReference | Ha egy azonos mintával rendelkező tömbmezőben található objektumokat szeretne iterálni, vagy azokból adatokat kinyerni, adja meg a tömb JSON-útvonalát. Ez a tulajdonság csak akkor támogatott, ha JSON **-** fájlokból másol adatok. | Nem |
| jsonPathDefinition | Megadja az egyes oszlopmegfeleltetések JSON-útvonalának kifejezését testre szabott oszlopnevekkel (kezdje kisbetűvel). Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokból, és kinyeri az adatait az objektumból vagy tömbből. <br/><br/> A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `jsonNodeReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt. | Nem |
| encodingName |A kódolási név megadására szolgál. Az érvényes kódolási nevek listáját lásd: [Encoding. EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) tulajdonsága. Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték: **UTF-8**. |Nem |
| nestingSeparator |A beágyazási szinteket elválasztó karakter. Az alapértelmezett érték a „.” (pont). |Nem |

>[!NOTE]
>A tömbben lévő, több sorból származó adatmennyiség (1. eset – > minta 2 [JsonFormat](#jsonformat-example)-példákban) esetében csak egy tömböt lehet kibontani a tulajdonság `jsonNodeReference`használatával.

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

**2. eset: Az adatírás JSON-fájlba**

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

## <a name="parquet-format"></a>Parquet formátum

>[!NOTE]
>Data Factory új parketta-formátumú datset vezetett be, a részleteket lásd a [parketta formátumáról](format-parquet.md) szóló cikket. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

Ha elemezni szeretné a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **ParquetFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (Térkép, lista).
* Az oszlopnév nem támogatja az üres helyet.
* A Parquet fájlban a következő tömörítéssel kapcsolatos beállítások érhetők el: NINCS, SNAPPY, GZIP és LZO. Data Factory támogatja a Parquet-fájlból származó adatok olvasását ezen tömörített formátumok bármelyikén, a LZO kivételével – ez a metaadatokban található tömörítési kodeket használja az adatok olvasásához. A Parquet-fájlokba való írás esetén azonban a Data Factory a SNAPPY tömörítést választja, amely az alapértelmezett a Parquet-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem a (z) rendszerű parketta **-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű, a Parquet-fájlok szerializálásával/deszerializálásával futó másolás esetén az ADF a Java futtatókörnyezetet úgy keresi meg, hogy *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* először ellenőrzi a JRE beállításjegyzékét, ha nem található, *`JAVA_HOME`* Másodsorban a OpenJDK rendszer-változó ellenőrzése.

- **A JRE használata**: A 64 bites IR-hez 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja a JVM. dll fájlt a OpenJDK összes többi szükséges szerelvényéhez a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változó JAVA_HOME.

>[!TIP]
>Ha az adatok másolása a parketta formátumba vagy a saját üzemeltetésű Integration Runtime használatával történt, és a következő hibaüzenet jelenik meg: "hiba történt a Java meghívásakor, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**", `_JAVA_OPTIONS` hozzáadhat egy környezeti változót a a saját üzemeltetésű integrációs modult futtató gép, amely a JVM minimális/maximális méretének módosítását teszi lehetővé az ilyen példányok kiépítéséhez, majd a folyamat újbóli futtatásához.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be `_JAVA_OPTIONS` a változót értékkel. `-Xms256m -Xmx16g` A jelző `Xms` meghatározza a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét `Xmx` , míg a maximális memória-kiosztási készletet adja meg. Ez azt jelenti, hogy a JVM a `Xms` memóriával fog elindulni, és a memória `Xmx` maximális mennyiségét fogja tudni használni. Alapértelmezés szerint az ADF min 64 MB és Max 1G értéket használ.

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
| Karakterlánc | Bináris | Utf8 | Utf8 |
| DateTime | Int96 | – | – |
| Időtartam | Int96 | – | – |
| DateTimeOffset | Int96 | – | – |
| ByteArray | Bináris | – | – |
| GUID | Bináris | Utf8 | Utf8 |
| CHAR | Bináris | Utf8 | Utf8 |
| CharArray | Nem támogatott | – | – |

## <a name="orc-format"></a>ORC formátum

Ha elemezni szeretné a ORC-fájlokat, vagy ORC formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **OrcFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "OrcFormat"
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, Térkép, lista, UNION).
* Az oszlopnév nem támogatja az üres helyet.
* Az ork-fájl három [tömörítéssel kapcsolatos beállítást](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)tartalmaz: NINCS, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárolók közötti másoláshoz, ha nem a **-ként**másolja az ork-fájlokat, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű IR-ben az ork-fájl szerializálásával/deszerializálásával futó másoláshoz az ADF megkeresi a Java-futtatókörnyezetet *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* úgy, hogy először ellenőrzi a JRE beállításjegyzékét, ha *`JAVA_HOME`* nem található, másodsorban a OpenJDK rendszer-változó ellenőrzése.

- **A JRE használata**: A 64 bites IR-hez 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja a JVM. dll fájlt a OpenJDK összes többi szükséges szerelvényéhez a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változó JAVA_HOME.

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
| UInt64 | Karakterlánc |
| Önálló | Lebegőpontos |
| Dupla | Dupla |
| tizedes tört | tizedes tört |
| Karakterlánc | Karakterlánc |
| DateTime | Időbélyeg |
| DateTimeOffset | Időbélyeg |
| Időtartam | Időbélyeg |
| ByteArray | Bináris |
| GUID | Karakterlánc |
| CHAR | Char(1) |

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

* [Összetett adattípusok](https://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, enumerálások, tömbök, leképezések, egyesítések, és rögzített típusok).

## <a name="binary-format"></a>Bináris formátum

A részletekért tekintse meg a [bináris formátumra](format-binary.md) vonatkozó cikket.

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

  * **Leggyorsabb** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.
  * **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe.

    További információkért lásd: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör.

> [!NOTE]
> Tömörítési beállítások nem támogatottak az adatok a **AvroFormat**, **OrcFormat**, vagy **ParquetFormat**. Ezek a formátumok a fájlok olvasásakor a Data Factory észleli, és a metaadatokban szereplő tömörítési kodeket használja. Ezek a formátumok fájlok írásakor a Data Factory úgy dönt, az alapértelmezett tömörítési kodeket azt a formátumot. Ha például ZLIB OrcFormat és ParquetFormat a SNAPPY.

## <a name="unsupported-file-types-and-compression-formats"></a>A fájltípusok és a tömörítési formátumok nem támogatottak

A Azure Data Factory bővíthetőségi funkciói a nem támogatott fájlok átalakítására használhatók.
A Azure Batch használatával két lehetőség Azure Functions és egyéni feladatokat is tartalmaz.

Egy olyan minta látható, amely egy Azure-függvényt használ [egy tar-fájl tartalmának](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)kibontásához. További információ: [Azure functions tevékenység](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Ezt a funkciót egyéni DotNet-tevékenység használatával is létrehozhatja. További információ [itt](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) érhető el

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
