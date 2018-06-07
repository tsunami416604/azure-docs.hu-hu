---
title: Adatkészletek létrehozása az Azure Data Factory |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre adathalmazokat az Azure Data Factoryben, például az eltolás és a anchorDateTime használó példák.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6a3401f620f7dfe8b42bad9ed1a3981325b2ce1e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620479"
---
# <a name="datasets-in-azure-data-factory"></a>Az Azure Data Factory adathalmazok
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-create-datasets.md)
> * [2. verzió – Előzetes verzió](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [V2 adathalmazok](../concepts-datasets-linked-services.md).

Ez a cikk ismerteti, milyen adatkészletek, hogyan vannak definiálva JSON formátumú, és hogy ezek hogyan használhatók az Azure Data Factory folyamatok. Az adatkészlet JSON-definícióban az egyes szakaszokon (például struktúra, rendelkezésre állási és házirend) kapcsolatos adatokat biztosít. A cikk példákat is tartalmaz az a **eltolás**, **anchorDateTime**, és **stílus** tulajdonságok az adatkészlet JSON-definícióban.

> [!NOTE]
> Ha most ismerkedik a Data Factory, lásd: [Bevezetés az Azure Data Factory](data-factory-introduction.md) áttekintése. Ha nem rendelkezik adat-előállítók létrehozása a gyakorlati tapasztalatok, akkor is értelmezésében ehhez beolvassa a [data transformation oktatóanyag](data-factory-build-your-first-pipeline.md) és a [adatok mozgása oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **csővezeték** logikai csoportosítása **tevékenységek** , amelyek együtt a feladat elvégzésére. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használhatja például a adatok másolása az egy helyi SQL Server az Azure Blob Storage tárolóban. Ezután használhatja a egy Hive tevékenységet, amely a Hive parancsfájlok futtatására szolgál egy Azure HDInsight fürt adatfeldolgozásra történő blobtárolóból eredményezett kimeneti adatokat. Végezetül segítségével lehet egy második másolási tevékenység kimeneti adatok másolása az Azure SQL Data Warehouse, mely üzleti intelligenciával reporting megoldások beépített felett. Adatcsatornák és tevékenységgel kapcsolatos további információkért lásd: [folyamatok és az Azure Data Factory tevékenységek](data-factory-create-pipelines.md).

Egy tevékenység is igénybe vehet a nulla vagy több bemeneti **adatkészletek**, majd előállítanak egy vagy több kimeneti adatkészletek. Egy bemeneti adatkészlet a tevékenység a folyamat a megadott, és egy kimeneti adatkészlet a tevékenység kimeneti jelenti. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Például egy Azure Blob-adathalmazra határozza meg a blob-tároló és mappa Blob-tároló, ahonnan a feldolgozási sor olvassa el az adatokat. 

A DataSet adatkészlet létrehozása előtt hozzon létre egy **társított szolgáltatás** az adattároló csatolása az adat-előállítóban. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Adatkészletek azonosíthatja az adatokat a csatolt adatokat tárolja, például az SQL-táblák, fájlok, mappák és dokumentumok belül. Például egy Azure Storage kapcsolódó szolgáltatás hivatkozások egy tárfiókot az adat-előállítóban. Egy Azure Blob-adathalmazra jelenti. a blob-tároló és a bemeneti BLOB feldolgozandó tartalmazó mappát. 

Íme egy példa. Adatok másolása Blob-tároló SQL-adatbázis, a két társított szolgáltatások létrehozásához: Azure Storage és az Azure SQL Database. Ezután hozzon létre két adatkészletet: Azure Blob-adathalmazra (amely az Azure tárolás társított szolgáltatásának vonatkozik) és az Azure SQL-tábla a dataset (amely a kapcsolódó Azure SQL Database szolgáltatás vonatkozik). Az Azure Storage és az Azure SQL Database kapcsolódó szolgáltatások tartalmaznia az csatlakozni az Azure Storage és az Azure SQL Database, illetve futásidőben használja a Data Factory-kapcsolati karakterláncok. Az Azure Blob-adathalmazra blobtárolót és a bemeneti BLOB a Blob Storage tárolóban tartalmazó blob mappát adja meg. Az Azure SQL-tábla adatkészletet az SQL-tábla az SQL-adatbázis, amelyre az adatok másolása az határozza meg.

Az alábbi ábrán látható a folyamat, a tevékenység, a dataset és a társított szolgáltatás közötti kapcsolatok adat: 

![Adatcsatorna, adatkészlet, tevékenység társított szolgáltatások közötti kapcsolat](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Adatkészlet JSON
A Data Factory dataset az alábbiak szerint definiáltuk JSON formátumban:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
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

A következő táblázat ismerteti a fenti JSON-tulajdonságokat:   

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| név |A DataSet adatkészlet neve. Lásd: [Azure Data Factory - elnevezési szabályait](data-factory-naming-rules.md) elnevezési szabályait. |Igen |NA |
| type |A dataset típusa. Adja meg a Data Factory által támogatott típusú (például: AzureBlob, AzureSqlTable). <br/><br/>További információkért lásd: [adathalmaztípushoz](#Type). |Igen |NA |
| struktúra |Az adatkészlet sémája.<br/><br/>További információkért lásd: [adatkészlet-szerkezetekben](#Structure). |Nem |NA |
| typeProperties | A típus tulajdonságokat eltérőek az egyes (például: Azure Blob, Azure SQL-tábla). A támogatott típusok és tulajdonságaikat a részletekért lásd: [adathalmaztípushoz](#Type). |Igen |NA |
| external | Logikai jelző, amely adja meg, hogy a data factory-folyamathoz explicit módon létrehozott adatkészlet vagy nem. Ha az aktuális folyamat nem hozzák a tevékenység bemeneti adatkészletet, ez a jelző értéke igaz. Ez a jelző értéke igaz, a folyamat az első tevékenység bemeneti adatkészlet.  |Nem |false |
| rendelkezésre állás | A feldolgozási időszakában (például óránként vagy naponta) vagy az adatkészlet üzemi slicing modell határoz meg. Felhasznált és egy tevékenység futott által előállított adatok tárolóegységekhez egy adatszelet nevezik. Ha egy kimeneti adatkészlet rendelkezésre állását napi (gyakoriság -, időköz - 1 nap) be van állítva, a szelet naponta elő. <br/><br/>További információkért lásd: [adatkészlet rendelkezésre állási](#Availability). <br/><br/>Az adatkészlet modell felosztás a részletekért lásd: a [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md) cikk. |Igen |NA |
| szabályzat |Meghatározza a feltételek vagy a feltétellel, hogy a dataset szeletek teljesítenie kell. <br/><br/>További információkért lásd: a [Dataset házirend](#Policy) szakasz. |Nem |NA |

## <a name="dataset-example"></a>A DataSet – példa
A következő példában a dataset nevű táblázatot jelölő **MyTable** SQL-adatbázisban.

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
* **tableName** típusa (AzureSqlTable típus adott) tulajdonsága táblanév.
* **linkedServiceName** AzureSqlDatabase, amelyet a következő JSON-részlet típusú társított szolgáltatás hivatkozik. 
* **rendelkezésre állási gyakoriság** napja, és **időköz** 1 értékre van állítva. Ez azt jelenti, hogy a dataset szelet naponta jön létre.  

**AzureSqlLinkedService** az alábbiak szerint definiáltuk:

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

Az előző JSON kódtöredékre:

* **típus** AzureSqlDatabase értékre van állítva.
* **connectionString** típusú tulajdonság határozza meg az adatokat egy SQL-adatbázishoz való kapcsolódáshoz.  

Ahogy látja, a társított szolgáltatás SQL-adatbázis csatlakoztatása határozza meg. A dataset határozza meg, milyen tábla bemenetként használja, ezért a folyamat tevékenység kimeneti.   

> [!IMPORTANT]
> Ha dataset a folyamat alatt keletkezik, azt kell megjelölni **külső**. Bemenet az adatcsatorna első tevékenység általában alkalmazza a beállítást.   


## <a name="Type"></a> A DataSet típusa
A dataset típusa attól függ, hogy a tárolót használja. Lásd az alábbi táblázatban a Data Factory által támogatott adattárolókhoz listáját. Kattintson a tárolóban annak megismerése, hogyan összekapcsolt szolgáltatás és az adott tároló adatkészlet létrehozásához.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Adatok tárolja a * lehet helyszíni vagy Azure-infrastruktúra (IaaS) szolgáltatás. Ezek adattárolókhoz telepítését igénylik [az adatkezelési átjáró](data-factory-data-management-gateway.md).

A példában az előző szakaszban, a DataSet adatbázisoszlophoz **AzureSqlTable**. Ehhez hasonlóan egy Azure-Blob adatkészlet a DataSet adatbázisoszlophoz **AzureBlob**, ahogy az az alábbi JSON:

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
A **struktúra** szakasz nem kötelező megadni. Az adatkészlet sémája által tartalmazó nevét és az oszlopok adattípusát gyűjteményét határozza meg. A struktúra szakasz segítségével adja meg, amellyel típusok átalakítani, és képezze le a cél a forrás oszlop típusa információkat. A következő példában a dataset adatkészletben három oszlopot: `slicetimestamp`, `projectname`, és `pageviews`. Vannak típusú karakterlánc, karakterlánc, és tizedes, illetve.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Minden egyes oszlopának a struktúra tartalmaz a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név |Az oszlop neve. |Igen |
| type |Az oszlop adattípusát.  |Nem |
| Kulturális környezet |. A NET-alapú kulturális környezet lehet használni, ha a típus a .NET-típus: `Datetime` vagy `Datetimeoffset`. Az alapértelmezett érték `en-us`. |Nem |
| Formátumban |Formázó karakterlánc kell használni, ha a típus a .NET-típus: `Datetime` vagy `Datetimeoffset`. |Nem |

A következő irányelvek segítségével meghatározhatja, hogy mikor struktúra információval, és milyen ahhoz, hogy szerepeljen a **struktúra** szakasz.

* **A strukturált adatforrások**, adja meg a struktúra szakaszban csak akkor, ha a kívánt oszlopok gyűjtése forrásoszlopok leképezni, és a nevek nem azonosak. Strukturált adatforrás az ilyen adatok séma- és tároló típusa mellett az adatokat mozgatná. Strukturált adatforrások például SQL Server, az Oracle és az Azure-tábla. 
  
    Típusra vonatkozó adat már áll rendelkezésre a strukturált adatforrásokhoz, akkor nem tartalmazhat típusinformációt elvégzését indokló, hogy a struktúra szakasz.
* **Az olvasási adatforrások (kifejezetten Blob-tároló) séma**, választhat adatok tárolására nem tárolja az adatokat az séma vagy típus vonatkozó információk. Az ilyen típusú adatforrások tartalmazhat struktúra, ha a megjeleníteni kívánt oszlopok gyűjtése forrásoszlopok. Struktúra is tartalmazza, ha a dataset másolási tevékenységhez bemeneti, és a forrás adatkészlet adattípusok át kell alakítani a fogadó natív típust. 
    
    Adat-előállítót a következő értékek támogatja a struktúrában típusú adatokat ad: **Int16, Int32, Int64, egyetlen, Double, Decimal, Byte [], logikai érték, karakterlánc, Guid, Datetime, Datetimeoffset és Timespan**. Ezek az értékek közös nyelvi specifikáció (CLS)-kompatibilis. A NET-alapú típusú értékeket.

Adat-előállító automatikusan típuskonverziók hajt végre, amikor adatokat a forrás-tárolóban a fogadó tárolóban. 
  

## <a name="dataset-availability"></a>Adatkészlet rendelkezésre állása
A **rendelkezésre állási** DataSet adatkészletben szakaszban határozza meg a feldolgozási időszakában (például óránként, naponta, vagy hetente) a következő adatkészletnél. Tevékenység windows kapcsolatos további információkért lásd: [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md).

A következő rendelkezésre állással kapcsolatos szakaszának határozza meg, hogy a kimeneti adathalmazt vagy rendszer óránként készít adatszeletet, vagy a bemeneti adatkészlet óránkénti áll rendelkezésre:

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

A kimeneti adatkészlet hozzák óránkénti belül a folyamat kezdési és befejezési időpontja. Ezért nincsenek öt dataset szeletek által az adatcsatornát, egy az egyes tevékenységek ablakát (12 óra - 13 AM, 13: 00 – 2 óra, Reggel 2 - 3 AM, hajnali 3 Órakor - 4 AM, hajnali 4 Órakor – 5: 00). 

A következő táblázat ismerteti a rendelkezésre állással kapcsolatos szakaszának használható tulajdonságok:

| Tulajdonság | Leírás | Szükséges | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja a dataset szelet üzemi időegységét.<br/><br/><b>Támogatott gyakoriság</b>: perc, óra, nap, hét, hónap |Igen |NA |
| interval |Megadja a gyakoriság egy szorzóval.<br/><br/>"X időköz" határozza meg, milyen gyakran a szelet jön létre. Például, ha a adatkészlet kell szeletelhetők óránként, beállíthatja <b>gyakoriság</b> való <b>óra</b>, és <b>időköz</b> való <b>1</b>.<br/><br/>Vegye figyelembe, hogy ha a megadott **gyakorisága** , **perc**, nem lehet kisebb, mint 15 kell beállítani az időközt. |Igen |NA |
| stílus |Meghatározza, hogy a szelet akkor a rendszer a kezdő vagy intervallum végén.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Ha **gyakorisága** értéke **hónap**, és **stílus** értékre van állítva **EndOfInterval**, a szelet hónap utolsó napján jön létre. Ha **stílus** értéke **StartOfInterval**, a szelet hónap első napján jön létre.<br/><br/>Ha **gyakoriság** értéke **nap**, és **stílus** értéke **EndOfInterval**, a szelet jön létre az elmúlt órában a nap.<br/><br/>Ha **gyakorisága** értéke **óra**, és **stílus** értéke **EndOfInterval**, a szelet keletkezik az óra. Például a du. 1-2 PM időszak adatszelethez, a szelet hozzák 2 du. |Nem |EndOfInterval |
| anchorDateTime |Az ütemező által használt adatkészlet szelet határok számítási időben abszolút helyzet határozza meg. <br/><br/>Vegye figyelembe, hogy ha a propoerty dátum részeit, amelyek részletesebben, mint a megadott gyakorisággal, a részletesebb részek figyelmen kívül lesznek hagyva. Például ha a **időköz** van **óránkénti** (gyakoriság: óra és időköz: 1), és a **anchorDateTime** tartalmaz **percet és másodpercet**, majd a percet és másodpercet részeit **anchorDateTime** figyelmen kívül lesznek hagyva. |Nem |01/01/0001 |
| offset |TimeSpan érték, amely a kezdő és a záró összes adatkészlet szeletek vette. <br/><br/>Ne feledje, ha mindkét **anchorDateTime** és **eltolás** van adva, a kombinált shift eredménye. |Nem |NA |

### <a name="offset-example"></a>az eltolási – példa
Alapértelmezés szerint naponta (`"frequency": "Day", "interval": 1`) szeletek start: 00 (éjfél) egyezményes világidő (UTC). Ha azt szeretné, hogy a kezdési idő reggel 6 óra UTC idő helyette, állítsa be az eltolás látható módon a következő kódrészletet: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime – példa
A következő példában a dataset 23 óránként jön létre. Az első szelet elindítja a megadott időpontban által megadott **anchorDateTime**, melynek értéke `2017-04-19T08:00:00` (idő szerint UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Az offset/style – példa
A következő adatkészlet havi, és a 3., havonta, de a hozzák (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>A DataSet házirend
A **házirend** az adatkészlet-definícióban szakaszban határozza meg, a feltételek vagy a feltétellel, hogy a dataset szeletek teljesítenie kell.

### <a name="validation-policies"></a>Házirendek érvényesítése
| Házirend neve | Leírás | Vonatkozik. | Szükséges | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Azt ellenőrzi, hogy az adatok **Azure Blob Storage tárolóban** megfelel a minimális méretét (megabájtban). |Azure Blob Storage |Nem |NA |
| minimumRows |Azt ellenőrzi, hogy az adatokat egy **Azure SQL adatbázis** vagy egy **Azure-tábla** a sorok legkisebb számát tartalmazza. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |Nem |NA |

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

### <a name="external-datasets"></a>Külső adatkészletek
Külső adatkészletek azok, amelyekre egy futó folyamatot az adat-előállító nem hozzák létre. Ha a dataset van megjelölve **külső**, a **ExternalData** házirend meghatározhatják a dataset szelet rendelkezésre állási működését befolyásolják.

Kivéve, ha a Data Factory hozzák alatt álló adatkészlet, azt kell megjelölni **külső**. Alkalmazza a beállítást általában a bemenetek első tevékenység egy sorban, kivéve, ha a tevékenység vagy csővezeték-láncolás használatban van.

| Name (Név) | Leírás | Szükséges | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay |A külső adatokat az adott szelet rendelkezésre állásának az ellenőrzését késleltetési idő. Például egy óránkénti ellenőrzést késleltetheti a beállítás használatával.<br/><br/>A beállítás csak a jelenlegi időpont vonatkozik.  Például ha 1:00 PM azonnal, és az értéke 10 perc, az érvényesítési kezdődik, 1:10 óra.<br/><br/>Vegye figyelembe, hogy ez a beállítás nincs hatással szeletek a múltban. A szeletek **szelet befejezésének** + **dataDelay** < **most** dolgoznak fel késedelem nélkül.<br/><br/>Időpontokban nagyobb, mint 23:59 óra kell használatával adhatók meg a `day.hours:minutes:seconds` formátumban. Például adja meg a 24 órát, ne használja 24:00:00. Ehelyett használjon 1.00:00:00. Ha 24:00:00 használja, akkor a rendszer 24 napos (24.00:00:00). 1 nap és 4 óra adja meg 1:04:00:00. |Nem |0 |
| RetryInterval |A várakozási idő hiba és a következő kísérlet között. A beállítás jelenlegi idő vonatkozik. Ha az előző sikertelen, a következő kísérlet után van-e a **retryInterval** időszak. <br/><br/>Ha 1:00 PM most, az első lépések az első próbálkozás. Ha az első ellenőrzési ellenőrzés időtartam 1 perc és a művelet sikertelen volt, a következő újrapróbálkozási jelenleg 1:00 + 1 perc (időtartam) + 1 perces (újrapróbálkozási időköz) = 1:02 PM. <br/><br/>A múltban szeletek nincs késleltetés. Az újrapróbálkozási azonnal történik. |Nem |00:01:00 (1 perc) |
| retryTimeout |Az egyes újrapróbálkozások időkorlátját.<br/><br/>Ha ez a tulajdonság 10 percre van beállítva, az érvényesítési 10 percen belül kell végrehajtani. Ha az érvényesítés végrehajtásához 10 percnél hosszabb ideig tart, az ismételt próbálkozás túllépi az időkorlátot.<br/><br/>Ha az érvényesítés időkorlát összes kísérleteit, a szelet van megjelölve, **időtúllépésbe került**. |Nem |00:10:00 (10 perc) |
| maximumRetry |A száma a rendelkezésre állási, a külső adatok kereséséhez. A megengedett maximális érték: 10. |Nem |3 |


## <a name="create-datasets"></a>Adatkészletek létrehozása
Az ezen eszközök vagy az SDK-k adatkészletek hozhat létre: 

- Másolás varázsló 
- Azure Portal
- Visual Studio
- PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

Az alábbi oktatóanyagok lépésenkénti adatcsatornákat és adathalmazokat egyikével a következő eszközök, illetve az SDK-k létrehozásához lásd:
 
- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Adatok mozgása tevékenységgel folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Miután egy folyamat létrehozása és telepítése, kezelése, és a folyamatok figyelése az Azure portál paneleken, vagy a figyelés és a felügyeleti alkalmazás használatával. A következő témakörök részletes utasításokat: 

- [Figyelheti és folyamatok kezelése az Azure portál paneleken használatával](data-factory-monitor-manage-pipelines.md)
- [Figyelheti és kezelheti a kimenetátirányítási mechanizmusát használó műveletekről a figyelés és felügyelet alkalmazással](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Hatókört használó adatkészletek
Létrehozhat, amelyek egy folyamat használatával hatóköre adatkészletek a **adatkészletek** tulajdonság. Ezek az adatkészletek csak akkor használható, ez az adatcsatorna tevékenységét, nem pedig más folyamatok tevékenységek. A következő példa egy folyamat (InputDataset-rdc és OutputDataset-rdc) a feldolgozási sor belül két adatkészletekkel határozza meg.  

> [!IMPORTANT]
> Egyszeri adatcsatornák csak a hatókörön belüli adatkészletek támogatottak (ahol **pipelineMode** értéke **OneTime**). Lásd: [Onetime csővezeték](data-factory-create-pipelines.md#onetime-pipeline) részleteiről.
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
- Folyamatok kapcsolatos további információkért lásd: [hozzon létre adatcsatornák](data-factory-create-pipelines.md). 
- Hogyan adatcsatornák ütemezett és végrehajtott kapcsolatos további információkért lásd: [ütemezés és a végrehajtása az Azure Data Factory](data-factory-scheduling-and-execution.md). 
