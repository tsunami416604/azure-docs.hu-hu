---
title: Parquet formátum az Azure Data Factoryban |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure Data Factoryban Parquet formátum kezelése.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 5b711fac2a7bc41d11bcc80927f460390888cc3e
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64878058"
---
# <a name="parquet-format-in-azure-data-factory"></a>Az Azure Data Factoryban parquet formátum

Ez a cikk kövesse, ha meg szeretné **elemezni a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni**. 

Az alábbi csatlakozók parquet formátum támogatott: [Az Amazon S3](connector-amazon-simple-storage-service.md), [az Azure Blob](connector-azure-blob-storage.md), [az Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [az Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [az Azure File Storage](connector-azure-file-storage.md), [Fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), és [ Az SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a parquet eszközökben adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A type tulajdonságot az adatkészlet értékre kell állítani **Parquet**. | Igen      |
| location         | A fájl(ok) beállításai. Minden egyes fájl alapú összekötő saját hely típusa és a támogatott tulajdonságok alapján `location`. **A részleteit az összekötő -> Tulajdonságok szakaszának adatkészlet**. | Igen      |
| compressionCodec | A Parquet-fájlok írásához használt tömörítési kodek. A Parquet-fájlok olvasásakor a Data Factory automatikusan meghatározni a tömörítési kodek alapján a fájl metaadatait.<br>Támogatott típusok a következők "**nincs**","**gzip**","**snappy**" (alapértelmezett), és a "**lzo**". Jelenleg vegye figyelembe, a másolási tevékenység nem támogatja a LZO. | Nem       |

> [!NOTE]
> Az oszlop neve szóközt nem támogatott a Parquet-fájlokat.

Alább példaként szolgál a Parquet-adatkészlet az Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a parquet eszközökben forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="parquet-as-source"></a>Parquet forrásként

A következő tulajdonságok támogatottak a másolási tevékenység ***\*forrás\**** szakaszban.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrása típusa tulajdonságát állítsa **ParquetSource**. | Igen      |
| storeSettings | Egy csoport a adatok olvasása-adatokat az adattárból tulajdonságait. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállítás alatt `storeSettings`. **A részleteit az összekötő -> a másolási tevékenység tulajdonságok szakaszának**. | Nem       |

### <a name="parquet-as-sink"></a>Ahogy a fogadó parquet eszközökben

A következő tulajdonságok támogatottak a másolási tevékenység ***\*fogadó\**** szakaszban.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrása típusa tulajdonságát állítsa **ParquetSink**. | Igen      |
| storeSettings | Hogyan lehet adatokat írni az adattár tulajdonságait tartalmazó csoport. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításait `storeSettings`. **A részleteit az összekötő -> a másolási tevékenység tulajdonságok szakaszának**. | Nem       |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok hozzárendelése

A részletek [átalakítási forrás](data-flow-source.md) és [átalakítási fogadó](data-flow-sink.md) az adatfolyam-leképezés.

## <a name="data-type-support"></a>Adattípus-támogatás

A parquet eszközökben összetett adattípusok jelenleg nem támogatott (pl. MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul használatával

> [!IMPORTANT]
> A példány által felhatalmazott helyi integrációs modul például a helyszíni és a felhő között adatokat tárolja, ha nem másol Parquet-fájlokat **,-van**, telepítenie kell a **64 bites JRE 8 (Java-futtatókörnyezet) vagy Openjdk csomagját** az integrációs modul gépen. Olvassa el az alábbi részletekkel kapcsolatban.

Futó helyi IR a parquet eszközökben fájl szerializálás/deszerializálás másolásához, ADF a Java-futtatókörnyezet megkeresi a beállításjegyzék először is ellenőrzésével *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* a JRE, ha nem talál, másodszor ellenőrzése rendszerváltozó *`JAVA_HOME`* az openjdk csomagját.

- **JRE használandó**: A 64 bites integrációs modul 64 bites JRE szükséges. Annak a [Itt](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Openjdk csomagját használandó**: IR 3.13 verziója óta támogatott. A jvm.dll, az összes többi csomagot annak megfelelően szükséges szerelvények openjdk csomagját, helyi integrációs modul gép és a rendszer környezeti változó beállítása JAVA_HOME.

> [!TIP]
> Ha másolja és a parquet vagy egyéb adatok formázása a helyi integrációs modul használatával, és hiba üzenettel találati "hiba történt a java meghívásakor üzenet: **java.lang.OutOfMemoryError:Java halommemória terület**", hozzáadhat egy környezeti változó `_JAVA_OPTIONS` a gépen, amelyen a helyi integrációs modul beállításához JVM megjelenő új ilyen példány a minimális/maximális halommemória mérete, majd futtassa újra a folyamatot.

![Állítsa be a JVM-halommemória mérete a helyi integrációs modul](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: set a változó `_JAVA_OPTIONS` értékkel `-Xms256m -Xmx16g`. A jelző `Xms` adja meg a hozzárendelés kezdeti memóriakészletben számára a Java virtuális gép (JVM), miközben `Xmx` megadja a maximális memóriát foglalási készletet. Ez azt jelenti, hogy a JVM fog elindulni `Xms` memória mennyiségét, és legfeljebb használható lesz `Xmx` memória mennyisége. Alapértelmezés szerint az ADF minimális 64 MB-ot használja, és legfeljebb 1G.

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Az adatfolyam-leképezés](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)