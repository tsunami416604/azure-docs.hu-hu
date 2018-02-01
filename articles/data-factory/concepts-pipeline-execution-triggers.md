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
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: fc34cfbab796c6e1e4cd25ce13dcc63c39c6699d
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-scheduling-and-execution.md)
> * [2. verzió – Előzetes verzió](concepts-pipeline-execution-triggers.md)

A _folyamatfuttatás_ az Azure Data Factory 2. verziójának egy szakkifejezése, amely egy folyamat-végrehajtás egy példányát határozza meg. Tegyük fel például, hogy rendelkezik egy délelőtt 8:00-kor, 9:00-kor és 10:00-kor végbemenő folyamattal. Ebben az esetben három külön folyamatfuttatás lesz. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. Ez az adott folyamatfuttatást egyedien meghatározó GUID. A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatokban meghatározott paraméterekhez történő továbbításával történik. Egy folyamat két módon hajtható végre: manuálisan vagy egy _eseményindító_ használatával. A cikk részletesen ismerteti a folyamatok végrehajtásának mindkét módját.

> [!NOTE]
> Ez a cikk az Azure Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha az Azure Data Factory általánosan elérhető 1. verzióját használja, lásd: [Ütemezés és végrehajtás a Data Factory 1. verziójában](v1/data-factory-scheduling-and-execution.md).

## <a name="manual-execution-on-demand"></a>Manuális végrehajtás (igény szerint)
Egy folyamat manuális végrehajtását _igény szerinti_ végrehajtásnak nevezik.

Tegyük fel például, hogy rendelkezik egy **copyPipeline** nevű egyszerű folyamattal, amelyet végre szeretne hajtani. A folyamat egyetlen tevékenységgel rendelkezik, amely egy, az Azure Blob Storage-ban lévő forrásmappából másol egy ugyanazon a tárterületen található célmappába. A következő JSON-definíció ezt a mintafolyamatot mutatja be:

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

A JSON-definícióban a folyamathoz két paraméter szükséges: **sourceBlobContainer** és **sinkBlobContainer**. Ezekhez a paraméterekhez futtatás közben adja meg az értékeket.

A következő módszerekkel manuálisan futtathatja a folyamatot:
- A .NET SDK.
- Az Azure PowerShell-modul.
- A REST API.
- A Python SDK.

### <a name="the-rest-api"></a>A REST API
A következő mintaparancs bemutatja, hogyan futtathatja manuálisan a folyamatot a REST API használatával:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Teljes minta: [Gyors útmutató: adat-előállító létrehozása a REST API használatával](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell
A következő mintaparancs bemutatja, hogyan futtathatja manuálisan a folyamatot az Azure PowerShell használatával:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

A kérelem hasznos adatainak törzsébe adja át a paramétereket. A .NET SDK, az Azure Powershell és a Python SDK esetében az értékeket egy, a hívás argumentumaként átadott szótárba adja át:

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

Teljes minta: [Gyors útmutató: adat-előállító létrehozása az Azure PowerShell használatával](quickstart-create-data-factory-powershell.md).

### <a name="the-net-sdk"></a>A .NET SDK
A következő mintahívás bemutatja, hogyan futtathatja manuálisan a folyamatot a .NET SDK használatával:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Teljes minta: [Gyors útmutató: adat-előállító létrehozása a .NET SDK használatával](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> A .NET SDK-t Azure Data Factory-folyamatok meghívásához használhatja, többek között az Azure Functionsből vagy egy saját webszolgáltatásból stb.

<h2 id="triggers">Eseményindító végrehajtása</h2>
Az eseményindítók a folyamatfuttatás végrehajtásának másik eszközei. Az eseményindítók olyan feldolgozási egységek, amelyek meghatározzák, hogy mikor kezdődjön egy folyamat végrehajtása. Az Azure Data Factory jelenleg az eseményindítók két típusát támogatja:
- Ütemezési eseményindító: a folyamatokat időpont alapú ütemezés szerint meghívó eseményindító.
- Átfedésmentes ablakos eseményindító: az állapot megőrzése mellett, rendszeres időközönként működő eseményindító. Az Azure Data Factory jelenleg nem támogatja az eseményalapú eseményindítókat. Például egy fájlérkezési eseményre válaszoló folyamatfuttatás eseményindítóját.

A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egy folyamatot több eseményindító is indíthat, és egyetlen eseményindító indíthat több folyamatot is. Az eseményindító alábbi definíciójában a **folyamatok** tulajdonság az adott eseményindító által aktivált folyamatok listájára vonatkozik. A tulajdonság meghatározása a folyamatparaméterek értékeit is tartalmazza.

### <a name="basic-trigger-definition"></a>Alap eseményindító meghatározása

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

## <a name="schedule-trigger"></a>Ütemezési eseményindító
Az ütemezési eseményindító egy időpontalapú ütemezés szerint futtatja a folyamatokat. Ez az eseményindító támogatja az időszakos és speciális naptárbeállításokat. Az eseményindító például az ezekhez hasonló időközöket támogatja: „hetente” vagy „hétfőnként délután 5:00-kor és csütörtökönként este 9:00-kor”. Az ütemezési eseményindító rugalmas, mert az adatkészlet-mintázat független, és az eseményindító nem tesz különbséget az idősorozat-adatok és a nem idősorozat-adatok között.

Példák és további információ az ütemezési eseményindítókról: [Ütemezési eseményindító létrehozása](how-to-create-schedule-trigger.md).

## <a name="tumbling-window-trigger"></a>Átfedésmentes ablakos eseményindító
Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok. Példák és további információ az átfedésmentes ablakos eseményindítókról: [Átfedésmentes ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md).

## <a name="schedule-trigger-definition"></a>Ütemezési eseményindító meghatározása
Amikor létrehoz egy ütemezési eseményindítót, a JSON-definíció segítségével megadhatja az ütemezést és az ismétlődést. 

Ahhoz, hogy az ütemező eseményindító kiváltsa egy folyamat indítását, az eseményindító meghatározásába foglalja bele az adott folyamat referenciáját. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
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
> A **paraméterek** tulajdonság a **folyamatok** elem kötelező tulajdonsága. Ha a folyamat nem használ paramétert, akkor is vegyen fel egy üres JSON-definíciót a **paraméterek** tulajdonságba.

### <a name="schema-overview"></a>Séma áttekintése
Az alábbi táblázat magas szintű áttekintést nyújt az eseményindítóval kapcsolatos ismétlődés és ütemezés fő sémaelemeiről:

| JSON-tulajdonság | Leírás |
|:--- |:--- |
| **startTime** | Dátum-idő érték. Egyszerű ütemezések esetében a **startTime** tulajdonság értéke az első előfordulásra vonatkozik. Összetett ütemezéseknél az eseményindító nem indul korábban a megadott **startTime** értéknél. |
| **endTime** | Az eseményindító záró dátuma és időpontja. Az eseményindító nem lesz végrehajtva a megadott záró dátum és idő után. A tulajdonság értéke nem lehet múltbéli időpont. <!-- This property is optional. --> |
| **timeZone** | Az időzóna. Jelenleg csak az UTC időzóna támogatott. |
| **recurrence** | Az eseményindítóhoz tartozó ismétlődési szabályokat megadó recurrence objektum. A recurrence objektum a **frequency**, **interval**, **endTime**, **count** és **schedule** elemeket támogatja. Recurrence objektum meghatározásakor a **frequency** elem megadása kötelező. A recurrence objektum többi elemének megadása nem kötelező. |
| **frequency** | Az eseményindító ismétlődésének gyakorisági egysége. A támogatott értékek például: „minute”, „hour”, „day”, „week”, és „month” (percenként, óránként, naponta, hetente és havonta). |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **interval** 3, a **frequency** pedig „week”, az eseményindító 3 hetente ismétlődik. |
| **schedule** | Az eseményindító ismétlődési ütemezése. Egy megadott **frequency** értékkel rendelkező eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A **schedule** tulajdonságban az ismétlődéshez tartozó módosítások szerepelnek, amelyek alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál.

### <a name="schedule-trigger-example"></a>Példa ütemezési eseményindítóra

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Séma alapértékei, korlátai és példái

| JSON-tulajdonság | Típus | Kötelező | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Karakterlánc | Igen | Nincs | ISO-8601 dátum-idők | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Objektum | Igen | None | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Szám | Nem | 1 | 1–1000 | `"interval":10` |
| **endTime** | Karakterlánc | Igen | Nincs | Egy jövőbeli időpontot jelölő dátum-idő érték. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Objektum | Nem | Nincs | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázatban látható, hogy a **startTime** tulajdonság hogyan irányítja a folyamatfuttatást:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |
| Múltbeli kezdési időpont | Kiszámítja a kezdőidőpontot követő első jövőbeli végrehajtási időpontot, és abban az időpontban fut.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.<br/><br/>Lásd a táblázat alatti példát. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |
| Jövőbeli vagy aktuális kezdési időpont | Egyszer fut a megadott kezdési időpontban.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |

Tekintsünk meg egy példát arról, hogy mi történik, ha a kezdési időpont egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont `2017-04-08 13:00`, a kezdési idő `2017-04-07 14:00`, és az eseményindító kétnaponta ismétlődik. (A **recurrence** értéke a **frequency** tulajdonság „day” értékre és az **interval** tulajdonság 2 értékre történő állításával adható meg.) Figyelje meg, hogy a **startTime** értéke múltbeli időpont, és az aktuális időpont elé esik.

Ezen feltételek mellett az első végrehajtási időpont `2017-04-09 at 14:00`. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Tehát ebben a forgatókönyvben a kezdési időpont `2017-04-07 at 2:00pm`, így a következő alkalom két napra esik ettől, azaz `2017-04-09 at 2:00pm`.

Az első végrehajtási időpont akkor is ez, ha a **startTime** értéke `2017-04-05 14:00` vagy `2017-04-01 14:00`. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Ezért a következő végrehajtások a következő időpontokban történnek: `2017-04-11 at 2:00pm`, `2017-04-13 at 2:00pm` majd `2017-04-15 at 2:00pm` és így tovább.

Végül, ha az eseményindító ütemezésében nincs megadva az óra vagy perc, a rendszer alapértelmezés szerint az első végrehajtás óra vagy perc értékeit használja.

### <a name="schedule-property"></a>schedule tulajdonság
A schedule használata egyrészt korlátozhatja a eseményindító végrehajtásainak számát. Ha például egy havi gyakoriságú eseményindító csak a 31. napon való futásra van ütemezve, akkor az eseményindító csak a 31 napos hónapokban fut.

Ezzel szemben az ütemezés növelheti is az eseményindító végrehajtásának számát. Ha például egy havi gyakoriságú eseményindító a hónap 1. és 2. napján való futásra van ütemezve, akkor az eseményindító minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több **schedule** elem is meg van adva, a kiértékelési sorrend a legnagyobb ütemezési beállítástól halad a legkisebb felé. A kiértékelés a hét számával kezdődik, majd ezt követi a hónap napja, a hét napja, az óra és végül a perc.

A következő táblázat részletesen ismerteti a **schedule** elemeit:

| JSON-elem | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **minutes** | Az óra azon perce, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
| **hours** | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul> |
| **weekDays** | A hét azon napjai, amelyeken az eseményindító fut. Az érték csak heti gyakorisággal adható meg. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday (Hétfő, Kedd, Szerda, Csütörtök, Péntek, Szombat, Vasárnap)</li><li>A nap értékek tömbje (a tömb maximális értéke 7)</li><li>A nap értékek nem tesznek különbséget a kis- és nagybetű között</li></ul> |
| **monthlyOccurrences** | A hónap azon napjai, amelyeken az eseményindító fut. Az érték csak havi gyakorisággal adható meg. | <ul><li>A **monthlyOccurence** objektumok tömbje: `{ "day": day,  "occurrence": occurence }`.</li><li>A **day** attribútum a hét azon napja, amelyen az eseményindító fut. Például a `{Sunday}` értékű **day** attribútummal rendelkező **monthlyOccurrences** tulajdonság a hónap minden vasárnapját jelenti. A **day** attribútum megadása kötelező.</li><li>Az **occurrence** attribútum a megadott **day** attribútum előfordulása a hónapban. Például a `{Sunday, -1}` értékű **day** és **occurrence** attribútumokkal rendelkező **monthlyOccurrences** tulajdonság a hónap utolsó vasárnapját jelenti. Az **occurrence** attribútum megadása nem kötelező.</li></ul> |
| **monthDays** | A hónap azon napja, amelyen az eseményindító lefut. Az érték csak havi gyakorisággal adható meg. | <ul><li>Bármilyen érték -1 és -31 között</li><li>Bármilyen érték 1 és 31 között</li><li>Értékek tömbje</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Eseményindító-ismétlődési ütemezések példái
Ez a szakasz az ismétlődésütemezésekre mutat példákat, és a **schedule** objektumra és annak elemeire koncentrál.

Ez a példa azt feltételezi, hogy az **interval** értéke 1, és hogy a **frequency** értéke helyes, az ütemezés meghatározása szerint. Nem lehet például a **frequency** értéke „day”, ha a **schedule** objektum „monthDays” értékre lett módosítva. Ez és az ehhez hasonló korlátozások az előző szakaszban található táblázatban szerepelnek.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` | Minden nap 05:00-kor fut le. |
| `{"minutes":[15], "hours":[5]}` | Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` | Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` | Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` | 15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le. Ez az eseményindító óránként fut le. A perceket a **startTime** értéke szabályozza, ha az érték meg van adva. Ha az érték nincs megadva, akkor a perceket a létrehozás ideje szabályozza. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor.<br/><br/>Ez az ütemezés egyenértékű azzal, mintha egy „hour” értékű **frequency** attribútummal rendelkező eseményindító lenne, 1-es **interval** értékkel, **schedule** nélkül. Ez az ütemezés különböző **frequency** és **interval** értékekkel használható egyéb eseményindítók létrehozásához. Ha például a **frequency** értéke „month”, az ütemezés egy hónapban csak egyszer fut le, nem pedig minden nap, amikor a **frequency** értéke „day”. |
| `{"minutes":[0]}` | Minden óra kezdetén fut le. Ez az eseményindító minden óra kezdetén fut le, 00:00-kor, 01:00-kor, 02:00-kor és így tovább.<br/><br/>Ez az ütemezés megegyezik egy olyan eseményindítóval, amely „hour” **frequency** értékkel és nulla perc **startTime** értékkel rendelkezik, vagy nincs **schedule** érték, de a **frequency** értéke „day”. Ha a **frequency** értéke „week” vagy „month”, az ütemezés csak egy héten egyszer vagy egy hónapban egyszer fut le. |
| `{"minutes":[15]}` | Minden óra 15. percében fut le. Ez az eseményindító minden óra 15. percében fut le, 00:15-kor, 01:15-kor, 02:15-kor és így tovább, egészen 23:15-ig. |
| `{"hours":[17], "weekDays":["saturday"]}` | Minden héten szombaton, 17:00-kor fut le. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:00-kor fut le. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le 09:00 és 16:45 között. |
| `{"weekDays":["tuesday", "thursday"]}` | Keddenként és csütörtökönként, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap 28. napján, 06:00-kor fut le (azt feltételezi, hogy a **frequency** „month” értékre van állítva). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Minden hónap utolsó napján, 06:00-kor fut le. Ha az adott eseményindítót a hónap utolsó napján kívánja futtatni, a 28., 29., 30. vagy 31. nap megadása helyett használjon -1-et. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján, 06:00-kor fut le. |
| `{monthDays":[1,14]}` | Minden hónap első és 14. napján, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, 05:00-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | A hónap végétől számított harmadik pénteken, minden hónapban, a megadott kezdési időpontban fut le. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Minden hónap ötödik péntekjén, a megadott kezdési időpontban fut le. Ha az adott hónapban nincs ötödik péntek, a folyamat nem fut le, mivel kizárólag a minden ötödik pénteken történő futásra van ütemezve. Az eseményindító a hónap utolsó péntekjén való futtatásához az **occurrence** értékénél használjon -1-et 5 helyett. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="trigger-type-comparison"></a>Eseményindító-típusok összehasonlítása
Az átfedésmentes ablakos eseményindító és az ütemezési eseményindító is időszívverés alapján működik. Mi a különbség közöttük?

Az alábbi táblázatban az átfedésmentes ablakos eseményindító és az ütemezési eseményindító összehasonlítása látható:

|  | Átfedésmentes&nbsp;ablakos&nbsp;eseményindító | Ütemezési&nbsp;eseményindító |
|:--- |:--- |:--- |
| **Visszatöltési&nbsp;forgatókönyvek** | Támogatott. A folyamatfuttatások ütemezhetők múltbéli időszakokra. | Nem támogatott. A folyamatfuttatások csak az aktuális időszaktól vagy egy jövőbeli időszaktól hajthatók végre. |
| **Megbízhatóság** | 100%-os megbízhatóság. A folyamatfuttatások egy adott kezdési időponttól minden időszakhoz ütemezhetők, hézagok nélkül. | Kevésbé megbízható. |
| **Újrapróbálkozási&nbsp;képesség** | Támogatott. A sikertelen folyamatfuttatások alapértelmezett újrapróbálkozási szabályzata 0, vagy a felhasználó által az eseményindító definíciójában megadott szabályzat. Automatikusan újrapróbálkozik, ha a folyamatfuttatás egyidejűségi/kiszolgálói/szabályozási korlátok miatt meghiúsul (ilyenek például a 400-as (felhasználói hiba), 429-es (túl sok kérés), 500-as (belső kiszolgálóhiba) állapotkódok). | Nem támogatott. |
| **Párhuzamosság** | Támogatott. A felhasználók explicit módon adhatják meg az eseményindító egyidejűségi korlátját. Legalább 1, de legfeljebb 50 egyidejű aktivált folyamatfuttatást engedélyez. | Nem támogatott. |
| **Rendszerváltozók&nbsp;** | Támogatja a **WindowStart** és **WindowEnd** rendszerváltozók használatát. A felhasználók hozzáférhetnek a `triggerOutputs().windowStartTime` és `triggerOutputs().windowEndTime` értékhez az eseményindító rendszerváltozójaként az eseményindító meghatározásában. Az értékeket az időszak kezdési és záró időpontjaként használja a rendszer. Például a minden órában lefutó átfedésmentes ablakos eseményindító esetében az 01:00 és 02:00 közötti időszakban a meghatározás `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` és `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Nem támogatott. |
| **Pipeline&#x2011;to&#x2011;trigger relationship** | Támogatja az egy-az-egyhez kapcsolatot. Csak egy folyamat indítható el. | Támogatja a több-a-többhöz kapcsolatokat. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is. | 

## <a name="next-steps"></a>További lépések
Lásd az alábbi oktatóanyagokat:

- [Gyors útmutató: adat-előállító létrehozása a .NET SDK-val](quickstart-create-data-factory-dot-net.md)
- [Ütemezési eseményindító létrehozása](how-to-create-schedule-trigger.md)
- [Átfedésmentes ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md)
