---
title: Az Azure Media Services élő eseményhibakódjai | Microsoft dokumentumok
description: Ez a cikk az élő esemény hibakódjait sorolja fel.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654579"
---
# <a name="media-services-live-event-error-codes"></a>Media Services Live Event hibakódok

Az ebben a szakaszban található táblázatok az [Élő esemény](live-events-outputs-concept.md) hibakódjait sorolják fel.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionElutasított

Amikor feliratkozik az [Eseményrács](https://docs.microsoft.com/azure/event-grid/) eseményeire egy élő eseményhez, a [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) esemény alábbi hibáinak egyike jelenhet meg.

| Eredménykód | Leírás |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Helytelen betöltési URL-cím |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | A kódoló IP-címe nincs megállítva az IP-engedélyezési listában |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Az RTMP kódoló nem küldte el a setDataFrame parancsot. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | A megadott kodek nem támogatott. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |A médialeírási információ nem érkezett meg a tényleges médiaadatok kézbesítése előtt.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |A hang- vagy videotípus minőségének száma meghaladta a megengedett maximális korlátot.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Az élő eseményekben vagy csatornaszolgáltatásban beérkező teljes átviteli sebesség meghaladta a megengedett maximális korlátot.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | A video- vagy audio FLVTag időbélyege érvénytelen az RTMP kódolótól. |
| MPE_INGEST_FRAMERATE_EXCEEDED | A bejövő kódoló lenyelte a framerates-t tartalmazó adatfolyamokat, amelyek túllépték az élő események/csatornák kódolásához engedélyezett maximális 30 képkocka/órás sebességet.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | A bejövő kódoló lenyelt adatfolyamok meghaladta a következő megengedett felbontások: 1920x1088 kódolásélő események / csatornák és 4096 x 2160 átmenő élő események / csatornák.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderLe

A [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) esemény alábbi hibáinak egyike jelenhet meg.

|Eredménykód|Leírás|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Az RTMP-munkamenet időtúllépés után túllépte az engedélyezett időkorlátot.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|A video- vagy audio FLVTag időbélyege érvénytelen az RTMP kódolótól.|
|MPE_CAPACITY_LIMIT_REACHED|A kódoló túl gyorsan küldi az adatokat.|
|Ismeretlen hibakódok|Ezek a hibakódok a memóriahibától a kivonattérkép ismétlődő bejegyzéseiig terjedhetnek.|


## <a name="see-also"></a>Lásd még

[Végpont (Origin) streamelési hibakódjai](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Élő közvetítés a Media Services szolgáltatással](stream-live-tutorial-with-api.md)
