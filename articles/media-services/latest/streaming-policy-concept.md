---
title: Streamelési szabályzatok az Azure Media Servicesszolgáltatásban | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogy mik a streamelési szabályzatok, és hogyan használják az Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392929"
---
# <a name="streaming-policies"></a>Streamelési szabályok

Az Azure Media Services v3-as [eszközében a streamelési szabályzatok](https://docs.microsoft.com/rest/api/media/streamingpolicies) lehetővé teszik a streamelési protokollok és titkosítási beállítások meghatározását a [streamelési lokátorokhoz.](streaming-locators-concept.md) A Media Services v3 néhány előre definiált streamelési szabályzatot biztosít, így közvetlenül használhatja őket próbaverzióra vagy éles környezetre. 

A jelenleg rendelkezésre álló előre definiált streamelési házirendek:<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* "Predefined_DownloadAndClearStreaming"
* "Predefined_ClearKey"
* "Predefined_MultiDrmCencStreaming" 
* "Predefined_MultiDrmStreaming"

A következő "Döntési fa" segítségével kiválaszthatja az előre meghatározott streamelési szabályzatot a forgatókönyvhöz.

> [!IMPORTANT]
> * A Datetime típusú **streamelési házirendek** tulajdonságai mindig UTC formátumúak.
> * A Media Service-fiókhoz korlátozott házirendeket kell terveznie, és újra fel kell használnia őket a streamelési lokátorokhoz, amikor ugyanazokra a beállításokra van szükség. További információt a [Kvóták és korlátozások](limits-quotas-constraints.md)című témakörben talál.

## <a name="decision-tree"></a>Döntési fa

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

A tartalom titkosításakor létre kell [hoznia egy tartalomkulcs-házirendet,](content-key-policy-concept.md)a **tartalomkulcs-házirendre** nincs szükség a tiszta adatfolyam-továbbításhoz vagy -letöltéshez. 

Ha speciális követelményekkel rendelkezik (például ha különböző protokollokat szeretne megadni, egyéni kulcskézbesítési szolgáltatást kell használnia, vagy tiszta hangsávot kell használnia), [létrehozhat](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) egy egyéni streamelési szabályzatot. 

## <a name="get-a-streaming-policy-definition"></a>Streamelési szabályzat definíciójának beszereznie  

Ha szeretné látni a streaming szabályzat definícióját, használja a [Leget és](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) adja meg a házirend nevét. Példa:

### <a name="rest"></a>REST

Kérés:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Válasz:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Szűrés, rendelés, lapozás

Lásd: [Media Services-entitások szűrése, rendelése, lapozása.](entities-overview.md)

## <a name="next-steps"></a>További lépések

* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata](protect-with-aes128.md)
* [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
