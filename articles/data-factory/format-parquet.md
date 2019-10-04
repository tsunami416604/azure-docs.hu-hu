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
ms.openlocfilehash: 572547f4e22a4fcb63a030e64ca95a0b9d3eff00
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734487"
---
# <a name="parquet-format-in-azure-data-factory"></a>A parketta formátuma Azure Data Factory

Kövesse ezt a cikket, ha szeretné **elemezni a parketta-fájlokat, vagy a parketta formátumba írja az**adatírást. 

A parketta formátuma a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Parquet-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Szükséges |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Az adatkészlet Type tulajdonságát a Parquet értékrekell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location`. **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Parquet forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="parquet-as-source"></a>Parketta forrásként

A másolási tevékenység ***\*forrása\**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSource**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival `storeSettings`rendelkeznek. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

### <a name="parquet-as-sink"></a>Parketta mint fogadó

A másolási tevékenység ***\*\**** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Szükséges |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSink**értékre kell állítani. | Igen      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival `storeSettings`rendelkeznek. **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="data-type-support"></a>Adattípusok támogatása

A Parquet komplex adattípusok jelenleg nem támogatottak (például Térkép, lista, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime használata

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem a (z) rendszerű parketta **-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű, a Parquet-fájlok szerializálásával/deszerializálásával futó másolás esetén az ADF a Java futtatókörnyezetet úgy keresi meg, hogy *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* először ellenőrzi a JRE beállításjegyzékét, ha nem található, *`JAVA_HOME`* Másodsorban a OpenJDK rendszer-változó ellenőrzése.

- **A JRE használata**: A 64 bites IR-hez 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja a JVM. dll fájlt a OpenJDK összes többi szükséges szerelvényéhez a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változó JAVA_HOME.

> [!TIP]
> Ha az adatok másolása a parketta formátumba vagy a saját üzemeltetésű Integration Runtime használatával történt, és a következő hibaüzenet jelenik meg: "hiba történt a Java meghívásakor, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**", `_JAVA_OPTIONS` hozzáadhat egy környezeti változót a a saját üzemeltetésű integrációs modult futtató gép, amely a JVM minimális/maximális méretének módosítását teszi lehetővé az ilyen példányok kiépítéséhez, majd a folyamat újbóli futtatásához.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be `_JAVA_OPTIONS` a változót értékkel. `-Xms256m -Xmx16g` A jelző `Xms` meghatározza a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét `Xmx` , míg a maximális memória-kiosztási készletet adja meg. Ez azt jelenti, hogy a JVM a `Xms` memóriával fog elindulni, és a memória `Xmx` maximális mennyiségét fogja tudni használni. Alapértelmezés szerint az ADF min 64 MB és Max 1G értéket használ.

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
