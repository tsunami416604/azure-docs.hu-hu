---
title: Data Factory-függvények és rendszerváltozók |} A Microsoft Docs
description: Azure Data Factory-függvények és rendszerváltozók listáját tartalmazza
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5293c44a3e4494593e069ab45fbc38806c6999ee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57976776"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Az Azure Data Factory - függvények és rendszerváltozók
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [rendszerváltozók adat-előállítóban](../control-flow-system-variables.md).

Ez a cikk a functions és az Azure Data Factory által támogatott változók kapcsolatos információkat.

## <a name="data-factory-system-variables"></a>Data Factory rendszerváltozók

| Változó neve | Leírás | Forrásobjektum hatóköre | JSON-hatókör és alkalmazási helyzetek |
| --- | --- | --- | --- |
| WindowStart |Időtartam a jelenlegi művelet futtatása ablak kezdete |tevékenység |<ol><li>Adja meg az adatkijelölési lekérdezésekben. Tekintse meg a hivatkozott összekötő cikkeket a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk.</li> |
| WindowEnd |A jelenlegi művelet futtatása ablak időintervallum végén |tevékenység |ugyanaz, mint WindowStart. |
| SliceStart |Adatok szelet előállítása időtartam kezdete |tevékenység<br/>Adatkészlet |<ol><li>Adja meg a dinamikus mappa elérési útját, és használata során fájlneveket [Azure Blob](data-factory-azure-blob-connector.md) és [fájlrendszer adatkészletek](data-factory-onprem-file-system-connector.md).</li><li>Adja meg a bemeneti függőségek a data factory függvények a tevékenység bemeneti gyűjteményében.</li></ol> |
| SliceEnd |Az aktuális adatszelet időintervallum végén. |tevékenység<br/>Adatkészlet |ugyanaz, mint a SliceStart. |

> [!NOTE]
> Jelenleg a data factory megköveteli, hogy az ütemezés a tevékenységben megadott pontosan megegyezik-e a rendelkezésre állás, a kimeneti adatkészlet a megadott ütemezés. Ezért WindowStart, a WindowEnd, és a SliceStart és a SliceEnd mindig képezze le az azonos időtartam és a egy egyetlen kimeneti szelet.
> 

### <a name="example-for-using-a-system-variable"></a>Példa egy Rendszerváltozó használatával
Az alábbi példa, év, hónap, nap, és az idő **SliceStart** külön által használt változók kinyert **folderPath** és **fileName** tulajdonságait.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory-funkciók
Használhatja függvények és rendszerváltozók adat-előállítóban az alábbi célokra:

1. Adja meg az adatkijelölési lekérdezésekben (által hivatkozott összekötő cikkekben talál a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk.
   
   A data factory függvény hívása szintaxisa a következő: **$$ <function>** adatkijelölési lekérdezésekben és egyéb tulajdonságok a tevékenységről, és adatkészletek.  
2. A bemeneti függőségek megadása a data factory függvények a tevékenység bemeneti gyűjteményében.
   
    $$ bemeneti függőségi kifejezés megadása nem szükséges.     

Az alábbi minta **sqlReaderQuery** JSON-fájlban tulajdonság által visszaadott értéket van rendelve a `Text.Format` függvény. Ez a minta is használ egy Rendszerváltozó nevű **WindowStart**, amely jelöli, hogy a tevékenység-végrehajtásonként ablak kezdési idejét.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Lásd: [egyéni dátum- és időformátum karakterláncokat](https://msdn.microsoft.com/library/8kb3ddd4.aspx) témakör, amely ismerteti a használható különböző formázási beállítások (például: éééé és pi). 

### <a name="functions"></a>Functions
Az alábbi táblázatok sorolják fel az Azure Data Factoryban összes funkció áttekintésével:

| Kategória | Függvény | Paraméterek | Leírás |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Hozzáadja az adott idő alatt X Y óra. <br/><br/>Például: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |X Y perc hozzá.<br/><br/>Például: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: Dátum és idő |Lekérdezi a kezdési időpontot az X óra összetevőjét által jelölt óra. <br/><br/>Például: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Dátum |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |X, Y napot ad. <br/><br/>Példa: 9/15/2013 du. 12:00:00 + 2 nap = 9/17/2013 12:00:00 PM.<br/><br/>Akkor is napok kivonása túl negatív számként Y megadásával.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Dátum |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |X Y hónap hozzáadása.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Hónap túl kivonása Y-t egy negatív szám adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Dátum |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Y hozzáadja * X 3 hónap.<br/><br/>Például: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Dátum |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Y hozzáadja * X 7 napig<br/><br/>Példa: 9/15/2013 du. 12:00:00 + 1 hét = 9/22-es/2013 12:00:00 PM<br/><br/>Hét túl kivonása Y-t egy negatív szám adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Dátum |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |X Y év hozzáadása.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Év túl kivonása Y-t egy negatív szám adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Dátum |Day(X) |X: DateTime |A nap összetevőt az x beolvasása.<br/><br/>Példa: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Dátum |DayOfWeek(X) |X: DateTime |A hét napja összetevőjét x beolvasása.<br/><br/>Példa: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Dátum |DayOfYear(X) |X: DateTime |A nap X év összetevőjét által jelölt év beolvasása.<br/><br/>Példák:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Dátum |DaysInMonth(X) |X: DateTime |A hónap összetevőt X paraméter által jelölt hónap napjainak beolvasása.<br/><br/>Példa: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Dátum |EndOfDay(X) |X: DateTime |Lekérdezi a nap (napi összetevő) x záró dátuma és időpontja.<br/><br/>Példa: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Dátum |EndOfMonth(X) |X: DateTime |A hónap összetevőjét X paraméter által jelölt hónap végén kapja. <br/><br/>Példa: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (dátum és időpont, szeptember hónap végét jelölő) |
| Dátum |StartOfDay(X) |X: DateTime |Lekérdezi a nap összetevőt X paraméter által jelölt nap kezdete.<br/><br/>Példa: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |FROM(X) |X: String |Egy dátum-idő X karakterláncot elemezni. |
| DateTime |Ticks(X) |X: DateTime |Lekérdezi a órajel során végbemenő X paraméter tulajdonságát. Egy osztásjelek 100 nanoszekundumban egyenlő. Ez a tulajdonság értékét a számát, amely 12:00:00 éjféltől. január 1. óta eltelt 0001 jelöli. |
| Szöveg |Format(X) |X: Karakterlánc-változóhoz |Formázza a szöveget (használata `\\'` escape-kombinációt `'` karakter).|

> [!IMPORTANT]
> Egy másik függvényen belül a funkció használatakor nem kell használni **$$** a belső függvény előtag. For example: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' and RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Ebben a példában láthatja, hogy **$$** előtag nem használatos a **Time.AddHours** függvény. 

#### <a name="example"></a>Példa
A következő példa a Hive-tevékenység bemeneti és kimeneti paraméterek használatával határozza a `Text.Format` függvény és a SliceStart rendszerváltozóhoz. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>2. példa

A következő példában a dátum és idő paraméter a tárolt eljárási tevékenység határozza meg a szöveg használatával. Függvény, és a SliceStart változó formátumban. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>3. példa
Helyett a SliceStart által jelölt nap az előző nap adatainak olvasására, használja a napokHozzaadasa függvény a következő példában látható módon: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Lásd: [egyéni dátum- és időformátum karakterláncokat](https://msdn.microsoft.com/library/8kb3ddd4.aspx) témakör, amely ismerteti a használható különböző formázási beállítások (például: nn és éééé). 

