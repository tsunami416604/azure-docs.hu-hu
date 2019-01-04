---
title: Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hajtható végre egy folyamat az Azure Data Factoryban akár igény szerint, akár eseményindító létrehozásával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: shlo
ms.openlocfilehash: 6d0524471ddc62e1ff6285bd0c80049917e726a6
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014947"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [1-es verzió](v1/data-factory-scheduling-and-execution.md)
> * [Aktuális verzió](concepts-pipeline-execution-triggers.md)

A _folyamatfuttatás_ egy folyamat-végrehajtás egy példányát határozza meg az Azure Data Factoryben. Tegyük fel például, hogy rendelkezik egy délelőtt 8:00-kor, 9:00-kor és 10:00-kor végbemenő folyamattal. Ebben az esetben három külön folyamatfuttatás lesz. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. A futtatási azonosító az adott folyamatfuttatást egyedien meghatározó GUID. 

A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatban meghatározott paraméterekhez történő továbbításával történik. A folyamatok manuálisan vagy egy _eseményindító_ használatával hajthatók végre. A cikk részletesen ismerteti a folyamatok végrehajtásának mindkét módját.

## <a name="manual-execution-on-demand"></a>Manuális végrehajtás (igény szerint)
Egy folyamat manuális végrehajtását _igény szerinti_ végrehajtásnak nevezik.

Tegyük fel például, hogy rendelkezik egy **copyPipeline** nevű alapszintű folyamattal, amelyet végre szeretne hajtani. A folyamat egyetlen tevékenységgel rendelkezik, amely egy Azure Blob Storage-ban lévő forrásmappából másol tartalmakat egy ugyanazon a tárterületen található célmappába. A következő JSON-definíció ezt a mintafolyamatot mutatja be:

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

A következő módszerek egyikével manuálisan futtathatja a folyamatot:
- .NET SDK
- Azure PowerShell-modul
- REST API
- Python SDK

### <a name="rest-api"></a>REST API
A következő mintaparancs bemutatja, hogyan futtathatja manuálisan a folyamatot a REST API használatával:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

A teljes minta: [a rövid útmutató: Adat-előállító létrehozása a REST API-val](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell
A következő mintaparancs bemutatja, hogyan futtathatja manuálisan a folyamatot az Azure PowerShell használatával:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

A kérelem hasznos adatainak törzsébe adja át a paramétereket. A .NET SDK, az Azure PowerShell és a Python SDK esetében az értékeket a hívás argumentumaként átadott szótárba adja át:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

A válasz hasznos adat a folyamatfuttatás egyedi azonosítója:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

A teljes minta: [a rövid útmutató: Adat-előállító létrehozása az Azure PowerShell-lel](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>.NET SDK
A következő mintahívás bemutatja, hogyan futtathatja manuálisan a folyamatot a .NET SDK használatával:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

A teljes minta: [a rövid útmutató: Adat-előállító létrehozása a .NET SDK-val](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> A .NET SDK-t Data Factory-folyamatok meghívásához használhatja, többek között az Azure Functionsből vagy egy saját webszolgáltatásból stb.

<h2 id="triggers">Eseményindító végrehajtása</h2>
A folyamatok futtatása triggerek használatával is elvégezhető. Az eseményindítók olyan feldolgozási egységek, amelyek meghatározzák, hogy mikor kezdődjön egy folyamat végrehajtása. A Data Factory jelenleg a triggerek három típusát támogatja:

- Ütemezési eseményindító: Egy folyamat egy időpontalapú ütemezés szerint meghívó eseményindító.

- Átfedésmentes ablakos eseményindító: Állapot megőrzése mellett, rendszeres időközönként működő eseményindító.

- Eseményalapú eseményindító: Egy eseményindítót, amelyek reagálnak az eseményre.

A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egy folyamatot több eseményindító is indíthat, és egyetlen eseményindító indíthat több folyamatot is. Az eseményindító alábbi definíciójában a **folyamatok** tulajdonság az adott eseményindító által aktivált folyamatok listájára vonatkozik. A tulajdonság meghatározása a folyamatparaméterek értékeit is tartalmazza.

### <a name="basic-trigger-definition"></a>Alap eseményindító meghatározása

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
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
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Ütemezési eseményindító
Az ütemezési eseményindító egy időpontalapú ütemezés szerint futtatja a folyamatokat. Ez az eseményindító támogatja az időszakos és speciális naptárbeállításokat. Az eseményindító például az ezekhez hasonló időközöket támogatja: „hetente” vagy „hétfőnként délután 5:00-kor és csütörtökönként este 9:00-kor”. Az ütemezési eseményindító rugalmas, mert az adatkészlet-mintázat független, és az eseményindító nem tesz különbséget az idősorozat-adatok és a nem idősorozat-adatok között.

Példák és további információ az ütemezési triggerekről: [Ütemezési trigger létrehozása](how-to-create-schedule-trigger.md).

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
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
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
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> A **paraméterek** tulajdonság a **folyamatok** elem kötelező tulajdonsága. Ha a folyamat nem használ paramétert, akkor is vegyen fel egy üres JSON-definíciót a **paraméterek** tulajdonságba.

### <a name="schema-overview"></a>Séma áttekintése
Az alábbi táblázat nagy vonalakban áttekintést nyújt az eseményindítóval kapcsolatos ismétlődés és ütemezés fő sémaelemeiről:

| JSON-tulajdonság | Leírás |
|:--- |:--- |
| **startTime** | Dátum-idő érték. Alapszintű ütemezések esetében a **startTime** tulajdonság értéke az első előfordulásra vonatkozik. Összetett ütemezéseknél az eseményindító nem indul korábban a megadott **startTime** értéknél. |
| **endTime** | Az eseményindító záró dátuma és időpontja. Az eseményindító nem lesz végrehajtva a megadott záró dátum és idő után. A tulajdonság értéke nem lehet múltbéli időpont. <!-- This property is optional. --> |
| **timeZone** | Az időzóna. Jelenleg csak az UTC időzóna támogatott. |
| **recurrence** | Az eseményindítóhoz tartozó ismétlődési szabályokat megadó recurrence objektum. A recurrence objektum a **frequency**, **interval**, **endTime**, **count** és **schedule** elemeket támogatja. Recurrence objektum meghatározásakor a **frequency** elem megadása kötelező. A recurrence objektum többi elemének megadása nem kötelező. |
| **frequency** | Az eseményindító ismétlődésének gyakorisági egysége. A támogatott értékek például: minute, hour, day, week és month (percenként, óránként, naponta, hetente és havonta). |
| **interval** | Pozitív egész szám, amely a **frequency** érték időközét jelöli. A **frequency** érték határozza meg a trigger futtatási gyakoriságát. Ha például az **interval** értéke 3, a **frequency** értéke pedig week, az eseményindító három hetente ismétlődik. |
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

| JSON-tulajdonság | Típus | Szükséges | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | sztring | Igen | None | ISO 8601 dátum-idő értékek | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | objektum | Igen | None | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | szám | Nem | 1 | 1–1000 | `"interval":10` |
| **endTime** | sztring | Igen | None | Egy jövőbeli időpontot jelölő dátum-idő érték | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | objektum | Nem | None | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázatban látható, hogy a **startTime** tulajdonság hogyan irányítja a folyamatfuttatást:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |
| **A kezdés időpontja a múltban van** | Kiszámítja a kezdő időpont utáni első jövőbeli végrehajtási időpontot, és abban az időpontban fut.<br /><br />A további végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.<br /><br />Lásd a táblázat alatti példát. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul.<br /><br />Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |
| **A kezdő időpont jövőbeli, illetve az aktuális időpont** | Egyszer fut a megadott kezdési időpontban.<br /><br />A további végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul.<br /><br />Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |

Tekintsünk meg egy példát arról, hogy mi történik, ha a kezdő időpont egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont 2017-04-08 13:00, a kezdési idő 2017-04-07 14:00, és a művelet kétnaponta ismétlődik. (A **recurrence** értéke a **frequency** tulajdonság „day” értékre és az **interval** tulajdonság 2 értékre történő állításával adható meg.) Figyelje meg, hogy a **startTime** értéke múltbeli időpont, és az aktuális időpont elé esik.

Ezen feltételek mellett az első végrehajtási időpont 2017-04-09 14:00. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Tehát ebben a forgatókönyvben a kezdő időpont 2017-04-07 14:00. A következő alkalom két napra esik ettől, azaz 2017-04-09 14:00.

Az első végrehajtási időpont akkor is ez, ha a **startTime** értéke 2017-04-05 14:00 vagy 2017-04-01 14:00. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Ezért a további végrehajtások a következő időpontokban történnek: 2017-04-11 14:00, 2017-04-13 14:00, majd 2017-04-15 14:00 és így tovább.

Végül, ha az eseményindító ütemezésében nincs megadva az óra vagy perc, a rendszer alapértelmezés szerint az első végrehajtás óra vagy perc értékeit használja.

### <a name="schedule-property"></a>schedule tulajdonság
A **schedule** tulajdonsággal *korlátozhatja* a trigger végrehajtásainak számát. Ha például egy havi gyakoriságú trigger csak a 31. napon való futásra van ütemezve, akkor a trigger csak a harmincegy napos hónapokban fut.

A **schedule** tulajdonsággal *növelheti* is a trigger végrehajtásainak számát. Ha például egy havi gyakoriságú eseményindító a hónap 1. és 2. napján való futásra van ütemezve, akkor a trigger minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több **schedule** elem is meg van adva, a kiértékelési sorrend a legnagyobb ütemezési beállítástól halad a legkisebb felé: hét száma, hónap napja, hét napja, óra, perc.

A következő táblázat részletesen ismerteti a **schedule** elemeit:

| JSON-elem | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **minutes** | Az óra azon perce, amikor az eseményindító fut. |– Egész szám<br />– Egész számok tömbje|
| **hours** | A nap azon órái, amikor az eseményindító fut. |– Egész szám<br />– Egész számok tömbje|
| **weekDays** | A hét azon napjai, amelyeken az eseményindító fut. Az érték csak heti gyakorisággal adható meg.|<br />– Hétfő<br />– Kedd<br />– Szerda<br />– Csütörtök<br />– Péntek<br />– Szombat<br />– Vasárnap<br />– Nap értékek tömbje (a tömb maximális mérete 7)<br /><br />A nap értékek nem tesznek különbséget a kis- és nagybetű között|
| **monthlyOccurrences** | A hónap azon napjai, amelyeken az eseményindító fut. Az érték csak havi gyakorisággal adható meg. |– Tömbje **monthlyOccurrence** objektumok: `{ "day": day,  "occurrence": occurrence }`<br />– A **day** attribútum a hét azon napja, amelyen a trigger fut. Például a `{Sunday}` értékű **day** attribútummal rendelkező **monthlyOccurrences** tulajdonság a hónap minden vasárnapját jelenti. A **day** attribútum megadása kötelező.<br />– Az **occurrence** attribútum a megadott **day** attribútum előfordulása a hónapban. Például a `{Sunday, -1}` értékű **day** és **occurrence** attribútumokkal rendelkező **monthlyOccurrences** tulajdonság a hónap utolsó vasárnapját jelenti. Az **occurrence** attribútum megadása nem kötelező.|
| **monthDays** | A hónap azon napja, amelyen az eseményindító lefut. Az érték csak havi gyakorisággal adható meg. |– Bármilyen érték -1 és -31 között<br />– Bármilyen érték 1 és 31 között<br />– Értékek tömbje|

## <a name="tumbling-window-trigger"></a>Átfedésmentes ablakos eseményindító
Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok.

Példák és további információ az átfedésmentes ablakos triggerekről: [Átfedésmentes ablakos trigger létrehozása](how-to-create-tumbling-window-trigger.md).

## <a name="event-based-trigger"></a>Eseményalapú trigger

Egy esemény alapú eseményindító egy esemény, például egy fájl beérkezés vagy egy fájlt, az Azure Blob Storage törlésének válaszul futtatja a folyamatokat.

További információkat az eseményalapú triggerekkel kapcsolatban az [eseményre válaszul folyamatot futtató trigger létrehozásával](how-to-create-event-trigger.md) kapcsolatos témakörben olvashat.

## <a name="examples-of-trigger-recurrence-schedules"></a>Eseményindító-ismétlődési ütemezések példái
Ez a szakasz az ismétlődésütemezésekre mutat be példákat. A **schedule** objektumra és annak elemeire koncentrál.

Ez a példa azt feltételezi, hogy az **interval** értéke 1, és hogy a **frequency** értéke helyes, az ütemezés meghatározása szerint. Nem lehet például a **frequency** értéke day, ha a **schedule** objektum **monthDays** értékre lett módosítva. Ezen korlátozások leírása az előző szakaszban található táblázatban szerepel.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` | Minden nap 05:00-kor fut le. |
| `{"minutes":[15], "hours":[5]}` | Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` | Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` | Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` | 15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le.<br /><br />Ez az eseményindító óránként fut le. A perceket a **startTime** értéke szabályozza, ha az érték meg van adva. Ha az érték nincs megadva, akkor a perceket a létrehozás ideje szabályozza. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor.<br /><br />Ez az ütemezés egyenértékű azzal, mintha egy hour értékű **frequency** attribútummal rendelkező eseményindító lenne, 1 **interval** értékkel, **schedule** nélkül. Ez az ütemezés különböző **frequency** és **interval** értékekkel használható egyéb eseményindítók létrehozásához. Ha például a **frequency** értéke month, az ütemezés egy hónapban csak egyszer fut, nem pedig minden nap, amikor a **frequency** értéke day. |
| `{"minutes":[0]}` | Minden óra kezdetén fut le.<br /><br />Ez az eseményindító minden óra kezdetén fut le, 00:00-kor, 01:00-kor, 02:00-kor és így tovább.<br /><br />Ez az ütemezés megegyezik egy olyan eseményindítóval, amely hour **frequency** értékkel és nulla perc **startTime** értékkel rendelkezik, és nincs **schedule** érték, de a **frequency** értéke day. Ha a **frequency** értéke week vagy month, az ütemezés csak egy héten egyszer vagy egy hónapban egyszer fut le. |
| `{"minutes":[15]}` | Minden óra 15. percében fut le.<br /><br />Ez az eseményindító minden óra 15. percében fut le, 00:15-kor, 01:15-kor, 02:15-kor és így tovább, egészen 23:15-ig. |
| `{"hours":[17], "weekDays":["saturday"]}` | Minden héten szombaton, 17:00-kor fut le. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:00-kor fut le. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le 09:00 és 16:45 között. |
| `{"weekDays":["tuesday", "thursday"]}` | Keddenként és csütörtökönként, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap huszonnyolcadik napján, 06:00-kor fut (azt feltételezi, hogy a **frequency** month értékre van állítva). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Minden hónap utolsó napján, 06:00-kor fut le.<br /><br />Ha az adott eseményindítót a hónap utolsó napján kívánja futtatni, a 28., 29., 30. vagy 31. nap megadása helyett használjon -1-et. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján, 06:00-kor fut le. |
| `{monthDays":[1,14]}` | Minden hónap első és tizennegyedik. napján, a megadott kezdési időpontban fut. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, 05:00-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | A hónap végétől számított harmadik pénteken, minden hónapban, a megadott kezdési időpontban fut le. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Minden hónap ötödik péntekjén, a megadott kezdési időpontban fut le.<br /><br />Ha az adott hónapban nincs ötödik péntek, a folyamat nem fut le. Az eseményindító a hónap utolsó péntekjén való futtatásához az **occurrence** értékénél használjon -1-et 5 helyett. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="trigger-type-comparison"></a>Eseményindító-típusok összehasonlítása
Az átfedésmentes ablakos eseményindító és az ütemezési eseményindító is időszívverés alapján működik. Mi a különbség közöttük?

Az alábbi táblázatban az átfedésmentes ablakos eseményindító és az ütemezési eseményindító összehasonlítása látható:

|  | Átfedésmentes ablakos eseményindító | Ütemezési eseményindító |
|:--- |:--- |:--- |
| **Visszatöltési forgatókönyvek** | Támogatott. A folyamatfuttatások ütemezhetők múltbéli időszakokra. | Nem támogatott. A folyamatfuttatások csak az aktuális időszaktól vagy egy jövőbeli időszaktól hajthatók végre. |
| **Megbízhatóság** | 100%-os megbízhatóság. A folyamatfuttatások egy adott kezdési időponttól minden időszakhoz ütemezhetők, hézagok nélkül. | Kevésbé megbízható. |
| **Újrapróbálkozási képesség** | Támogatott. A sikertelen folyamatfuttatások alapértelmezett újrapróbálkozási szabályzata 0, vagy a felhasználó által az eseményindító definíciójában megadott szabályzat. Automatikusan újrapróbálkozik, ha a folyamatfuttatás egyidejűségi/kiszolgálói/szabályozási korlátok miatt meghiúsul (azaz állapotkódok 400: Felhasználói hiba, a 429-es: Túl sok kérelmet, és 500-as: Belső kiszolgálóhiba). | Nem támogatott. |
| **Párhuzamosság** | Támogatott. A felhasználók explicit módon adhatják meg az eseményindító egyidejűségi korlátját. 1 és 50 közötti egyidejű aktivált folyamatfuttatást engedélyez. | Nem támogatott. |
| **Rendszerváltozók** | Támogatja a **WindowStart** és **WindowEnd** rendszerváltozók használatát. A felhasználók hozzáférhetnek a `triggerOutputs().windowStartTime` és `triggerOutputs().windowEndTime` értékhez az eseményindító rendszerváltozójaként az eseményindító meghatározásában. Az értékeket az időszak kezdési és záró időpontjaként használja a rendszer. Például a minden órában lefutó átfedésmentes ablakos eseményindító esetében az 01:00 és 02:00 közötti időszakban a meghatározás `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` és `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Nem támogatott. |
| **Folyamat–trigger kapcsolat** | Támogatja az egy-az-egyhez kapcsolatot. Csak egy folyamat indítható el. | Támogatja a több-a-többhöz kapcsolatokat. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is. | 

## <a name="next-steps"></a>További lépések
Lásd az alábbi oktatóanyagokat:

- [Gyors útmutató: Adat-előállító létrehozása a .NET SDK-val](quickstart-create-data-factory-dot-net.md)
- [Ütemezési eseményindító létrehozása](how-to-create-schedule-trigger.md)
- [Átfedésmentes ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md)
