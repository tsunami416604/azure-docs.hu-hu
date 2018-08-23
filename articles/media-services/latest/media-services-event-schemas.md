---
title: A Media Services-események Azure Event Grid-sémák
description: A Media Services-események az Azure Event GRID használatával biztosított tulajdonságait ismerteti
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055314"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>A Media Services-események Azure Event Grid-sémák

Ez a cikk biztosítja a sémák és a tulajdonságok a Media Services-események.

Mintaszkriptek és oktatóanyagok listáját lásd: [Media Services eseményforrás](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

A Media Services a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| A feladat állapota megváltozik. |
| Microsoft.Media.LiveEventConnectionRejected | Kódoló kapcsolatfelvételt. |
| Microsoft.Media.LiveEventEncoderConnected | Kódoló az élő esemény kapcsolatot létesít. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kódoló leválasztása. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Kiszolgáló adathalmaz csökken, mert késő vagy egy átfedő timestamp (időbélyeg az új adatok adattömbök érték kisebb, mint az előző adathalmaz befejezési időpontja). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media kiszolgáló egyes nyomon követése az első adathalmaz kap a streamben vagy a kapcsolat. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-kiszolgáló észleli a hang és video-adatfolyamokat nincsenek szinkronban. Figyelmeztetés használjuk, mert előfordulhat, hogy nem változik a felhasználói élmény. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-kiszolgáló észleli a külső kódoló érkező két video-adatfolyamokat bármelyikét nincsenek szinkronban. Figyelmeztetés használjuk, mert előfordulhat, hogy nem változik a felhasználói élmény. |
| Microsoft.Media.LiveEventIngestHeartbeat | Közzétett 20 másodpercenként minden egyes nyomon követése, az élő esemény futtatásakor. Itt állapotösszegzése betöltését. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Kiszolgáló kihagyást észleli a bejövő nyomon követése. |

A két kategóriába sorolhatók a **élő** események: stream-szintű eseményeit, és nyomon követése-szintű eseményeit. 

Stream-szintű eseményeit stream vagy kapcsolat aktiválódnak. Minden egyes esemény egy `StreamId` paraméter, amely azonosítja a kapcsolatot, vagy adatfolyamként továbbíthatja. Minden stream vagy kapcsolat van egy vagy több, különböző típusú nyomon követi. Egy kapcsolat forrása lehet például négy videó nyomon követi, és egy hangsávra. A stream esemény típusok a következők:

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Nyomon követheti-szintű eseményeit számonkénti aktiválódnak. A sledování událostí típusok a következők:

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

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
| state | sztring | A feladat ebben az esetben bejelentett új állapotát. Például "várólistán: erőforrások vár a feladat" vagy "ütemezett: A feladat indítása készen áll".|

Ha a feladat állapota lehet egy értéket: *várólistán*, *ütemezett*, *feldolgozása*, *befejezett*, *hiba*, *Meg lett szakítva*, *megszakítása*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

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
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| StreamId | sztring | A stream vagy kapcsolat azonosítója. Kódoló vagy ügyfél felelős adja hozzá ezt az Azonosítót a bemeneti URL-címet. |  
| IngestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |  
| EncoderIp | sztring | A kódoló IP-cím. |
| EncoderPort | sztring | A kódoló a stream forrását a port. |
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

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

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
| StreamId | sztring | A stream vagy kapcsolat azonosítója. Kódoló vagy ügyfél ezt az Azonosítót a bemeneti URL-címet a nyújtó felelős. |
| IngestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |
| EncoderIp | sztring | A kódoló IP-cím. |
| EncoderPort | sztring | A kódoló a stream forrását a port. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

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
| StreamId | sztring | A stream vagy kapcsolat azonosítója. Kódoló vagy ügyfél felelős adja hozzá ezt az Azonosítót a bemeneti URL-címet. |  
| IngestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |  
| EncoderIp | sztring | A kódoló IP-cím. |
| EncoderPort | sztring | A kódoló a stream forrását a port. |
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

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

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
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| TrackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track neve. |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| Időbélyeg | sztring | Az adathalmaz időbélyegét eltávolította. |
| időskálára | sztring | Az időbélyeg-időskálára. |
| Eredménykód | sztring | Az adatok adatköteget csepp OK. **FragmentDrop_OverlapTimestamp** vagy **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

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
| TrackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track nevét (vagy a kódoló vagy -esetén RTMP-kiszolgálót állít elő, a megadott *TrackType_Bitrate* formátumban). |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| IngestUrl | sztring | Betöltési URL-címe, az élő esemény által biztosított. |
| EncoderIp | sztring  | A kódoló IP-cím. |
| EncoderPort | sztring | A kódoló a stream forrását a port. |
| Időbélyeg | sztring | Első időbélyegzője az adathalmaz kapott. |
| időskálára | sztring | Amelyben időbélyeg jelölt időskálára. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

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
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| MinLastTimestamp | sztring | Legalább utolsó időbélyegeket minden szám (hang- vagy) között. |
| TypeOfTrackWithMinLastTimestamp | sztring | A nyomon követése (hang- vagy) a minimális utolsó időbélyeg típusú. |
| MaxLastTimestamp | sztring | Minden szám (hang- vagy) között az összes időbélyegei legfeljebb. |
| TypeOfTrackWithMaxLastTimestamp | sztring | A nyomon követése (hang- vagy) az utolsó maximális időbélyegző típusú. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

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
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| FirstTimestamp | sztring | Időbélyeg típusú videó nyomon követi és minőségi szintjének érkezett. |
| FirstDuration | sztring | Az adathalmaz első időbélyeggel időtartama. |
| SecondTimestamp | sztring  | Az időbélyeg néhány egyéb nyomon követése és minőségi szintjét a videó típus érkezett. |
| SecondDuration | sztring | A második időbélyeggel adathalmaz időtartama. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

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
| TrackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track nevét (vagy a kódoló vagy -esetén RTMP-kiszolgálót állít elő, a megadott *TrackType_Bitrate* formátumban). |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| IncomingBitrate | egész szám | Számított sávszélességű származó kódolóval adattömbök alapján. |
| LastTimestamp | sztring | Az elmúlt 20 másodperc nyomon kapott legújabb időbélyegző. |
| időskálára | sztring | Időskálára, amelyben időbélyeggel van megadva. |
| OverlapCount | egész szám | Adattömbök száma az elmúlt 20 másodperc kellett átfedett időbélyegzőnél. |
| DiscontinuityCount | egész szám | Az elmúlt 20 másodperc megfigyelt folytonosság megszakítását száma. |
| NonIncreasingCount | egész szám | A múltban időbélyegzőnél adattömbök száma az elmúlt 20 másodperc alatt érkezett. |
| UnexpectedBitrate | Logikai | Ha a várt és tényleges bitsebességre való átkódolása eltér az elmúlt 20 másodperc alatt több mint engedélyezett korlátot. IGAZ, ha, és csak akkor, ha, IncomingBitrate > = 2 * sávszélességű vagy IncomingBitrate < = vagy IncomingBitrate sávszélességű/2 = 0. |
| Állapot | sztring | Az élő esemény állapota. |
| Kifogástalan | Logikai | Azt jelzi, hogy betöltési állapota megfelelő számát és a jelzők alapján. Kifogástalan igaz. Ha OverlapCount = 0 & & DiscontinuityCount = 0 & & NonIncreasingCount = 0 & & UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

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
| TrackType | sztring | A track típusa (hang / kép). |
| TrackName | sztring | A track nevét (vagy a kódoló vagy -esetén RTMP-kiszolgálót állít elő, a megadott *TrackType_Bitrate* formátumban). |
| Átviteli sebesség | egész szám | A szám sávszélességű. |
| PreviousTimestamp | sztring | Az előző töredék időbélyegét. |
| NewTimestamp | sztring | Az aktuális töredék időbélyegét. |
| DiscontinuityGap | sztring | Különbség a két időbélyegek felett. |
| időskálára | sztring | A melyik időbélyeg és a kihagyást gap időskálára szerepelnek. |

## <a name="common-event-properties"></a>Közös Eseménytulajdonságok

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A EventGrid-témakör. Ez a tulajdonság az erőforrás-azonosító, a Media Services-fiók rendelkezik. |
| Tulajdonos | sztring | A Media Services-csatorna a Media Services-fiók erőforrás elérési útja. A témakör és a tulajdonos számára, az erőforrás a feladat AZONOSÍTÓJÁT kiszámításával. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. Például "Microsoft.Media.JobStateChange." |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | A Media Services eseményadatokat. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

## <a name="next-steps"></a>További lépések

[Regisztráljon a feladat állapotváltozási események](job-state-events-cli-how-to.md)