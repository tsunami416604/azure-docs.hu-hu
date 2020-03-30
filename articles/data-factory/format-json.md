---
title: JSON formátum az Azure Data Factoryban
description: Ez a témakör ismerteti, hogyan kezelje a JSON formátumot az Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260643"
---
# <a name="json-format-in-azure-data-factory"></a>JSON formátum az Azure Data Factoryban

Kövesse ezt a cikket, ha elemezni szeretné **a JSON-fájlokat, vagy az adatokat JSON formátumba**szeretné írni. 

A JSON formátum a következő összekötőkesetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a JSON-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet típustulajdonságát **Json**-ra kell állítani. | Igen      |
| location         | A fájl(ok) helybeállításai. Minden fájlalapú összekötő saját helytípussal és `location`támogatott tulajdonságokkal rendelkezik a csoportban. **További részletek az összekötő -> adatkészlet tulajdonságai szakaszban.** | Igen      |
| encodingName     | A tesztfájlok olvasásához/írásához használt kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM437", "IBM437", "IBM437", "IBM437", "IBM430", "IBM400", IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01148", IBM "01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nem       |
| tömörítési kodek | A szövegfájlok olvasására/írására használt tömörítési kodek. <br>Megengedett **értékek: bzip2**, **gzip,** **deflate,** **ZipDeflate**, **lendületes,** vagy **lz4**. a fájl mentésekor. <br>Megjegyzés: Jelenleg a másolási tevékenység nem támogatja a "lendületes" & az "lz4"-et.<br>Megjegyzés: Ha másolási tevékenységet használ a ZipDeflate fájl(ok) kibontásához és a fájlalapú `<path specified in dataset>/<folder named as source zip file>/`fogadó adattárba való íráshoz, a fájlok kibontása a következő mappába kerül: . | Nem       |
| tömörítésI szint | A tömörítési arány. <br>Az engedélyezett értékek **az Optimális** vagy **a Leggyorsabb**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban el kell végeznie, még akkor is, ha az eredményül kapott fájl nincs optimálisan tömörítve.<br>- **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet hosszabb időt vesz igénybe. További információt a [Tömörítési szint témakörben](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) talál. | Nem       |

Az alábbiakban egy példa a JSON-adatkészlet az Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a JSON-forrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="json-as-source"></a>JSON forrásként

A következő tulajdonságokat a másolási tevékenység *** \*forrása\* *** szakasz támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságát **JSONSource**-ra kell állítani. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adattárból történő adatok olvasásáról. Minden fájlalapú összekötő saját támogatott olvasási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

### <a name="json-as-sink"></a>JSON mint mosogató

A következő tulajdonságokat a *** \*\* *** másolási tevékenység fogadó szakasza támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **JSONSink (JSONSink**. | Igen      |
| formatSettings (Beállítások) | Tulajdonságok csoportja. Lásd az alábbi **JSON írási beállítások** táblázatot. | Nem       |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adatok adattárba való írásának módjáról. Minden fájlalapú összekötő saját támogatott írási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

Támogatott **JSON írási beállítások a** következő alatt: `formatSettings`

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | A formátum típusátA Beállítások beállítást **JsonWriteSettings**- formátumra kell állítani. | Igen                                                   |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |

### <a name="json-file-patterns"></a>JSON-fájlminták

Másolás tevékenység automatikusan felismeri és elemzi a következő mintákat a JSON fájlokat. 

- **I. típus: setOfObjects**

    Minden fájl egyetlen objektumot, illetve több, sorokkal határolt/összefűzött objektumot tartalmaz. 
    Ha ezt a beállítást választja a másolási tevékenység fogadójában, a másolási tevékenység egyetlen JSON-fájlt hoz létre minden objektummal soronként (vonaltag).

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

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

A JSON fájltípusok fogadóként és forrásként is használhatók az adatfolyam leképezésében.

### <a name="creating-json-structures-in-a-derived-column"></a>JSON-struktúrák létrehozása származtatott oszlopban

A származtatott oszlopkifejezés-szerkesztőn keresztül összetett oszlopot adhat az adatfolyamhoz. A származtatott oszlop átalakításában vegyen fel egy új oszlopot, és a kék mezőre kattintva nyissa meg a kifejezésszerkesztőt. Oszlopkomplexushoz manuálisan is megadhatja a JSON-struktúrát, vagy a felhasználói felület segítségével interaktívan adhat hozzá aloszlopokat.

#### <a name="using-the-expression-builder-ux"></a>A kifejezésszerkesztő felhasználói felületének használata

A kimeneti séma oldalsó ablaktábláján mutasson egy oszlopra, és kattintson a plusz ikonra. Ha az oszlopot összetett típussá szeretné tenni, válassza az **Aloszlop hozzáadása** lehetőséget.

![Aloszlop hozzáadása](media/data-flow/addsubcolumn.png "Aloszlop hozzáadása")

Ugyanígy további oszlopokat és aloszlopokat is hozzáadhat. Minden nem összetett mezőhöz egy kifejezés adható a jobb oldali kifejezésszerkesztőhöz.

![Összetett oszlop](media/data-flow/complexcolumn.png "Összetett oszlop")

#### <a name="entering-the-json-structure-manually"></a>A JSON-struktúra manuális megadása

JSON-struktúra manuális hozzáadásához vegyen fel egy új oszlopot, és írja be a kifejezést a szerkesztőbe. A kifejezés a következő általános formátumot követi:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Ha ezt a kifejezést egy "complexColumn" nevű oszlophoz adta meg, akkor a következő JSON-ként írná be a fogadóba:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Minta kézi parancsfájl a teljes hierarchikus definícióhoz
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Forrásformátum beállításai

A JSON-adatkészlet forrásként való használata lehetővé teszi öt további beállítás beállítását. Ezek a beállítások a **JSON beállítások** harmonika alatt találhatók a **Forrásbeállítások** lapon.  

![JSON-beállítások](media/data-flow/json-settings.png "JSON-beállítások")

#### <a name="default"></a>Alapértelmezett

Alapértelmezés szerint a JSON-adatok olvasása a következő formátumban történik.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Egyetlen dokumentum

Ha **az Egy dokumentum van** kiválasztva, a leképezési adatfolyamok minden fájlból egy JSON-dokumentumot olvasnak be. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Nem jegyzett oszlopnevek

Ha **a nem idézett oszlopnevek** et választja, az adatfolyamok leképezése olyan JSON-oszlopokat olvas, amelyeket nem vesznek körül idézőjelek. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Megjegyzések

Válassza **a Megjegyzések hez** lehetőséget, ha a JSON-adatok C vagy C++ stílusú megjegyzésekkel rendelkeznek.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Egyszeres idézett

Válassza az **Egyszeres idézőjel** lehetőséget, ha a JSON mezők és értékek idézőjeleket használnak idézőjelek helyett.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>A fordított perjel megszökött

Jelölje **be az Egyszeridézett lehetőséget,** ha fordított perjelek et használ a JSON-adatok karaktereinek kikerülésére.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>További lépések

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
