---
title: "Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan hajtható végre egy folyamat az Azure Data Factoryban akár igény szerint, akár eseményindító létrehozásával."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: c319979cce23da69965d4fbab037919461f67b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-scheduling-and-execution.md)
> * [2. verzió – Előzetes verzió](concepts-pipeline-execution-triggers.md)

A **folyamatfuttatás** az Azure Data Factory 2. verziójának egy szakkifejezése, amely egy folyamat-végrehajtás egy példányát határozza meg. Tegyük fel például, hogy rendelkezik egy délelőtt 8-kor, 9-kor és 10-kor végbemenő folyamattal. Ebben az esetben három külön folyamatfuttatás lenne. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. Ez az adott folyamatfuttatást egyedien meghatározó GUID. A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatokban meghatározott paraméterekhez történő továbbításával történik. Egy folyamat két módon hajtható végre: **manuálisan** vagy egy **eseményindítóval**. A cikk részletesen ismerteti a folyamatok végrehajtásának mindkét módját. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [ütemezés és végrehajtás a Data Factory 1. verziójában](v1/data-factory-scheduling-and-execution.md).

## <a name="run-pipeline-on-demand"></a>Folyamat futtatása igény szerint
Ennél a módszernél manuálisan futtatja a folyamatot. Tekinthető a folyamat igény szerinti végrehajtásának is. 

Tegyük fel például, hogy rendelkezik egy **copyPipeline** nevű folyamattal, amelyet végre szeretne hajtani. A folyamat egy egyszerű folyamat egyetlen tevékenységgel, amely egy, az Azure Blob Storage-ban lévő forrásmappából másol egy ugyanazon a tárterületen található célmappába. Itt látható a minta folyamatdefiníció: 

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
A folyamathoz két paraméter szükséges: sourceBlobContainer és sinkBlobContainer (amint az a JSON-definícióban látható). Ezekhez a paraméterekhez futtatás közben adja meg az értékeket. 

A folyamat manuális futtatásához az alábbi lehetőségeket használhatja: .NET, PowerShell, REST és Python. 

### <a name="rest-api"></a>REST API
Példa a REST-parancsra:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Teljes példa: [Gyors útmutató: adat-előállító létrehozása REST API használatával](quickstart-create-data-factory-rest-api.md).

### <a name="powershell"></a>PowerShell
Példa a PowerShell-parancsra: 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

A kérelem hasznos adatainak törzsébe adja át a paramétereket. .NET, Powershell és Python esetében az értékeket egy, a hívásnak argumentumként átadott szótárba adja át.

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

A válasz hasznos adat a folyamatfuttatás egyedi azonosítója:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


Teljes példa: [Gyors útmutató: adat-előállító létrehozása PowerShell használatával](quickstart-create-data-factory-powershell.md).

### <a name="net"></a>.NET 
Példa a .NET hívásra: 

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Teljes példa: [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> Használhatja a .NET API-t a Data Factory folyamatoknak az Azure Functionsból, saját webszolgáltatásaiból stb. való meghívásához.

## <a name="triggers"></a>Eseményindítók
Az eseményindítók a folyamatfuttatás végrehajtásának másik eszközei. Az eseményindítók olyan feldolgozási egységek, amelyek meghatározzák, hogy mikor kezdődjön egy folyamat végrehajtása. A Data Factory jelenleg a folyamatokat időpont-alapú ütemező szerint meghívó eseményindítókat támogatja. A neve **Ütemező eseményindító**. A Data Factory jelenleg nem támogatja az eseményalapú eseményindítókat, mint például azokat az eseményindítókat, amelyek egy fájlérkezés esetében indítják el a folyamatot.

A folyamatok és az eseményindítók között nincsen „n-m” kapcsolat. Egyetlen folyamatot több eseményindító is indíthat, és ugyanaz az eseményindító indíthat több folyamatot is. Az eseményindító alábbi JSON-definíciójában a **folyamatok** tulajdonság az egy adott eseményindító által aktivált folyamatok és a folyamat paramétereihez megadott értékek listájára vonatkozik.

### <a name="basic-trigger-definition"></a>Alap eseményindító meghatározása: 
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
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
```

## <a name="scheduler-trigger"></a>Ütemező eseményindító
Az ütemező eseményindító egy időpont-alapú ütemezőn futtatja a folyamatokat. Az eseményindító támogatja az időszakos és speciális naptárbeállításokat (hetente, hétfőn délután 5-kor és csütörtökön este 9-kor). Rugalmas, mert független az adatkészlet-mintázattól, és nem tesz különbséget az idősorozat-adatok és a nem idősorozat-adatok között.

### <a name="scheduler-trigger-json-definition"></a>Ütemező eseményindító JSON-definíciója
Amikor létrehoz egy ütemező eseményindítót, a JSON segítségével megadhatja az ütemezést és az ismétlődést a szakaszban található példában látható módon. 

Ahhoz, hogy az ütemező eseményindító kiváltsa egy folyamat indítását, az eseményindító meghatározásába foglalja bele az adott folyamat referenciáját. A folyamatok és az eseményindítók között „n-m” kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Ugyanaz az eseményindító elindíthat több folyamatot is.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": <<default UTC>>
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
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
                    "<parameter 2 Name> : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

### <a name="overview-scheduler-trigger-schema"></a>Áttekintés: az ütemező eseményindító sémája
Az alábbi táblázat magas szintű áttekintést nyújt az eseményindítókkal kapcsolatos ismétlődés és ütemezés fő elemeiről:

JSON-tulajdonság |     Leírás
------------- | -------------
startTime | A startTime egy dátum és idő. Egyszerű ütemezéseknél a startTime az első előfordulás. Összetett ütemezéseknél az eseményindító nem indul korábban, mint a startTime.
recurrence | A recurrence objektum adja meg az eseményindítóhoz tartozó ismétlődési szabályokat. A recurrence objektum a következő elemeket támogatja: frequency, interval, endTime, count és schedule. Ha a recurrence meg van adva, a frequency elemet meg kell adni, a recurrence többi eleme választható.
frequency | Az eseményindító ismétlődésének gyakorisági egységét jelöli. A támogatott értékek a következők: `minute`, `hour`, `day`, `week` vagy `month`.
interval | Az interval egy pozitív egész szám. Az eseményindító futásának gyakoriságát meghatározó frequency időközét jelöli. Ha például az interval 3, a frequency pedig „week”, az eseményindító 3 hetente ismétlődik.
endTime | Az eseményindító befejező dátumát határozza meg. A rendszer ezt az időpontot követően nem hajtja végre az eseményindítót. Múlt idejű endTime nem érvényes.
schedule | Egy frequency tulajdonsággal ellátott eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A schedule elemben szereplő módosítások alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál.

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Áttekintés: az ütemező eseményindító sémájának alapértékei, korlátai és példák

JSON-név | Érték típusa | Kötelező? | Alapértelmezett érték | Érvényes értékek | Példa
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Karakterlánc | Igen | None | ISO-8601 dátum-idők | ```"startTime" : "2013-01-09T09:30:00-08:00"```
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

Másrészt az ütemezés növelheti is az eseményindító végrehajtásának számát. Ha például egy „month” gyakoriságú eseményindítóhoz a hónap 1. és 2. napján futó ütemezés van megadva, akkor az eseményindító minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több ütemezési elem is meg van adva, a kiértékelési sorrend a legnagyobbtól halad a legkisebb felé: hét száma, hónap napja, hét napja, óra és perc.

A következő táblázat részletesen ismerteti a schedule elemeit:


JSON-név | Leírás | Érvényes értékek
--------- | ----------- | ------------
minutes | Az óra azon perce, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
hours | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
weekDays | A hét azon napjai, amelyeken az eseményindító fut. Csak heti gyakoriság mellett adható meg. | <ul><li>Hétfő, kedd, szerda, csütörtök, péntek, szombat vagy vasárnap</li><li>Az előző értékek bármelyikének tömbje (a tömb maximális mérete 7)</li></p>Nem különbözteti meg a kis- és nagybetűket</p>
monthlyOccurrences | Meghatározza, hogy az eseményindító a hónap mely napjain fusson le. Csak havi gyakoriság mellett adható meg. | monthlyOccurence objektumok tömbje: `{ "day": day,  "occurrence": occurence }`. <p> A „day” a hét azon napja, amelyen az eseményindító lefut. A `{Sunday}` például a hónap minden vasárnapja. Kötelező.<p>Az „occurrence ” az adott nap előfordulása a hónapban. A `{Sunday, -1}` például a hónap utolsó vasárnapja. Választható.
monthDays | A hónap azon napja, amelyen az eseményindító lefut. Csak havi gyakoriság mellett adható meg. | <ul><li>Bármilyen érték -1 és -31 között</li><li>Bármilyen érték 1 és 31 között</li><li>A fenti értékek tömbje</li>


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




## <a name="next-steps"></a>Következő lépések
Lásd az alábbi oktatóanyagokat: 

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
