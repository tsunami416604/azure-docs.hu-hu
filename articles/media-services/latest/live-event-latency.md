---
title: Az Azure Media Servicesben videókhoz késés |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt a videókhoz késés, és bemutatja, hogyan állíthatja be a közel valós idejű.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619816"
---
# <a name="liveevent-latency-in-media-services"></a>A Media Services videókhoz késés

Ez a cikk bemutatja, hogyan lehet beállítani a közel valós idejű a egy **videókhoz**. Emellett ismerteti a tipikus eredményeket közel valós idejű beállításainak használatával által is látható. Az eredmények függ a CDN és a hálózati késést. 

Az új használandó **LowLatency** szolgáltatást, állítsa be a **StreamOptionsFlag** való **LowLatency** a a **videókhoz**. A stream működik-e, ha a [Azure Media Player](http://ampdemo.azureedge.net/) (és) bemutató lapon, és módosítsa a lejátszási a használatához a "alacsony késés a heurisztika profil".

A következő .NET példa bemutatja, hogyan állíthatja be **LowLatency** a a **videókhoz**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

A teljes példa: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Az átmenő LiveEvents késés

Az alábbi táblázat jellemző várakozási ideje (Ha engedélyezve van a LowLatency jelző) eredményei a Media Services szolgáltatásban a hozzájárulás hírcsatorna eléri a szolgáltatást, hogy ha egy lejátszási is kérhető az idő mérése történik.

||2S Képcsoporttal kis késés, engedélyezve van|1s Képcsoporttal kis késés, engedélyezve van|
|---|---|---|
|VONAL-és a|10 egység|8S|
|A natív iOS-lejátszó HLS|14s|10 egység|

> [!NOTE]
> A végpontok közötti késés eltérőek lehetnek attól függően, helyi hálózati körülmények vagy egy CDN gyorsítótárazási réteg bevezetésével. A pontos konfigurációk kell tesztelni.

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)

