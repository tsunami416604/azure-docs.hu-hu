---
title: Parketta formátuma Azure Data Factoryban | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan kezelhető a parketta formátuma Azure Data Factoryban.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 92950a79be43bc656d50e2ced106a2fdb47f53bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387709"
---
# <a name="parquet-format-in-azure-data-factory"></a>A parketta formátuma Azure Data Factory

Kövesse ezt a cikket, ha szeretné **elemezni a parketta-fájlokat, vagy a parketta formátumba írja az adatírást**. 

A parketta formátuma a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Felhőalapú tárolás](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Parquet-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát a **Parquet**értékre kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságok `location` alatt. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| compressionCodec | A Parquet-fájlokba való íráskor használandó tömörítési kodek. A Parquet-fájlokból való olvasáskor Data Factory automatikusan meghatározza a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**", "**gzip**", "**Snappy**" (alapértelmezett) és "**LZO**". Megjegyzés: a másolási tevékenység jelenleg nem támogatja a LZO. | Nem       |

> [!NOTE]
> A Parquet-fájlok nem támogatják a szóközöket az oszlop nevében.

Alább látható egy példa a Parquet-adatkészletre az Azure Blob Storageban:

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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Parquet forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="parquet-as-source"></a>Parketta forrásként

A másolási tevékenység ***\*source @ no__t-2*** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal `storeSettings` alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="parquet-as-sink"></a>Parketta mint fogadó

A másolási tevékenység ***\*sink @ no__t-2*** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSink**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival `storeSettings` alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="data-type-support"></a>Adattípusok támogatása

A Parquet komplex adattípusok jelenleg nem támogatottak (például Térkép, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime használata

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem a (z) **rendszerű parketta-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű, valamint a Parquet-fájlok szerializálásával/deszerializálásával futó másoláshoz az ADF a Java-futtatókörnyezetet úgy keresi meg, hogy először ellenőrzi a *(z) `(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* for JRE beállításjegyzéket, ha nem található, másodsorban a (z) *`JAVA_HOME`* rendszerváltozó ellenőrzése a OpenJDK.

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja a JVM. dll fájlt a OpenJDK összes többi szükséges szerelvényéhez a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változó JAVA_HOME.

> [!TIP]
> Ha saját üzemeltetésű Integration Runtime használatával másol adatokba vagy a-ból a parketta formátumba, és a "hiba történt a Java meghívásakor, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**", a (z) `_JAVA_OPTIONS` környezeti változót adhat hozzá a a saját üzemeltetésű integrációs modult futtató gép, amely a JVM minimális/maximális méretének módosítását teszi lehetővé az ilyen példányok kiépítéséhez, majd a folyamat újbóli futtatásához.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be a `_JAVA_OPTIONS` változót a `-Xms256m -Xmx16g` értékkel. A `Xms` jelző jelzi a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét, míg a `Xmx` érték a maximális memória-kiosztási készletet határozza meg. Ez azt jelenti, hogy a JVM `Xms` mennyiségű memóriával fog elindulni, és legfeljebb `Xmx` mennyiségű memóriát tud használni. Alapértelmezés szerint az ADF min 64 MB és Max 1G értéket használ.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
