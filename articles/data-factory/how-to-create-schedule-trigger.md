---
title: "Ütemezés eseményindítók létrehozása az Azure Data Factory |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet indítót létrehozni az Azure Data Factoryben, amelyek egy folyamat ütemezés szerint futtatott."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Egy eseményindítót, amelyek ütemezés szerint fut, a folyamat létrehozása
Ez a cikk és nyújt információt az ütemezés eseményindító létrehozása, indítsa el, és egy eseményindító figyelése. Eseményindítók más típusú, lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="schedule-trigger-json-definition"></a>Ütemezés eseményindító JSON-definícióból
Ütemezés eseményindítót hoz létre, amikor az ütemezés és a JSON használatával, ahogy a jelen szakaszban ismertetett példa az ismétlődési is megadhat.

Ahhoz, hogy az ütemezés eseményindító futtatni egy folyamatot indítsa, vegye fel az adott folyamatának csővezeték hivatkozást az eseményindító definícióját. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A **paraméterek** tulajdonság megadása a **folyamatokon** belül kötelező. Még abban az esetben is, ha az adott folyamat nem használ semmilyen paramétert, vegyen fel egy üres JSON-objektumot a paraméterek közé, mivel a tulajdonságnak léteznie kell.


### <a name="overview-schedule-trigger-schema"></a>Áttekintés: Ütemezés eseményindító séma
Az alábbi táblázat magas szintű áttekintést nyújt az eseményindítókkal kapcsolatos ismétlődés és ütemezés fő elemeiről:

JSON-tulajdonság |     Leírás
------------- | -------------
startTime | A startTime egy dátum és idő. Egyszerű ütemezéseknél a startTime az első előfordulás. Összetett ütemezéseknél az eseményindító nem indul korábban, mint a startTime.
endTime | Az eseményindító befejező dátumát határozza meg. A rendszer ezt az időpontot követően nem hajtja végre az eseményindítót. Múlt idejű endTime nem érvényes. Ez a tulajdonságot nem kötelező megadni.
timeZone | Jelenleg csak az egyezményes világidő (UTC) használata támogatott.
recurrence | A recurrence objektum adja meg az eseményindítóhoz tartozó ismétlődési szabályokat. A recurrence objektum a következő elemeket támogatja: frequency, interval, endTime, count és schedule. Ha a recurrence meg van adva, a frequency elemet meg kell adni, a recurrence többi eleme választható.
frequency | Az eseményindító ismétlődésének gyakorisági egységét jelöli. A támogatott értékek a következők: `minute`, `hour`, `day`, `week` vagy `month`.
interval | Az interval egy pozitív egész szám. Az eseményindító futásának gyakoriságát meghatározó frequency időközét jelöli. Ha például az interval 3, a frequency pedig „week”, az eseményindító 3 hetente ismétlődik.
schedule | Egy frequency tulajdonsággal ellátott eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A schedule elemben szereplő módosítások alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Áttekintés: Ütemezés eseményindító séma alapértelmezett értékeket, korlátok és példák

JSON-név | Érték típusa | Kötelező? | Alapértelmezett érték | Érvényes értékek | Példa
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Karakterlánc | Igen | Nincs | ISO-8601 dátum-idők | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objektum | Igen | None | Recurrence objektum | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Szám | Nem | 1 | 1 és 1000 között. | ```"interval":10```
endTime | Karakterlánc | Igen | None | Egy jövőbeli időpontot jelölő dátum-idő | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objektum | Nem | None | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>A startTime részletes bemutatása
Az alábbi tábla bemutatja, hogyan befolyásolja a startTime egy eseményindító futását:

startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel
--------------- | --------------------------- | ------------------------
Múltbeli kezdési időpont | Kiszámítja a kezdőidőpontot követő első jövőbeli végrehajtási időpontot, és akkor fut.<p>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.</p><p>Lásd a táblázat alatti példát.</p> | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul. <p>Az azt követő végrehajtások az ismétlődési ütemezés alapján futnak</p>
Jövőbeli vagy aktuális kezdési időpont | Egyszer fut a megadott kezdési időpontban. <p>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.</p> | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<p>Az azt követő végrehajtások az ismétlődési ütemezés alapján futnak.</p>

Tekintsük át egy példán, mi történik, ha a startTime egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont `2017-04-08 13:00`, a startTime `2017-04-07 14:00`, és az eseményindító kétnaponta ismétlődik (a frequency értéke: day, az interval értéke 2). Figyeljük meg, hogy a startTime múltbeli időpont, és az aktuális időpont elé esik.

Ezen feltételek mellett az első végrehajtási időpont `2017-04-09 at 14:00`. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Tehát ebben az esetben a startTime `2017-04-07 at 2:00pm`, így a következő alkalom két napra esik ettől, azaz `2017-04-09 at 2:00pm`.

Az első végrehajtási időpont akkor is ez, ha a startTime `2017-04-05 14:00` vagy `2017-04-01 14:00`. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Azaz a következő időpontok `2017-04-11 at 2:00pm`, `2017-04-13 at 2:00pm`, majd `2017-04-15 at 2:00pm` stb.

Végül, ha az eseményindítóhoz tartozik ütemezés, és az ütemezésben nincsen megadva az óra és/vagy perc, a rendszer alapértelmezés szerint az első végrehajtás megfelelő óra/perc értékeit használja.

### <a name="deep-dive-schedule"></a>A schedule részletes bemutatása
A schedule egyrészt korlátozhatja a eseményindító végrehajtásainak számát. Ha például egy „month” gyakoriságú eseményindítóhoz csak a 31. napon futó ütemezés van megadva, akkor az eseményindító csak a 31 napos hónapokban fut.

Ezzel szemben az ütemezés növelheti is az eseményindító végrehajtásának számát. Ha például egy „month” gyakoriságú eseményindítóhoz a hónap 1. és 2. napján futó ütemezés van megadva, akkor az eseményindító minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több ütemezési elem is meg van adva, a kiértékelési sorrend a legnagyobbtól halad a legkisebb felé: hét száma, hónap napja, hét napja, óra és perc.

A következő táblázat részletesen ismerteti a schedule elemeit:


JSON-név | Leírás | Érvényes értékek
--------- | ----------- | ------------
minutes | Az óra azon perce, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
hours | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
weekDays | A hét azon napjai, amelyeken az eseményindító fut. Csak heti gyakoriság mellett adható meg. | <ul><li>Hétfő, kedd, szerda, csütörtök, péntek, szombat vagy vasárnap</li><li>Az értékek bármelyikének tömbje (a tömb maximális mérete: 7)</li></p>Nem különbözteti meg a kis- és nagybetűket</p>
monthlyOccurrences | Meghatározza, hogy az eseményindító a hónap mely napjain fusson le. Csak havi gyakoriság mellett adható meg. | monthlyOccurence objektumok tömbje: `{ "day": day,  "occurrence": occurence }`. <p> A „day” a hét azon napja, amelyen az eseményindító lefut. A `{Sunday}` például a hónap minden vasárnapja. Kötelező.<p>Az „occurrence ” az adott nap előfordulása a hónapban. A `{Sunday, -1}` például a hónap utolsó vasárnapja. Választható.
monthDays | A hónap azon napja, amelyen az eseményindító lefut. Csak havi gyakoriság mellett adható meg. | <ul><li>Bármilyen érték -1 és -31 között</li><li>Bármilyen érték 1 és 31 között</li><li>Értékek tömbje</li>


## <a name="examples-recurrence-schedules"></a>Példák: ismétlődésütemezések
Ez a szakasz az ismétlődésütemezésekre mutat példákat, az ütemezési objektumra és annak alelemeire koncentrálva.

A példában szereplő ütemezések azt feltételezik, hogy az időköz értéke 1. Feltételezzük azt is, hogy a gyakoriság megfelelő összhangban van az ütemezéssel – nem használható például „day” gyakoriság, ha az ütemezést „monthDays” értékre módosítjuk. Ezek a korlátozások az előző szakaszban található táblázatban szerepelnek.

Példa | Leírás
------- | -----------
`{"hours":[5]}` | Minden nap 05:00-kor fut le
`{"minutes":[15], "hours":[5]}` | Minden nap 05:15-kor fut le
`{"minutes":[15], "hours":[5,17]}` | Minden nap 05:15-kor és 17:15-kor fut le
`{"minutes":[15,45], "hours":[5,17]}` | Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le
`{"minutes":[0,15,30,45]}` | 15 percenként fut le
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le. Ez az eseményindító óránként fut le. A perc változót a „startTime” paraméter vezérli, ha meg van adva, vagy ha nincs, akkor a létrehozás ideje. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor. Ez az ütemezés egyenértékű azzal, mint ha egy „hour” gyakoriságú eseményindítónk lenne, 1-es időközzel, ütemezés nélkül. A különbség az, hogy ez az ütemezés eltérő gyakorisággal és időközzel is használható további eseményindítók létrehozásához. Ha például a gyakoriság „month” lenne, az ütemezés csak havonta egyszer futna le, nem naponta, amikor a gyakoriság „day” lenne.
`{"minutes":[0]}` | Minden óra kezdetén lefut. Ez az eseményindító szintén óránként fut le, az adott óra kezdetén (például 12:00-kor, 01:00-kor, 02:00-kor stb.). Ez a beállítás egyenértékű egy „hour ” gyakoriságú eseményindítóval, nulla perces startTime paraméterrel, ütemezés nélkül, ha a gyakoriság „day” lenne. Ha viszont a gyakoriság „week” vagy „month” értékű, az ütemezés hetente vagy havonta csak egyetlen napon futna le.
`{"minutes":[15]}` | Minden óra 15. percében fut le. Óránként fut le, 0:15-kor, 01:15-kor. 02:15-kor stb., egészen 22:15-ig és 23:15-ig.
`{"hours":[17], "weekDays":["saturday"]}` | Minden hét szombatján, 17:00-kor fut le
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden hét hétfőjén, szerdáján és péntekjén, 17:00-kor fut le
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden hét hétfőjén, szerdáján és péntekjén, 17:15-kor és 17:45-kor fut le
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le 09:00 és 16:45 között
`{"weekDays":["tuesday", "thursday"]}` | Keddenként és csütörtökönként, a megadott kezdési időpontban fut le.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap 28. napján, 06:00-kor fut le (havi gyakoriságot feltételezve)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Minden hónap utolsó napján, 06:00-kor fut le. Ha az adott eseményindítót a hónap utolsó napján kívánja futtatni, a 28., 29., 30. vagy 31. nap megadása helyett használjon -1-et.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján, 06:00-kor fut le
`{monthDays":[1,14]}` | Minden hónap első és 14. napján, a megadott kezdési időpontban fut le.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, 05:00-kor fut le
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, a megadott kezdési időpontban fut le.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Minden hónap végétől számított harmadik pénteken, a megadott kezdési időpontban fut le
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, 05:15-kor fut le
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, a megadott kezdési időpontban fut le
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Minden hónap ötödik péntekjén, a megadott kezdési időpontban fut le. Ha az adott hónapban nincs ötödik péntek, a folyamat nem fut le, mivel kizárólag a minden ötödik pénteken történő futásra van ütemezve.  Ha az eseményindítót a hónap utolsó péntekjén kívánja futtatni, az előfordulás esetében 5 helyett inkább használjon -1-et.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Minden hónap utolsó péntekjén, 15 percenként fut le.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Minden hónap harmadik szerdáján 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le.


## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ez a szakasz bemutatja, hogyan lehet Azure PowerShell használatával létrehozása, indítsa el, és figyelheti a Feladatütemező eseményindítót. Ha azt szeretné, hogy ez a minta működő, először halad át a [gyors üzembe helyezés: hozzon létre egy adat-előállítót az Azure PowerShell](quickstart-create-data-factory-powershell.md). Ezt követően adja hozzá a következő kódot a fő metódus, amely a metódus létrehozza és elindítja egy ütemezés eseményindítót, amely 15 percenként fut. Az eseményindító társítva egy folyamat (**Adfv2QuickStartPipeline**) az Ön által létrehozott a gyors üzembe helyezés részeként.

1. Hozzon létre egy JSON fájlt MyTrigger.json a C:\ADFv2QuickStartPSH\ mappában, a következő tartalommal:

    > [!IMPORTANT]
    > Állítsa be **startTime** a jelenlegi UTC-időre és **endTime** egy óra korábbi a jelenlegi UTC-idő a JSON-fájl mentése előtt.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    A JSON kódtöredékre:
    - A **típus** az eseményindító értéke **ScheduleTrigger**.
    - A **gyakoriság** értéke **perc** és **időköz** értéke **15**. Ezért az eseményindító a feldolgozási sor 15 percenként fut. kezdési és befejezési időpontjai között.
    - A **endTime** van egy órával azután a **startTime**, így a trigger futtatja a feldolgozási sor 15 perc, 30 percet, és a kezdő időpont után 45 percig. Ne felejtse el frissíteni a kezdő időpont a jelenlegi UTC idő szerint, és az EndTime megadása a startTime elmúlt egy óra.  
    - Az eseményindító társítva van a **Adfv2QuickStartPipeline** folyamat. Több folyamatok társítandó eseményindítót, adja hozzá több **pipelineReference** szakaszok.
    - A kimenetátirányítási mechanizmusával a gyors üzembe helyezési időt vesz igénybe, két **paraméterek**. Ezek a paraméterek értékeit, ezért a eseményindítóval át.
2. Egy eseményindító létrehozása a **Set-AzureRmDataFactoryV2Trigger** parancsmag.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Győződjön meg arról, hogy az állapot az eseményindító **leállítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmag.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Az eseményindító használatával indítsa el a **Start-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Győződjön meg arról, hogy az állapot az eseményindító **elindítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmag.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Első eseményindító fut, a PowerShell használatával a **Get-AzureRmDataFactoryV2TriggerRun** parancsmag. Ahhoz, hogy az információkat az eseményindító fut, a következő parancsot rendszeresen: frissítés **TriggerRunStartedAfter** és **TriggerRunStartedBefore** az eseményindító-definícióban szereplő értékekre értékek .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Eseményindító fut/folyamat fut az Azure portálon figyeléséről lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>A .NET SDK használata
Ez a szakasz bemutatja, hogyan használhatja a .NET SDK létrehozását, indítsa el, és egy eseményindító megfigyelését. Ha azt szeretné, hogy működik-e ezt a kódot, először halad át a [.NET SDK használatával adat-előállító létrehozása a gyors üzembe helyezés](quickstart-create-data-factory-dot-net.md). Ezt követően adja hozzá a következő kódot a fő metódus, amely a metódus létrehozza és elindítja egy ütemezés eseményindítót, amely 15 percenként fut. Az eseményindító társítva egy folyamat (**Adfv2QuickStartPipeline**) az Ön által létrehozott a gyors üzembe helyezés részeként.

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Futtatás eseményindítót figyelő, vegye fel a következő kódot az utolsó `Console.WriteLine` utasítást:

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Eseményindító fut/folyamat fut az Azure portálon figyeléséről lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>Python SDK használata
Ez a szakasz bemutatja, hogyan használható Python SDK létrehozása, indítsa el, és egy eseményindító figyelése. Ha azt szeretné, hogy működik-e ezt a kódot, először halad át a [Python SDK használatával adat-előállító létrehozása a gyors üzembe helyezés](quickstart-create-data-factory-python.md). Adja hozzá a következő kódrészletet a "figyelése a Futtatás futószalag" kódblokk, a python-parancsfájl után. Ez a kód egy eseményindítót hoz létre ütemezés, amely 15 percenként fut. az adott kezdő és befejező időpontok között. Frissítse a start_time a jelenlegi UTC-idő és a end_time korábbi a jelenlegi UTC idő szerint egy óra.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Eseményindító fut/folyamat fut az Azure portálon figyeléséről lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Használja a Resource Manager-sablon
Az Azure Resource Manager-sablon segítségével hozható létre eseményindító. Részletes útmutatásért lásd: [létrehozása egy Resource Manager-sablon használata az Azure data factory](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Az eseményindító kezdési ideje átadni egy folyamat
1-es verziójával Azure Data Factory Olvasás vagy írás a particionált SliceStart/SliceEnd/WindowStart/WindowEnd rendszerváltozók használatával támogatott. A 2-es verzióját ez a viselkedés a paraméter értékének csővezeték paraméter és a trigger elem ütemezett időpont/kezdete segítségével érhet el. A következő példában az eseményindítót az ütemezett időpont kerülnek értékként a feldolgozási sor paraméter scheduledRunTime.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
További információkért lásd: [olvasására vagy írására, hogyan particionálva adatok](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>További lépések
Eseményindítók kapcsolatos részletes információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
