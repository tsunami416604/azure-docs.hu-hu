---
title: A parketta formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a parketta formátuma Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597626"
---
# <a name="parquet-format-in-azure-data-factory"></a>A parketta formátuma Azure Data Factory

Kövesse ezt a cikket, ha szeretné **elemezni a parketta-fájlokat, vagy a parketta formátumba írja az adatírást**. 

A parketta formátuma a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Parquet-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát a **Parquet**értékre kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz a saját hely típusa és a `location`alatt támogatott tulajdonságok tartozik. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| compressionCodec | A Parquet-fájlokba való íráskor használandó tömörítési kodek. A Parquet-fájlokból való olvasáskor az adat-előállítók automatikusan meghatározzák a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**", "**gzip**", "**Snappy**" (alapértelmezett) és "**LZO**". Megjegyzés: a másolási tevékenység jelenleg nem támogatja a LZO a parketta-fájlok olvasása/írása közben. | Nem       |

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

A másolási tevékenység ***\*forrás\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott olvasási beállításokkal a `storeSettings`alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="parquet-as-sink"></a>Parketta mint fogadó

A másolási tevékenység\*a fogadó ***\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSink**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. Minden fájl alapú összekötő rendelkezik a saját támogatott írási beállításaival a `storeSettings`alatt. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="data-type-support"></a>Adattípusok támogatása

A Parquet komplex adattípusok jelenleg nem támogatottak (például Térkép, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime használata

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem a (z) **rendszerű parketta-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** és a **Microsoft C++ Visual 2010 újraterjeszthető csomagot** az IR-gépen. További részletekért olvassa el a következő bekezdést.

A saját üzemeltetésű, a Parquet-fájlok szerializálásával/deszerializálásával futó másolás esetén az ADF a Java futtatókörnyezetet úgy keresi meg, hogy először ellenőrzi a (z) JRE beállításjegyzék- *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`ét* , ha nem található, másodsorban ellenőrzi a rendszerváltozót *`JAVA_HOME`* a OpenJDK.

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja a JVM. dll fájlt a OpenJDK összes többi szükséges szerelvényéhez a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.
- **A Visual C++ 2010 Újraterjeszthető csomag telepítése**: a Visual C++ 2010 újraterjeszthető csomagja nincs telepítve a saját üzemeltetésű IR-telepítésekkel. [Itt](https://www.microsoft.com/download/details.aspx?id=14632)találhatja meg.

> [!TIP]
> Ha saját üzemeltetésű Integration Runtime használatával másol adatokba vagy a-ból a parketta formátumba, és a "hiba történt a Java, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**" kifejezéssel, akkor hozzáadhat egy környezeti változót is `_JAVA_OPTIONS` a saját üzemeltetésű integrációs modult futtató GÉPEN a JVM minimális/maximális méretének módosításához.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be a változó `_JAVA_OPTIONS` értéket a `-Xms256m -Xmx16g`értékkel. A jelző `Xms` megadja egy Java virtuális gép (JVM) kezdeti memória-kiosztási készletét, míg a `Xmx` megadja a maximális memória-kiosztási készletet. Ez azt jelenti, hogy a JVM `Xms` mennyiségű memóriával fog elindulni, és legfeljebb `Xmx` mennyiségű memóriát tud használni. Alapértelmezés szerint az ADF minimális 64 MB és Max 1G értéket használ.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
