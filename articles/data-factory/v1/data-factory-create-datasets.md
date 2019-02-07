---
title: Adatkészletek létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan-adatkészletek létrehozása az Azure Data Factory által használt tulajdonságokat, például az eltolás és anchorDateTime példákkal.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: af90a946f12e11602d45300a2796787f839dcf02
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811086"
---
# <a name="datasets-in-azure-data-factory"></a>Adatkészleteket az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-create-datasets.md)
> * [2-es verzió (aktuális verzió)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben adatkészletek](../concepts-datasets-linked-services.md).

Ez a cikk bemutatja, milyen adatkészletek, hogyan vannak definiálva JSON formátumban, és hogy ezek hogyan használhatók az Azure Data Factory-folyamatok. Azt (például a szerkezetet, rendelkezésre állási és házirend) minden szakasz részletesen ismerteti az adatkészlet JSON-definíciójában. A cikk példákat is tartalmaz a a **eltolás**, **anchorDateTime**, és **stílus** az adatkészlet JSON-definíció tulajdonságait.

> [!NOTE]
> Ha most ismerkedik a Data Factory, [az Azure Data Factory bemutatását](data-factory-introduction.md) áttekintését. Ha nem rendelkezik adat-előállítók létrehozásának gyakorlati tapasztalatokat, olvassa el jobb megértése szerezhet a [data transformation oktatóanyag](data-factory-build-your-first-pipeline.md) és a [adatok mozgását útmutató](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** logikai csoportosítása **tevékenységek** , amelyek együttesen hajtanak végre egy feladatot. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Például használhat egy másolási tevékenység adatokat másol egy helyszíni SQL Server az Azure Blob storage. Ezt követően használható a Hive-tevékenység, amely egy Hive-szkriptet az Azure HDInsight-fürtön az adatfeldolgozás kimeneti adatok előállításához a Blob storage-ból. Végül egy második másolási tevékenység használatával lehet, hogy a kimeneti adatok másolása az Azure SQL Data Warehouse, amelyre üzletiintelligencia (BI-) jelentéskészítési megoldások épülnek. További információ a folyamatok és tevékenységek: [az Azure Data Factory folyamatai és tevékenységei](data-factory-create-pipelines.md).

Egy tevékenység is igénybe vehet a nulla vagy több bemeneti **adatkészletek**, és a egy vagy több kimeneti adatkészlet létrehozásához. Bemeneti adatkészlet a folyamat egyik tevékenységének bemeneti, és a egy kimeneti adatkészletet jelenti a tevékenység kimenetét. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Például az Azure Blob-adatkészlet meghatározza a blobtárolót és mappát, amelyből a folyamat beolvassa az adatokat Blob storage-ban.

Mielőtt egy adatkészletet hoz létre, létre kell hoznia egy **társított szolgáltatás** az adattárhoz összekapcsolása a data factoryhoz. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Az adatkészletek azonosíthatja az adatokat a társított adattárakban, mint például az SQL-táblák, fájlok, mappák és dokumentumok belül. Például egy Azure Storage társított szolgáltatás egy storage-fiókot a data factoryhoz. Az Azure Blob-adatkészlet a blobtárolót és a bemeneti blobokat feldolgozású tartalmazó mappát jelöli.

Íme egy példa forgatókönyv. Adatok másolása Blob storage-ból egy SQL Database-adatbázishoz, akkor hozzon létre két társított szolgáltatást: Az Azure Storage és az Azure SQL-adatbázis. Ezután hozzon létre két adatkészletet: Az Azure Blob-adatkészlet (amely az Azure Storage társított szolgáltatásra vonatkozik) és az Azure SQL Table adatkészlet (amely az Azure SQL Database-beli társított szolgáltatásra vonatkozik). Az Azure Storage és Azure SQL Database-beli társított szolgáltatások tartalmaznak, amelyek a Data Factory használ futtatáskor az Azure Storage és Azure SQL Database-csatlakozás kapcsolati karakterláncok. Az Azure Blob-adatkészlet pedig meghatározza a blobtárolót és a Blob storage-ban a bemeneti blobokat tartalmazó blob mappát. Az Azure SQL Table adatkészlet adja meg az SQL-adatbázis, amelyhez az adatokat kell másolni az SQL-táblát.

Az alábbi ábrán látható a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat a Data Factoryban:

![Folyamat, a tevékenység, az adatkészlet, a társított szolgáltatások közötti kapcsolat](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Adatkészlet JSON
Egy adatkészletet a Data Factory a következőképpen van meghatározva JSON formátumban:

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

A következő táblázat ismerteti a fenti JSON-tulajdonságok:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| név |Az adatkészlet nevét. Lásd: [Azure Data Factory – elnevezési szabályok](data-factory-naming-rules.md) elnevezési szabályait. |Igen |NA |
| type |Az adatkészlet típusa. Adja meg a Data Factory által támogatott típusú (például: AzureBlob, AzureSqlTable). <br/><br/>További információkért lásd: [adatkészlettípus](#Type). |Igen |NA |
| struktúra |Az adatkészlet sémája.<br/><br/>További információkért lásd: [adatkészlet-szerkezetekben](#Structure). |Nem |NA |
| typeProperties | A típus tulajdonságokat különböznek az egyes (például: Az Azure Blob, az Azure SQL-tábla). További információ a támogatott típusok és a hozzájuk tartozó tulajdonságok: [adatkészlettípus](#Type). |Igen |NA |
| external | Adja meg, hogy data factory-folyamatok explicit módon előállított adatkészlet vagy nem a logikai jelzőt. Ha a tevékenység bemeneti adatkészlete nem a jelenlegi folyamat által előállított, ezt a jelzőt true értékre. Ezt a jelzőt igaz értékre a folyamat az első tevékenység bemeneti adatkészlete esetében.  |Nem |false |
| rendelkezésre állás | Határozza meg (például óránként vagy naponta) feldolgozási időszakának vagy a slicing az adatkészlet üzemi modellt. Egy tevékenység futtatása által felhasznált és előállított adatok minden egysége adatszelet nevezzük. Kimeneti adatkészlet rendelkezésre állásának napi (gyakorisága –, időköz – 1 nap) értékre van állítva, ha a szelet előállítása naponta. <br/><br/>További információkért lásd: az adatkészlet rendelkezésre. <br/><br/>Felosztási modelljét a adatkészlet részletes ismertetéséért tekintse meg a [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) cikk. |Igen |NA |
| szabályzat |Határozza meg a feltételeket és a feltétellel, hogy az adatkészlet szeleteit meg kell felelniük. <br/><br/>További információkért lásd: a [adatkészlet házirend](#Policy) szakaszban. |Nem |NA |

## <a name="dataset-example"></a>Példa adatkészlet
A következő példában az adatkészlet jelöli nevű tábla **MyTable** egy SQL database-ben.

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

* **típus** AzureSqlTable értékre van állítva.
* **Táblanév** típusa (jellemző AzureSqlTable típus) tulajdonsága MyTable.
* **linkedServiceName** AzureSqlDatabase, amelyet a következő JSON-kódrészletben típusú társított szolgáltatás hivatkozik.
* **rendelkezésre állási gyakorisága** napja, és **időköz** 1 értékre van állítva. Ez azt jelenti, hogy az adatkészlet szelet előállítása naponta.

**AzureSqlLinkedService** a következők:

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

A fenti JSON-kódrészletben:

* **típus** AzureSqlDatabase értékre van állítva.
* **connectionString** tulajdonság adatait egy SQL-adatbázishoz való csatlakozáshoz adja meg.

Amint láthatja, a társított szolgáltatás hogyan csatlakozhat egy SQL-adatbázis határozza meg. Az adatkészlet határozza meg, melyik táblát bemenetként használja, és a folyamat a tevékenység kimeneti.

> [!IMPORTANT]
> A folyamat egy adatkészlet előállítása, hacsak azt kell megjelölni **külső**. Ez a beállítás általában az első tevékenységet a folyamat bemeneti adatai vonatkozik.

## <a name="Type"></a> Forrásadatkészlet típusa
Az adatkészlet típusa attól függ, hogy az adattár használata. Tekintse meg az alábbi táblázat a Data Factory által támogatott adattárak listáját. Kattintson egy adattár kiválasztásával megtudhatja, hogyan hozhat létre a társított szolgáltatás és az adattár egy adatkészletet.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Az adattárak * a helyszínen vagy az Azure infrastruktúra-szolgáltatás (IaaS). Ezek adattárak telepítését igénylik [adatkezelési átjáró](data-factory-data-management-gateway.md).

A példában az előző szakaszban az adatkészlet típusa értéke **AzureSqlTable**. Ehhez hasonlóan az Azure Blob-adatkészlet az adatkészlet típusa értéke **AzureBlob**, ahogyan az az alábbi JSON-ra:

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

## <a name="Structure"></a>Adatkészlet-szerkezetekben
A **struktúra** szakasz nem kötelező. A séma az adatkészlet nevét és az oszlopok adattípusát gyűjteményét tartalmazó szerint határozza meg. A struktúra szakasz segítségével konvertálása típusok és oszlopait a forrásból a cél típusa információkat tartalmaznak. A következő példában az adatkészlet három oszlopot tartalmaz: `slicetimestamp`, `projectname`, és `pageviews`. Típusúak karakterlánc, karakterlánc, és tizedes tört, illetve.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Minden egyes oszlopának struktúrája a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusát.  |Nem |
| kulturális környezet |. NET-alapú kulturális környezetet használni, amikor a típus a .NET-típus: `Datetime` vagy `Datetimeoffset`. A mező alapértelmezett értéke: `en-us`. |Nem |
| Formátum |Formázó karakterlánc típus egy .NET-típus esetén használandó: `Datetime` vagy `Datetimeoffset`. |Nem |

Az alábbi irányelvek segítségével eldöntheti, hogy mikor struktúra információval, és mit kell foglalni a **struktúra** szakaszban.

* **A strukturált adatok források**, adja meg a struktúrát a szakasz csak akkor, ha azt szeretné, hogy a forrás oszlopait fogadó oszlopokat, és a nevek nem azonosak. Strukturált adatok forrása az ilyen típusú adatok séma- és a típus adatait együtt az adat tárolja. Strukturált adatforrások közé tartoznak az SQL Server, Oracle- és Azure-tábla.
  
    Ha típussal kapcsolatos információk már elérhető a strukturált adatok források, műveket nem szabad típussal kapcsolatos információk Ha belefoglalja a struktúra szakaszban.
* **Az olvasási adatforráson (ebben az esetben blobtárolót) séma**, dönthet úgy, hogy adatokat tárolni az adatokat bármely séma vagy típusú adatok tárolására nélkül. Az ilyen típusú adatforrások közé tartozik az struktúra, ha le szeretné képezni a fogadó-oszlopok forrásoszlopok. Struktúra is tartalmazhatnak, ha az adatkészlet egy másolási tevékenység bemeneti, és forrásadatkészlet adattípusát át kell alakítani natív típusai a fogadóhoz.
    
    A Data Factory szerkezetét írja be az adatokat, amelyek biztosítják a következő értékeket támogatja: **Int16, Int32, Int64, egyetlen, Double, tizedes tört, Byte [], logikai érték, karakterlánc, Guid, Datetime, Datetimeoffset és Timespan**. Ezek az értékek Common Language Specification (CLS)-kompatibilis. NET-alapú típusú értékek.

A Data Factory automatikusan típuskonverziók hajt végre, amikor, amely adatokat helyez át egy forrásadattárból egy fogadó adattárba.

## <a name="dataset-availability"></a>Adatkészlet rendelkezésre állása
A **rendelkezésre állási** szakasz egy adatkészlet feldolgozási időszakának (például óránként, naponta, vagy hetente) az adatkészlet határozza meg. Tevékenységablakok kapcsolatos további információkért lásd: [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md).

A következő rendelkezésre állási szakasz meghatározza, hogy a kimeneti adatkészlet vagy előállítása óránként, vagy a bemeneti adatkészlet érhető el Óránként:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Ha a folyamat a következő kezdő és befejező időpontok:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

A kimeneti adatkészlet előállítása óránként belül a folyamat elindításához és befejezési időpontja. Vannak, ezért ez a folyamat, egy egyes tevékenységablakot (12 AM - 1 AM, 1 AM - 2 AM, hajnali 2 óra – 3 AM, 3 AM - 4 AM, 4 AM - 05-kor) az előállított öt adatkészlet szeleteit.

A következő táblázat ismerteti a rendelkezésre állási szakaszban használható tulajdonságok:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészlet szelet éles üzemi környezetek részei.<br/><br/><b>Támogatott gyakoriság</b>: Perc, óra, nap, hét, hónap |Igen |NA |
| interval |Megadja egy szorzóval gyakoriság esetén.<br/><br/>"X időköz" határozza meg, hogy milyen gyakran a szelet előállítása. Például ha az adatkészlet óradíjat kell szeletelt van szüksége, akkor be <b>gyakorisága</b> való <b>óra</b>, és <b>időköz</b> való <b>1</b>.<br/><br/>Vegye figyelembe, hogy ha megad **gyakorisága** , **perc**, az intervallum nem lehet kisebb, mint 15-re kell beállítani. |Igen |NA |
| stílus |Itt adhatja meg, hogy a szeletet, vagy az időszak végén kell mutatni.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Ha **gyakorisága** értékre van állítva **hónap**, és **stílus** értékre van állítva **EndOfInterval**, a szelet előállítása a hónap utolsó napján. Ha **stílus** értékre van állítva **StartOfInterval**, a szelet előállítása a hónap első napján.<br/><br/>Ha **gyakorisága** értékre van állítva **nap**, és **stílus** értékre van állítva **EndOfInterval**, a szelet előállítása a nap az elmúlt órában.<br/><br/>Ha **gyakorisága** értékre van állítva **óra**, és **stílus** értékre van állítva **EndOfInterval**, a szelet előállítása a óra végén. Például egy szelet az du. 1-2 PM időszakban, a rendszer óránként létrehoz egy 2-kor. |Nem |EndOfInterval |
| anchorDateTime |Az ütemező által használt adatkészlet szelet határok számítási idő abszolút pozícióját határozza meg. <br/><br/>Vegye figyelembe, hogy ha a propoerty rendelkezik dátum részei, amelyek részletesebben, mint a megadott gyakorisággal, a részletesebb részek figyelmen kívül hagyja. Például ha a **időköz** van **óránként** (frequency: hour és interval: 1.), és a **anchorDateTime** tartalmaz **perceket és másodperceket**, akkor a perceket és másodperceket részeit **anchorDateTime** figyelmen kívül hagyja. |Nem |01/01/0001 |
| offset |Időtartam, amely szerint a kezdő és befejező az összes adatkészlet szeleteit áttért. <br/><br/>Ne feledje, ha mindkét **anchorDateTime** és **eltolás** meg van adva, a kombinált shift eredménye. |Nem |NA |

### <a name="offset-example"></a>a példában eltolása
Alapértelmezetten naponta (`"frequency": "Day", "interval": 1`) a szeletek start, Éjfélkor (éjfél) egyezményes világidő (UTC). Ha azt szeretné, reggel 6 óra UTC idő helyett lennie a kezdési időpont, állítsa be az eltolás az alábbi kódrészletben látható módon:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime example
A következő példában az adatkészlet 23 óránként jön létre. A megadott időtartam indul az első szelet **anchorDateTime**, amelynek beállítása `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>Példa offset/stílusa
A következő adatkészlet havonta, és a 3. 8:00 órakor minden hónap előállítása (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Adatkészlet-házirend
A **házirend** az adatkészlet-definícióban szakasz definiálja a feltételeket és a feltétellel, hogy az adatkészlet szeleteit meg kell felelniük.

### <a name="validation-policies"></a>Érvényesítési házirendek
| Házirend neve | Leírás | A alkalmazni | Szükséges | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Azt ellenőrzi, hogy az adatok **Azure Blob storage** megfelel a minimális méret (megabájtban). |Azure Blob Storage |Nem |NA |
| minimumRows |Azt ellenőrzi, hogy az adatok egy **Azure SQL database** vagy egy **Azure-tábla** sorok legkisebb számát tartalmazza. |<ul><li>Azure SQL-adatbázis</li><li>Azure-tábla</li></ul> |Nem |NA |

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

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Külső adathalmazok
Külső adatkészlet azok, amelyekre nem hoz létre egy futó folyamatot az adat-előállítóban. Ha az adatkészlet van megjelölve **külső**, a **ExternalData** házirend előfordulhat, hogy kell meghatározni, hogy az adatkészlet szelet elérhetősége viselkedését befolyásolják.

Adat-előállítók által adatkészlet előállítása, hacsak azt kell megjelölni **külső**. Ezzel a beállítással általában vonatkozik egy folyamatot, az első tevékenység bemenetei, kivéve, ha a tevékenység vagy csővezeték-láncolás használatban van.

| Name (Név) | Leírás | Szükséges | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay |Az ellenőrzés rendelkezésre állását a külső adatokat az adott szeletre vonatkozó késleltetés ideje. Például egy óránkénti ellenőrzést késleltetheti a beállítás használatával.<br/><br/>A beállítás csak a jelenlegi idő vonatkozik. Például ha 1:00 Órakor most, és ez az érték 10 perc, az érvényesítési kezdődik, 1:10 PM.<br/><br/>Vegye figyelembe, hogy ez a beállítás nem befolyásolja a szeletek múltbeli. Az a szeleteket **Adatszelet befejezési időpontja** + **dataDelay** < **most** dolgozzák fel késedelem nélkül.<br/><br/>Időpontokban nagyobb, mint a 23:59 óra használatával adható meg a `day.hours:minutes:seconds` formátumban. Ha például annak megadásához, 24 órában, ne használjon 24:00:00. Ehelyett használjon 1.00:00:00. Ha 24:00:00 használ, azt számít 24 nap (24.00:00:00). 1 nap és 4 órán keresztül adja meg a 1:04:00:00. |Nem |0 |
| retryInterval |A várakozási idő hiba és a következő kísérlet között. Ez a beállítás jelenlegi idő vonatkozik. Ha az előző sikertelen, a következő kísérlet után van-e a **retryInterval** időszak. <br/><br/>Ha 1:00 Órakor most, hogy első próbálkozáskor kezdődik. Ha az első érvényesítési ellenőrzés idejére 1 perc, és a művelet sikertelen volt, a következő újrapróbálkozás jelenleg 1:00 + 1 perc (időtartam) + 1 perc (újrapróbálkozási időköz) = 13:02-kor. <br/><br/>A múltban szeletek nem lesz késleltetés. Az újrapróbálkozás akkor történik meg azonnal. |Nem |00:01:00 (1 perc) |
| retryTimeout |Minden egyes újrapróbálkozási kísérlet időtúllépés.<br/><br/>Ez a tulajdonság értéke 10 perc, ha az érvényesítés 10 percen belül meg kell adni. Az érvényesítés végrehajtásához 10 percnél hosszabb ideig tart, ha az újrapróbálkozás időkorlátja.<br/><br/>Ha az érvényesítés időkorlát minden kísérleteit, a szelet van megjelölve **időtúllépés miatt megszakadt**. |Nem |00:10:00 (10 perc) |
| maximumRetry |A külső adatok rendelkezésre állásának ellenőrzéséhez hányszor. A megengedett maximális értéket értéke 10. |Nem |3 |


## <a name="create-datasets"></a>Adatkészletek létrehozása
Az adatkészletek a következő eszközök és SDK-k használatával hozhat létre:

- Másolás varázsló
- Azure Portal
- Visual Studio
- PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

A folyamatok és adatkészletek egyet a következő eszközök és SDK-k használatával történő létrehozásának részletes útmutatás a következő oktatóanyagokban talál:

- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Adatok áthelyezése tevékenységgel rendelkező folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Miután a folyamat a létrehozása és telepítése, kezelése, és -folyamatok figyelése az Azure portal paneleit vagy a Monitoring and Management app használatával. Az alábbi témakörökben részletes útmutatás:

- [Folyamatok figyelése és felügyelete az Azure portal paneljeinek használatával](data-factory-monitor-manage-pipelines.md)
- [Folyamatok figyelése és felügyelete a Monitoring and Management app használatával](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Hatókörrel rendelkező adatkészletek
Létrehozhat adatkészleteket a folyamat használatával is meghatározhat az **adatkészletek** tulajdonság. Ezek az adatkészletek csak a tevékenységek a folyamaton belül, más folyamatok a tevékenységek által nem használható. Az alábbi példa meghatároz egy folyamatot a két adatkészletet (az rdc – InputDataset és OutputDataset-rdc) a folyamaton belül használható.

> [!IMPORTANT]
> Hatókörrel rendelkező adatkészletek csak egyszer használatos folyamatok támogatottak (ahol **pipelineMode** értékre van állítva **OneTime**). Lásd: [Onetime folyamat](data-factory-create-pipelines.md#onetime-pipeline) részleteiről.
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
- További információ a folyamatok: [folyamatokat hozhat létre](data-factory-create-pipelines.md).
- A folyamatok ütemezése és végrehajtott további információkért lásd: [ütemezés és végrehajtás az Azure Data Factoryban](data-factory-scheduling-and-execution.md).
