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
ms.openlocfilehash: b167d3424d520cf8be515eec9d495164dd9785ab
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682095"
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
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

A teljes példa: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="liveevents-latency"></a>LiveEvents késés

Az alábbi táblázatok a Media Services szolgáltatásban a hozzájárulás hírcsatorna eléri a szolgáltatást, hogy amikor egy megjelenítőt látja a lejátszó a lejátszás ameddig késés (Ha engedélyezve van a LowLatency jelző) jellemző eredmény megjelenítése. Közel valós idejű optimális használatához meg kell finomhangolása lefelé 1 másodperc "Csoportot a képek" (Képcsoporttal) hossza a kódoló beállításai. Magasabb Képcsoporttal hossza használatakor, sávszélesség-használat minimalizálása érdekében, és csökkentheti a sávszélességű azonos sebessége alapján. Különösen hasznos a videókat, amelyekhez kevesebb mozgásban lévő adatoknak egyaránt.

### <a name="pass-through"></a>Továbbítás 

||2S Képcsoporttal kis késés, engedélyezve van|1s Képcsoporttal kis késés, engedélyezve van|
|---|---|---|
|VONAL-és a|10 egység|8S|
|A natív iOS-lejátszó HLS|14s|10 egység|

### <a name="live-encoding"></a>Live Encoding

||2S Képcsoporttal kis késés, engedélyezve van|1s Képcsoporttal kis késés, engedélyezve van|
|---|---|---|
|VONAL-és a|14s|10 egység|
|A natív iOS-lejátszó HLS|18s|13s|

> [!NOTE]
> A végpontok közötti késés eltérőek lehetnek attól függően, helyi hálózati körülmények vagy egy CDN gyorsítótárazási réteg bevezetésével. A pontos konfigurációk kell tesztelni.

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)

