---
title: Adatkészletek létrehozása az Azure Data Factoryban
description: Ismerje meg, hogyan hozhat létre adatkészleteket az Azure Data Factoryban, példákkal, amelyek olyan tulajdonságokat használnak, mint az eltolás és az anchorDateTime.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 18a5e11d2341fb020fc442d2f9ce7c1d44de9d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260435"
---
# <a name="datasets-in-azure-data-factory"></a>Adathalmazok az Azure Data Factoryben
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-create-datasets.md)
> * [2-es verzió (aktuális verzió)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Adatkészletek a V2 alkalmazásban című témakört.](../concepts-datasets-linked-services.md)

Ez a cikk ismerteti, hogy milyen adatkészletek, hogyan vannak definiálva JSON formátumban, és hogyan használják őket az Azure Data Factory folyamatokban. Az adatkészlet JSON-definíciójában az egyes szakaszok (például a struktúra, a rendelkezésre állás és a házirend) részleteit tartalmazza. A cikk példákat is tartalmaz az **eltolás**, **anchorDateTime**és **stílus** tulajdonságok használatára egy adatkészlet JSON-definíciójában.

> [!NOTE]
> Ha most látja el a Data Factory, olvassa [el az Azure Data Factory bevezetés](data-factory-introduction.md) című témakört az áttekintést. Ha nincs gyakorlati tapasztalata az adatgyárak létrehozásában, jobban megértheti az [adatátalakítási oktatóanyag](data-factory-build-your-first-pipeline.md) és az [adatmozgásoktató anyag olvasásával.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen hajtanak végre egy feladatot. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Például előfordulhat, hogy egy másolási tevékenység segítségével adatokat másolni egy helyszíni SQL Server az Azure Blob storage. Ezután előfordulhat, hogy egy Hive-tevékenység, amely egy Hiv-parancsfájlt futtat egy Azure HDInsight-fürtön a Blob storage-ból származó adatok feldolgozásához kimeneti adatok előállításához. Végül előfordulhat, hogy egy második másolási tevékenység segítségével másolja a kimeneti adatokat az Azure SQL Data Warehouse, amelyen felül az üzleti intelligencia (BI) jelentéskészítési megoldások épülnek. A folyamatokról és a tevékenységekről további információt a [Folyamatok és tevékenységek az Azure Data Factoryban című témakörben talál.](data-factory-create-pipelines.md)

Egy tevékenység nulla vagy több bemeneti **adatkészletet**vehet fel, és egy vagy több kimeneti adatkészletet hozhat létre. A bemeneti adatkészlet a folyamatban lévő tevékenység bemenetét, a kimeneti adatkészlet pedig a tevékenység kimenetét jelöli. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Például egy Azure Blob-adatkészlet határozza meg a blob tároló és a blob storage, ahonnan a folyamat kell olvasni az adatokat.

Adatkészlet létrehozása előtt hozzon létre egy **csatolt szolgáltatást,** amely az adattárést az adat-előállítóhoz kapcsolja. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Az adatkészletek azonosítják a csatolt adattárakon belüli adatokat, például az SQL-táblákat, fájlokat, mappákat és dokumentumokat. Például egy Azure Storage-kapcsolt szolgáltatás egy tárfiókot az adat-előállítóhoz kapcsol. Az Azure Blob-adatkészlet a blobtárolót és a feldolgozandó bemeneti blobokat tartalmazó mappát jelöli.

Íme egy példa forgatókönyv. Adatok másolása a Blob storage-ból egy SQL-adatbázisba, hozzon létre két csatolt szolgáltatások: Az Azure Storage és az Azure SQL Database. Ezután hozzon létre két adatkészletet: Az Azure Blob adatkészlet (amely az Azure Storage-kapcsolt szolgáltatásra hivatkozik) és az Azure SQL Table adatkészlet (amely az Azure SQL Database-hez csatolt szolgáltatásra hivatkozik). Az Azure Storage és az Azure SQL Database-hez kapcsolódó szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyeket a Data Factory futásidőben használ az Azure Storage-hoz és az Azure SQL Database-hez való csatlakozáshoz. Az Azure Blob-adatkészlet határozza meg a blob-tároló és blob mappát, amely tartalmazza a blob storage bemeneti blobok. Az Azure SQL Table adatkészlet határozza meg az SQL-tábla az SQL-adatbázisban, amelybe az adatokat kell másolni.

Az alábbi ábra a data factory-i folyamat, tevékenység, adatkészlet és csatolt szolgáltatás közötti kapcsolatokat mutatja be:

![A folyamat, a tevékenység, az adatkészlet, a kapcsolódó szolgáltatások közötti kapcsolat](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON adatkészlet
A Data Factory adatkészlete JSON formátumban a következőképpen van definiálva:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

Az alábbi táblázat a fenti JSON-tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| név |Az adatkészlet neve. Lásd: [Azure Data Factory – elnevezési szabályok](data-factory-naming-rules.md) elnevezési szabályok. |Igen |NA |
| type |Az adatkészlet típusa. Adja meg a Data Factory által támogatott típusok egyikét (például: AzureBlob, AzureSqlTable). <br/><br/>További információt az Adatkészlet típusa ( Adatkészlet típusa ) [(Adatkészlet típusa) (Adatkészlet típusa) (Adatkészlet típusa) (Adatkészlet típusa) (](#Type) |Igen |NA |
| Szerkezet |Az adatkészlet sémája.<br/><br/>További információt az [Adatkészletszerkezet ben](#Structure)talál. |Nem |NA |
| typeProperties | A típus tulajdonságok különbözőek az egyes típusok (például: Azure Blob, Azure SQL-tábla). A támogatott típusokról és azok tulajdonságairól az [Adatkészlet típusa című](#Type)témakörben talál részleteket. |Igen |NA |
| external | Logikai jelző annak megadásához, hogy egy adatkészletet kifejezetten egy adat-előállító folyamat hoz-e létre. Ha egy tevékenység bemeneti adatkészletét nem az aktuális folyamat hozza létre, állítsa ezt a jelzőt true értékre. Állítsa ezt a jelzőt true értékre a folyamatban lévő első tevékenység bemeneti adatkészletéhez.  |Nem |hamis |
| availability | A feldolgozási ablakot (például óránkéntvagy naponta) vagy az adatkészlet-előállítás szeletelési modelljét határozza meg. A tevékenységfuttatással felhasznált és előállított adatok minden egyes egységét adatszeletnek nevezzük. Ha a kimeneti adatkészlet rendelkezésre állása napi (gyakoriság - nap, intervallum - 1) értékre van állítva, a rendszer naponta egy szeletet állít elő. <br/><br/>További információt az Adatkészlet elérhetősége. <br/><br/>Az adatkészlet szeletelési modelljével kapcsolatos részleteket az [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) című cikkben találja. |Igen |NA |
| szabályzat |Meghatározza azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet szeleteinek meg kell felelniük. <br/><br/>További információt az [Adatkészlet-házirend](#Policy) szakaszban talál. |Nem |NA |

## <a name="dataset-example"></a>Példa adatkészletre
A következő példában az adatkészlet egy **MyTable** nevű táblát jelöl egy SQL-adatbázisban.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Vegye figyelembe a következő szempontokat:

* **a típus** azuresqltable lesz beállítva.
* **tableName** type tulajdonság (az AzureSqlTable típusra jellemző) A MyTable értékre van állítva.
* **A linkedServiceName** egy AzureSqlDatabase típusú összekapcsolt szolgáltatásra hivatkozik, amely a következő JSON-kódrészletben van definiálva.
* **a rendelkezésre állás gyakorisága** Nap, **az intervallum** pedig 1. Ez azt jelenti, hogy az adatkészlet szelet naponta keletkezik.

**Az AzureSqlLinkedService** meghatározása a következő:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Az előző JSON-kódrészletben:

* **a típus** azuresqldatabase lesz.
* **a connectionString** type tulajdonság az SQL-adatbázishoz való csatlakozás adatait adja meg.

Amint láthatja, a csatolt szolgáltatás határozza meg, hogyan lehet csatlakozni egy SQL-adatbázishoz. Az adatkészlet határozza meg, hogy milyen tábla ként használja a folyamat bemeneti és kimeneti.

> [!IMPORTANT]
> Ha a folyamat nem állít elő adatkészletet, **külsőként**kell megjelölni. Ez a beállítás általában a folyamat első tevékenységének bemeneteire vonatkozik.

## <a name="dataset-type"></a><a name="Type"></a>Adatkészlet típusa
Az adatkészlet típusa a használt adattártól függ. Az alábbi táblázatban a Data Factory által támogatott adattárak listáját tartalmazza. Kattintson egy adattárra, és ismerje meg, hogyan hozhat létre csatolt szolgáltatást és adatkészletet az adott adattárhoz.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A *-os adattárak lehetnek a helyszínen vagy az Azure-infrastruktúra szolgáltatásként (IaaS). Ezek az adattárak az Adatkezelési átjáró telepítését [igénylik.](data-factory-data-management-gateway.md)

Az előző szakaszban szereplő példában az adatkészlet típusa **AzureSqlTable**lesz beállítva. Hasonlóképpen, egy Azure Blob-adatkészlet esetén az adatkészlet típusa **AzureBlob**lesz beállítva, ahogy az a következő JSON-ban látható:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="dataset-structure"></a><a name="Structure"></a>Adatkészlet szerkezete
A **műtárgy** szakasz nem kötelező. Az adatkészlet sémáját nevek és oszloptípusok gyűjteményének jelentésével határozza meg. A struktúraszakasz segítségével típusadatokat adhat meg, amelyek a típusok átalakítására és az oszlopok forrásból a célhoz való hozzárendelésére szolgálnak. A következő példában az adatkészlet három `slicetimestamp` `projectname`oszlopból `pageviews`áll: , , és . Ezek a típus String, String, és decimális, illetve.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A struktúra minden oszlopa a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusa.  |Nem |
| Kultúra |. NET-alapú kulturális környezet, amelyet a típus `Datetime` .NET típus esetén használ, vagy `Datetimeoffset`. A mező alapértelmezett értéke: `en-us`. |Nem |
| Formátum |A típus .NET típusú szövege esetén `Datetime` használandó karakterlánc formázása: vagy `Datetimeoffset`. |Nem |

Az alábbi irányelvek segítségével meghatározhatja, hogy mikor kell **structure** megadni a szerkezeti információkat, és mit vegyen fel a struktúraszakaszba.

* **Strukturált adatforrások esetén**csak akkor adja meg a struktúraszakaszt, ha azt szeretné, hogy a térképforrás-oszlopok oszlopokat süllyesztenek, és a nevük nem egyezik meg. Ez a fajta strukturált adatforrás az adatsémát tárolja, és magát az adatokat is beírja. Strukturált adatforrások például az SQL Server, az Oracle és az Azure tábla.
  
    Mivel a strukturált adatforrásokhoz már rendelkezésre állnak típusadatok, a struktúraszakasz felvételekor ne adjon meg típusadatokat.
* **Olvasási adatforrások (különösen a Blob storage) sémája esetén**dönthet úgy, hogy az adatokat a séma tárolása vagy az adatokkal való adatok megadása nélkül tárolja. Az ilyen típusú adatforrások esetében akkor is legyen struktúra, ha a forrásoszlopokat oszlopok hozadékához szeretné leképezni. Azt is vegye fel a struktúra, ha az adatkészlet egy másolási tevékenység bemenete, és a forrásadatkészlet adattípusait natív típusokká kell konvertálni a fogadó számára.
    
    A Data Factory a következő értékeket támogatja a típusadatok szerkezetben történő megadásához: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset és Timespan**. Ezek az értékek a common language specification (CLS) előírásainak megfelelőek, . NET-alapú típusértékek.

A Data Factory automatikusan szövegkonverziókat hajt végre, amikor adatokat helyez át a forrásadattárból a fogadó adattárba.

## <a name="dataset-availability"></a>Az adatkészlet elérhetősége
Az adatkészlet **rendelkezésre állási** szakasza határozza meg az adatkészlet feldolgozási ablakát (például óránkénti, napi vagy heti). A tevékenységablakokról az [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md)című témakörben talál további információt.

A következő rendelkezésre állási szakasz azt határozza meg, hogy a kimeneti adatkészlet óránként készül, vagy a bemeneti adatkészlet óránként érhető el:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Ha a folyamat a következő kezdési és befejezési időpontokkal rendelkezik:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

A kimeneti adatkészlet óránként jön létre a folyamat kezdési és befejezési időszakán belül. Ezért a folyamat öt adatkészletszeletet hozott létre, egyet minden tevékenységablakhoz (12:00 - 1:00, 1 AM - 2 AM, 2 AM - 3 AM, 3 AM - 4 AM, 4 AM - 5 AM).

Az alábbi táblázat a rendelkezésre állási szakaszban használható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészletszelet előállításának időegységét.<br/><br/><b>Támogatott gyakoriság:</b>Perc, Óra, Nap, Hét, Hónap |Igen |NA |
| interval |A frekvencia szorzóját adja meg.<br/><br/>A "Gyakoriság x intervallum" határozza meg, hogy milyen gyakran keletkezik a szelet. Ha például az adatkészletet óránként kell szeletelni, a <b>gyakoriságot</b> <b>Óra</b>, <b>az intervallumot</b> pedig <b>1-re</b>kell állítani.<br/><br/>Ne feledje, hogy ha **a gyakoriságot** **percként**adja meg, az intervallumot legalább 15-re kell állítania. |Igen |NA |
| stílus |Itt adható meg, hogy a szeletet az intervallum elején vagy végén kell-e előállítani.<ul><li>StartOfInterval (Kezdési intervallum)</li><li>EndOfInterval (Időköz vége)</li></ul>Ha a **gyakoriság** **hónap,** a **stílus** pedig **EndOfInterval**értékre van állítva, a szelet a hónap utolsó napján keletkezik. Ha a **stílus** **startofinterval**értékre van állítva, a szelet a hónap első napján jön létre.<br/><br/>Ha a **gyakoriság** **a Nap**, és a **stílus** beállítása **EndOfInterval**, a szelet a nap utolsó órájában keletkezik.<br/><br/>Ha a **gyakoriság** **óra**, és a **stílus** **beállítása EndOfInterval**, a szelet az óra végén keletkezik. Például egy szelet a 1:00-2:00 időszak, a szelet elő 14:00. |Nem |EndOfInterval (Időköz vége) |
| anchorDateTime |Az adathalmazszelet-határok kiszámításához az ütemező által használt abszolút időbeosztást határozza meg. <br/><br/>Vegye figyelembe, hogy ha ez a tulajdonság olyan dátumrészekkel rendelkezik, amelyek részletesebbek, mint a megadott gyakoriság, a részletesebb részeket a rendszer figyelmen kívül hagyja. Ha például az **időköz** **óránkénti** (gyakoriság: óra és időköz: 1), és a **anchorDateTime** **perceket és másodperceket**tartalmaz, akkor a **anchorDateTime** perc és másodperc részei figyelmen kívül lesznek hagyva. |Nem |01/01/0001 |
| offset |Az az időtartam, amely alatt az összes adatkészletszelet kezdő és záróeleme eltolódik. <br/><br/>Vegye figyelembe, hogy ha mind **a anchorDateTime,** mind az **eltolás** meg van adva, az eredmény a kombinált eltolás. |Nem |NA |

### <a name="offset-example"></a>példa eltolásra
Alapértelmezés szerint a`"frequency": "Day", "interval": 1`napi ( ) szeletek 12:00 órakor (éjfélkor) kezdődnek az egyezményes világidő (UTC) szerint. Ha azt szeretné, hogy a kezdési idő 6:00-kor legyen UTC idő szerint, állítsa be az eltolást a következő kódrészletben látható módon:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime példa
A következő példában az adatkészlet 23 óránként egyszer keletkezik. Az első szelet az **anchorDateTime**által megadott időpontban `2017-04-19T08:00:00` kezdődik, amelynek beállítása (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>példa eltolásra/stílusra
A következő adatkészlet havonta, és minden hónap 3-án 8:00`3.08:00:00`órakor ():

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Adatkészlet-házirend
Az adatkészlet-definíció **házirendszakasza** határozza meg azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet szeleteinek meg kell felelniük.

### <a name="validation-policies"></a>Érvényesítési házirendek
| Házirend neve | Leírás | Alkalmazása | Kötelező | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ellenőrzi, hogy az **Azure Blob storage-ban** lévő adatok megfelelnek-e a minimális méretkövetelményeknek (megabájtban). |Azure Blob Storage |Nem |NA |
| minimumSorok |Ellenőrzi, hogy az **Azure SQL-adatbázisban** vagy egy **Azure-táblában** lévő adatok tartalmazzák-e a sorok minimális számát. |<ul><li>Azure SQL-adatbázis</li><li>Azure-tábla</li></ul> |Nem |NA |

#### <a name="examples"></a>Példák
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumSorok:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Külső adatkészletek
A külső adatkészletek azok, amelyeket nem az adat-előállító ban futó folyamat állít elő. Ha az adatkészlet **külsőként**van megjelölve, a **ExternalData** házirend definiálható az adatkészletszelet ek rendelkezésre állásának befolyásolására.

Hacsak a Data Factory nem állít elő adatkészletet, **külsőként**kell megjelölni. Ez a beállítás általában a folyamat első tevékenységének bemeneteire vonatkozik, kivéve, ha tevékenység vagy csővezeték-láncolás van használatban.

| Név | Leírás | Kötelező | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay (adatkésleltetés) |Az adott szelet külső adatainak rendelkezésre állásának késleltetéséhez szükséges idő. Ezzel a beállítással például késleltetheti az óránkénti ellenőrzést.<br/><br/>A beállítás csak az jelen időpontra vonatkozik. Ha például jelenleg 13:00 óra, és ez az érték 10 perc, az ellenőrzés 13:10-kor kezdődik.<br/><br/>Ne feledje, hogy ez a beállítás nem befolyásolja a korábbi szeleteket. **Szeletbefejezési befejezési idő** + **adatkésleltetés** < **most** azonnal feldolgozása.<br/><br/>A 23:59 óránál hosszabb időt a `day.hours:minutes:seconds` formátum használatával kell megadni. Ha például 24 órát szeretne megadni, ne használja a 24:00:00 értéket. Ehelyett használja az 1:00:00:00.Instead, use 1.00:00.Instead, use 1.00:00:00. A 24:00:00 használata esetén a kezelés 24 nap (24.00:00:00). 1 nap és 4 óra esetén adja meg az 1:04:00:00 értéket. |Nem |0 |
| retryInterval |A hiba és a következő kísérlet közötti várakozási idő. Ez a beállítás a jelen időpontra vonatkozik. Ha az előző próbálkozás sikertelen volt, a következő próbálkozás az **újrapróbálkozási időszak** után lesz. <br/><br/>Ha most délután 1 óra van, kezdjük az első próbálkozást. Ha az első érvényesítési ellenőrzés befejezésének időtartama 1 perc, és a művelet sikertelen, a következő újrapróbálkozás 1:00 + 1min (időtartam) + 1 perc (újrapróbálkozási időköz) = 1:02 DU. <br/><br/>A korábbi szeletek esetében nincs késés. Az újrapróbálkozás azonnal megtörténik. |Nem |00:01:00 (1 perc) |
| újrapróbálkozásidő-ki( újrapróbálkozás) |Az egyes újrapróbálkozási próbálkozások időmeghosszabbítása.<br/><br/>Ha ez a tulajdonság 10 percre van állítva, az ellenőrzést 10 percen belül be kell fejezni. Ha az ellenőrzés végrehajtása 10 percnél tovább tart, az újrapróbálkozás időtúllépése.<br/><br/>Ha az érvényesítési idő minden kísérlete megtörténik, a szelet **időhelyesítésként**van megjelölve. |Nem |00:10:00 (10 perc) |
| maximumRetry |A külső adatok elérhetőségének ellenőrzéséhez szükséges alkalmak száma. A megengedett maximális érték 10. |Nem |3 |


## <a name="create-datasets"></a>Adatkészletek létrehozása
Adatkészleteket az alábbi eszközök vagy SDK-k egyikével hozhat létre:

- Másolás varázsló
- Visual Studio
- PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

Az alábbi útmutatókban részletesen ismerteti a folyamatok és adatkészletek ezen eszközök vagy SDK-k használatával történő létrehozását:

- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Folyamat létrehozása adatmozgatási tevékenységgel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

A folyamat létrehozása és üzembe helyezése után kezelheti és figyelheti a folyamatokat az Azure Portal blades vagy a Figyelés és felügyeleti alkalmazás használatával. Az alábbi témakörökben részletes enklúzsa:

- [Folyamatok figyelése és kezelése az Azure Portal paneljein keresztül](data-factory-monitor-manage-pipelines.md)
- [Folyamatok figyelése és kezelése a Figyelés és kezelés alkalmazással](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Hatókörrel érintett adatkészletek
Létrehozhat olyan adatkészleteket, amelyek hatóköre egy folyamathoz az **adatkészletek** tulajdonság használatával történik. Ezeket az adatkészleteket csak a folyamaton belüli tevékenységek használhatják, más folyamatoktevékenységei nem. A következő példa egy két adatkészletet (InputDataset-rdc és OutputDataset-rdc) rendelkező folyamatot határoz meg a folyamaton belül.

> [!IMPORTANT]
> Scoped adatkészletek csak egyszeri folyamatok (ahol **pipelineMode** van beállítva **OneTime**). A részleteket az [Egyszeri folyamat](data-factory-create-pipelines.md#onetime-pipeline) ban találja.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>További lépések
- A folyamatokról további információt a [Folyamatok létrehozása című témakörben talál.](data-factory-create-pipelines.md)
- A folyamatok ütemezéséről és végrehajtásáról az [Ütemezés és végrehajtás az Azure Data Factoryban](data-factory-scheduling-and-execution.md)című témakörben talál további információt.
