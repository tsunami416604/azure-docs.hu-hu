---
title: Tagolt szöveg formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a tagolt szöveges formátum a Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: jingwang
ms.openlocfilehash: 1b32685aa060363d00f1566e009beee36bbf9680
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298550"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tagolt szöveg formátuma Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha el szeretné végezni **a tagolt szövegfájlok elemzését vagy az adatmennyiség tagolt szöveges formátumba való írását**. 

A tagolt szöveges formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a tagolt szöveges adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát **DelimitedText**értékre kell állítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` .  | Igen      |
| columnDelimiter  | A fájlban lévő oszlopok elkülönítésére szolgáló karakter (ek). <br>Az alapértelmezett érték a **vessző `,` **. Ha az oszlop elválasztója üres karakterláncként van definiálva, amely nem tartalmaz határolójelet, akkor a teljes sor egyetlen oszlopként lesz elvégezve.<br>Jelenleg az oszlop elválasztója üres sztringként vagy többkarakteres értékként csak a leképezési adatforgalom esetében támogatott, a másolási tevékenység azonban nem.  | Nem       |
| rowDelimiter     | Az egyetlen karakter vagy "\r\n" egy fájl sorainak elkülönítésére szolgál. <br>Az alapértelmezett érték a következő értékek egyike **: ["\r\n", "\r", "\n"]** és **"\n" vagy "\r\n", íráshoz** az adatforgalom és a másolási tevékenység hozzárendelésével <br>Ha a sor elválasztó értéke nem határolójel (üres karakterlánc), akkor az oszlop elválasztóját nem kötelező megadni (üres karakterlánc) is, ami azt jelenti, hogy a teljes tartalmat egyetlen értékként kell kezelni.<br>Jelenleg a sorok elválasztója üres sztringként csak a leképezési folyamat esetében támogatott, a másolási tevékenység azonban nem. | Nem       |
| quoteChar        | Az oszlop értékeit idézőjelek közé tartalmazó karakter. <br>Az alapértelmezett **érték idézőjelek** `"` . <br>Az adatforgalom leképezése `quoteChar` nem lehet üres karakterlánc. <br>A másolási tevékenység esetén ha `quoteChar` az üres sztringként van definiálva, az azt jelenti, hogy nincs idézőjel karakter, és az oszlop értéke nincs megadva, és a `escapeChar` rendszer az oszlop elválasztóját és saját magát is elkerüli. | Nem       |
| escapeChar       | Az idézőjelek között egy idézett értéken belüli egyetlen karakter.<br>Az alapértelmezett érték **fordított perjel `\` **. <br>Az adatforgalom leképezése `escapeChar` nem lehet üres karakterlánc. <br/>A másolási tevékenység esetén, ha `escapeChar` üres karakterláncként van definiálva, a `quoteChar` értéket üres karakterláncként kell megadni, amely esetben ügyeljen arra, hogy az összes oszlop értéke ne tartalmazzon elhatárolókat. | Nem       |
| firstRowAsHeader | Meghatározza, hogy az első sort fejlécként kell-e kezelni/kijelölni az oszlopok neveivel.<br>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | Nem       |
| nullValue        | Megadja a null értékű karakterlánc-ábrázolást. <br>Az alapértelmezett érték **üres karakterlánc**. | Nem       |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Megjegyzés: a leképezési folyamat nem támogatja az UTF-7 kódolást. | Nem       |
| compressionCodec | A szövegfájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **Snappy**vagy a **lz4**. Az alapértelmezett érték nincs tömörítve. <br>**Megjegyzés:** a másolási tevékenység nem támogatja a "snappy" & "lz4", és a leképezési adatfolyam nem támogatja a "ZipDeflate". <br>**Megjegyzés** : Ha a másolási tevékenységgel kibontja a **ZipDeflate** -fájl (oka) t, és a fájl alapú fogadó adattárolóba ír fájlokat, az alapértelmezett fájlok a mappába kerülnek, a `<path specified in dataset>/<folder named as source zip file>/` `preserveZipFileNameAsFolder` [másolási tevékenység forrásaként](#delimited-text-as-source) pedig megadhatja, hogy a rendszer a zip-fájl nevét a mappa szerkezeteként őrizze meg. | Nem       |
| compressionLevel | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

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
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a tagolt szöveges forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="delimited-text-as-source"></a>Tagolt szöveg forrásként 

A másolási tevékenység *** \* forrása \* *** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| típus           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSource**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveg olvasási beállítások** táblázatát. | Nem       |
| storeSettings  | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . | Nem       |

Támogatott **tagolt szöveges olvasási beállítások** a következő alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A formatSettings típusát **DelimitedTextReadSettings**értékre kell beállítani. | Igen      |
| skipLineCount | Az adatok bemeneti fájlokból való olvasásakor kihagyható **nem üres** sorok számát jelzi. <br>Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. | Nem       |
| compressionProperties | Egy adott tömörítési kodekhez tartozó adatok kibontására szolgáló tulajdonságok csoportja. | Nem       |
| preserveZipFileNameAsFolder<br>(*alatt `compressionProperties` *) | Akkor érvényes, ha a bemeneti adatkészlet **ZipDeflate** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás zip-fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni. Ha TRUE (alapértelmezett) értékre van állítva, Data Factory Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source zip file>/` `<path specified in dataset>`  | Nem |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Tagolt szöveg fogadóként

A másolási *** \* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| típus           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSink**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveges írási beállítások** táblázatát. |          |
| storeSettings  | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` .  | Nem       |

Támogatott **tagolt szöveges írási beállítások** a (z) alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| típus          | A formatSettings típusát **DelimitedTextWriteSettings**értékre kell beállítani. | Igen                                                   |
| fileExtension | A kimeneti fájlok elnevezésére használt fájlkiterjesztés, például: `.csv` `.txt` . Meg kell adni, ha az `fileName` nincs megadva a kimeneti DelimitedText adatkészletben. Ha a fájl neve konfigurálva van a kimeneti adatkészletben, akkor a rendszer a fogadó fájl nevét fogja használni, és a fájlkiterjesztés beállítás figyelmen kívül lesz hagyva.  | Igen, ha a fájl neve nincs megadva a kimeneti adatkészletben |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
