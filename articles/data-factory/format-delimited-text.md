---
title: Tagolt szöveges formátum a Azure Data Factoryban | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan kezelhető a tagolt szöveges formátum a Azure Data Factoryban.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: a545617c9e93a9a5fd0a34acc1dd5e2825917b62
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387682"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tagolt szöveg formátuma Azure Data Factory

Kövesse ezt a cikket, ha el szeretné végezni **a tagolt szövegfájlok elemzését vagy az adatmennyiség tagolt szöveges formátumba való írását**. 

A tagolt szöveges formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md) [ Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a tagolt szöveges adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát **DelimitedText**értékre kell állítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságok `location` alatt. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| columnDelimiter  | A fájlban lévő oszlopok elkülönítésére szolgáló karakter (ek). Jelenleg a több karakterből álló elválasztó csak a leképezési adatforgalom esetében támogatott, a másolási tevékenység azonban nem. <br>Az alapértelmezett érték a következő: **vessző `,`** , ha az oszlop elválasztó értéke üres sztring, amely nem tartalmaz határolójelet, a teljes sor egyetlen oszlopként lesz elvégezve. | Nem       |
| rowDelimiter     | Az egyetlen karakter vagy "\r\n" egy fájl sorainak elkülönítésére szolgál.<br>Az alapértelmezett érték a következő értékek egyike **: ["\r\n", "\r", "\n"]** és **"\n" vagy "\r\n", íráshoz** az adatforgalom és a másolási tevékenység hozzárendelésével <br>Ha a `rowDelimiter` nem határolójel (üres karakterlánc) értékre van állítva, akkor a `columnDelimiter` értéknek nem határolójel (üres karakterlánc) értékűnek kell lennie, ami azt jelenti, hogy a teljes tartalmat egyetlen értékként kell kezelni. | Nem       |
| quoteChar        | Az oszlop értékeit idézőjelek közé tartalmazó karakter. <br>Az alapértelmezett érték **idézőjelek** `"`. <br>Az adatforgalom leképezése esetében a `quoteChar` nem lehet üres karakterlánc. <br>Másolási tevékenység esetén, ha a `quoteChar` üres sztringként van definiálva, az azt jelenti, hogy nincs idézőjel karakter, és az oszlop értéke nincs megadva, és a `escapeChar` az oszlop elválasztójának és magának a megmenekülésére szolgál. | Nem       |
| escapeChar       | Az idézőjelek között egy idézett értéken belüli egyetlen karakter.<br>Az alapértelmezett érték a **fordított perjel `\`** . <br>Az adatforgalom leképezése esetében a `escapeChar` nem lehet üres karakterlánc. <br/>Másolási tevékenység esetén, ha a `escapeChar` üres karakterláncként van definiálva, akkor a `quoteChar` értéknek üres karakterláncnak kell lennie, és ebben az esetben ügyeljen arra, hogy az összes oszlop értéke ne tartalmazzon határolójeleket. | Nem       |
| firstRowAsHeader | Meghatározza, hogy az első sort fejlécként kell-e kezelni/kijelölni az oszlopok neveivel.<br>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem       |
| nullValue        | Megadja a null értékű karakterlánc-ábrázolást. <br>Az alapértelmezett érték **üres karakterlánc**. | Nem       |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".<br>Megjegyzés: a leképezési folyamat nem támogatja az UTF-7 kódolást. | Nem       |
| compressionCodec | A szövegfájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **Snappy**vagy a **lz4**. a fájl mentésekor használatos. <br>Megjegyzés: a másolási tevékenység nem támogatja a "Snappy" & "lz4", és a leképezési adatfolyam nem támogatja a "ZipDeflate". | Nem       |
| compressionLevel | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>@no__t – 0 –**leggyorsabb:** a tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>@no__t – 0**optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

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

A másolási tevékenység ***\*source @ no__t-2*** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Szükséges |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSource**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveg olvasási beállítások** táblázatát. | Nem       |
| storeSettings  | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal `storeSettings` alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

Támogatott **tagolt szöveges olvasási beállítások** az `formatSettings` alatt:

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A formatSettings típusát **DelimitedTextReadSetting**értékre kell beállítani. | Igen      |
| skipLineCount | Az adatok bemeneti fájlokból való olvasásakor kihagyható **nem üres** sorok számát jelzi. <br>Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. | Nem       |

### <a name="delimited-text-as-sink"></a>Tagolt szöveg fogadóként

A másolási tevékenység ***\*sink @ no__t-2*** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Szükséges |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSink**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveges írási beállítások** táblázatát. |          |
| storeSettings  | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival `storeSettings` alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

A `formatSettings` alatt támogatott **tagolt szöveges írási beállítások** :

| Tulajdonság      | Leírás                                                  | Szükséges                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | A formatSettings típusát **DelimitedTextWriteSetting**értékre kell beállítani. | Igen                                                   |
| fileExtension | A kimeneti fájlok elnevezésére szolgáló fájlkiterjesztés, például `.csv`, `.txt`. Meg kell adni, ha a `fileName` nincs megadva a kimeneti DelimitedText adatkészletben. | Igen, ha a fájl neve nincs megadva a kimeneti adatkészletben |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)