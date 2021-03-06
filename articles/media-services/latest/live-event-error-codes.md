---
title: Azure Media Services élő események hibakódja | Microsoft Docs
description: Ez a cikk az élő események hibakódait sorolja fel.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 13277c3d46d5b68aa5705699f45a2bf9e97a78d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291534"
---
# <a name="media-services-live-event-error-codes"></a>Media Services élő események hibakódja

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az ebben a szakaszban szereplő táblázatok az [élő események](live-events-outputs-concept.md) hibakódait sorolja fel.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Amikor előfizet egy élő esemény [Event Grid](../../event-grid/index.yml) eseményeire, a következő hibák valamelyikét láthatja a [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected)  eseményből.

| Eredménykód | Leírás |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Helytelen betöltési URL-cím |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | A kódoló IP-címe nem található a beállított IP-engedélyezési listán |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Az RTMP-kódoló nem küldött setDataFrame-parancsot. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | A megadott kodek nem támogatott. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |A Media Description információ nem érkezett meg a tényleges adathordozó-adatok kézbesítése előtt.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |A hang-vagy képtípushoz tartozó minőségek száma túllépte a maximálisan megengedett korlátot.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Egy élő esemény vagy csatorna szolgáltatás teljes bejövő bitrátája túllépte a maximálisan megengedett korlátot.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | A videó-vagy hangFLVTag időbélyege érvénytelen az RTMP-kódolótól. |
| MPE_INGEST_FRAMERATE_EXCEEDED | A bejövő kódoló a framerátával betöltött adatfolyamokat túllépte az élő események/csatornák kódolásához megengedett maximális 30fps.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | A bejövő kódoló által betöltött adatfolyamok meghaladták a következő engedélyezett felbontásokat: 1920x1088 az élő események/csatornák kódolásához, valamint a 4096 x 2160 a továbbított élő eseményekhez/csatornákhoz.|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | Az élő esemény nagy mennyiségű hanganyagot kapott egyszerre, vagy nagy mennyiségű videó-adatkeret nélkül. Leválasztottuk a kódolót annak érdekében, hogy a megfelelő adatokkal próbálkozzon újra. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

A következő hibák valamelyikét láthatja a [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) eseményből.

|Eredménykód|Leírás|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Az RTMP-munkamenet időtúllépést követően időtúllépést követett el az engedélyezett időkorlát után.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|A videó-vagy hangFLVTag időbélyege érvénytelen az RTMP-kódolótól.|
|MPE_CAPACITY_LIMIT_REACHED|A kódoló túl gyorsan küldi az adatokat.|
|Ismeretlen hibakódok|Ezek a hibakódok a kivonatoló leképezésben a memóriából a duplikált bejegyzések között lehetnek.|


## <a name="see-also"></a>Lásd még

[Adatfolyam-végpont (forrás) hibakódok](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: élő közvetítés Media Services](stream-live-tutorial-with-api.md)
