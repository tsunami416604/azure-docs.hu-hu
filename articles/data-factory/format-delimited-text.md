---
title: Tagolt szöveg formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a tagolt szöveges formátum a Azure Data Factoryban.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: b710492b3416371d8d554945a60160261e5a8c6a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674824"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tagolt szöveg formátuma Azure Data Factory

Kövesse ezt a cikket, ha el szeretné végezni **a tagolt szövegfájlok elemzését vagy az adatmennyiség tagolt szöveges formátumba való írását**. 

A tagolt szöveges formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md) [ Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a tagolt szöveges adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát **DelimitedText**értékre kell állítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz a saját hely típusa és a `location`alatt támogatott tulajdonságok tartozik.  | Igen      |
| columnDelimiter  | A fájlban lévő oszlopok elkülönítésére szolgáló karakter (ek). Jelenleg a több karakterből álló elválasztó csak a leképezési adatforgalom esetében támogatott, a másolási tevékenység azonban nem. <br>Az alapértelmezett érték a **vessző `,`** , ha az oszlop elválasztója üres sztringként van definiálva, amely nem tartalmaz határolójelet, a teljes sort egyetlen oszlopként veszi át. | Nem       |
| rowDelimiter     | Az egyetlen karakter vagy "\r\n" egy fájl sorainak elkülönítésére szolgál.<br>Az alapértelmezett érték a következő értékek egyike **: ["\r\n", "\r", "\n"]** és **"\n" vagy "\r\n", íráshoz** az adatforgalom és a másolási tevékenység hozzárendelésével <br>Ha `rowDelimiter` a No határolójel (üres karakterlánc) értékre van állítva, a `columnDelimiter` nem lehet elválasztó (üres karakterlánc) értékűnek lennie, ami azt jelenti, hogy a teljes tartalmat egyetlen értékként kell kezelni. | Nem       |
| quoteChar        | Az oszlop értékeit idézőjelek közé tartalmazó karakter. <br>Az alapértelmezett **érték idézőjelek `"`.** <br>Az adatforgalom leképezése `quoteChar` nem lehet üres karakterlánc. <br>Másolási tevékenység esetén, ha a `quoteChar` üres karakterláncként van definiálva, az azt jelenti, hogy nincs idézőjel karakter, és az oszlop értéke nincs megadva, és `escapeChar` az oszlop elválasztójának és magának a megmenekülésére szolgál. | Nem       |
| escapeChar       | Az idézőjelek között egy idézett értéken belüli egyetlen karakter.<br>Az alapértelmezett érték a **fordított perjel `\`** . <br>Az adatforgalom leképezése `escapeChar` nem lehet üres karakterlánc. <br/>Másolási tevékenység esetén, ha `escapeChar` üres karakterláncként van definiálva, a `quoteChar`nak üres karakterláncként kell beállítania, és ebben az esetben ügyeljen arra, hogy az összes oszlop értéke ne tartalmazzon határolójeleket. | Nem       |
| firstRowAsHeader | Meghatározza, hogy az első sort fejlécként kell-e kezelni/kijelölni az oszlopok neveivel.<br>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem       |
| nullValue        | Megadja a null értékű karakterlánc-ábrázolást. <br>Az alapértelmezett érték **üres karakterlánc**. | Nem       |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".<br>Megjegyzés: a leképezési folyamat nem támogatja az UTF-7 kódolást. | Nem       |
| compressionCodec | A szövegfájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **Snappy**vagy a **lz4**. a fájl mentésekor használatos. <br>Megjegyzés: a másolási tevékenység nem támogatja a "Snappy" & "lz4", és a leképezési adatfolyam nem támogatja a "ZipDeflate". | Nem       |
| compressionLevel | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **leggyorsabb:** a tömörítési műveletnek a lehető leggyorsabbnak kell lennie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

Alább látható egy példa az Azure Blob Storage tagolt szöveges adatkészletre:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a tagolt szöveges forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="delimited-text-as-source"></a>Tagolt szöveg forrásként 

A másolási tevékenység ***\*forrás\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSource**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveg olvasási beállítások** táblázatát. | Nem       |
| storeSettings  | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal a `storeSettings`alatt. | Nem       |

Támogatott **tagolt szöveges olvasási beállítások** a `formatSettings`alatt:

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A formatSettings típusát **DelimitedTextReadSetting**értékre kell beállítani. | Igen      |
| skipLineCount | Az adatok bemeneti fájlokból való olvasásakor kihagyható **nem üres** sorok számát jelzi. <br>Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. | Nem       |

### <a name="delimited-text-as-sink"></a>Tagolt szöveg fogadóként

A másolási tevékenység\*a fogadó ***\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSink**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveges írási beállítások** táblázatát. |          |
| storeSettings  | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival a `storeSettings`alatt.  | Nem       |

Támogatott **tagolt szöveges írási beállítások** a `formatSettings`alatt:

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | A formatSettings típusát **DelimitedTextWriteSetting**értékre kell beállítani. | Igen                                                   |
| fileExtension | A kimeneti fájlok elnevezéséhez használt fájlkiterjesztés, például `.csv`, `.txt`. Meg kell adni, ha a `fileName` nincs megadva a kimeneti DelimitedText adatkészletben. | Igen, ha a fájl neve nincs megadva a kimeneti adatkészletben |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
