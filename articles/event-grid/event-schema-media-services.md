---
title: Az Azure Media Services mint eseményrács forrása
description: Az Azure Event Grid szolgáltatással a Media Services-eseményekhez biztosított tulajdonságok ismertetése
services: media-services
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-grid
ms.workload: ''
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: spelluru
ms.openlocfilehash: d5d50bbde927efd4aee0cedd69486a52ab8c328b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394331"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Az Azure Media Services eseményrács-forrásként

Ez a cikk a Media Services-események sémákat és tulajdonságait tartalmazza.

## <a name="job-related-event-types"></a>Feladattal kapcsolatos eseménytípusok

A Media Services az alábbiakban **ismertetett, feladattal** kapcsolatos eseménytípusokat bocsátki. A **feladattal** kapcsolatos eseményeknek két kategóriája van: "A feladatállapot-változások figyelése" és "A feladatkimeneti állapot változásainak figyelése". 

Az összes eseményre a JobStateChange eseményre való feliratkozással regisztrálhat. Vagy csak bizonyos eseményekre iratkozhat fel (például végleges állapotok, például JobErrored, JobFinished és JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Feladat állapotának változásainak figyelése

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Esemény beszereznie az összes feladatállapot-módosításhoz. |
| Microsoft.Media.JobScheduled| Esemény leváltása, amikor a feladat áttér az ütemezett állapotba. |
| Microsoft.Media.JobProcessing| Esemény bekéselése, amikor a feladat áttér a feldolgozási állapotba. |
| Microsoft.Media.JobLemondás| Esemény betöltése, amikor a feladat átvált az állapot megszakítására. |
| Microsoft.Media.JobFinished| Esemény leváltása, amikor a feladat kész állapotba kerül. Ez egy végső állapot, amely tartalmazza a feladat kimenetek.|
| Microsoft.Media.FeladatTörölve| Esemény leváltásakor kap egy eseményt, amikor a feladat átvált a megszakított állapotba. Ez egy végső állapot, amely tartalmazza a feladat kimenetek.|
| Microsoft.Media.JobErrored| Esemény beírása, amikor a feladat hibaállapotba lép. Ez egy végső állapot, amely tartalmazza a feladat kimenetek.|

Lásd [a következő sémapéldákat.](#event-schema-examples)

### <a name="monitoring-job-output-state-changes"></a>Feladat kimeneti állapotváltozásainak figyelése

Egy feladat több kimenetet is tartalmazhat (ha az átalakítást több feladatkimenetre konfigurálta.) Ha szeretné nyomon követni az egyes kimeneti adatokat, figyelje a feladat kimenetének változása esemény.

Minden **feladat** magasabb szinten lesz, mint **a JobOutput,** így a feladat kimeneti események a megfelelő feladaton belül aktiválódnak. 

A hibaüzenetek `JobFinished` `JobCanceled`a `JobError` , , kimeneti összesített eredmények minden feladat kimenet – ha mindegyik befejeződött. Mivel a feladat kimeneti események tűz, mint az egyes feladatok befejeződik. Például ha egy kódolási kimenet, majd egy Video Analytics kimenet, akkor két esemény tüzelési feladat kimeneti események, mielőtt a végső JobFinished esemény tüzek az összesített adatokat.

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Esemény beszereznie az összes feladat kimeneti állapot módosításai. |
| Microsoft.Media.JobOutputÜtemezett| Esemény leváltása, amikor a feladat kimenete átkerül az ütemezett állapotba. |
| Microsoft.Media.JobOutputProcessing| Esemény bekéselése, amikor a feladat kimenete átkerül a feldolgozási állapotba. |
| Microsoft.Media.JobOutputLemondás| Esemény betöltése, amikor a feladat kimenete átkerül az állapot megszakítására.|
| Microsoft.Media.JobOutputKész| Esemény leváltása, amikor a feladat kimenete befejezett állapotba kerül.|
| Microsoft.Media.JobOutputTörölve| Esemény betöltése, amikor a feladat kimenete átkerül a megszakított állapotba.|
| Microsoft.Media.JobOutputHibás| Esemény leváltása, amikor a feladat kimenete hibaállapotba kerül.|

Lásd [a következő sémapéldákat.](#event-schema-examples)

### <a name="monitoring-job-output-progress"></a>A feladat kimenetének állapotának figyelése

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Ez az esemény a feladat feldolgozási folyamatát tükrözi, 0%-ról 100%-ra. A szolgáltatás megkísérli az esemény küldését, ha a folyamatérték 5%-kal vagy nagyobb mértékben nőtt, vagy több mint 30 másodperc telt el az utolsó esemény óta (szívverés). A haladásértéke nem garantáltan 0%-kal, vagy eléri a 100%-ot, és nem garantált, hogy az idő múlásával állandó ütemben növekszik. Ez az esemény nem használható annak megállapítására, hogy a feldolgozás befejeződött– ehelyett az állapotváltozási eseményeket kell használnia.|

Lásd [a következő sémapéldákat.](#event-schema-examples)

## <a name="live-event-types"></a>Élő eseménytípusok

A Media Services az alább ismertetett **élő** eseménytípusokat is kibocsátja. Az **élő** eseményeknek két kategóriája van: a streamszintű események és a pályaszintű események. 

### <a name="stream-level-events"></a>Streamszintű események

A streamszintű események streamenként vagy kapcsolatonként kerülnek előzésre. Minden eseményhez `StreamId` van egy paraméter, amely azonosítja a kapcsolatot vagy az adatfolyamot. Minden adatfolyam nak vagy kapcsolatnak van egy vagy több különböző típusú sávja. Egy kódoló egyik kapcsolata például egy hangsávval és négy videosávdal rendelkezhet. Az adatfolyam-eseménytípusok a következők:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionElutasítve | A kódoló csatlakozási kísérlete elutasításra kerül. |
| Microsoft.Media.LiveEventEncoderConnected | A kódoló kapcsolatot létesít az élő eseménysel. |
| Microsoft.Media.LiveEventEncoderLeed | A kódoló bontja a kapcsolatot. |

Lásd [a következő sémapéldákat.](#event-schema-examples)

### <a name="track-level-events"></a>Pályaszintű események

A pályaszintű események számonként emelkednek. 

> [!NOTE]
> Az élő kódoló csatlakoztatása után minden pályaszintű esemény következik be.

A pályaszintű eseménytípusok a következők:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | A médiakiszolgáló eldobja az adattömböt, mert túl késő, vagy átfedésben van az időbélyeg (az új adattömb időbélyege kisebb, mint az előző adatadattömb befejezési ideje). |
| Microsoft.Media.LiveEventIncomingStreamBeérkezett | A médiakiszolgáló az adatfolyam vagy kapcsolat minden egyes sávjára megkapja az első adattömböt. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | A médiakiszolgáló észleli, hogy a hang- és videofolyamok nincsenek szinkronban. Figyelmeztetésként használható, mert a felhasználói élményt nem befolyásolhatja. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | A médiakiszolgáló észleli, hogy a külső kódolóból érkező két videoadatfolyam bármelyike nincs szinkronban. Figyelmeztetésként használható, mert a felhasználói élményt nem befolyásolhatja. |
| Microsoft.Media.LiveEventIngestSzívverés | Az élő események futása kor minden egyes számhoz 20 másodpercenként közzétéve. A betöltésállapot-összegzést tartalmazza.<br/><br/>A kódoló először csatlakoztatása után a szívverési esemény továbbra is 20 másodpercenként bocsát ki, függetlenül attól, hogy a kódoló még csatlakoztatva van-e vagy sem. |
| Microsoft.Media.LiveEventTrackDisfolytonosságDetected | A médiakiszolgáló a bejövő zeneszám folytonosságának megszakadását észleli. |

Lásd [a következő sémapéldákat.](#event-schema-examples)

## <a name="event-schema-examples"></a>Példák eseménysémára

### <a name="jobstatechange"></a>JobStateChange

A következő példa a **JobStateChange** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| előzőÁllam | sztring | A feladat állapota az esemény előtt. |
| state | sztring | A feladat új állapota ebben az esetben értesítést kap. Például "Ütemezett: A feladat készen áll az indításra" vagy "Kész: A feladat befejeződött" .|

Ha a feladat állapota lehet az egyik érték: *Várakozási sorba állított,* *Ütemezett*, *Feldolgozás,* *Befejezett*, *Hiba*, *Megszakítva*, *Megszakítás*

> [!NOTE]
> *A várólistára helyezett* csak az előzőState tulajdonságban lesz **jelen,** az **állami** tulajdonságban nem.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>Feladatütemezés, Feladatfeldolgozás, Feladattörlése

A feladat minden nem végleges állapotának módosítása (például JobScheduled, JobProcessing, JobCanceling) esetében a példaséma a következőhöz hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>Befejezett, Leszakított feladat, Hiba

A feladat minden végső állapotának módosítása kor (például A feladat meglett, a Feladattörölve, JobErrored) esetében a példaséma a következőhöz hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Kimenetek | Tömb | Leveszi a feladat kimeneteket.|

### <a name="joboutputstatechange"></a>Feladatkimenetállapota

A következő példa a **JobOutputStateChange** esemény sémáját mutatja be:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Minden JobOutput állapot módosítása esetén a példaséma a következőhöz hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

A példaséma a következőhöz hasonlóan néz ki:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionElutasított

A következő példa a **LiveEventConnectionRejected** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| streamId | sztring | Az adatfolyam vagy a kapcsolat azonosítója. A kódoló vagy az ügyfél felelős azért, hogy ezt az azonosítót a betöltési URL-címhez adja hozzá. |  
| ineszturl | sztring | Az élő esemény által biztosított betöltési URL-cím. |  
| kódolóip | sztring | A kódoló IP-címe. |
| kódolóport | sztring | A kódoló portja, ahonnan ez az adatfolyam jön. |
| resultCode | sztring | A kapcsolat elutasításának oka. Az eredménykódok az alábbi táblázatban találhatók. |

A hibaeredmény-kódokat az [élő esemény hibakódjaiban](../media-services/latest/live-event-error-codes.md)találja.

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

A következő példa a **LiveEventEncoderConnected** esemény sémáját mutatja be: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| streamId | sztring | Az adatfolyam vagy a kapcsolat azonosítója. A kódoló vagy az ügyfél felelős azért, hogy ezt az azonosítót a betöltési URL-ben adja meg. |
| ineszturl | sztring | Az élő esemény által biztosított betöltési URL-cím. |
| kódolóip | sztring | A kódoló IP-címe. |
| kódolóport | sztring | A kódoló portja, ahonnan ez az adatfolyam jön. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderLe

A következő példa a **LiveEventEncoderDisconnected** esemény sémáját mutatja be: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| streamId | sztring | Az adatfolyam vagy a kapcsolat azonosítója. A kódoló vagy az ügyfél felelős azért, hogy ezt az azonosítót a betöltési URL-címhez adja hozzá. |  
| ineszturl | sztring | Az élő esemény által biztosított betöltési URL-cím. |  
| kódolóip | sztring | A kódoló IP-címe. |
| kódolóport | sztring | A kódoló portja, ahonnan ez az adatfolyam jön. |
| resultCode | sztring | A kódoló leválasztásának oka. Ez lehet kecses húzza ki, vagy egy hiba. Az eredménykódok az alábbi táblázatban találhatók. |

A hibaeredmény-kódokat az [élő esemény hibakódjaiban](../media-services/latest/live-event-error-codes.md)találja.

A kecses leválasztó eredménykódok a következők:

| Eredménykód | Leírás |
| ----------- | ----------- |
| S_OK | A kódoló leválasztása sikeresen megtörtént. |
| MPE_CLIENT_TERMINATED_SESSION | A kódoló leválasztva (RTMP). |
| MPE_CLIENT_DISCONNECTED | A kódoló leválasztva (FMP4). |
| MPI_REST_API_CHANNEL_RESET | A csatorna alaphelyzetbe állítása parancs a fogadásra kerül. |
| MPI_REST_API_CHANNEL_STOP | A csatorna leállítási parancsa megérkezett. |
| MPI_REST_API_CHANNEL_STOP | A csatorna karbantartás alatt áll. |
| MPI_STREAM_HIT_EOF | Az EOF-adatfolyamot a kódoló küldi. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

A következő példa a **LiveEventIncomingDataChunkDropped** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType típus | sztring | A szám típusa (Hang / Videó). |
| trackName | sztring | A pálya neve. |
| Bitráta | egész szám | A pálya bitrája. |
| időbélyeg | sztring | Az adattömb időbélyege eldobva. |
| Időskála | sztring | Az időbélyeg időskálája. |
| resultCode | sztring | Az adattömbes ledobás oka. **FragmentDrop_OverlapTimestamp** vagy **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamBeérkezett

A következő példa a **LiveEventIncomingStreamReceived** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType típus | sztring | A szám típusa (Hang / Videó). |
| trackName | sztring | A pálya neve (vagy a kódoló adja meg, vagy RTMP esetén a kiszolgáló *TrackType_Bitrate* formátumban generál). |
| Bitráta | egész szám | A pálya bitrája. |
| ineszturl | sztring | Az élő esemény által biztosított betöltési URL-cím. |
| kódolóip | sztring  | A kódoló IP-címe. |
| kódolóport | sztring | A kódoló portja, ahonnan ez az adatfolyam jön. |
| időbélyeg | sztring | A fogadott adattömb első időbélyege. |
| Időskála | sztring | Időskála, amelyben az időbélyeg képviselteti magát. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

A következő példa a **LiveEventIncomingStreamsOutOfSync** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| minLastTimestamp | sztring | Az utolsó időbélyegek minimális száma az összes zeneszám (hang vagy videó) között. |
| typeOfTrackWithMinLastTimestamp | sztring | A sáv típusa (hang vagy videó) minimális last timestamp.Type of the track (audio or video) with minimum last timestamp. |
| maxLastTimestamp | sztring | Az összes zeneszám (hang vagy videó) közötti időbélyeg ek maximális száma. |
| typeOfTrackWithMaxLastTimestamp | sztring | A legnagyobb utolsó időbélyeggel rendelkező zeneszám (hang vagy videó) típusa. |
| timescaleOfMinLastTimestamp| sztring | Leveszi azt az időskálát, amelyben a "MinLastTimestamp" képviselteti magát.|
| timescaleOfMaxLastTimestamp| sztring | Lemeríti a "MaxLastTimestamp" ábrázolásának időskálája.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

A következő példa a **LiveEventIncomingVideoStreamsOutOfSync** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| firstTimestamp (első időbélyeg) | sztring | Időbélyeg érkezett az egyik a pálya / minőségi szint típusú videó. |
| firstDuration (első időtartam) | sztring | Az adattömb időtartama az első időbélyeggel. |
| második időbélyeg | sztring  | A videó típusának más pálya/minőségi szintje esetén kapott időbélyegző. |
| másodikIdőtartam | sztring | Az adattömb időtartama második időbélyeggel. |
| Időskála | sztring | Az időbélyegek és az időtartam időskálája.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

A következő példa a **LiveEventIngestHeartbeat** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType típus | sztring | A szám típusa (Hang / Videó). |
| trackName | sztring | A pálya neve (vagy a kódoló adja meg, vagy RTMP esetén a kiszolgáló *TrackType_Bitrate* formátumban generál). |
| Bitráta | egész szám | A pálya bitrája. |
| bejövő bitráta | egész szám | A kódolótól érkező adattömbök alapján számított bitráta. |
| lastTimestamp (lastTimestamp) | sztring | A legutóbbi időbélyeg az elmúlt 20 másodpercben érkezett egy számhoz. |
| Időskála | sztring | Időskála, amelyben az időbélyegek vannak kifejezve. |
| átfedések száma | egész szám | Az adattömbök száma átfedésben időbélyegek az elmúlt 20 másodpercben. |
| diszkontinkulitásszáma | egész szám | Az elmúlt 20 másodpercben megfigyelt megszakítások száma. |
| nem növekvő szám | egész szám | Az időbélyeggel rendelkező adattömbök száma az elmúlt 20 másodpercben érkezett. |
| váratlan Bitráta | Bool | Ha a várt és a tényleges bitráták az elmúlt 20 másodpercben a megengedettnél nagyobb mértékben térnek el. Ez akkor és csak akkor igaz, ha a bejövőBitráta >= 2* bitráta vagy bejövőBitráta <= bitráta/2 VAGY Bejövőbitráta = 0. |
| state | sztring | Az élő esemény állapota. |
| Egészséges | Bool | Azt jelzi, hogy a betöltés kifogástalan-e a számlálók és a jelzők alapján. Kifogástalan igaz, ha overlapCount = 0 && megszakításicount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDisfolytonosságDetected

A következő példa a **LiveEventTrackDiscontinuityDetected** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType típus | sztring | A szám típusa (Hang / Videó). |
| trackName | sztring | A pálya neve (vagy a kódoló adja meg, vagy RTMP esetén a kiszolgáló *TrackType_Bitrate* formátumban generál). |
| Bitráta | egész szám | A pálya bitrája. |
| korábbi időbélyeg | sztring | Az előző töredék időbélyege. |
| új Időbélyeg | sztring | Az aktuális töredék időbélyege. |
| megszakításhiányGap | sztring | A fenti két időbélyeg közötti rés. |
| Időskála | sztring | Időskála, amelyben mind az időbélyeg, mind a folytonossági hiány képviselteti magát. |

### <a name="common-event-properties"></a>Gyakori eseménytulajdonságok

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az EventGrid témakör. Ez a tulajdonság rendelkezik a Media Services-fiók erőforrásazonosítójának. |
| Tárgy | sztring | A Media Services-fiók Media Services csatornájának erőforrás-elérési útja. A témakör és a tárgy összefűzése megadja a feladat erőforrás-azonosítóját. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. Például a "Microsoft.Media.JobStateChange". |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Media Services eseményadatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

## <a name="next-steps"></a>További lépések

[Regisztráció a feladatállapot-módosítási eseményekre](../media-services/latest/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Lásd még

- [Media Service-eseményeket tartalmazó EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [A Media Services eseményeinek meghatározásai](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Élő események hibakódjai](../media-services/latest/live-event-error-codes.md)
