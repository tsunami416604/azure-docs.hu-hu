---
title: Fájl- és tömörítési formátumok az Azure Data Factoryban
description: Ismerje meg az Azure Data Factory által támogatott fájlformátumokat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 901e15994b8a51a5fd45d57ca7a4db7778d968e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281612"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Az Azure Data Factory által támogatott fájl- és tömörítési formátumok
*Ez a témakör a következő összekötőkre vonatkozik: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure Blob](data-factory-azure-blob-connector.md), Azure Data [Lake Store](data-factory-azure-datalake-connector.md), [File System](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [HTTP](data-factory-http-connector.md)és [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [támogatott fájlformátumok at és a tömörítési kodekeket a Data Factory alkalmazásban.](../supported-file-formats-and-compression-codecs.md)

Az Azure Data Factory a következő fájlformátum-típusokat támogatja:

* [Szöveg formátuma](#text-format)
* [JSON formátum](#json-format)
* [Avro formátum](#avro-format)
* [ORC formátum](#orc-format)
* [Parketta formátum](#parquet-format)

## <a name="text-format"></a>Szöveg formátuma
Ha szövegfájlból szeretne olvasni vagy szövegfájlba írni, `type` állítsa `format` az adatkészlet szakaszának tulajdonságát TextFormat ( **szövegformátum**) beállításra. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A TextFormat használatát bemutató példa](#textformat-example) című szakaszt.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| columnDelimiter |A fájlokban az oszlopok elválasztására használt karakter. Érdemes lehet olyan ritka, nem nyomtatható karaktert használni, amely valószínűleg nem szerepel az adatokban. Adja meg például a "\u0001" értéket, amely a Címsor (SOH) kezdetét jelöli. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték a **vessző (,)**. <br/><br/>Unicode karakter használatához olvassa el a [Unicode karakterek című kódot.](https://en.wikipedia.org/wiki/List_of_Unicode_characters) |Nem |
| rowDelimiter |A fájlokban a sorok elválasztására használt karakter. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték olvasáskor a következő értékek bármelyike: **[„\r\n”, „\r”, „\n”]**, illetve **„\r\n”** írás esetén. |Nem |
| escapeChar |Az oszlophatároló feloldására szolgáló speciális karakter a bemeneti fájl tartalmában. <br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: „Helló, világ”), megadhatja a „$” karakter feloldójelként, és a „Helló$, világ” sztringet használhatja a forrásban. |Nem |
| quoteChar |Egy sztringérték idézéséhez használt karakter. Ekkor az idézőjel-karakterek közötti oszlop- és sorhatárolókat a rendszer a sztringérték részeként kezeli. Ez a tulajdonság a bemeneti és a kimeneti adatkészleteken is alkalmazható.<br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: &lt;Helló, világ&gt;), megadhatja a " (angol dupla idézőjel) értéket idézőjel-karakterként, és a "Helló$, világ" sztringet használhatja a forrásban. |Nem |
| nullValue |A null értéket jelölő egy vagy több karakter. |Egy vagy több karakter. Az **alapértelmezett** értékek az **„\N” és „NULL”** olvasás, illetve **„\N”** írás esetén. |Nem |
| encodingName |A kódolási név megadására szolgál. |Egy érvényes kódolási név. Lásd az [Encoding.EncodingName tulajdonságot](https://msdn.microsoft.com/library/system.text.encoding.aspx). Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |Nem |
| firstRowAsHeader |Megadja, hogy az első sort fejlécnek kell-e tekinteni. A bemeneti adatkészletek első sorát a Data Factory fejlécként olvassa be. A kimeneti adatkészletek első sorát a Data Factory fejlécként írja ki. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |True (Igaz)<br/><b>False (alapértelmezett)</b> |Nem |
| skipLineCount |Az adatok bemeneti fájlokból való olvasásakor kihagyandó sorok számát jelzi. Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Egész szám |Nem |
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

## <a name="json-format"></a>JSON formátum
A **JSON-fájl importálása/exportálása az Azure Cosmos DB-be/az Azure Cosmos DB-ből**című témakörben található: [Adatok áthelyezése](data-factory-azure-documentdb-connector.md#importexport-json-documents) [az Azure Cosmos DB-be/az Azure Cosmos DB-ből](data-factory-azure-documentdb-connector.md) című cikkben.

Ha elemezni szeretné a JSON-fájlokat, vagy JSON formátumban `type` szeretné `format` írni az adatokat, állítsa a szakasz tulajdonságát **JsonFormat**értékre. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |
| jsonNodeReference | Ha egy azonos mintával rendelkező tömbmezőben található objektumokat szeretne iterálni, vagy azokból adatokat kinyerni, adja meg a tömb JSON-útvonalát. Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokat. | Nem |
| jsonPathDefinition | Megadja az egyes oszlopmegfeleltetések JSON-útvonalának kifejezését testre szabott oszlopnevekkel (kezdje kisbetűvel). Ez a tulajdonság csak akkor támogatott, ha JSON-fájlokból másol adatokat, és ki tud nyerni adatokat objektumokból vagy tömbökből. <br/><br/> A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `jsonNodeReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt. | Nem |
| encodingName |A kódolási név megadására szolgál. Az érvényes kódolási nevekkel kapcsolatban lásd az [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) tulajdonságot. Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték: **UTF-8**. |Nem |
| nestingSeparator |A beágyazási szinteket elválasztó karakter. Az alapértelmezett érték a „.” (pont). |Nem |

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

A JSON-fájlokból származó adatok másolásakor tekintse meg az alábbi két mintát. Az általános pontok megjegyezni:

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

| id | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További részletekért olvassa el a [Forrásadatkészlet-oszlopok leképezése a céladatkészletek oszlopaihoz](data-factory-map-columns.md) című szakaszt.
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. A tömbből származó adatok másolásához a **tömb[x].tulajdonsággal** kinyerheti az adott tulajdonság értékét az x.-objektumból, vagy a **tömb[*].tulajdonsággal** megkeresheti az ilyen tulajdonságot tartalmazó objektumok értékét.

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

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További részletekért olvassa el a [Forrásadatkészlet-oszlopok leképezése a céladatkészletek oszlopaihoz](data-factory-map-columns.md) című szakaszt.
- A `jsonNodeReference` jelzi az orderlines **tömb** alatti, azonos mintával rendelkező objektumok iterálását, illetve az adatok azokból való kinyerését.
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Ebben a példában az „ordernumber”, az „orderdate” és a „city” a „$.” értékkel kezdődő JSON-útvonallal jelzett gyökérobjektum alatt találhatók, míg az „order_pd” és az „order_price” a „$.” értéket nem tartalmazó tömbelemből származtatott útvonallal vannak meghatározva.

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

| id | order_date | order_price | order_by |
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

## <a name="avro-format"></a>AVRO formátum
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

## <a name="orc-format"></a>ORC formátum
Ha elemezni szeretné a ORC-fájlokat, vagy ORC formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **OrcFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Ha nem **adott állapotban** másol ORC-fájlokat a helyszíni és a felhőbeli adattárolók között, telepítenie kell a JRE (Java-futtatókörnyezet) 8-as verzióját az átjáró számítógépre. A 64 bites átjáróhoz 64 bites JRE, a 32 bites átjáróhoz 32 bites JRE szükséges. Mindkét verziót megtalálja [itt](https://go.microsoft.com/fwlink/?LinkId=808605). Válassza ki a megfelelő verziót.
>
>

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, MAP, LIST, UNION)
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

## <a name="parquet-format"></a>Parquet formátum
Ha elemezni szeretné a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **ParquetFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Ha nem **adott állapotban** másol Parquet-fájlokat a helyszíni és a felhőbeli adattárolók között, telepítenie kell a JRE (Java-futtatókörnyezet) 8-as verzióját az átjáró számítógépre. A 64 bites átjáróhoz 64 bites JRE, a 32 bites átjáróhoz 32 bites JRE szükséges. Mindkét verziót megtalálja [itt](https://go.microsoft.com/fwlink/?LinkId=808605). Válassza ki a megfelelő verziót.
>
>

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (MAP, LIST)
* A Parquet-fájlok a következő tömörítéshez kapcsolódó beállításokat használják: NONE, SNAPPY, GZIP és LZO. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. A Parquet-fájlokba való írás esetén azonban a Data Factory a SNAPPY tömörítést választja, amely az alapértelmezett a Parquet-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

## <a name="compression-support"></a>Tömörítés támogatása
Nagy adatkészletek feldolgozása i/o- és hálózati szűk keresztmetszeteket okozhat. Ezért az üzletekben lévő tömörített adatok nem csak felgyorsíthatják az adatátvitelt a hálózaton keresztül, és lemezterületet takaríthatnak meg, hanem jelentős teljesítményjavulást is eredményeznek a big data feldolgozásában. Jelenleg a tömörítés támogatott fájlalapú adattárak, például az Azure Blob vagy a helyszíni fájlrendszer.  

Ha egy adatkészlethez tömörítést szeretne megadni, használja a JSON adatkészlet **tömörítési** tulajdonságát az alábbi példában:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Tegyük fel, hogy a minta adatkészlet egy másolási tevékenység kimeneteként használatos, a másolási tevékenység tömöríti a kimeneti adatokat a GZIP codec optimális arány használatával, és írja be a tömörített adatokat egy pagecounts.csv.gz nevű fájlba az Azure Blob Storage-ban.

> [!NOTE]
> Az **AvroFormat**, **OrcFormat**vagy **ParquetFormat**formátumban lévő adatok nem támogatottak a tömörítési beállítások. Ha ilyen formátumú fájlokat olvas, a Data Factory észleli és használja a metaadatokban lévő tömörítési kodeket. Amikor ilyen formátumú fájlokba ír, a Data Factory kiválasztja az adott formátumhoz tartozó alapértelmezett tömörítési kodeket. Például ZLIB az OrcFormat és a SNAPPY a ParquetFormat esetében.   

A **tömörítési** szakasznak két tulajdonsága van:  

* **Típus:** a tömörítő kodek, amely lehet **GZIP,** **Deflate,** **BZIP2**vagy **ZipDeflate**.  
* **Szint:** a tömörítési arány, amely lehet **optimális** vagy **leggyorsabb**.

  * **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban el kell végeznie, még akkor is, ha az eredményül kapott fájl nincs optimálisan tömörítve.
  * **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet hosszabb időt vesz igénybe.

    További információt a [Tömörítési szint témakörben](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) talál.

Ha tulajdonságot ad meg `compression` egy JSON bemeneti adatkészletben, a folyamat képes olvasni a forrásból származó tömörített adatokat; és amikor megadja a tulajdonságot egy kimeneti adatkészletben JSON, a másolási tevékenység írhat tömörített adatokat a cél. Íme néhány példa forgatókönyv:

* GZIP tömörített adatokat olvashat egy Azure blobból, kibonthatja, és eredményadatokat írhat egy Azure SQL-adatbázisba. A bemeneti Azure Blob-adatkészletet a `compression` `type` JSON tulajdonsággal GZIP-ként definiálja.
* Adatok olvasása egy egyszerű szöveges fájlból a helyszíni fájlrendszerből, tömörítheti azt GZip formátumban, és írja a tömörített adatokat egy Azure blobba. A `compression` `type` JSON-tulajdonsággal rendelkező kimeneti Azure Blob-adatkészletet GZip-ként definiál.
* Olvassa el a .zip fájlt az FTP-kiszolgálóról, bontsa ki a fájlokat, és tegye le ezeket az Azure Data Lake Store-ba. A JSON tulajdonsággal `compression` `type` rendelkező bemeneti FTP-adatkészletet ZipDeflate néven definiálhat.
* GZIP-tömörített adatok at egy Azure blob, kibontani, tömöríteni, tömöríteni a BZIP2 használatával, és írja az eredményadatokat egy Azure blobba. A bemeneti Azure Blob-adatkészletet `compression` `type` a GZIP-re `compression` `type` és a kimeneti adatkészletet a BZIP2-re állítva határozza meg ebben az esetben.   


## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi cikkeket az Azure Data Factory által támogatott fájlalapú adattárakról:

- [Azure Blob Storage](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Fájlrendszer](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)
