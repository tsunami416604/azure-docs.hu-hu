---
title: "Adatkészletek és a kapcsolódó szolgáltatások az Azure Data Factory |} Microsoft Docs"
description: "Tudnivalók az adatkészletek és összekapcsolt szolgáltatások adat-előállítóban. Társított szolgáltatások csatolása adat-előállító compute/adattárolókhoz. Adatkészletek bemeneti/kimeneti adatait tartalmazzák."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 98d58b97457cc64954094d7e8d8b4defca7e05ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Adatkészletek és az Azure Data Factory összekapcsolt szolgáltatások 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-create-datasets.md)
> * [2. verzió – Előzetes verzió](concepts-datasets-linked-services.md)

Ez a cikk ismerteti, milyen adatkészletek, hogyan vannak definiálva JSON formátumú, és hogy ezek hogyan használhatók az Azure Data Factory V2 folyamatok. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [a Data Factory V1 adatkészletek](v1/data-factory-create-datasets.md).

Ha most ismerkedik a Data Factory, lásd: [Bevezetés az Azure Data Factory](introduction.md) áttekintése. 

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **csővezeték** logikai csoportosítása **tevékenységek** , amelyek együtt a feladat elvégzésére. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használhatja például a adatok másolása az egy helyi SQL Server az Azure Blob Storage tárolóban. Ezután használhatja a egy Hive tevékenységet, amely a Hive parancsfájlok futtatására szolgál egy Azure HDInsight fürt adatfeldolgozásra történő blobtárolóból eredményezett kimeneti adatokat. Végezetül segítségével lehet egy második másolási tevékenység kimeneti adatok másolása az Azure SQL Data Warehouse, mely üzleti intelligenciával reporting megoldások beépített felett. Adatcsatornák és tevékenységgel kapcsolatos további információkért lásd: [folyamatok és a tevékenységek](concepts-pipelines-activities.md) az Azure Data Factory.

Most egy **dataset** egyszerűen mutat, vagy a használni kívánt adatforrásra hivatkozik adatok nevű nézete a **tevékenységek** bemenetekhez és kimenetekhez. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt a Blob Storage-mappát, amelyből a tevékenység beolvassa az adatokat.

Mielőtt létrehozna egy adatkészletet, létre kell hoznia egy **társított szolgáltatás** az adattároló csatolása az adat-előállítóban. A társított szolgáltatások nagyon hasonlóak a kapcsolati karakterláncokhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Azt gondolja, hogy így; a dataset jelenti. a csatolt adattárolókhoz belül az adatok szerkezete, és a társított szolgáltatás határozza meg a kapcsolat az adatforráshoz. Például egy Azure Storage kapcsolódó szolgáltatás hivatkozások egy tárfiókot az adat-előállítóban. Egy Azure Blob-adathalmazra jelenti. a blob-tároló és a mappában, hogy Azure storage-fiók, amely tartalmazza a bemeneti BLOB feldolgozásra.

Íme egy példa. Adatok másolása Blob-tároló SQL-adatbázis, a két társított szolgáltatások létrehozásához: Azure Storage és az Azure SQL Database. Ezután hozzon létre két adatkészletet: Azure Blob-adathalmazra (amely az Azure tárolás társított szolgáltatásának vonatkozik) és az Azure SQL-tábla a dataset (amely a kapcsolódó Azure SQL Database szolgáltatás vonatkozik). Az Azure Storage és az Azure SQL Database kapcsolódó szolgáltatások tartalmaznia az csatlakozni az Azure Storage és az Azure SQL Database, illetve futásidőben használja a Data Factory-kapcsolati karakterláncok. Az Azure Blob-adathalmazra blobtárolót és a bemeneti BLOB a Blob Storage tárolóban tartalmazó blob mappát adja meg. Az Azure SQL-tábla adatkészletet az SQL-tábla az SQL-adatbázis, amelyre az adatok másolása az határozza meg.

Az alábbi ábrán látható a folyamat, a tevékenység, a dataset és a társított szolgáltatás közötti kapcsolatok adat:

![Adatcsatorna, adatkészlet, tevékenység társított szolgáltatások közötti kapcsolat](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Társított szolgáltatás JSON
A Data Factory összekapcsolt szolgáltatás az alábbiak szerint definiáltuk JSON formátumban:

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

A következő táblázat ismerteti a fenti JSON-tulajdonságokat:

Tulajdonság | Leírás | Szükséges |
-------- | ----------- | -------- |
név | A társított szolgáltatás neve. Lásd: [Azure Data Factory - elnevezési szabályait](naming-rules.md). |  Igen |
type | A társított szolgáltatás típusa. Például: AzureStorage (adattár) vagy AzureBatch (számítást). Tekintse meg a typeProperties leírását. | Igen |
typeProperties | A típus tulajdonságokat minden adattároló különböző vagy számítási. <br/><br/> A támogatott adatok tárolására, típusok és a tulajdonságokat, tekintse meg a [adathalmaztípushoz](#dataset-type) tábla ebben a cikkben. Nyissa meg az adatok tároló összekötő cikkben tájékozódhat az adattárat jellemző tulajdonságokat. <br/><br/> A támogatott számítási típusok és azok típustulajdonságokat: [összekapcsolt szolgáltatások számítási](compute-linked-services.md). | Igen |
connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló egy magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem

## <a name="linked-service-example"></a>A kapcsolódószolgáltatás-példa
A következő társított szolgáltatás az Azure tárolás társított szolgáltatásának. Figyelje meg, hogy a típus AzureStorage van beállítva. Az Azure tárolás társított szolgáltatásának típus tulajdonságai közé tartozik egy kapcsolati karakterláncot. A Data Factory szolgáltatásnak a kapcsolati karakterlánc az adattár futási időben való kapcsolódáshoz használ. 

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

## <a name="dataset-json"></a>Adatkészlet JSON
A Data Factory dataset az alábbiak szerint definiáltuk JSON formátumban:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
A következő táblázat ismerteti a fenti JSON-tulajdonságokat:

Tulajdonság | Leírás | Szükséges |
-------- | ----------- | -------- |
név | A DataSet adatkészlet neve. Lásd: [Azure Data Factory - elnevezési szabályait](naming-rules.md). |  Igen |
type | A dataset típusa. Adja meg a Data Factory által támogatott típusú (például: AzureBlob, AzureSqlTable). <br/><br/>További információkért lásd: [Dataset típusok](#dataset-types). | Igen |
struktúra | Az adatkészlet sémája. További információkért lásd: [adatkészlet-szerkezetekben](#dataset-structure). | Nem |
typeProperties | A típus tulajdonságokat eltérőek az egyes (például: Azure Blob, Azure SQL-tábla). A támogatott típusok és tulajdonságaikat a részletekért lásd: [adathalmaztípushoz](#dataset-type). | Igen |

## <a name="dataset-example"></a>A DataSet – példa
A következő példában a dataset jelenti. az SQL-adatbázisban MyTable nevű tábla.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Vegye figyelembe a következő szempontokat:

- AzureSqlTable típusúra van beállítva.
- tableName típusa (AzureSqlTable típus adott) tulajdonsága táblanév.
- linkedServiceName AzureSqlDatabase, amelyet a következő JSON-részlet típusú társított szolgáltatás hivatkozik.

## <a name="dataset-type"></a>A DataSet típusa
Nincsenek adathalmazok, attól függően, hogy a tárolót használja számos különböző típusú. Lásd az alábbi táblázatban a Data Factory által támogatott adattárolókhoz listáját. Kattintson a tárolóban annak megismerése, hogyan összekapcsolt szolgáltatás és az adott tároló adatkészlet létrehozásához.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

A példában az előző szakaszban, a DataSet adatbázisoszlophoz **AzureSqlTable**. Ehhez hasonlóan egy Azure-Blob adatkészlet a DataSet adatbázisoszlophoz **AzureBlob**, ahogy az az alábbi JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>Adatkészlet-szerkezetekben
A **struktúra** szakasz nem kötelező megadni. Az adatkészlet sémája által tartalmazó nevét és az oszlopok adattípusát gyűjteményét határozza meg. A struktúra szakasz segítségével adja meg, amellyel típusok átalakítani, és képezze le a cél a forrás oszlop típusa információkat.

Minden egyes oszlopának a struktúra tartalmaz a következő tulajdonságokkal:

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
név | Az oszlop neve. | Igen
type | Az oszlop adattípusát. Adat-előállító megengedett értékek, a következő ideiglenes adattípusokat támogatja: **Int16, Int32, Int64, egyetlen, Double, Decimal, Byte [], logikai érték, karakterlánc, Guid, Datetime, Datetimeoffset és Timespan** | Nem
Kulturális környezet | . A NET-alapú kulturális környezet lehet használni, ha a típus a .NET-típus: `Datetime` vagy `Datetimeoffset`. Az alapértelmezett érték `en-us`. | Nem
Formátumban | Formázó karakterlánc kell használni, ha a típus a .NET-típus: `Datetime` vagy `Datetimeoffset`. Tekintse meg [egyéni dátum és idő formátumú karakterláncok](https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings) a dátum és idő formázása. | Nem

### <a name="example"></a>Példa
A következő példában tegyük fel, hogy a forrás Blobadatok CSV formátumú három oszlopot tartalmaz: a felhasználói azonosítóját, nevét és lastlogindate. Vannak Int64, típusú karakterlánc, és a DateTime típusú érték a hét napjára rövidített francia nevekkel egyéni dátum és idő formátumban.

Adja meg a Blob adatkészlet-szerkezetekben típusdefiníciók az oszlopok együtt az alábbiak szerint:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Útmutatás

A következő irányelveket segítenek megérteni, mikor struktúra információval, és milyen ahhoz, hogy szerepeljen a **struktúra** szakasz. További tudnivalókért, hogyan adat-előállító leképezi a forrás-adatok gyűjtése és mikor kell a struktúra információk megadása a [séma és a típusleképezéshez](copy-activity-schema-and-type-mapping.md).

- **Az erős séma adatforrásokat**, adja meg a struktúra szakaszban csak akkor, ha a kívánt oszlopok gyűjtése forrásoszlopok leképezni, és a nevek nem azonosak. Strukturált adatforrás az ilyen adatok séma- és tároló típusa mellett az adatokat mozgatná. Strukturált adatforrások például SQL Server, az Oracle és az Azure SQL Database.<br/><br/>Típusra vonatkozó adat már áll rendelkezésre a strukturált adatforrásokhoz, akkor nem tartalmazhat típusinformációt elvégzését indokló, hogy a struktúra szakasz.
- **Nem/weak séma adatforrások pl. a fájlt a blob storage**, struktúra tartalmazhat, ha a dataset másolási tevékenységhez bemeneti, és a forrás adatkészlet adattípusok át kell alakítani a fogadó natív típust. És struktúra tartalmazhat, ha a megjeleníteni kívánt oszlopok gyűjtése forrásoszlopok...

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ezen eszközök vagy az SDK-k használatával is létrehozhat a adatkészletek: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-sablon, és az Azure-portálon

## <a name="v1-vs-v2-datasets"></a>V1 vs. V2 adatkészletek

Az alábbiakban néhány különbség az adat-előállító v1 és v2 adatkészletek között: 

- A v2 nem támogatja a külső tulajdonságot. A rendszer felülírja a [eseményindító](concepts-pipeline-execution-triggers.md).
- A házirend és a rendelkezésre állási tulajdonságok nem támogatottak a 2. Egy folyamat kezdési időpontja függ [eseményindítók](concepts-pipeline-execution-triggers.md).
- Hatókört használó adatkészletek (adatcsatorna meghatározott adatkészletek) V2 nem támogatottak. 

## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi részletes útmutatás adatcsatornákat és adathalmazokat egyikével a következő eszközök, illetve az SDK-k létrehozásához. 

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Gyors üzembe helyezés: hozzon létre egy adat-előállító PowerShell használatával](quickstart-create-data-factory-powershell.md)
- [Gyors üzembe helyezés: hozzon létre egy adat-előállító REST API használatával](quickstart-create-data-factory-rest-api.md)
- Gyors üzembe helyezés: hozzon létre egy adat-előállító Azure-portál használatával
