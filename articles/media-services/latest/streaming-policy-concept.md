---
title: Házirendek az Azure Media Services Streaming |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési szabályzatok vannak, és hogyan használják az Azure Media Services ismertetése.
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
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392929"
---
# <a name="streaming-policies"></a>Streamelési szabályok

Az Azure Media Services v3 [szabályzatokat. adatfolyam](https://docs.microsoft.com/rest/api/media/streamingpolicies) lehetővé teszi az adatfolyam-továbbítási protokollok és a titkosítási beállítások megadása a [Streamelési Lokátorok](streaming-locators-concept.md). A Media Services v3 tartalmaz néhány előre definiált adatfolyam-szabályzatok, így közvetlenül a próbaverzióra vagy éles környezetben is használhatja őket. 

A jelenleg elérhető előre definiált adatfolyam-szabályzatokat:<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* "Predefined_DownloadAndClearStreaming"
* "Predefined_ClearKey"
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

Válasszon egy előre definiált adatfolyam-szabályzatot a forgatókönyvhöz a következő "döntési fa" segítségével.

> [!IMPORTANT]
> * Tulajdonságainak **szabályzatokat. adatfolyam** a DateTime típusú állandóan UTC formátumban vannak.
> * Korlátozott számú házirendeket tervezzen a Media Services-fiók és újból felhasználja őket a Streamelési Lokátorok, amikor szükség van a beállítások. További információkért lásd: [kvóták és korlátozások](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Döntési fa

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Tartalom titkosítása, ha szeretne létrehozni egy [tartalom kulcs házirend](content-key-policy-concept.md), a **tartalom kulcs házirend** nem szükségesek a tiszta streamelési vagy letöltése. 

Ha nincsenek különleges követelmények (például ha más protokollt adja meg, ezzel kell használnia egy egyéni kulcstovábbítást, vagy szeretné használni, törölje a jelet hangsávra), akkor az [létrehozása](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) egyéni Streamelési házirendet. 

## <a name="get-a-streaming-policy-definition"></a>Egy adatfolyam-definíció beolvasása  

Ha meg szeretné tekinteni a egy adatfolyam-szabályzat definíciója, [első](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) , és adja meg a szabályzat nevét. Példa:

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

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [AES-128, a dinamikus titkosítás és a kulcstovábbítást használata](protect-with-aes128.md)
* [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
