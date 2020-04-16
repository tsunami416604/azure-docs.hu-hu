---
title: Parketta formátum az Azure Data Factoryban
description: Ez a témakör ismerteti, hogyan kell kezelni a parketta formátumot az Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 223b1b996b82acaa753eb55723e251dc5901bbec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417708"
---
# <a name="parquet-format-in-azure-data-factory"></a>Parketta formátum az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha elemezni szeretné **a Parketta fájlokat, vagy az adatokat Parketta formátumba**szeretné írni. 

Parketta formátum támogatott a következő összekötők: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a Parketta adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet típustulajdonságát **Parketta**értékre kell állítani. | Igen      |
| location         | A fájl(ok) helybeállításai. Minden fájlalapú összekötő saját helytípussal és `location`támogatott tulajdonságokkal rendelkezik a csoportban. **További részletek az összekötő -> adatkészlet tulajdonságai szakaszban.** | Igen      |
| tömörítési kodek | A parettatfájlokba íráshoz használandó tömörítési kodek. A Parquet-fájlokból történő olvasáskor az adatgyárak automatikusan meghatározzák a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**", "**gzip**", "**snappy**" (alapértelmezett) és "**lzo**". Megjegyzés: Jelenleg a másolási tevékenység nem támogatja az LZO-t a parquet fájlok olvasása/írásakor. | Nem       |

> [!NOTE]
> A Parkettafájlok nem támogatják az oszlopnévben lévő szóközt.

Az alábbiakban egy példa a Parketta adatkészlet az Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Parketta forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="parquet-as-source"></a>Parketta forrásként

A következő tulajdonságokat a másolási tevékenység *** \*forrása\* *** szakasz támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságát **ParquetSource**értékre kell állítani. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adattárból történő adatok olvasásáról. Minden fájlalapú összekötő saját támogatott olvasási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

### <a name="parquet-as-sink"></a>Parketta mosogatóként

A következő tulajdonságokat a *** \*\* *** másolási tevékenység fogadó szakasza támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **ParquetSink**értékre kell állítania. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adatok adattárba való írásának módjáról. Minden fájlalapú összekötő saját támogatott írási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

## <a name="mapping-data-flow-properties"></a>Adatfolyam tulajdonságainak leképezése

Ismerje meg a [részleteket a forrás átalakítása](data-flow-source.md) és [a fogadó átalakítása](data-flow-sink.md) leképezési adatfolyam.

## <a name="data-type-support"></a>Adattípus-támogatás

Parketta komplex adattípusok jelenleg nem támogatottak (pl. MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs futásidő használata

> [!IMPORTANT]
> A saját üzemeltetésű integrációs futásidejű ek által engedélyezett másoláshoz pl. a helyszíni és a felhőbeli adattárak között, ha nem a Parquet-fájlokat **másolja,** akkor telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy openjdk** és **microsoft Visual C++ 2010 terjeszthető csomagot** az infravörös gépen. További részletekkel ellenőrizze a következő bekezdést.

A parquet fájl szerializálással/deszerializálással rendelkező saját üzemeltetésű infravörös kapcsolaton *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* futó másolás esetén az ADF úgy *`JAVA_HOME`* keresi meg a Java futtatóidőt, hogy először ellenőrzi a JRE rendszerleíró adatbázisát, ha nem található, másrészt ellenőrzi az OpenJDK rendszerváltozóját.

- **A JRE használata**: A 64 bites ir használatához 64 bites JRE szükséges. Megtalálható, hogy [innen](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Az OpenJDK használata**: A 3.13-as infravörös verzió óta támogatott. Csomagolja be a jvm.dll-t az OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű infravörös gépbe, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.
- **A Visual C++ 2010 újraterjeszthető csomag telepítése**: A Visual C++ 2010 újraterjeszthető csomag nincs telepítve saját üzemeltetésű infravörös telepítéssel. Megtalálható, hogy [innen](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Ha adatokat másol/onnan a Saját üzemeltetésű integrációs futásidejű használatával, és a "Hiba történt java, message: **java.lang.OutOfMemoryError:Java halomterület"** meghívásakor, hozzáadhat egy környezeti változót `_JAVA_OPTIONS` a saját üzemeltetésű integrációs integrációs szolgáltatást tartalmazó számítógépen, amely a JVM min/max heap méretét állítja be az ilyen másolás felhatalmazásához, majd futtassa újra a folyamatot.

![JVM halommemória méretének beállítása saját üzemeltetésű infravörös rendszeren](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa `_JAVA_OPTIONS` be `-Xms256m -Xmx16g`a változót értékkel. A `Xms` jelző a Java virtuális gép (JVM) kezdeti `Xmx` memóriafoglalási készletét adja meg, míg a maximális memóriafoglalási készletet. Ez azt jelenti, hogy a `Xms` `Xmx` JVM memóriával indul el, és legfeljebb ennyi memóriát tud használni. Alapértelmezés szerint az ADF min 64 MB-ot és max 1G-t használ.

## <a name="next-steps"></a>További lépések

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Adatfolyam leképezése](concepts-data-flow-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
