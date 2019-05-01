---
title: Társított szolgáltatásokat az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg a Data Factory társított szolgáltatásokat. Társított szolgáltatások adattárakat számítási vagy adat-előállítóhoz.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870132"
---
# <a name="linked-services-in-azure-data-factory"></a>Az Azure Data Factory társított szolgáltatásokat
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-datasets-linked-services.md)

Ez a cikk azt ismerteti, milyen társított szolgáltatásokat is, hogyan vannak definiálva JSON formátumban, és hogy ezek hogyan használhatók az Azure Data Factory-folyamatok.

Ha most ismerkedik a Data Factory, [az Azure Data Factory bemutatását](introduction.md) áttekintését.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** logikai csoportosítása **tevékenységek** , amelyek együttesen hajtanak végre egy feladatot. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Például használhat egy másolási tevékenység adatokat másol egy helyszíni SQL Server az Azure Blob storage. Ezt követően használható a Hive-tevékenység, amely egy Hive-szkriptet az Azure HDInsight-fürtön az adatfeldolgozás kimeneti adatok előállításához a Blob storage-ból. Végül egy második másolási tevékenység használatával lehet, hogy a kimeneti adatok másolása az Azure SQL Data Warehouse, amelyre üzletiintelligencia (BI-) jelentéskészítési megoldások épülnek. További információ a folyamatok és tevékenységek: [folyamatok és tevékenységek](concepts-pipelines-activities.md) az Azure Data Factoryban.

Most egy **adatkészlet** van egy azon adatok elnevezett nézete, amelyek egyszerűen mutat, vagy az adatok a használni kívánt hivatkozik a **tevékenységek** bemeneti és kimeneti.

Mielőtt egy adatkészletet hoz létre, létre kell hoznia egy **társított szolgáltatás** az adattárhoz összekapcsolása a data factoryhoz. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Ezzel a módszerrel; tekinthetjük Az adatkészlet jelöli a társított adattárakban lévő adatok szerkezetét, és a társított szolgáltatás határozza meg a kapcsolat az adatforráshoz. Például egy Azure Storage társított szolgáltatás egy storage-fiókot a data factoryhoz. Az Azure Blob-adatkészlet a blobtárolót és az adott Azure storage-fiókot, amely tartalmazza a bemeneti blobokat feldolgozású mappát jelöli.

Íme egy példa forgatókönyv. Adatok másolása Blob storage-ból egy SQL Database-adatbázishoz, akkor hozzon létre két társított szolgáltatást: Az Azure Storage és az Azure SQL-adatbázis. Ezután hozzon létre két adatkészletet: Az Azure Blob-adatkészlet (amely az Azure Storage társított szolgáltatásra vonatkozik) és az Azure SQL Table adatkészlet (amely az Azure SQL Database-beli társított szolgáltatásra vonatkozik). Az Azure Storage és Azure SQL Database-beli társított szolgáltatások tartalmaznak, amelyek a Data Factory használ futtatáskor az Azure Storage és Azure SQL Database-csatlakozás kapcsolati karakterláncok. Az Azure Blob-adatkészlet pedig meghatározza a blobtárolót és a Blob storage-ban a bemeneti blobokat tartalmazó blob mappát. Az Azure SQL Table adatkészlet adja meg az SQL-adatbázis, amelyhez az adatokat kell másolni az SQL-táblát.

Az alábbi ábrán látható a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat a Data Factoryban:

![Folyamat, a tevékenység, az adatkészlet, a társított szolgáltatások közötti kapcsolat](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>A társított szolgáltatás JSON
Egy társított szolgáltatás a Data Factory a következőképpen van meghatározva JSON formátumban:

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

A következő táblázat ismerteti a fenti JSON-tulajdonságok:

Tulajdonság | Leírás | Szükséges |
-------- | ----------- | -------- |
név | A társított szolgáltatás neve. Lásd: [Azure Data Factory – elnevezési szabályok](naming-rules.md). |  Igen |
type | A társított szolgáltatás típusa. Példa: (Adattár) AzureStorage vagy AzureBatch (számítás). Tekintse meg a typeProperties leírását. | Igen |
typeProperties | A tulajdonságait különböznek az összes adattárat vagy számítási. <br/><br/> A támogatott adatok tárolására, típusok és azok tulajdonságait, tekintse meg a [adatkészlettípus](concepts-datasets-linked-services.md#dataset-type) tábla ebben a cikkben. Keresse meg az adatokat tároló összekötő cikkből megtudhatja adott adattárba tulajdonságait. <br/><br/> A támogatott számításicsomópont-típusok és azok tulajdonságait: [társított szolgáltatások számítása](compute-linked-services.md). | Igen |
connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem

## <a name="linked-service-example"></a>A kapcsolódószolgáltatás-példa
A következő társított szolgáltatás az Azure Storage társított szolgáltatása. Figyelje meg, hogy a típusuk értéke AzureStorage. A tulajdonságait az Azure Storage társított szolgáltatás egy kapcsolati karakterláncot tartalmaznak. A Data Factory szolgáltatás ezt a kapcsolati karakterláncot az adattár futásidőben való kapcsolódáshoz használ.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
A következő eszközök és SDK-k használatával is létrehozhat a társított szolgáltatást: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API-val](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-sablon és az Azure Portalon

## <a name="data-store-linked-services"></a>Adatok tárolása a társított szolgáltatások
Kapcsolódás adattárak tekintheti meg a [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats). Referencia a lista az adott kapcsolat tulajdonságainak különféle áruházakban szükséges.

## <a name="compute-linked-services"></a>Társított szolgáltatások számítása
Hivatkozás [számítási környezetekben támogatott](compute-linked-services.md) adatait különböző számítási környezeteket csatlakozhat a data factory, valamint a különböző konfigurációkat.

## <a name="next-steps"></a>További lépések
Tekintse meg a következő oktatóanyaggal, a részletes folyamatokról és adatkészletekről létrehozásához a következő eszközök és SDK-k használatával.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Gyors útmutató: a PowerShell használatával egy adat-előállító létrehozása](quickstart-create-data-factory-powershell.md)
- [Gyors útmutató: hozzon létre egy adat-előállítót a REST API használatával](quickstart-create-data-factory-rest-api.md)
- [Gyors útmutató: Azure portal használatával egy adat-előállító létrehozása](quickstart-create-data-factory-portal.md)
