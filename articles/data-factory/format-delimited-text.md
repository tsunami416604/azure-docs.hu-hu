---
title: Tagolt szövegformátum az Azure Data Factoryban |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure Data Factoryban tagolt szövegformátum kezelése.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146003"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Az Azure Data Factoryban tagolt szövegformátum

Ez a cikk kövesse, ha meg szeretné **elemezni a elválasztójellel tagolt szöveges fájlok, vagy tagolt szöveges formátumban szeretne adatokat írni**. 

Az alábbi csatlakozók elválasztójellel tagolt szöveges formátum támogatott: [Az Amazon S3](connector-amazon-simple-storage-service.md), [az Azure Blob](connector-azure-blob-storage.md), [az Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [az Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [az Azure File Storage](connector-azure-file-storage.md), [Fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), és [ Az SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a elválasztójellel tagolt szöveges adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A type tulajdonságot az adatkészlet értékre kell állítani **DelimitedText**. | Igen      |
| location         | A fájl(ok) beállításai. Minden egyes fájl alapú összekötő saját hely típusa és a támogatott tulajdonságok alapján `location`. **A részleteit az összekötő -> Tulajdonságok szakaszának adatkészlet**. | Igen      |
| columnDelimiter  | Az oszlopok elválasztására használt karaktert vagy karaktereket. Elválasztó karaktere több adatfolyam leképezése, de nem a másolási tevékenység csak támogatott. <br>Az alapértelmezett érték **vesszővel `,`** , amikor az oszlophatároló számít, ha üres karakterláncot, ami azt jelenti, nincs elválasztó, a teljes sort kell venni egy oszlopban. | Nem       |
| rowDelimiter     | Az egyetlen karakter vagy a sorok elválasztására használt "\r\n".<br>Az alapértelmezett érték a következő értékek bármelyike: **Olvasás: ["\r\n", "\r", "\n"]**, és **"\n" vagy "\r\n" íráskor** térképadatokat Flow és másolási tevékenység jelölik. <br>Amikor `rowDelimiter` nincs elválasztó (üres) értékre van állítva a `columnDelimiter` nincs elválasztó (üres karakterlánc) szerint, amely azt jelenti, hogy kezelje a teljes tartalom egyetlen értékként kell beállítani. | Nem       |
| quoteChar        | Az egyetlen karakternek kell ajánlat oszlop értékeit, ha az oszlop elválasztó karaktert tartalmaz. <br>Az alapértelmezett érték **idézőjel** `"`. <br>Az adatfolyam-leképezés `quoteChar` nem lehet üres karakterlánc. <br>A másolási tevékenység amikor `quoteChar` van definiálva üres karakterlánc, akkor nincs idézőjel karakter van és není uveden v uvozovkách oszlop értékét, és `escapeChar` karaktert az oszlophatároló, és saját maga szolgál. | Nem       |
| escapeChar       | Az escape-ajánlatok a határolójeles érték belül egyetlen karakter.<br>Az alapértelmezett érték **fordított perjel `\`** . <br>Az adatfolyam-leképezés `escapeChar` nem lehet üres karakterlánc. <br/>A másolási tevékenység amikor `escapeChar` üres karakterláncként van definiálva a `quoteChar` állítja be, valamint az üres karakterlánc, ebben az esetben ellenőrizze, hogy minden oszlop értékei nem tartalmaz elválasztó karaktereket kell lennie. | Nem       |
| firstRowAsHeader | Megadja, hogy a treat/ügyeljen az első sor egy fejlécsort, az oszlopok nevét.<br>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett). | Nem       |
| nullValue        | Itt adható meg null érték karakteres formáját. <br>Az alapértelmezett érték **üres karakterlánc**. | Nem       |
| encodingName     | A használni kívánt olvasási/írási tesztfájlok kódolási típusát. <br>Megengedett értékek a következők: "AZ UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC JP", "EUC-KOREA", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149","ISO-2022-JP","ISO-2022-KOREA ","ISO-8859-1","ISO-8859 – 2","ISO-8859-3","ISO-8859-4","ISO-8859-5","ISO-8859-6","ISO-8859-7","ISO-8859-8","ISO-8859-9","ISO-8859-13","ISO-8859-15","A WINDOWS-874","A WINDOWS-1250","A WINDOWS-1251","A WINDOWS-1252-ES","A WINDOWS-1253"," A WINDOWS-1254 ","A WINDOWS-1255","A WINDOWS-1256","A WINDOWS-1257","A WINDOWS-1258".<br>Vegye figyelembe, hogy adatfolyam-leképezés nem támogatja a kódolás az UTF-7. | Nem       |
| compressionCodec | A szöveges fájlok olvasására/írására használt tömörítési kodek. <br>Engedélyezett értékek a következők **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, vagy **lz4**. a fájl mentésekor használatára. <br>Megjegyzés: jelenleg a másolási tevékenység nem támogatja a "snappy" & "lz4", "ZipDeflate" adatfolyam-leképezés nem támogatja. | Nem       |
| compressionLevel | A tömörítési arány. <br>Engedélyezett értékek a következők **Optimal** vagy **leggyorsabb**.<br>- **Leggyorsabb:** A tömörítés kell művelet minél gyorsabban, még akkor is, ha az eredményül kapott fájl nem optimális tömöríti.<br>- **Optimális**: A tömörítés művelet optimálisan tömöríteni kell, akkor is, ha a művelet végrehajtásához hosszabb ideig tart. További információkért lásd: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

Alább egy példa az Azure Blob Storage elválasztójellel tagolt szöveges adatkészlet van:

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a elválasztójellel tagolt szöveges forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="delimited-text-as-source"></a>Tagolt szöveges forrásként 

A következő tulajdonságok támogatottak a másolási tevékenység ***\*forrás\**** szakaszban.

| Tulajdonság       | Leírás                                                  | Szükséges |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenység forrása típusa tulajdonságát állítsa **DelimitedTextSource**. | Igen      |
| formatSettings | Csoport tulajdonságai. Tekintse meg **tagolt szöveg beállítások beolvasása** az alábbi táblázat. | Nem       |
| storeSettings  | Egy csoport a adatok olvasása-adatokat az adattárból tulajdonságait. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállítás alatt `storeSettings`. **A részleteit az összekötő -> a másolási tevékenység tulajdonságok szakaszának**. | Nem       |

Támogatott **elválasztójellel tagolt szöveges beállítások beolvasása** alatt `formatSettings`:

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings típusát állítsa **DelimitedTextReadSetting**. | Igen      |
| skipLineCount | Azt jelzi, hogy hány **nem üres** az adatok bemeneti fájlokból való olvasásakor kihagyandó sorok. <br>Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. | Nem       |

### <a name="delimited-text-as-sink"></a>Fogadóként elválasztójellel tagolt szöveges

A következő tulajdonságok támogatottak a másolási tevékenység ***\*fogadó\**** szakaszban.

| Tulajdonság       | Leírás                                                  | Szükséges |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A másolási tevékenység forrása típusa tulajdonságát állítsa **DelimitedTextSink**. | Igen      |
| formatSettings | Csoport tulajdonságai. Tekintse meg **tagolt szöveg beállítások írása** az alábbi táblázat. |          |
| storeSettings  | Hogyan lehet adatokat írni az adattár tulajdonságait tartalmazó csoport. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításait `storeSettings`. **A részleteit az összekötő -> a másolási tevékenység tulajdonságok szakaszának**. | Nem       |

Támogatott **elválasztójellel tagolt szöveges beállítások írása** alatt `formatSettings`:

| Tulajdonság      | Leírás                                                  | Szükséges                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings típusát állítsa **DelimitedTextWriteSetting**. | Igen                                                   |
| fileExtension | A fájl kiterjesztése például név a kimeneti fájlok `.csv`, `.txt`. Lehet megadni, ha a `fileName` nincs megadva a kimenetben DelimitedText adatkészlet. | Igen, ha a fájl neve nincs megadva a kimeneti adatkészlet |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok hozzárendelése

A részletek [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) az adatfolyam-leképezés.

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)