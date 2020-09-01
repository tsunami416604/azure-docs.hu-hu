---
title: Adathordozó-feldolgozó létrehozása a .NET-hez készült Azure Media Services SDK-val | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre a médiafájlok kódolására, konvertálására, titkosítására és visszafejtésére szolgáló adathordozó-feldolgozó összetevőt Azure Media Services számára. A kód minták C# nyelven íródtak, és a .NET-hez készült Media Services SDK-t használják.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: df89acb7d3686a478c87c12bbf8a42962597dca6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269386"
---
# <a name="how-to-get-a-media-processor-instance"></a>Útmutató: adathordozó-feldolgozó példány beszerzése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
Media Services a Media Processor olyan összetevő, amely egy adott feldolgozási feladatot kezel, például a kódolást, a konvertálást, a titkosítást vagy a médiatartalom visszafejtését. Általában akkor hozhat létre adathordozó-processzort, ha a tartalom kódolására, titkosítására vagy konvertálására szolgáló feladatot hoz létre.

## <a name="azure-media-processors"></a>Azure Media processors 

A következő témakör az adathordozó-processzorok listáját tartalmazza:

* [Médiafeldolgozók kódolása](scenarios-and-availability.md#encoding-media-processors)
* [Elemzési médiafeldolgozók](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Adathordozó-feldolgozó letöltése

A következő módszer azt mutatja be, hogyan szerezhet be egy Media Processor-példányt. A kód példa feltételezi, hogy egy **_context** nevű modul szintű változót használ a kiszolgálói környezetre való hivatkozáshoz a következő témakörben ismertetett módon [: a Kapcsolódás a Media Services programhoz](media-services-use-aad-auth-to-access-ams-api.md)című részben leírtak szerint.

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy már tudja, hogyan szerezhet be egy Media Processor-példányt, ugorjon az adategységek [kódolása](media-services-dotnet-encode-with-media-encoder-standard.md) témakörre, amely bemutatja, hogyan kódolhat egy adategységet a Media Encoder standard használatával.

