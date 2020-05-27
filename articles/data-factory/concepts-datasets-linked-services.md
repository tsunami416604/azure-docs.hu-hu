---
title: Adathalmazok
description: Tudnivalók a Data Factory adatkészletekről. Az adatkészletek bemeneti/kimeneti adatokat jelölnek.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 122725bff616a49d27981b88f465e04418db9526
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826111"
---
# <a name="datasets-in-azure-data-factory"></a>Adathalmazok az Azure Data Factoryben
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Ez a cikk ismerteti, hogy mely adatkészletek, hogyan vannak meghatározva JSON formátumban, és hogyan használják őket Azure Data Factory folyamatokban.

Ha Data Factory új, tekintse meg az áttekintést a [Azure Data Factory bemutatása](introduction.md) című témakört.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen végeznek feladatokat. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Az **adatkészlet** mostantól olyan elnevezett nézet, amely egyszerűen rámutat vagy hivatkozik a **tevékenységekben** használni kívánt adatokra bemenetként és kimenetként. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt a Blob Storage-mappát, amelyből a tevékenység beolvassa az adatokat.

Adatkészlet létrehozása előtt létre kell hoznia egy [**társított szolgáltatást**](concepts-linked-services.md) , amely összekapcsolja az adattárat az adat-előállítóval. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Gondoljon erre; az adatkészlet a társított adattárakon belüli adatszerkezetet jelöli, és a társított szolgáltatás határozza meg az adatforráshoz való kapcsolódást. Egy Azure Storage-beli társított szolgáltatás például egy Storage-fiókhoz csatolja az adatelőállítót. Az Azure Blob-adatkészlet az Azure Storage-fiókban található BLOB-tárolót és mappát jelöli, amely a feldolgozandó bemeneti blobokat tartalmazza.

Példa erre a forgatókönyvre. Az adatok blob Storage-ból SQL Databaseba való másolásához létre kell hoznia két társított szolgáltatást: Azure Storage és Azure SQL Database. Ezután hozzon létre két adatkészletet: az Azure Blob-adatkészletet (amely az Azure Storage társított szolgáltatásra vonatkozik) és az Azure SQL Table-adatkészletet (amely a Azure SQL Database társított szolgáltatásra hivatkozik). Az Azure Storage és a Azure SQL Database társított szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyeket a futtatókörnyezet az Azure Storage-hoz és a Azure SQL Databasehoz való kapcsolódáshoz Data Factory használ. Az Azure Blob-adatkészlet meghatározza a blob-tárolóban található bemeneti blobokat tartalmazó BLOB-tárolót és blob mappát. Az Azure SQL Table adatkészlet meghatározza a SQL Database azon SQL-tábláját, amelybe az adatokat másolni kívánja.

A következő ábra a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat mutatja Data Factoryban:

![Kapcsolat a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatások között](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Adatkészlet JSON
Data Factoryban található adatkészlet a következő JSON-formátumban van definiálva:

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
A fenti JSON-tulajdonságokat a következő táblázat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
name | Az adatkészlet neve. Lásd: [Azure Data Factory elnevezési szabályok](naming-rules.md). |  Igen |
típus | Az adatkészlet típusa. A Data Factory által támogatott típusok egyikét kell megadnia (például: AzureBlob, tulajdonsága azuresqltable). <br/><br/>Részletekért lásd: [adatkészletek típusai](#dataset-type). | Igen |
szerkezet | Az adatkészlet sémája. Részletekért lásd: [adatkészlet sémája](#dataset-structure-or-schema). | Nem |
typeProperties | A típus tulajdonságai eltérőek az egyes típusoknál (például: Azure Blob, Azure SQL Table). A támogatott típusokkal és azok tulajdonságaival kapcsolatos részletekért lásd: [adatkészlet típusa](#dataset-type). | Igen |

### <a name="data-flow-compatible-dataset"></a>Adatfolyam-kompatibilis adatkészlet



Az [adatáramlással](concepts-data-flow-overview.md) kompatibilis adatkészletek listáját a [támogatott adatkészletek típusainál](#dataset-type) tekintheti meg. Az adatáramlással kompatibilis adatkészletek esetében részletes adatkészlet-definíció szükséges az átalakításokhoz. Így a JSON-definíció némileg eltér. A _Structure_ tulajdonság helyett az adatáramlással kompatibilis adatkészletek _séma_ tulajdonsággal rendelkeznek.

Az adatforgalomban az adatkészletek a forrás-és fogadó átalakításokban használatosak. Az adatkészletek határozzák meg az alapszintű adatsémákat. Ha az adatai nem rendelkeznek sémával, a forrás és a fogadó esetében használhatja a séma eltolódását. Az adatkészlet sémája a fizikai adattípust és-alakzatot jelöli.

A séma az adatkészletből való definiálásával a kapcsolódó adattípusokat, adatformátumokat, a fájlok helyét és a kapcsolat adatait a társított társított szolgáltatásból kapja meg. Az adatkészletek metaadatai a forrás-átalakulásban jelennek meg a forrás- *kivetítés*során. A forrás-átalakításban a kivetítés a definiált nevekkel és típusokkal rendelkező adatfolyam-adatforgalomra vonatkozik.

Egy adatfolyam-adatkészlet sémájának importálásakor válassza a **séma importálása** gombot, és válassza az Importálás a forrásból vagy egy helyi fájlból lehetőséget. A legtöbb esetben közvetlenül a forrásból importálja a sémát. Ha azonban már van helyi sémafájl (egy parketta-fájl vagy egy, a fejlécekkel rendelkező CSV), akkor a Data Factory a fájlra alapozva kiválaszthatja a sémát.


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

A fenti JSON-tulajdonságokat a következő táblázat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
name | Az adatkészlet neve. Lásd: [Azure Data Factory elnevezési szabályok](naming-rules.md). |  Igen |
típus | Az adatkészlet típusa. A Data Factory által támogatott típusok egyikét kell megadnia (például: AzureBlob, tulajdonsága azuresqltable). <br/><br/>Részletekért lásd: [adatkészletek típusai](#dataset-type). | Igen |
séma | Az adatkészlet sémája. Részletekért lásd: [az adatfolyam-kompatibilis adatkészletek](#dataset-type). | Nem |
typeProperties | A típus tulajdonságai eltérőek az egyes típusoknál (például: Azure Blob, Azure SQL Table). A támogatott típusokkal és azok tulajdonságaival kapcsolatos részletekért lásd: [adatkészlet típusa](#dataset-type). | Igen |


## <a name="dataset-example"></a>Adatkészlet – példa
A következő példában az adatkészlet a Sajáttábla nevű táblázatot jelöli egy SQL Database.

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

- a típus értéke tulajdonsága azuresqltable.
- a táblanév Type tulajdonsága (amely a tulajdonsága azuresqltable típusra vonatkozik) a Sajáttábla értékre van beállítva.
- a linkedServiceName egy AzureSqlDatabase típusú társított szolgáltatásra hivatkozik, amely a következő JSON-kódrészletben van meghatározva.

## <a name="dataset-type"></a>Adatkészlet típusa
A használt adattártól függően számos különböző típusú adatkészlet létezik. A Data Factory által támogatott adatok listáját az [összekötő áttekintése című](connector-overview.md) cikkben találja. Kattintson egy adattárra, és Ismerje meg, hogyan hozhat létre egy társított szolgáltatást és egy adatkészletet az adott adattárhoz.

Az előző szakaszban szereplő példában az adatkészlet típusa **tulajdonsága azuresqltable**értékre van állítva. Hasonlóképpen, az Azure Blob-adatkészletek esetében az adatkészlet típusa **AzureBlob**értékre van állítva, ahogy az a következő JSON-ban látható:

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

## <a name="dataset-structure-or-schema"></a>Adatkészlet szerkezete vagy sémája
A **struktúra** szakasz vagy **séma** (adatfolyam-kompatibilis) szakasz adatkészletei nem kötelezőek. Meghatározza az adatkészlet sémáját úgy, hogy az oszlopok neveinek és adattípusának gyűjteményét tartalmazza. A struktúra szakasz használatával megadhatja a típusokat és a leképezési oszlopokat a forrásról a célhelyre.

A struktúra minden oszlopa a következő tulajdonságokat tartalmazza:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
name | Az oszlop neve. | Igen
típus | Az oszlop adattípusa. A Data Factory a következő közbenső adattípusokat támogatja megengedett értékként: **Int16, Int32, Int64, Single, Double, decimális, byte [], Boolean, string, GUID, datetime, DateTimeOffset és TimeSpan** | Nem
kulturális környezet | . A .NET-típus használata esetén használandó, NET-alapú kulturális környezet: `Datetime` vagy `Datetimeoffset` . A mező alapértelmezett értéke: `en-us`. | Nem
formátumban | A típus .NET-típusú típusaként használandó formázó karakterlánc: `Datetime` vagy `Datetimeoffset` . A DateTime formátumának formázásához tekintse meg az [Egyéni dátum-és időformátumot ismertető karakterláncot](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | Nem

### <a name="example"></a>Példa
A következő példában tegyük fel, hogy a forrás blob-fájl CSV formátumú, és három oszlopot tartalmaz: felhasználóazonosító, név és lastlogindate. A Int64, string és DateTime típusú egyéni datetime formátummal rendelkeznek, a hét napjainak rövidített francia neveivel.

Adja meg a blob-adatkészlet struktúráját a következőképpen, valamint az oszlopok típus-definícióit:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Útmutató

A következő irányelvek segítenek megismerni, hogy mikor kell belefoglalni a szerkezet adatait, és mit kell belefoglalni a **struktúra** szakaszba. További információ arról, hogy a adat-előállító hogyan képezi le a forrás adatait, és mikor kell megadnia a szerkezeti adatokat a [séma és típus leképezése](copy-activity-schema-and-type-mapping.md)alapján.

- **Erős séma-adatforrások**esetén csak akkor válassza a struktúra szakaszt, ha a forrás oszlopokat a fogadó oszlopokra szeretné leképezni, és a nevük nem egyeznek. Az ilyen strukturált adatforrás az adatsémát és a beírási adatokat az adatok mellett tárolja. Strukturált adatforrások például a következők: SQL Server, Oracle és Azure SQL Database.<br/><br/>A strukturált adatforrásokhoz már rendelkezésre áll a típus adatai, ezért a szerkezet szakasza nem tartalmazhat beírási adatokat.
- **A nem/gyenge séma típusú adatforrások esetében például a blob Storage-ban lévő szövegfájlban**szerepel a struktúra, ha az adatkészlet egy másolási tevékenység bemenete, és a forrás adatkészletének adattípusait át kell alakítani a fogadó natív típusaiba. És vegyen fel struktúrát, ha a forrás oszlopokat le szeretné képezni a fogadó oszlopokra

## <a name="create-datasets"></a>Adatkészletek létrehozása
Adatkészleteket az alábbi eszközök vagy SDK-k egyikével hozhat létre: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager sablon és Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Az aktuális verzió és az 1. verzió adatkészletei

Íme néhány különbség a Data Factory és a Data Factory 1. verziójú adatkészletek között:

- A külső tulajdonság nem támogatott a jelenlegi verzióban. Egy [trigger](concepts-pipeline-execution-triggers.md)váltja fel.
- A házirend és a rendelkezésre állási tulajdonságok nem támogatottak az aktuális verzióban. A folyamat kezdő időpontja az [eseményindítótól](concepts-pipeline-execution-triggers.md)függ.
- A hatókörrel rendelkező adatkészletek (a folyamatokban megadott adatkészletek) nem támogatottak az aktuális verzióban.

## <a name="next-steps"></a>További lépések
A következő oktatóanyag részletes útmutatást nyújt a folyamatok és adatkészletek létrehozásához ezen eszközök vagy SDK-k egyikének használatával.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Gyors útmutató: adatelőállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)
- [Gyors útmutató: adatelőállító létrehozása REST API használatával](quickstart-create-data-factory-rest-api.md)
- [Gyors útmutató: adatelőállító létrehozása Azure Portal használatával](quickstart-create-data-factory-portal.md)
