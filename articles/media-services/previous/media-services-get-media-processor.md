---
title: Az Azure Media Services SDK használatával a .NET-keretrendszerhez készült media processzort létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy adathordozó feldolgozó összetevő kódolni, formátum konvertálni, titkosítása vagy visszafejtése médiatartalom Azure Media Services. Kódminták C# nyelven íródtak, és a Media Services SDK használata a .NET-hez.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 60da450c11a2e65d96c15798854adfef371a694f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788192"
---
# <a name="how-to-get-a-media-processor-instance"></a>Útmutató: a Media Processzorpéldány beolvasása
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
A Media Services media processzort összetevő, amely kezeli a különleges feldolgozási feladat, például a kódolása titkosítása vagy visszafejtése médiatartalom az átalakítás formátumban. Általában létrehozhat egy adathordozó processzor kódolása, titkosítása vagy alakítsa át a formátum a médiatartalom feladat létrehozásakor.

## <a name="azure-media-processors"></a>Az Azure media processzor 

A következő témakör sorolja fel az adathordozó processzorok:

* [Kódolási media processzor](scenarios-and-availability.md#encoding-media-processors)
* [Elemzés media processzor](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Media processzor beolvasása

A következő metódus hogyan kérhet egy adathordozó processzorpéldány jeleníti meg. A Kódpélda feltételezi nevű modul szintű változó használatát **_context** kiszolgálói környezetbe hivatkozni, a szakaszban leírt módon [Útmutató: Media Services programokon keresztül csatlakozni](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy hogyan kérhet egy adathordozó processzorpéldány ismeri, navigáljon a [egy eszköz kódolással](media-services-dotnet-encode-with-media-encoder-standard.md) témakör, amely tartalmazza a Media Encoder Standard segítségével egy eszköz kódolása.

