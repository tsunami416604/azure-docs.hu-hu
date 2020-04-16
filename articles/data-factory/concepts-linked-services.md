---
title: Társított szolgáltatások az Azure Data Factoryben
description: További információ a Data Factory összekapcsolt szolgáltatásairól. A csatolt szolgáltatások a számítási/adattárolókat adat-előállítóhoz kapcsolják.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: a6002ed173ca5358df4257f4c8b41c88bcf60ad8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418375"
---
# <a name="linked-services-in-azure-data-factory"></a>Társított szolgáltatások az Azure Data Factoryben

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogy mik a csatolt szolgáltatások, hogyan vannak definiálva JSON formátumban, és hogyan használják őket az Azure Data Factory-folyamatokban.

Ha most látja el a Data Factory, olvassa [el az Azure Data Factory bevezetés](introduction.md) című témakört az áttekintést.

## <a name="overview"></a>Áttekintés

A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen hajtanak végre egy feladatot. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Például előfordulhat, hogy egy másolási tevékenység segítségével adatokat másolni egy helyszíni SQL Server az Azure Blob storage. Ezután előfordulhat, hogy egy Hive-tevékenység, amely egy Hiv-parancsfájlt futtat egy Azure HDInsight-fürtön a Blob storage-ból származó adatok feldolgozásához kimeneti adatok előállításához. Végül előfordulhat, hogy egy második másolási tevékenység segítségével másolja a kimeneti adatokat az Azure SQL Data Warehouse, amelyen felül az üzleti intelligencia (BI) jelentéskészítési megoldások épülnek. A folyamatokról és a tevékenységekről további információt a [Folyamatok és tevékenységek](concepts-pipelines-activities.md) az Azure Data Factoryban című témakörben talál.

Most egy **adatkészlet** az adatok névvel ellátott nézete, amely egyszerűen rámutat vagy hivatkozik a **tevékenységekben** bemenetként és kimenetként használni kívánt adatokra.

Az adatkészlet létrehozása előtt létre kell hoznia egy **csatolt szolgáltatást,** amely az adattáradat-tárolót az adat-előállítóhoz kapcsolja. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Gondolj rá így; az adatkészlet a csatolt adattárakon belüli adatok szerkezetét jelöli, a csatolt szolgáltatás pedig az adatforrással való kapcsolatot. Például egy Azure Storage-kapcsolt szolgáltatás egy tárfiókot az adat-előállítóhoz kapcsol. Az Azure Blob-adatkészlet a blobtárolót és az adott Azure Storage-fiókon belüli mappát jelöli, amely tartalmazza a feldolgozandó bemeneti blobokat.

Íme egy példa forgatókönyv. Adatok másolása a Blob storage-ból egy SQL-adatbázisba, hozzon létre két csatolt szolgáltatások: Az Azure Storage és az Azure SQL Database. Ezután hozzon létre két adatkészletet: Az Azure Blob adatkészlet (amely az Azure Storage-kapcsolt szolgáltatásra hivatkozik) és az Azure SQL Table adatkészlet (amely az Azure SQL Database-hez csatolt szolgáltatásra hivatkozik). Az Azure Storage és az Azure SQL Database-hez kapcsolódó szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyeket a Data Factory futásidőben használ az Azure Storage-hoz és az Azure SQL Database-hez való csatlakozáshoz. Az Azure Blob-adatkészlet határozza meg a blob-tároló és blob mappát, amely tartalmazza a blob storage bemeneti blobok. Az Azure SQL Table adatkészlet határozza meg az SQL-tábla az SQL-adatbázisban, amelybe az adatokat kell másolni.

Az alábbi ábra a data factory-i folyamat, tevékenység, adatkészlet és csatolt szolgáltatás közötti kapcsolatokat mutatja be:

![A folyamat, a tevékenység, az adatkészlet, a kapcsolódó szolgáltatások közötti kapcsolat](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Csatolt szolgáltatás JSON

A Data Factory összekapcsolt szolgáltatása JSON formátumban a következőképpen van meghatározva:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Az alábbi táblázat a fenti JSON-tulajdonságokat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
név | A csatolt szolgáltatás neve. Lásd: [Azure Data Factory – elnevezési szabályok.](naming-rules.md) |  Igen |
type | A csatolt szolgáltatás típusa. Például: Azure Storage (adattár) vagy AzureBatch (compute). Lásd a typeProperties leírását. | Igen |
typeProperties | A típustulajdonságok az egyes adattorak vagy számítási feladatok esetében eltérőek. <br/><br/> A támogatott adattártípusokat és azok típustulajdonságait ebben a cikkben az [adatkészlettípus-táblázat](concepts-datasets-linked-services.md#dataset-type) ban olvashat. Keresse meg az adattár összekötő cikket, hogy megismerjék a típus tulajdonságai egy adattárban. <br/><br/> A támogatott számítási típusokat és típustulajdonságaikat a [Csatolt szolgáltatások számítása](compute-linked-services.md)című témakörben tésszet. | Igen |
connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem

## <a name="linked-service-example"></a>Példa csatolt szolgáltatásra

A következő csatolt szolgáltatás egy Azure Storage-kapcsolt szolgáltatás. Figyelje meg, hogy a típus az Azure Storage-ra van beállítva. Az Azure Storage-hoz csatolt szolgáltatás típustulajdonságai között szerepel egy kapcsolati karakterlánc. A Data Factory szolgáltatás ezt a kapcsolati karakterláncot használja az adattárhoz futásidőben való csatlakozáshoz.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Összekapcsolt szolgáltatásokat az alábbi eszközök vagy SDK-k egyikével hozhat létre: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Azure Resource Manager Template és Azure Portal

## <a name="data-store-linked-services"></a>Adattárhoz csatolt szolgáltatások

A Data Factory által támogatott adattárak listáját az [összekötő áttekintéséről](copy-activity-overview.md#supported-data-stores-and-formats) szóló cikkből találja. Kattintson egy adattárra a támogatott kapcsolattulajdonságainak megismeréséhez.

## <a name="compute-linked-services"></a>Társított szolgáltatások számítása

Referencia [számítási környezetek támogatott](compute-linked-services.md) részletekért a különböző számítási környezetek csatlakozhat az adat-előállító, valamint a különböző konfigurációk.

## <a name="next-steps"></a>További lépések

Az alábbi oktatóanyagban részletes útmutatást talál a folyamatok és adatkészletek ezen eszközök vagy SDK-k egyikének használatával történő létrehozásához.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Rövid útmutató: adatgyár létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)
- [Rövid útmutató: adatgyár létrehozása rest API használatával](quickstart-create-data-factory-rest-api.md)
- [Rövid útmutató: adatgyár létrehozása az Azure Portal használatával](quickstart-create-data-factory-portal.md)
