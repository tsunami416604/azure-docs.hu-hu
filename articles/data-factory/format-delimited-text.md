---
title: Tagolt szövegformátum az Azure Data Factoryban
description: Ez a témakör ismerteti, hogyan kell kezelni a tagolt szövegformátum ot az Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 47d26ad452b8494e591ee919076e5ade8bf19cd7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011395"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tagolt szövegformátum az Azure Data Factoryban

Kövesse ezt a cikket, ha elemezni szeretné **a tagolt szövegfájlokat, vagy az adatokat tagolt szövegformátumba**szeretné írni. 

A tagolt szövegformátum a következő összekötőkhöz támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a tagolt szövegadatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet típustulajdonságát **TaglimitedText (Tagolt szöveg)** típusú tulajdonságra kell állítani. | Igen      |
| location         | A fájl(ok) helybeállításai. Minden fájlalapú összekötő saját helytípussal és `location`támogatott tulajdonságokkal rendelkezik a csoportban.  | Igen      |
| columnDelimiter  | A fájl oszlopainak szétválasztására használt karakter(ek). <br>Az alapértelmezett érték **vessző `,` **. Ha az oszlophatároló üres karakterláncként van definiálva, ami azt jelenti, hogy nincs határolójel, a teljes sor egyetlen oszlopként lesz definiálva.<br>Jelenleg az oszlophatároló üres karakterláncként vagy többkarakterként csak az adatfolyam leképezéséhez támogatott, a másolási tevékenységhez nem.  | Nem       |
| rowDelimiter     | A fájl sorainak szétválasztására használt egyetlen vagy "\r\n" karakter. <br>Az alapértelmezett érték a következő értékek bármelyike az **olvasáskor: ["\r\n", "\r", "\n"]** és **"\n" vagy "\r\n" íráskor** az adatfolyam és a Másolási tevékenység leképezésével. <br>Ha a sorhatároló nincs határolójel (üres karakterlánc), az oszlophatárolót nem határolójelként (üres karakterláncként) kell beállítani, ami azt jelenti, hogy a teljes tartalmat egyetlen értékként kell kezelni.<br>Jelenleg a sorhatároló üres karakterláncként csak az adatfolyam leképezéséhez támogatott, a másolási tevékenységhez nem. | Nem       |
| quoteChar        | Az oszlopértékeket idéző egyetlen karakter, ha oszlophatárolót tartalmaz. <br>Az alapértelmezett érték **dupla idézőjelek** `"`. <br>Az adatfolyam `quoteChar` leképezéséhez nem lehet üres karakterlánc. <br>A Másolási `quoteChar` tevékenység esetében, ha üres karakterláncként van definiálva, az azt `escapeChar` jelenti, hogy nincs idézőkarakter, és az oszlopérték nem kerüli el a programot, és az oszlophatároló és önmaga elől való kikerülésre szolgál. | Nem       |
| escapeChar       | Az idézett értéken belüli idézőjelek elől menekülő egyetlen karakter.<br>Az alapértelmezett érték a **fordított perjel `\` **. <br>Az adatfolyam `escapeChar` leképezéséhez nem lehet üres karakterlánc. <br/>A Másolás i `escapeChar` tevékenység esetében, ha `quoteChar` üres karakterláncként van definiálva, akkor a karakterláncot is üres karakterláncként kell beállítani, ebben az esetben győződjön meg arról, hogy az összes oszlopérték nem tartalmaz határolót. | Nem       |
| firstRowAsHeader | Itt adható meg, hogy az első sort oszlopnevekkel ellátott fejlécsorként kezelje/adja-e meg.<br>Az engedélyezett értékek **igazak** és **hamisak** (alapértelmezett). | Nem       |
| nullValue        | A null érték karakterlánc-ábrázolását adja meg. <br>Az alapértelmezett érték üres **karakterlánc**. | Nem       |
| encodingName     | A tesztfájlok olvasásához/írásához használt kódolási típus. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Megjegyzés: Az adatfolyam leképezése nem támogatja az UTF-7 kódolást. | Nem       |
| tömörítési kodek | A szövegfájlok olvasására/írására használt tömörítési kodek. <br>Megengedett **értékek: bzip2**, **gzip,** **deflate,** **ZipDeflate**, **lendületes,** vagy **lz4**. Az alapértelmezett érték nincs tömörítve. <br>**Megjegyzés:** jelenleg a másolási tevékenység nem támogatja a "lendületes" & az "lz4"-et, és az adatfolyam leképezése nem támogatja a "ZipDeflate" -t. <br>**Megjegyzés:** Ha másolási tevékenységet használ a ZipDeflate fájl(ok) kibontásához és a fájlalapú `<path specified in dataset>/<folder named as source zip file>/`fogadó adattárba való íráshoz, a fájlok kibontása a következő mappába kerül: . | Nem       |
| tömörítésI szint | A tömörítési arány. <br>Az engedélyezett értékek **az Optimális** vagy **a Leggyorsabb**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban el kell végeznie, még akkor is, ha az eredményül kapott fájl nincs optimálisan tömörítve.<br>- **Optimális**: A tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet hosszabb időt vesz igénybe. További információt a [Tömörítési szint témakörben](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) talál. | Nem       |

Az alábbiakban egy példa látható az Azure Blob Storage tagolt szöveges adatkészletére:

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a tagolt szövegforrás és -fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="delimited-text-as-source"></a>Tagolt szöveg forrásként 

A következő tulajdonságokat a másolási tevékenység *** \*forrása\* *** szakasz támogatja.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenységforrás típustulajdonságának **DelimitedTextSource (DelimitedTextSource**) típusú tulajdonságát kell állítani. | Igen      |
| formatSettings (Beállítások) | Tulajdonságok csoportja. Lásd az alábbi **Tagolt szöveg olvasási beállítások** táblázatát. | Nem       |
| storeSettings (tárolóbeállítások)  | Tulajdonságok csoportja az adattárból történő adatok olvasásáról. Minden fájlalapú összekötő saját támogatott olvasási beállításokkal rendelkezik a területen. `storeSettings` | Nem       |

Támogatott **tagolt szöveg olvasási beállítások a** következő alatt: `formatSettings`

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A formátum típusátA Beállítások beállítást **DelimitedTextReadSettings (Beállítások körülhatárolt) beállítással kell elévülve.** | Igen      |
| skipLineCount | A bemeneti fájlokból történő adatolvasáskor kihagyandó **nem üres** sorok számát jelzi. <br>Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. | Nem       |

### <a name="delimited-text-as-sink"></a>Tagolt szöveg fogadóként

A következő tulajdonságokat a *** \*\* *** másolási tevékenység fogadó szakasza támogatja.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenységforrás típustulajdonságának **DelimitedTextSink (DelimitedTextSink**) típusú tulajdonságát kell állítani. | Igen      |
| formatSettings (Beállítások) | Tulajdonságok csoportja. Lásd az alábbi **Tagolt szöveg írási beállítások** táblázatot. |          |
| storeSettings (tárolóbeállítások)  | Tulajdonságok csoportja az adatok adattárba való írásának módjáról. Minden fájlalapú összekötő saját támogatott írási beállításokkal rendelkezik a területen. `storeSettings`  | Nem       |

Támogatott **tagolt szövegírási beállítások a** következő alatt: `formatSettings`

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | A formátum típusátA Beállítások beállítást **DelimitedTextWriteSettings ( DetagoltSzöveg-írási beállítások**) beállításra kell állítani. | Igen                                                   |
| fájlkiterjesztés | A kimeneti fájlok elnevezésére használt fájlkiterjesztés, pl. `.csv`. `.txt` Akkor kell megadni, `fileName` ha nincs megadva a kimeneti DelimitedText adatkészletben. Ha a kimeneti adatkészletben fájlnév van konfigurálva, a program a fogadófájl neveként fogja használni, és a fájlkiterjesztés beállítását a program figyelmen kívül hagyja.  | Igen, ha a fájlnév nincs megadva a kimeneti adatkészletben |

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Ismerje meg a [részleteket a forrás átalakítása](data-flow-source.md) és [a fogadó átalakítása](data-flow-sink.md) leképezési adatfolyam.

## <a name="next-steps"></a>További lépések

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
