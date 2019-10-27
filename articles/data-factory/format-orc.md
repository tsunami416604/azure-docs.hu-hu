---
title: ORK formátum a Azure Data Factoryban | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan kezelhető az ork formátum a Azure Data Factoryban.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: beff81b7e8d207a2173497d039b915b7614fc30e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952276"
---
# <a name="orc-format-in-azure-data-factory"></a>Az ork formátuma Azure Data Factory

Kövesse ezt a cikket, ha szeretné **elemezni az ork-fájlokat, vagy az adatfájlt az ork formátumba írja**. 

Az ork formátum a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az ork-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát az **ork**értékre kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságok `location` alatt. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |

Alább látható egy példa az Azure Blob Storage-beli ork-adatkészletre:

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

* Az összetett adattípusok nem támogatottak (STRUCT, Térkép, lista, UNION).
* Az oszlopnév nem támogatja az üres helyet.
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az ork forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="orc-as-source"></a>ORK forrásként

A másolási tevékenység ***\*forrás\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **OrcSource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal `storeSettings` alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="orc-as-sink"></a>ORK mint fogadó

A másolási tevékenység\*a fogadó ***\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **OrcSink**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival `storeSettings` alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime használata

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárolók közötti másoláshoz, ha nem a **-ként**másolja az ork-fájlokat, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű IR-ben az ork-fájl szerializálásával/deszerializálásával futó másoláshoz az ADF megkeresi a Java-futtatókörnyezetet úgy, hogy először ellenőrzi a (z) JRE beállításjegyzék- *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`ét* , ha nem található, másodsorban ellenőrzi a rendszerváltozót *`JAVA_HOME`* a OpenJDK.

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja a JVM. dll fájlt a OpenJDK összes többi szükséges szerelvényéhez a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változó JAVA_HOME.

> [!TIP]
> Ha az Adatmásolást az ork formátumba vagy a saját üzemeltetésű Integration Runtime használatával másolja, és a "hiba történt a Java meghívása során – üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**", akkor hozzáadhat egy környezeti változót `_JAVA_OPTIONS` a gépen amely a saját üzemeltetésű integrációs modult futtatja a JVM minimális/maximális méretének módosításához az ilyen példányok létrehozásához, majd a folyamat újbóli futtatásához.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be a változó `_JAVA_OPTIONS` értéket a `-Xms256m -Xmx16g`értékkel. A `Xms` jelző jelzi a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét, míg a `Xmx` érték a maximális memória-kiosztási készletet határozza meg. Ez azt jelenti, hogy a JVM `Xms` mennyiségű memóriával fog elindulni, és legfeljebb `Xmx` mennyiségű memóriát tud használni. Alapértelmezés szerint az ADF min 64 MB és Max 1G értéket használ.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
