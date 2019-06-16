---
title: Adatkészleteket az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg az adatkészletek a Data Factoryban. Az adatkészletek bemeneti és kimeneti adatokat jelölik.
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
ms.openlocfilehash: 6b74f217d296b5de8886f608b1bc92e908b5d8b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64866480"
---
# <a name="datasets-in-azure-data-factory"></a>Adatkészleteket az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki a Data Factory szolgáltatás használ:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-datasets-linked-services.md)

Ez a cikk bemutatja, milyen adatkészletek, hogyan vannak definiálva JSON formátumban, és hogy ezek hogyan használhatók az Azure Data Factory-folyamatok.

Ha most ismerkedik a Data Factory, [az Azure Data Factory bemutatását](introduction.md) áttekintését.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** logikai csoportosítása **tevékenységek** , amelyek együttesen hajtanak végre egy feladatot. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Most egy **adatkészlet** van egy azon adatok elnevezett nézete, amelyek egyszerűen mutat, vagy az adatok a használni kívánt hivatkozik a **tevékenységek** bemeneti és kimeneti. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt a Blob Storage-mappát, amelyből a tevékenység beolvassa az adatokat.

Mielőtt egy adatkészletet hoz létre, létre kell hoznia egy [ **társított szolgáltatás** ](concepts-linked-services.md) az adattárhoz összekapcsolása a data factoryhoz. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Ezzel a módszerrel; tekinthetjük Az adatkészlet jelöli a társított adattárakban lévő adatok szerkezetét, és a társított szolgáltatás határozza meg a kapcsolat az adatforráshoz. Például egy Azure Storage társított szolgáltatás egy storage-fiókot a data factoryhoz. Az Azure Blob-adatkészlet a blobtárolót és az adott Azure storage-fiókot, amely tartalmazza a bemeneti blobokat feldolgozású mappát jelöli.

Íme egy példa forgatókönyv. Adatok másolása Blob storage-ból egy SQL Database-adatbázishoz, akkor hozzon létre két társított szolgáltatást: Az Azure Storage és az Azure SQL-adatbázis. Ezután hozzon létre két adatkészletet: Az Azure Blob-adatkészlet (amely az Azure Storage társított szolgáltatásra vonatkozik) és az Azure SQL Table adatkészlet (amely az Azure SQL Database-beli társított szolgáltatásra vonatkozik). Az Azure Storage és Azure SQL Database-beli társított szolgáltatások tartalmaznak, amelyek a Data Factory használ futtatáskor az Azure Storage és Azure SQL Database-csatlakozás kapcsolati karakterláncok. Az Azure Blob-adatkészlet pedig meghatározza a blobtárolót és a Blob storage-ban a bemeneti blobokat tartalmazó blob mappát. Az Azure SQL Table adatkészlet adja meg az SQL-adatbázis, amelyhez az adatokat kell másolni az SQL-táblát.

Az alábbi ábrán látható a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat a Data Factoryban:

![Folyamat, a tevékenység, az adatkészlet, a társított szolgáltatások közötti kapcsolat](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Adatkészlet JSON
A Data Factory-adatkészlet a következő JSON formátumban van definiálva:

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
A következő táblázat ismerteti a fenti JSON-tulajdonságok:

Tulajdonság | Leírás | Szükséges |
-------- | ----------- | -------- |
name | Az adatkészlet nevét. Lásd: [Azure Data Factory – elnevezési szabályok](naming-rules.md). |  Igen |
type | Az adatkészlet típusa. Adja meg a Data Factory által támogatott típusú (például: AzureBlob, AzureSqlTable). <br/><br/>További információkért lásd: [típus](#dataset-type). | Igen |
structure | Az adatkészlet sémája. További információkért lásd: [adathalmaz-séma](#dataset-structure-or-schema). | Nem |
typeProperties | A típus tulajdonságokat különböznek az egyes (például: Az Azure Blob, az Azure SQL-tábla). További információ a támogatott típusok és a hozzájuk tartozó tulajdonságok: [adatkészlettípus](#dataset-type). | Igen |

### <a name="data-flow-compatible-dataset"></a>Data flow kompatibilis adatkészlet

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Lásd: [típus támogatott](#dataset-type) vannak adatkészlettípusok listáját [adatfolyam](concepts-data-flow-overview.md) kompatibilis. Adatkészleteket, amelyek kompatibilisek-e az adatfolyam minden részletre kiterjedő adatkészlet-definíciókban átalakítások szükséges. Így a JSON-definíció némileg eltérő. Helyett egy _struktúra_ tulajdonság, az adatfolyam kompatibilis adatkészletek rendelkezik egy _séma_ tulajdonság.

Adatkészletek adatfolyam, a forrás- és fogadó átalakítások használhatók. Az adatkészletek az alapszintű adatsémák határozza meg. Ha az adatok semmilyen sémát, séma eltéréseket a forrás- és fogadóadattárként is használhat. A séma az adatkészletben lévő fizikai adattípus és alakzat jelöli.

A sémát, az adatkészlet definiálásával kap a kapcsolódó adattípusok, a adatformátumok a célnyelven, a fájl helye és a kapcsolati adatokat a társított társított szolgáltatás. Az adatkészletek metaadataiból jelenik meg a forrás-átalakítást, mint a forrás *leképezése*. Az adatforrás-átalakítás a leképezésről az adatfolyam adatok meghatározott nevét és típusát jelöli.

Amikor importálja a séma adatfolyam adatkészlet, válassza ki a **séma importálása** gombra, és válassza ki a forrás- vagy a helyi fájlokból való importálásához. A legtöbb esetben a séma közvetlenül a forrásból fogja importálni. Azonban ha már rendelkezik egy helyi sémafájl (a Parquet-fájlok vagy CSV-fejlécek), a Data Factory a sémát, hogy a fájl a kiinduló irányíthatók.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
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

A következő táblázat ismerteti a fenti JSON-tulajdonságok:

Tulajdonság | Leírás | Szükséges |
-------- | ----------- | -------- |
name | Az adatkészlet nevét. Lásd: [Azure Data Factory – elnevezési szabályok](naming-rules.md). |  Igen |
type | Az adatkészlet típusa. Adja meg a Data Factory által támogatott típusú (például: AzureBlob, AzureSqlTable). <br/><br/>További információkért lásd: [típus](#dataset-type). | Igen |
schema | Az adatkészlet sémája. További információkért lásd: [kompatibilis adatkészletek adatfolyam](#dataset-type). | Nem |
typeProperties | A típus tulajdonságokat különböznek az egyes (például: Az Azure Blob, az Azure SQL-tábla). További információ a támogatott típusok és a hozzájuk tartozó tulajdonságok: [adatkészlettípus](#dataset-type). | Igen |


## <a name="dataset-example"></a>Példa adatkészlet
A következő példában az adatkészlet egy SQL database-ben MyTable nevű tábla jelöli.

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

- típus AzureSqlTable van beállítva.
- Táblanév típusa (jellemző AzureSqlTable típus) tulajdonsága MyTable.
- linkedServiceName AzureSqlDatabase, amelyet a következő JSON-kódrészletben típusú társított szolgáltatás hivatkozik.

## <a name="dataset-type"></a>Forrásadatkészlet típusa
Nincsenek adatkészletek esetében használhatja az adattár függően számos különböző típusú. Tekintse meg az alábbi táblázat a Data Factory által támogatott adattárak listáját. Kattintson egy adattár kiválasztásával megtudhatja, hogyan hozhat létre a társított szolgáltatás és az adattár egy adatkészletet.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-dataflow.md)]

A példában az előző szakaszban az adatkészlet típusa értéke **AzureSqlTable**. Ehhez hasonlóan az Azure Blob-adatkészlet az adatkészlet típusa értéke **AzureBlob**, ahogyan az az alábbi JSON-ra:

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

## <a name="dataset-structure-or-schema"></a>Adatkészlet-szerkezetekben vagy séma
A **struktúra** szakasz vagy **séma** (adatfolyam-kompatibilis) szakasz adatkészletek nem kötelező. A séma az adatkészlet nevét és az oszlopok adattípusát gyűjteményét tartalmazó szerint határozza meg. A struktúra szakasz segítségével konvertálása típusok és oszlopait a forrásból a cél típusa információkat tartalmaznak.

Minden egyes oszlopának struktúrája a következő tulajdonságokat tartalmazza:

Tulajdonság | Leírás | Szükséges
-------- | ----------- | --------
név | Az oszlop neve. | Igen
type | Az oszlop adattípusát. A Data Factory a következő, köztes adattípusokat támogatja mint a megengedett értékek: **Int16, Int32, Int64, egyetlen, Double, tizedes tört, Byte [], logikai érték, karakterlánc, Guid, dátum és idő, Datetimeoffset és időtartam** | Nem
culture | . NET-alapú kulturális környezetet használni, amikor a típus a .NET-típus: `Datetime` vagy `Datetimeoffset`. A mező alapértelmezett értéke: `en-us`. | Nem
format | Formázó karakterlánc típus egy .NET-típus esetén használandó: `Datetime` vagy `Datetimeoffset`. Tekintse meg [egyéni dátum- és időformátum karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) a dátum és idő formázása. | Nem

### <a name="example"></a>Példa
Tegyük fel a következő példában a forrás Blob adatok CSV formátumban, és három oszlopot tartalmaz: a felhasználói azonosítóját, nevét és lastlogindate. Azok Int64, típusú karakterlánc, és a dátum és idő napja a héten francia rövidített használatával egyéni dátum és idő formátumban.

Adja meg a Blob adatkészlet-szerkezetekben módon az oszlopok típusdefiníciók együtt:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Útmutatás

Az alábbi irányelvek segítenek megérteni, mikor struktúra információval, és mit kell foglalni a **struktúra** szakaszban. Ismerje meg, hogyan leképezi az adat-előállító a forrásadatok a fogadó és struktúra adatait adja meg, hogy mikor [séma- és Típusleképezés](copy-activity-schema-and-type-mapping.md).

- **Az erős séma adatforrásokat**, adja meg a struktúrát a szakasz csak akkor, ha azt szeretné, hogy a forrás oszlopait fogadó oszlopokat, és a nevek nem azonosak. Strukturált adatok forrása az ilyen típusú adatok séma- és a típus adatait együtt az adat tárolja. Strukturált adatforrások közé tartoznak az SQL Server, Oracle- és Azure SQL Database.<br/><br/>Ha típussal kapcsolatos információk már elérhető a strukturált adatok források, műveket nem szabad típussal kapcsolatos információk Ha belefoglalja a struktúra szakaszban.
- **Nem/weak séma adatforrásokhoz például blob storage-ban szövegfájl**, ha az adatkészlet egy másolási tevékenység bemeneti, és adattípusok forrásadatkészlet, át kell alakítani a fogadóhoz natív típusai közé tartozik a struktúra. És tartalmazzák a struktúra, ha le szeretné képezni a fogadó-oszlopok forrásoszlopok...

## <a name="create-datasets"></a>Adatkészletek létrehozása
A következő eszközök és SDK-k használatával adatkészleteket is létrehozhat: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API-val](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-sablon és az Azure Portalon

## <a name="current-version-vs-version-1-datasets"></a>1\. verzió az adatkészletek és a jelenlegi verzió

Az alábbiakban a Data Factory és a Data Factory 1. verziójának adatkészletek közötti különbségeket:

- Az external tulajdonság a jelenlegi verzióban nem támogatott. A rendszer felülírja a [eseményindító](concepts-pipeline-execution-triggers.md).
- A házirend- és rendelkezésre állási tulajdonságok nem támogatottak a jelenlegi verzióban. A folyamat kezdési ideje függ [eseményindítók](concepts-pipeline-execution-triggers.md).
- A jelenlegi verzióban nem támogatottak a hatókörrel rendelkező adatkészletek (a folyamat megadott adatkészletek).

## <a name="next-steps"></a>További lépések
Tekintse meg a következő oktatóanyaggal, a részletes folyamatokról és adatkészletekről létrehozásához a következő eszközök és SDK-k használatával.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Gyors útmutató: a PowerShell használatával egy adat-előállító létrehozása](quickstart-create-data-factory-powershell.md)
- [Gyors útmutató: hozzon létre egy adat-előállítót a REST API használatával](quickstart-create-data-factory-rest-api.md)
- [Gyors útmutató: Azure portal használatával egy adat-előállító létrehozása](quickstart-create-data-factory-portal.md)
