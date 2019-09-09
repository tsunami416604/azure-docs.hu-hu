---
title: JSON formátum a Azure Data Factoryban | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan kezelhető a JSON formátuma Azure Data Factoryban.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 7ff164b378ac6981fcb9686d6264b0bcf50cfafb
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814810"
---
# <a name="json-format-in-azure-data-factory"></a>JSON formátum Azure Data Factory

Kövesse ezt a cikket, ha **elemezni szeretné a JSON-fájlokat, vagy írja be az adatbevitelt JSON formátumba**. 

A JSON formátum a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a JSON-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát **JSON**-ra kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location`. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 "," IBM855 "," IBM857 "," IBM860 "," IBM861 "," IBM863 "," IBM864 "," IBM865 "," IBM869 "," IBM870 "," IBM01140 "," IBM01141 "," IBM01142 "," IBM01143 "," IBM01144 "," IBM01145 "," IBM01146 "," IBM01147 "," IBM01148 "," IBM01149 "," "," ISO-2022-JP "," ISO-2022 "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 "," ISO-8859-15 "," WINDOWS-874 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".| Nem       |
| compressionCodec | A szövegfájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **Snappy**vagy a **lz4**. a fájl mentésekor használatos. <br>Megjegyzés a másolási tevékenység jelenleg nem támogatja a "Snappy" & "lz4". | Nem       |
| compressionLevel | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információkért lásd: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

Alább látható egy példa a JSON-adatkészletre az Azure Blob Storageban:

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a JSON-forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="json-as-source"></a>JSON forrásként

A másolási tevékenység ***\*forrása\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **JSONSource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival `storeSettings`rendelkeznek. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="json-as-sink"></a>JSON mint fogadó

A másolási ***\*tevékenység\**** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **JSONSink**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi **JSON írási beállítások** táblázatát. | Nem       |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival `storeSettings`rendelkeznek. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

Támogatott **JSON írási beállítások** a `formatSettings`alatt:

| Tulajdonság      | Leírás                                                  | Szükséges                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | A formatSettings típusát **JsonWriteSetting**értékre kell beállítani. | Igen                                                   |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |

### <a name="json-file-patterns"></a>JSON-fájlminták

A másolási tevékenység automatikusan képes azonosítani és elemezni a JSON-fájlok következő mintáit. 

- **I. típus: setOfObjects**

    Minden fájl egyetlen objektumot, illetve több, sorokkal határolt/összefűzött objektumot tartalmaz. 
    Ha ez a beállítás a másolási tevékenység fogadójában van kiválasztva, a másolási tevékenység egyetlen JSON-fájlt hoz létre, amely soronként minden objektummal rendelkezik (sorok tagolása).

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

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
