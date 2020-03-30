---
title: Adathalmazok
description: További információ a Data Factory adatkészleteiről. Az adatkészletek bemeneti/kimeneti adatokat jelölnek.
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
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246265"
---
# <a name="datasets-in-azure-data-factory"></a>Adathalmazok az Azure Data Factoryben
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-datasets-linked-services.md)

Ez a cikk ismerteti, hogy milyen adatkészletek, hogyan vannak definiálva JSON formátumban, és hogyan használják őket az Azure Data Factory folyamatokban.

Ha most látja el a Data Factory, olvassa [el az Azure Data Factory bevezetés](introduction.md) című témakört az áttekintést.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen hajtanak végre egy feladatot. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Most egy **adatkészlet** az adatok névvel ellátott nézete, amely egyszerűen rámutat vagy hivatkozik a **tevékenységekben** bemenetként és kimenetként használni kívánt adatokra. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt a Blob Storage-mappát, amelyből a tevékenység beolvassa az adatokat.

Az adatkészlet létrehozása előtt létre kell hoznia egy [**csatolt szolgáltatást,**](concepts-linked-services.md) amely az adattáradat-tárolót az adat-előállítóhoz kapcsolja. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Gondolj rá így; az adatkészlet a csatolt adattárakon belüli adatok szerkezetét jelöli, a csatolt szolgáltatás pedig az adatforrással való kapcsolatot. Például egy Azure Storage-kapcsolt szolgáltatás egy tárfiókot az adat-előállítóhoz kapcsol. Az Azure Blob-adatkészlet a blobtárolót és az adott Azure-tárfiókon belüli mappát jelöli, amely a feldolgozandó bemeneti blobokat tartalmazza.

Íme egy példa forgatókönyv. Adatok másolása a Blob storage-ból egy SQL-adatbázisba, hozzon létre két csatolt szolgáltatások: Az Azure Storage és az Azure SQL Database. Ezután hozzon létre két adatkészletet: Az Azure Blob adatkészlet (amely az Azure Storage-kapcsolt szolgáltatásra hivatkozik) és az Azure SQL Table adatkészlet (amely az Azure SQL Database-hez csatolt szolgáltatásra hivatkozik). Az Azure Storage és az Azure SQL Database-hez kapcsolódó szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyeket a Data Factory futásidőben használ az Azure Storage-hoz és az Azure SQL Database-hez való csatlakozáshoz. Az Azure Blob-adatkészlet határozza meg a blob-tároló és blob mappát, amely tartalmazza a blob storage bemeneti blobok. Az Azure SQL Table adatkészlet határozza meg az SQL-tábla az SQL-adatbázisban, amelybe az adatokat kell másolni.

Az alábbi ábra a data factory-i folyamat, tevékenység, adatkészlet és csatolt szolgáltatás közötti kapcsolatokat mutatja be:

![A folyamat, a tevékenység, az adatkészlet, a kapcsolódó szolgáltatások közötti kapcsolat](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON adatkészlet
A Data Factory adatkészlete a következő JSON formátumban van definiálva:

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
Az alábbi táblázat a fenti JSON-tulajdonságokat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
név | Az adatkészlet neve. Lásd: [Azure Data Factory – elnevezési szabályok.](naming-rules.md) |  Igen |
type | Az adatkészlet típusa. Adja meg a Data Factory által támogatott típusok egyikét (például: AzureBlob, AzureSqlTable). <br/><br/>További információt az [Adatkészlettípusok (Dataset types) (Adatkészlettípusok) témakörben talál.](#dataset-type) | Igen |
Szerkezet | Az adatkészlet sémája. További információt az [Adatkészletséma ( Dataset séma ) (Adatkészletséma) témakörben talál.](#dataset-structure-or-schema) | Nem |
typeProperties | A típus tulajdonságok különbözőek az egyes típusok (például: Azure Blob, Azure SQL-tábla). A támogatott típusokról és azok tulajdonságairól az [Adatkészlet típusa című](#dataset-type)témakörben talál részleteket. | Igen |

### <a name="data-flow-compatible-dataset"></a>Adatfolyam-kompatibilis adatkészlet



Az [adatfolyammal](concepts-data-flow-overview.md) kompatibilis adatkészlettípusok listájához tekintse meg a [támogatott adatkészlettípusokat.](#dataset-type) Az adatfolyammal kompatibilis adatkészletek átalakításokhoz részletes adatkészlet-definíciókat igényelnek. Így a JSON-meghatározás kissé eltérő. A _struktúratulajdonság_ helyett az adatfolyammal kompatibilis adatkészletek _sématulajdonsággal_ rendelkeznek.

Az adatfolyamban az adatkészletek forrás- és fogadóátalakításokban használatosak. Az adatkészletek határozzák meg az alapszintű adatsémákat. Ha az adatok nem rendelkeznek sémával, a forráshoz és a fogadóhoz sémaeltolódást használhat. Az adatkészlet sémája a fizikai adattípust és -alakzatot jelöli.

Ha az adatkészletből definiálja a sémát, a kapcsolódó adattípusokat, adatformátumokat, fájlhelyet és kapcsolati információkat a kapcsolódó Csatolt szolgáltatástól kapja meg. Az adatkészletek metaadatai a forrásátalakításban *forrásvetítésként*jelennek meg. A forrástranszformáció vetülete az adatfolyam-adatokat jelöli meghatározott nevekkel és típusokkal.

Az adatfolyam-adatkészlet sémájának importálásakor jelölje ki a **Séma importálása** gombot, és válassza az importálást a forrásból vagy egy helyi fájlból. A legtöbb esetben a sémát közvetlenül a forrásból importálja. De ha már rendelkezik helyi sémafájllal (parquet fájl vagy fejléccel rendelkező CSV), akkor a Data Factory-t arra a fájlra irányíthatja, hogy a sémát erre a fájlra alapozza.


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

Az alábbi táblázat a fenti JSON-tulajdonságokat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
név | Az adatkészlet neve. Lásd: [Azure Data Factory – elnevezési szabályok.](naming-rules.md) |  Igen |
type | Az adatkészlet típusa. Adja meg a Data Factory által támogatott típusok egyikét (például: AzureBlob, AzureSqlTable). <br/><br/>További információt az [Adatkészlettípusok (Dataset types) (Adatkészlettípusok) témakörben talál.](#dataset-type) | Igen |
Séma | Az adatkészlet sémája. További információt az [Adatfolyam-kompatibilis adatkészletek című témakörben talál.](#dataset-type) | Nem |
typeProperties | A típus tulajdonságok különbözőek az egyes típusok (például: Azure Blob, Azure SQL-tábla). A támogatott típusokról és azok tulajdonságairól az [Adatkészlet típusa című](#dataset-type)témakörben talál részleteket. | Igen |


## <a name="dataset-example"></a>Példa adatkészletre
A következő példában az adatkészlet egy MyTable nevű táblát jelöl egy SQL-adatbázisban.

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

- a típus azuresqltable lesz beállítva.
- tableName type tulajdonság (az AzureSqlTable típusra jellemző) A MyTable értékre van állítva.
- A linkedServiceName egy AzureSqlDatabase típusú összekapcsolt szolgáltatásra hivatkozik, amely a következő JSON-kódrészletben van definiálva.

## <a name="dataset-type"></a>Adatkészlet típusa
A használt adattártól függően számos különböző típusú adatkészlet létezik. A Data Factory által támogatott adatok listáját az [Összekötő áttekintéséről](connector-overview.md) szóló cikktartalmazza. Kattintson egy adattárra, és ismerje meg, hogyan hozhat létre csatolt szolgáltatást és adatkészletet az adott adattárhoz.

Az előző szakaszban szereplő példában az adatkészlet típusa **AzureSqlTable**lesz beállítva. Hasonlóképpen, egy Azure Blob-adatkészlet esetén az adatkészlet típusa **AzureBlob**lesz beállítva, ahogy az a következő JSON-ban látható:

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

## <a name="dataset-structure-or-schema"></a>Adatkészlet-struktúra vagy séma
A **struktúraszakasz** vagy **séma** (adatfolyam-kompatibilis) szakaszadatkészletek használata nem kötelező. Az adatkészlet sémáját nevek és oszloptípusok gyűjteményének jelentésével határozza meg. A struktúraszakasz segítségével típusadatokat adhat meg, amelyek a típusok átalakítására és az oszlopok forrásból a célhoz való hozzárendelésére szolgálnak.

A struktúra minden oszlopa a következő tulajdonságokat tartalmazza:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
név | Az oszlop neve. | Igen
type | Az oszlop adattípusa. A Data Factory a következő köztes adattípusokat támogatja engedélyezett értékként: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetimetime, Datetimeoffset és Timespan** | Nem
Kultúra | . NET-alapú kulturális környezet, amelyet a típus `Datetime` .NET típus esetén használ, vagy `Datetimeoffset`. A mező alapértelmezett értéke: `en-us`. | Nem
Formátum | A típus .NET típusú szövege esetén `Datetime` használandó karakterlánc formázása: vagy `Datetimeoffset`. A datetime formázásáról az [Egyéni dátum- és időformátum-karakterláncok hivatkozásra](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) hivatkozik. | Nem

### <a name="example"></a>Példa
A következő példában tegyük fel, hogy a forrásBlob-adatok CSV formátumúak, és három oszlopot tartalmaznak: userid, name és lastlogindate. Int64, String és Datetime típusúak, egyéni datetime formátumban, rövidített francia nevekkel a hét napjára.

A Blob-adatkészlet szerkezetének definiálása az alábbiak szerint az oszlopok típusdefinícióival együtt:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Útmutatás

Az alábbi irányelvek segítenek megérteni, hogy mikor kell **structure** a struktúraadatokat megadni, és mit kell szerepeltetni a struktúraszakaszban. További információ arról, hogy az adatgyár hogyan képezi le a forrásadatokat a fogadóhoz, és mikor kell megadni a struktúraadatokat [a Séma- és típusleképezésből.](copy-activity-schema-and-type-mapping.md)

- **Erős sémaadatforrások esetén**csak akkor adja meg a struktúraszakaszt, ha azt szeretné, hogy a térképforrás-oszlopok elsüllyesztsék az oszlopokat, és a nevük nem egyezik meg. Ez a fajta strukturált adatforrás az adatsémát tárolja, és magát az adatokat is beírja. Strukturált adatforrások például az SQL Server, az Oracle és az Azure SQL Database.<br/><br/>Mivel a strukturált adatforrásokhoz már rendelkezésre állnak típusadatok, a struktúraszakasz felvételekor ne adjon meg típusadatokat.
- **A nincs/gyenge sémaadatforrások, például a blobstorage-ban lévő szövegfájl esetében**a struktúra, ha az adatkészlet egy másolási tevékenység bemenete, és a forrásadatkészlet adattípusait natív típusokká kell konvertálni a fogadószámára. És akkor is vegyen fel struktúrát, ha a forrásoszlopokat oszlopokat szeretne leképezni..

## <a name="create-datasets"></a>Adatkészletek létrehozása
Adatkészleteket az alábbi eszközök vagy SDK-k egyikével hozhat létre: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Azure Resource Manager Template és Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Jelenlegi verzió és 1-es verziójú adatkészletek

Íme néhány különbség a Data Factory és a Data Factory 1-es verziójú adatkészletei között:

- A külső tulajdonság nem támogatott az aktuális verzióban. Ez helyébe egy [ravaszt](concepts-pipeline-execution-triggers.md).
- A házirend és a rendelkezésre állási tulajdonságok nem támogatottak az aktuális verzióban. A folyamat kezdési ideje az [eseményindítóktól](concepts-pipeline-execution-triggers.md)függ.
- A hatókörrel érintett adatkészletek (a folyamatban definiált adatkészletek) nem támogatottak a jelenlegi verzióban.

## <a name="next-steps"></a>További lépések
Az alábbi oktatóanyagban részletes útmutatást talál a folyamatok és adatkészletek ezen eszközök vagy SDK-k egyikének használatával történő létrehozásához.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Rövid útmutató: adatgyár létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)
- [Rövid útmutató: adatgyár létrehozása rest API használatával](quickstart-create-data-factory-rest-api.md)
- [Rövid útmutató: adatgyár létrehozása az Azure Portal használatával](quickstart-create-data-factory-portal.md)
