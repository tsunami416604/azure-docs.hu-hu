---
title: ORC formátum az Azure Data Factoryban
description: Ez a témakör ismerteti, hogyan kell kezelni az ORC formátumot az Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597490"
---
# <a name="orc-format-in-azure-data-factory"></a>ORC formátum az Azure Data Factoryban

Kövesse ezt a cikket, ha elemezni szeretné **az ORC fájlokat, vagy orc formátumba szeretné írni az adatokat.** 

Az ORC formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz az ORC adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet típustulajdonságát **Orc**. | Igen      |
| location         | A fájl(ok) helybeállításai. Minden fájlalapú összekötő saját helytípussal és `location`támogatott tulajdonságokkal rendelkezik a csoportban. **További részletek az összekötő -> adatkészlet tulajdonságai szakaszban.** | Igen      |

Az alábbiakban egy példa az ORC adatkészlet az Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, MAP, LIST, UNION).
* Az oszlopnévben lévő szóköz nem támogatott.
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az ORC forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="orc-as-source"></a>ORC forrásként

A következő tulajdonságokat a másolási tevékenység *** \*forrása\* *** szakasz támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **az OrcSource**beállításra kell legyen állítva. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adattárból történő adatok olvasásáról. Minden fájlalapú összekötő saját támogatott olvasási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

### <a name="orc-as-sink"></a>ORC mosogatóként

A következő tulajdonságokat a *** \*\* *** másolási tevékenység fogadó szakasza támogatja.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenységforrás típustulajdonságának **OrcSink (OrcSink**. | Igen      |
| storeSettings (tárolóbeállítások) | Tulajdonságok csoportja az adatok adattárba való írásának módjáról. Minden fájlalapú összekötő saját támogatott írási beállításokkal rendelkezik a területen. `storeSettings` **További részletek az összekötő cikkében -> a tevékenység tulajdonságainak másolása szakaszban.** | Nem       |

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs futásidő használata

> [!IMPORTANT]
> A saját üzemeltetésű integrációs futásidejű ek által engedélyezett másoláshoz pl. a helyszíni és a felhőbeli adattárak között, ha nem az ORC fájlokat **másolja,** akkor telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy az OpenJDK** és a **Microsoft Visual C++ 2010 újraterjeszthető csomagot** az infravörös gépen. További részletekkel ellenőrizze a következő bekezdést.

Az ORC fájlszerializálással/deszerializálással rendelkező saját üzemeltetésű infravörös kapcsolaton futó *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* másolás esetén az ADF úgy keresi *`JAVA_HOME`* meg a Java futtatóidőt, hogy először ellenőrzi a JRE rendszerleíró adatbázisát, ha nem található, másodsorban az OpenJDK rendszerváltozójának ellenőrzését.

- **A JRE használata**: A 64 bites ir használatához 64 bites JRE szükséges. Megtalálható, hogy [innen](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Az OpenJDK használata**: A 3.13-as infravörös verzió óta támogatott. Csomagolja be a jvm.dll-t az OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű infravörös gépbe, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.
- **A Visual C++ 2010 újraterjeszthető csomag telepítése**: A Visual C++ 2010 újraterjeszthető csomag nincs telepítve saját üzemeltetésű infravörös telepítéssel. Megtalálható, hogy [innen](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Ha az ORC formátumba másolja/onnan másolja az orc formátumot a saját üzemeltetésű integrációs futásidő használatával, és a "Hiba történt java, üzenet: `_JAVA_OPTIONS` **java.lang.OutOfMemoryError:Java halomterület"** meghívásakor, hozzáadhat egy környezeti változót a saját üzemeltetésű infravörös szolgáltatást tartalmazó gépen, hogy a JVM min/max halomméretét az ilyen másolás felhatalmazásához módosítsa, majd futtassa újra a folyamatot.

![JVM halommemória méretének beállítása saját üzemeltetésű infravörös rendszeren](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa `_JAVA_OPTIONS` be `-Xms256m -Xmx16g`a változót értékkel. A `Xms` jelző a Java virtuális gép (JVM) kezdeti `Xmx` memóriafoglalási készletét adja meg, míg a maximális memóriafoglalási készletet. Ez azt jelenti, hogy a `Xms` `Xmx` JVM memóriával indul el, és legfeljebb ennyi memóriát tud használni. Alapértelmezés szerint az ADF min 64 MB-ot és max 1G-t használ.

## <a name="next-steps"></a>További lépések

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
