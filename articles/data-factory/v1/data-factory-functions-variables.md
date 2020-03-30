---
title: Adatfeldolgozó függvények és rendszerváltozók
description: Az Azure Data Factory függvényeinek és rendszerváltozóinak listáját tartalmazza
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73667654"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory - Függvények és rendszerváltozók
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el [a Rendszerváltozók a Data Factory szolgáltatásban című témakört.](../control-flow-system-variables.md)

Ez a cikk az Azure Data Factory által támogatott függvényekről és változókról nyújt tájékoztatást.

## <a name="data-factory-system-variables"></a>Data Factory rendszerváltozók

| Változó neve | Leírás | Objektumhatókör | JSON hatóköre és használati esetei |
| --- | --- | --- | --- |
| Ablakkezdete |Az aktuális tevékenységfuttatási ablak időintervallumának kezdete |tevékenység |<ol><li>Adja meg az adatkijelölési lekérdezéseket. Tekintse meg az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkben hivatkozott összekötő cikkeket.</li> |
| Ablakvége |Az aktuális tevékenységfuttatási ablak időintervallumának vége |tevékenység |ugyanaz, mint a WindowStart. |
| SliceStart |Az előállított adatszelet időintervallumának kezdete |tevékenység<br/>Adatkészlet |<ol><li>Az [Azure Blob](data-factory-azure-blob-connector.md) és a File System adatkészleteivel való munka során adja meg a dinamikus mappaelérési utakat és [fájlneveket.](data-factory-onprem-file-system-connector.md)</li><li>Adja meg a bemeneti függőségeket adatfeldolgozó függvényekkel a tevékenységbemenetek gyűjteményében.</li></ol> |
| Szeletvége |Az aktuális adatszelet időintervallumának vége. |tevékenység<br/>Adatkészlet |ugyanaz, mint a SliceStart. |

> [!NOTE]
> Jelenleg az adat-előállító megköveteli, hogy a tevékenységben megadott ütemezés pontosan megegyezik a kimeneti adatkészlet rendelkezésre állásában megadott ütemezés. Ezért a WindowStart, a WindowEnd, a SliceStart és a SliceEnd mindig ugyanarra az időszakra és egyetlen kimeneti szeletre van leképezve.
> 

### <a name="example-for-using-a-system-variable"></a>Példa rendszerváltozó használatára
A következő példában a **SliceStart** évét, hónapját, napját és idejét a **folderPath** és **a fileName** tulajdonságok által használt különálló változókba bontják ki.

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

## <a name="data-factory-functions"></a>Adatgyári függvények
Az adat-előállító függvények és a rendszerváltozók a következő célokra használhatók:

1. Adatkijelölési lekérdezések megadása (lásd az [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkben hivatkozott összekötő cikkeket.
   
   Az adat-előállító függvény meghívásához a következő szintaxist kell adni: ** $$ \<függvény>** adatválasztó lekérdezésekhez és a tevékenység és az adatkészletek egyéb tulajdonságaihoz.  
2. Bemeneti függőségek megadása adatfeldolgozó függvényekkel a tevékenységbemenetek gyűjteményében.
   
    A bemeneti függőségi kifejezések megadásához nincs szükség $$ dollárra.     

A következő példában egy JSON-fájl **sqlReaderQuery** tulajdonsága a `Text.Format` függvény által visszaadott értékhez van rendelve. Ez a minta egy **WindowStart**nevű rendszerváltozót is használ, amely a tevékenységfuttatási ablak kezdési idejét jelöli.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Lásd: [Egyéni dátum- és időformátum-karakterláncok](https://msdn.microsoft.com/library/8kb3ddd4.aspx) témakör, amely a különböző használható formázási beállításokat ismerteti (például: ay vs. yyyy). 

### <a name="functions"></a>Functions
Az alábbi táblázatok az Azure Data Factory összes funkcióját felsorolják:

| Kategória | Függvény | Paraméterek | Leírás |
| --- | --- | --- | --- |
| Time |Idő hozzáadása(X;Y) |X: Dátumidő <br/><br/>Y: int |Y órát ad hozzá az adott x időhöz. <br/><br/>Például: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |Perchozzáadásak(X;Y) |X: Dátumidő <br/><br/>Y: int |Y perc hozzáadása X-hez.<br/><br/>Például: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: Dátumidő |Az X óra összetevőjével képviselt óra kezdési idejét kapja meg. <br/><br/>Például: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Dátum |Adddays(X;Y) |X: Dátumidő<br/><br/>Y: int |Y nap hozzáadása X-hez. <br/><br/>Példa: 2013.09.15. 12:00:00 DU + 2 nap = 2013.17.9.<br/><br/>A napokat is kivonhatja, ha az Y értéket negatív számként adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Dátum |Addmonths(X;Y) |X: Dátumidő<br/><br/>Y: int |Y hónapot ad az X-hez.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>A hónapokat is kivonhatja, ha az Y értéket negatív számként adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Dátum |Negyedek hozzáadása(X;Y) |X: Dátumidő <br/><br/>Y: int |Y * 3 hónapot ad az X-hez.<br/><br/>Például: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Dátum |Addweeks(X;Y) |X: Dátumidő<br/><br/>Y: int |Y * 7 napot ad hozzá X-hez<br/><br/>Példa: 2013.09.15. 12:00:00 DU + 1 hét = 2013.22.9. 12:00:00<br/><br/>A heteket is kivonhatja, ha az Y értéket negatív számként adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Dátum |Addyears(X;Y) |X: Dátumidő<br/><br/>Y: int |Y évet ad az X-hez.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Az éveket is kivonhatja, ha az Y értéket negatív számként adja meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Dátum |Nap(X) |X: Dátumidő |Megkapja az X napi komponensét.<br/><br/>Példa: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Dátum |Hétköznap(x) |X: Dátumidő |Megkapja a hét napja komponens X.<br/><br/>Példa: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Dátum |Év napja (x) |X: Dátumidő |Az X év összetevője által képviselt év napját kapja.<br/><br/>Példák:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Dátum |DaysInMonth(X) |X: Dátumidő |A hónap azon napjait kapja, amelyeket az X paraméter hónap eleme képvisel.<br/><br/>Példa: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Dátum |Zárónap(X) |X: Dátumidő |Leképezi az X nap végét (nap komponensét) jelző dátumot.Gets the date-time that represents the end of the day (day component) of X.<br/><br/>Példa: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Dátum |Hónap vége(X) |X: Dátumidő |A hónap végét az X paraméter hónap-összetevője jelöli. <br/><br/>Példa: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (a szeptember végi dátumot jelző dátum) |
| Dátum |Kezdőnap(X) |X: Dátumidő |A nap kezdetét az X paraméter napi összetevője jelöli.<br/><br/>Példa: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: Karakterlánc |Az X karakterlánc elemzése dátumidőre. |
| DateTime |Kullancsok(X) |X: Dátumidő |Leálja az X paraméter ticks tulajdonságát. Egy kullancs 100 nanoszekundumnak felel meg. A tulajdonság értéke a 0001. |
| Szöveg |Formátum(X) |X: Karakterlánc változó |Formázza a `\\'` szöveget `'` (kombinációval kikerüli a karaktert).|

> [!IMPORTANT]
> Ha egy függvényt egy másik függvényen **$$** belül használ, nem kell előtagot használnia a belső függvényhez. Például: $$Text.Format('PartitionKey \\eq\\'my_pkey_filter_value' \\és RowKey ge '{0: yyyy-MM-dd HH:mm:ss}\\',Time.AddHours(SliceStart, -6)). Ebben a példában **$$** figyelje meg, hogy a **Time.AddHours** függvényhez nem használatos előtag. 

#### <a name="example"></a>Példa
A következő példában a Hive-tevékenység bemeneti és kimeneti paramétereit a függvény és a `Text.Format` SliceStart rendszerváltozó segítségével határozzák meg. 

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

A következő példában a Tárolt eljárási tevékenység DateTime paraméterét a Szöveg határozza meg. Format függvény és a SliceStart változó. 

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
Ha a SliceStart által megjelenített nap helyett az előző nap adatait szeretné olvasni, használja az AddDays függvényt az alábbi példában látható módon: 

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

Lásd: [Egyéni dátum- és időformátum-karakterláncok](https://msdn.microsoft.com/library/8kb3ddd4.aspx) témakör, amely a különböző használható formázási beállításokat ismerteti (például: yy vs. yyyyy). 

