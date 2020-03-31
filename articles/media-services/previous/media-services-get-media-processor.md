---
title: Médiaprocesszor létrehozása az Azure Media Services SDK használatával . Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre médiaprocesszor-összetevőt az Azure Media Services médiatartalmának kódolásához, konvertálásához, titkosításához vagy visszafejtéséhez. A kódminták C# nyelven íródnak, és a Media Services SDK-t használják a .NET-hez.
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
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463839"
---
# <a name="how-to-get-a-media-processor-instance"></a>Útmutató: Médiaprocesszor-példány beszereznie
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Többi](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
A Media Services szolgáltatásban a médiaprocesszor egy olyan összetevő, amely egy adott feldolgozási feladatot kezel, például kódolást, formátumátalakítást, titkosítást vagy visszafejtést. Általában akkor hoz létre médiaprocesszort, amikor médiatartalom-formátumkódolásra, titkosításra vagy konvertálására irányuló feladatot hoz létre.

## <a name="azure-media-processors"></a>Azure médiaprocesszorok 

Az alábbi témakör a médiaprocesszorok listáját tartalmazza:

* [Médiafeldolgozók kódolása](scenarios-and-availability.md#encoding-media-processors)
* [Elemzési médiafeldolgozók](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Médiaprocesszor bekéselése

Az alábbi módszer bemutatja, hogyan szerezhet be egy médiaprocesszor-példányt. A példaerre vonatkozó példa egy **_context** nevű modulszintű változó használatát feltételezi, amely a kiszolgáló környezetére hivatkozik a Hogyan csatlakozzon a [Media Services programhoz programozott módon](media-services-use-aad-auth-to-access-ams-api.md)című szakaszban leírtak szerint.

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, hogyan szerezhet be egy médiaprocesszor-példányt, nyissa meg a [Hogyan kódoljon egy eszköz](media-services-dotnet-encode-with-media-encoder-standard.md) témakört, amely megmutatja, hogyan használhatja a Media Encoder Standard-ot egy eszköz kódolásához.

