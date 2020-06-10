---
title: Adatkonzisztencia-ellenőrzés a másolási tevékenységben
description: Ismerje meg, hogyan engedélyezhető az adatkonzisztencia-ellenőrzés a másolási tevékenységekben a Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a45c8ce820532d11f18758924dc3399818cb9158
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610219"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Adatkonzisztencia-ellenőrzés a másolási tevékenységben (előzetes verzió)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha a forrásról a célhelyre helyezi át az adatátvitelt, Azure Data Factory másolási tevékenység lehetővé teszi további adatkonzisztencia-ellenőrzés elvégzését, hogy az adatok ne legyenek csak a forrásról a célhelyre, hanem konzisztensek legyenek a forrás-és a célhely-tároló között. Ha inkonzisztens adatok találhatók az adatáthelyezés során, megszakíthatja a másolási tevékenységet, vagy folytathatja a másolást. ehhez engedélyezze a hibatűrési beállítást a inkonzisztens adatok kihagyása érdekében. A kihagyott objektumok nevének beszerzéséhez engedélyezze a munkamenet-napló beállításait a másolási tevékenységben. 

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és a következő korlátozásokkal dolgozunk aktívan:
>- Az adatkonzisztencia-ellenőrzés csak olyan bináris fájlokon érhető el, amelyek a másolási tevékenységben a "PreserveHierarchy" viselkedésű fájl-alapú tárolók között vannak. Táblázatos adatok másolásához az adatkonzisztencia-ellenőrzés még nem érhető el másolási tevékenységben.
>- Ha engedélyezi a munkamenet-napló beállítását a másolási tevékenységben a kihagyott inkonzisztens fájlok naplózásához, a naplófájl teljessége nem lehet 100%-ban garantált, ha a másolási tevékenység meghiúsult.
>- A munkamenet-napló csak inkonzisztens fájlokat tartalmaz, ahol a sikeresen másolt fájlok nincsenek naplózva eddig.

## <a name="supported-data-stores"></a>Támogatott adattárak

### <a name="source-data-stores"></a>Forrásadatok tárolói

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Fájlrendszer](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Célhely adattárai

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Fájlrendszer](connector-file-system.md)


## <a name="configuration"></a>Konfiguráció
Az alábbi példa egy JSON-definíciót biztosít az adatkonzisztencia-ellenőrzés engedélyezéséhez a másolási tevékenységben: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Ha a tulajdonság értéke TRUE (igaz), akkor a másolási tevékenység a forrás és a cél tároló közötti adatkonzisztencia biztosítása érdekében a forrásról a célhelyre másolt összes objektum esetében a fájl méretét, a lastModifiedDate és az MD5 ellenőrzőösszeget fogja ellenőrizni. Vegye figyelembe, hogy a másolási teljesítmény a beállítás engedélyezésével lesz hatással.  | True (Igaz)<br/>False (alapértelmezett) | No
dataInconsistency | A skipErrorFile tulajdonság táska egyik kulcs-érték párja, amely meghatározza, hogy ki szeretné-e hagyni a inkonzisztens adatmennyiséget.<br/> -True (igaz): az inkonzisztens adatok kihagyásával szeretné átmásolni a REST-et.<br/> -False (hamis): a másolási tevékenységet szeretné megszakítani, ha a rendszer inkonzisztens adathalmazt talált.<br/>Ügyeljen arra, hogy ez a tulajdonság csak akkor érvényes, ha a validateDataConsistency értéke TRUE (igaz).  | True (Igaz)<br/>False (alapértelmezett) | No
logStorageSettings | Olyan tulajdonságok csoportja, amelyekkel engedélyezhető a munkamenet-napló kihagyott objektumainak naplózása. | | No
linkedServiceName | Az [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatása a munkamenet naplófájljainak tárolására. | Egy `AzureBlobStorage` vagy több `AzureBlobFS` típusú társított szolgáltatás neve, amely a naplófájlok tárolásához használt példányra hivatkozik. | No
path | A naplófájlok elérési útja. | Itt adhatja meg a naplófájlok tárolásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No

>[!NOTE]
>- Az átmeneti másolási forgatókönyv nem támogatja az adatkonzisztencia használatát. 
>- Amikor fájlokat másol a vagy az Azure Blobba vagy Azure Data Lake Storage Gen2ba, az ADF az [Azure Blob API](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy) -t és a [Azure Data Lake Storage Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update#request-headers)-t kihasználva blokkolja a szintű MD5 ellenőrzőösszeg-ellenőrzést. Ha az Azure blobon vagy Azure Data Lake Storage Gen2 adatforrásként ContentMD5 vannak, az ADF a fájlok elolvasása után a fájl szintű MD5 ellenőrzőösszeg-ellenőrzést is támogatja. Miután fájlokat másol az Azure Blobba vagy Azure Data Lake Storage Gen2 adatcélhelyként, az ADF az Azure Blobba vagy Azure Data Lake Storage Gen2ba írja az ContentMD5, amelyet az alárendelt alkalmazások tovább használhatnak adatkonzisztencia-ellenőrzés céljából.
>- Az ADF a fájlok tárolási tárolók közötti másolásakor a fájl méretének ellenőrzését végzi.

## <a name="monitoring"></a>Figyelés

### <a name="output-from-copy-activity"></a>Kimenet a másolási tevékenységből
A másolási tevékenység teljes futtatása után megtekintheti az adatkonzisztencia-ellenőrzés eredményét az egyes másolási tevékenységek kimenetéről:

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
Az adatkonzisztencia-ellenőrzés részleteit a "dataConsistencyVerification" tulajdonságban tekintheti meg.

**VerificationResult**értéke: 
-   **Ellenőrizve**: a másolt adatai konzisztensek a forrás-és a célhely-tároló között. 
-   **NotVerified**: a másolt adatait nem ellenőrizte, hogy konzisztensek legyenek, mert nem engedélyezte a validateDataConsistency a másolási tevékenységben. 
-   Nem **támogatott**: a másolt adatok nem lettek konzisztensek, mert az adatkonzisztencia-ellenőrzés nem támogatott az adott másolási pár esetében. 

**InconsistentData**értéke: 
-   **Found**: Az ADF másolási tevékenysége inkonzisztens adathalmazt talált. 
-   **Kihagyva**: Az ADF-másolási tevékenység megtalálta és kihagyta a inkonzisztens adatértékeket. 
-   **Nincs**: Az ADF másolási tevékenysége nem talált inkonzisztens adathalmazt. Ennek oka az lehet, hogy az adatai konzisztensek a forrás-és a célhelyek között, vagy azért, mert letiltotta a validateDataConsistency a másolási tevékenységben. 

### <a name="session-log-from-copy-activity"></a>Munkamenet-napló a másolási tevékenységből

Ha úgy konfigurálja, hogy naplózza a inkonzisztens fájlt, a naplófájlt a következő elérési úttal tekintheti meg: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  A naplófájlok a CSV-fájlok lesznek. 

A naplófájl sémája a következő:

Oszlop | Leírás 
-------- | -----------  
Időbélyeg | A nem konzisztens fájlok kihagyása az ADF-ben.
Szint | Az adott tétel naplózási szintje. A fájl kihagyása esetén a rendszer figyelmeztetési szinten jeleníti meg az elemeket.
OperationName | Az ADF másolási tevékenységének működési viselkedése minden fájlban. A kihagyni kívánt fájl megadásához "FileSkip" lesz.
OperationItem | A kihagyni kívánt fájl neve.
Üzenet | További információ a kihagyott fájlok okának szemléltetéséről.

A naplófájl példája a következő: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
A fenti naplófájlban láthatja, hogy a sample1. csv fájl ki lett hagyva, mert nem sikerült ellenőrizni, hogy konzisztens-e a forrás-és a célhely-tároló között. További részleteket tudhat meg arról, hogy a sample1. csv miért inkonzisztens lesz, mert más alkalmazások is módosították, amikor az ADF másolási tevékenysége egyidejű másolást végez. 



## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység hibatűrése](copy-activity-fault-tolerance.md)


