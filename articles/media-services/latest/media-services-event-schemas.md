---
title: A Media Services-események Azure Event Grid-sémák
description: A Media Services-események az Azure Event GRID használatával biztosított tulajdonságait ismerteti
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: f9748d61b1aa336c5300dd414d53388f48a41368
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243985"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>A Media Services-események Azure Event Grid-sémák

Ez a cikk biztosítja a sémák és a tulajdonságok a Media Services-események.

Mintaszkriptek és oktatóanyagok listáját lásd: [Media Services eseményforrás](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="job-related-event-types"></a>Feladat kapcsolatos események típusai

A Media Services bocsát ki a **feladat** alább ismertetett eseménytípusok kapcsolatos. A két kategóriába sorolhatók a **feladat** kapcsolatos eseményeket: "Figyelési feladat állapota" és "Figyelési feladat kimeneti állapota". 

Regisztrálhat az események a JobStateChange esemény való feliratkozással. Vagy, előfizethet a meghatározott események csak (például végső államok JobErrored JobFinished és JobCanceled hasonlóan). 

### <a name="monitoring-job-state-changes"></a>Figyelési feladat állapotváltozások

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Esemény beolvasása az összes feladat állapota változáshoz. |
| Microsoft.Media.JobScheduled| Egy eseményt, amikor a feladat átkerül az ütemezett állapot beolvasása. |
| Microsoft.Media.JobProcessing| Egy eseményt, amikor a feladat átkerül a feldolgozási állapot beolvasása. |
| Microsoft.Media.JobCanceling| Egy eseményt, amikor a feladat megszakítása állapot átkerül beolvasása. |
| Microsoft.Media.JobFinished| Egy eseményt, amikor a feladat kész állapot átkerül beolvasása. Ez a végállapota feladatkimenetek tartalmazza.|
| Microsoft.Media.JobCanceled| Első egy eseményt, amikor a feladat megszakítva állapotba értékre vált. Ez a végállapota feladatkimenetek tartalmazza.|
| Microsoft.Media.JobErrored| Egy eseményt, amikor a feladat hibás állapotú átkerül beolvasása. Ez a végállapota feladatkimenetek tartalmazza.|

[Séma példák](#event-schema-examples) kövesse.

### <a name="monitoring-job-output-state-changes"></a>Figyelési feladat kimeneti állapotváltozások

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Az esemény állapota az összes feladat kimenetének beolvasása. |
| Microsoft.Media.JobOutputScheduled| Egy eseményt, amikor a feladat kimeneti tér át, ütemezett állapot beolvasása. |
| Microsoft.Media.JobOutputProcessing| Egy eseményt, amikor a feladat kimeneti tér át, a feldolgozási állapot beolvasása. |
| Microsoft.Media.JobOutputCanceling| Egy eseményt, amikor a feladat kimeneti tér át, az állapot megszakítása beolvasása.|
| Microsoft.Media.JobOutputFinished| Egy eseményt, amikor a feladat kimeneti tér át, Befejezett állapot beolvasása.|
| Microsoft.Media.JobOutputCanceled| Egy eseményt, amikor a feladat kimeneti tér át, meg lett szakítva állapot beolvasása.|
| Microsoft.Media.JobOutputErrored| Egy eseményt, amikor a feladat kimeneti tér át, a hibás állapotú beolvasása.|

[Séma példák](#event-schema-examples) kövesse.

## <a name="live-event-types"></a>Élő események típusai

A Media Services emellett bocsát ki a **élő** eseménytípusok az alábbiakban. A két kategóriába sorolhatók a **élő** események: stream-szintű eseményeit, és nyomon követése-szintű eseményeit. 

### <a name="stream-level-events"></a>Stream-szintű eseményeit

Stream-szintű eseményeit stream vagy kapcsolat aktiválódnak. Minden egyes esemény egy `StreamId` paraméter, amely azonosítja a kapcsolatot, vagy adatfolyamként továbbíthatja. Minden stream vagy kapcsolat van egy vagy több, különböző típusú nyomon követi. Egy kapcsolat forrása lehet például négy videó nyomon követi, és egy hangsávra. A stream esemény típusok a következők:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | Kódoló kapcsolatfelvételt. |
| Microsoft.Media.LiveEventEncoderConnected | Kódoló az élő esemény kapcsolatot létesít. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kódoló leválasztása. |

[Séma példák](#event-schema-examples) kövesse.

### <a name="track-level-events"></a>Nyomon követheti-szintű eseményeit

Nyomon követheti-szintű eseményeit számonkénti aktiválódnak. A sledování událostí típusok a következők:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Kiszolgáló adathalmaz csökken, mert késő vagy egy átfedő timestamp (időbélyeg az új adatok adattömbök érték kisebb, mint az előző adathalmaz befejezési időpontja). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media kiszolgáló egyes nyomon követése az első adathalmaz kap a streamben vagy a kapcsolat. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-kiszolgáló észleli a hang és video-adatfolyamokat nincsenek szinkronban. Figyelmeztetés használjuk, mert előfordulhat, hogy nem változik a felhasználói élmény. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-kiszolgáló észleli a külső kódoló érkező két video-adatfolyamokat bármelyikét nincsenek szinkronban. Figyelmeztetés használjuk, mert előfordulhat, hogy nem változik a felhasználói élmény. |
| Microsoft.Media.LiveEventIngestHeartbeat | Közzétett 20 másodpercenként minden egyes nyomon követése, az élő esemény futtatásakor. Itt állapotösszegzése betöltését. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Kiszolgáló kihagyást észleli a bejövő nyomon követése. |

[Séma példák](#event-schema-examples) kövesse.

## <a name="event-schema-examples"></a>Esemény séma példák

### <a name="jobstatechange"></a>JobStateChange

Az alábbi példa bemutatja a séma, a **JobStateChange** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| previousState | sztring | Az esemény előtt a feladat állapotát. |
| state | sztring | A feladat ebben az esetben bejelentett új állapotát. Például "ütemezett: A feladat elkészült elindításához"vagy" kész: A feladat befejeződött".|

Ahol a feladat állapota lehet az értékek egyike: *Várólistára helyezett*, *ütemezett*, *feldolgozása*, *befejeződött*, *hiba*, *meg lett szakítva*, *Megszakítása*

> [!NOTE]
> *Várólistára helyezett* csak topológiában szerepel a **previousState** tulajdonság, de nem található a **állapot** tulajdonság.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Feladat (például JobScheduled, JobProcessing, JobCanceling) állapotban nem végleges módosítás esetén a példa séma a következőhöz hasonlóan néz ki:

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

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Végső feladat állapota módosítás (például JobFinished, JobCanceled, JobErrored) esetén a példa séma az alábbihoz hasonlóan néz ki:

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Kimenetek | Tömb | A feladat kimeneti beolvasása.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

Az alábbi példa bemutatja a séma, a **JobOutputStateChange** esemény:

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

Az egyes JobOutput állapotváltozás a példa séma az alábbihoz hasonlóan néz ki:

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

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Az alábbi példa bemutatja a séma, a **LiveEventConnectionRejected** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| streamId | sztring | A stream vagy kapcsolat azonosítója. Kódoló vagy ügyfél felelős adja hozzá ezt az Azonosítót a bemeneti URL-címet. |  
| ingestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |  
| EncoderIp | sztring | A kódoló IP-cím. |
| encoderPort | sztring | A kódoló a stream forrását a port. |
| Eredménykód | sztring | Az internetkapcsolat visszautasították okát. A eredménykódok az alábbi táblázatban láthatók. |

Az eredmény kódok a következők:

| Eredménykód | Leírás |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Helytelen betöltési URL-címe |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Kódoló IP nem lesznek jelen IP-engedélyezési lista konfigurálása |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Kódoló nem küldött metaadatok kapcsolatban a streamet. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | A megadott kodek nem támogatott. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Egy kódrészletet kapott forgalmat fogadó és a Stream fejléc előtt. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | A megadott tulajdonságok száma meghaladja a megengedett maximális korlátot. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | Összesített sávszélességű meghaladja a megengedett maximális korlátot. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | A video- és audiotartalmak FLVTag történő küldés időbélyegzője legyen az RTMP-kódoló érvénytelen. |

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

Az alábbi példa bemutatja a séma, a **LiveEventEncoderConnected** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| streamId | sztring | A stream vagy kapcsolat azonosítója. Kódoló vagy ügyfél ezt az Azonosítót a bemeneti URL-címet a nyújtó felelős. |
| ingestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |
| EncoderIp | sztring | A kódoló IP-cím. |
| encoderPort | sztring | A kódoló a stream forrását a port. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Az alábbi példa bemutatja a séma, a **LiveEventEncoderDisconnected** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| streamId | sztring | A stream vagy kapcsolat azonosítója. Kódoló vagy ügyfél felelős adja hozzá ezt az Azonosítót a bemeneti URL-címet. |  
| ingestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |  
| EncoderIp | sztring | A kódoló IP-cím. |
| encoderPort | sztring | A kódoló a stream forrását a port. |
| Eredménykód | sztring | A kódoló leválasztása a okát. Lehet, hogy biztonságos kapcsolat bontása vagy hiba történt. A eredménykódok az alábbi táblázatban láthatók. |

Az eredmény hibakódok a következők:

| Eredménykód | Leírás |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | RTMP-munkamenet üresjárati megengedett határidő után túllépte az időkorlátot. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | A video- és audiotartalmak FLVTag történő küldés időbélyegzője legyen az RTMP-kódoló érvénytelen. |
| MPE_CAPACITY_LIMIT_REACHED | Túl gyorsan adatokat küldő kódoló. |
| Ismeretlen hibakód alapján | Az ezen hibakódok elég memória hiba az ismétlődő bejegyzéseket a szórótáblája között lehet. |

A biztonságos kapcsolat bontása eredménykódok a következők:

| Eredménykód | Leírás |
| ----------- | ----------- |
| S_OK | A kódoló leválasztása sikeresen megtörtént. |
| MPE_CLIENT_TERMINATED_SESSION | Kódoló leválasztása (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kódoló leválasztása (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Csatorna visszaállítási parancs érkezik. |
| MPI_REST_API_CHANNEL_STOP | Csatorna a kapott leállítási parancsot. |
| MPI_REST_API_CHANNEL_STOP | A csatorna karbantartás alatt áll. |
| MPI_STREAM_HIT_EOF | A kódoló EOF stream küldi. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Az alábbi példa bemutatja a séma, a **LiveEventIncomingDataChunkDropped** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track neve. |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| időbélyeg | sztring | Az adathalmaz időbélyegét eltávolította. |
| időskálára | sztring | Az időbélyeg-időskálára. |
| Eredménykód | sztring | Az adatok adatköteget csepp OK. **FragmentDrop_OverlapTimestamp** vagy **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Az alábbi példa bemutatja a séma, a **LiveEventIncomingStreamReceived** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track nevét (vagy a kódoló vagy -esetén RTMP-kiszolgálót állít elő, a megadott *TrackType_Bitrate* formátumban). |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| ingestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |
| EncoderIp | sztring  | A kódoló IP-cím. |
| encoderPort | sztring | A kódoló a stream forrását a port. |
| időbélyeg | sztring | Első időbélyegzője az adathalmaz kapott. |
| időskálára | sztring | Amelyben időbélyeg jelölt időskálára. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Az alábbi példa bemutatja a séma, a **LiveEventIncomingStreamsOutOfSync** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| minLastTimestamp | sztring | Legalább utolsó időbélyegeket minden szám (hang- vagy) között. |
| TypeOfTrackWithMinLastTimestamp | sztring | A nyomon követése (hang- vagy) a minimális utolsó időbélyeg típusú. |
| maxLastTimestamp | sztring | Minden szám (hang- vagy) között az összes időbélyegei legfeljebb. |
| typeOfTrackWithMaxLastTimestamp | sztring | A nyomon követése (hang- vagy) az utolsó maximális időbélyegző típusú. |
| TimescaleOfMinLastTimestamp| sztring | Lekérdezi a "MinLastTimestamp" jelöli, amelyben időskálára.|
| timescaleOfMaxLastTimestamp| sztring | Lekérdezi a "MaxLastTimestamp" jelöli, amelyben időskálára.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Az alábbi példa bemutatja a séma, a **LiveEventIncomingVideoStreamsOutOfSync** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| FirstTimestamp | sztring | Időbélyeg típusú videó nyomon követi és minőségi szintjének érkezett. |
| firstDuration | sztring | Az adathalmaz első időbélyeggel időtartama. |
| secondTimestamp | sztring  | Az időbélyeg néhány egyéb nyomon követése és minőségi szintjét a videó típus érkezett. |
| secondDuration | sztring | A második időbélyeggel adathalmaz időtartama. |
| időskálára | sztring | Időskálára időbélyegeket és időtartamát.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Az alábbi példa bemutatja a séma, a **LiveEventIngestHeartbeat** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track nevét (vagy a kódoló vagy -esetén RTMP-kiszolgálót állít elő, a megadott *TrackType_Bitrate* formátumban). |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| IncomingBitrate | egész szám | Számított sávszélességű származó kódolóval adattömbök alapján. |
| LastTimestamp | sztring | Az elmúlt 20 másodperc nyomon kapott legújabb időbélyegző. |
| időskálára | sztring | Időskálára, amelyben időbélyeggel van megadva. |
| OverlapCount | egész szám | Adattömbök száma az elmúlt 20 másodperc kellett átfedett időbélyegzőnél. |
| DiscontinuityCount | egész szám | Az elmúlt 20 másodperc megfigyelt folytonosság megszakítását száma. |
| nonIncreasingCount | egész szám | A múltban időbélyegzőnél adattömbök száma az elmúlt 20 másodperc alatt érkezett. |
| unexpectedBitrate | Logikai | Ha a várt és tényleges bitsebességre való átkódolása eltér az elmúlt 20 másodperc alatt több mint engedélyezett korlátot. IGAZ, ha, és csak akkor, ha, incomingBitrate > = 2 * sávszélességű vagy incomingBitrate < = vagy IncomingBitrate sávszélességű/2 = 0. |
| state | sztring | Az élő esemény állapota. |
| kifogástalan | Logikai | Azt jelzi, hogy betöltési állapota megfelelő számát és a jelzők alapján. Kifogástalan igaz. Ha overlapCount = 0 & & discontinuityCount = 0 & & nonIncreasingCount = 0 & & unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

Az alábbi példa bemutatja a séma, a **LiveEventTrackDiscontinuityDetected** esemény: 

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

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| trackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track nevét (vagy a kódoló vagy -esetén RTMP-kiszolgálót állít elő, a megadott *TrackType_Bitrate* formátumban). |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| PreviousTimestamp | sztring | Az előző töredék időbélyegét. |
| NewTimestamp | sztring | Az aktuális töredék időbélyegét. |
| DiscontinuityGap | sztring | Különbség a két időbélyegek felett. |
| időskálára | sztring | A melyik időbélyeg és a kihagyást gap időskálára szerepelnek. |

### <a name="common-event-properties"></a>Közös Eseménytulajdonságok

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A EventGrid-témakör. Ez a tulajdonság az erőforrás-azonosító, a Media Services-fiók rendelkezik. |
| tárgy | sztring | A Media Services-csatorna a Media Services-fiók erőforrás elérési útja. A témakör és a tulajdonos számára, az erőforrás a feladat AZONOSÍTÓJÁT kiszámításával. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. Például "Microsoft.Media.JobStateChange." |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adat | objektum | A Media Services eseményadatokat. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

## <a name="next-steps"></a>További lépések

[Regisztráljon a feladat állapotváltozási események](job-state-events-cli-how-to.md)

## <a name="see-also"></a>Lásd még

- [EventGrid .NET SDK-t, amely tartalmazza a Media Services-események](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [A Media Services-események definíciók](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
