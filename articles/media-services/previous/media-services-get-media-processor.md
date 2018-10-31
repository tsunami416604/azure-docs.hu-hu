---
title: Hogyan hozhat létre az Azure Media Services SDK használatával a .NET-keretrendszerhez készült media processzort |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre kódolása, formátum konvertálása, titkosítása vagy visszafejtése a médiatartalmak az Azure Media Services media processzor összetevő. Kódminták nyelven írták C# , és használja a Media Services SDK a .NET-hez.
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
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 64e353bbb83c7696960fc1d2e478478afbc94241
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249237"
---
# <a name="how-to-get-a-media-processor-instance"></a>Útmutató: a Media processzor-példány beolvasása
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
A Media Services media processzort egy összetevő, amely egy meghatározott feldolgozási feladatot, például a kódolást, kezeli az átalakítás titkosítása vagy visszafejtése a médiatartalom formátumban. Általában létrehozhat egy médiaprocesszorral kódolása, titkosítása vagy konvertálás médiatartalmak formátumát feladat létrehozásakor.

## <a name="azure-media-processors"></a>Az Azure médiaelemzés médiafeldolgozói 

A következő témakör ismerteti a médiaelemzés médiafeldolgozói listáját tartalmazza:

* [Médiafeldolgozók kódolása](scenarios-and-availability.md#encoding-media-processors)
* [Elemzési médiafeldolgozók](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Első Médiafeldolgozót.

A következő metódust mutatja egy adathordozó processzor-példány beolvasása. A mintakód feltételezi, hogy egy modul szintű nevű változó használatát **_context** való hivatkozáshoz kiszolgálói környezetbe, a szakaszban leírt módon [hogyan: Kapcsolódás a Media Services programozott módon](media-services-use-aad-auth-to-access-ams-api.md).

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
Most, hogy hogyan tehet szert egy adathordozó processzorpéldány ismeri, nyissa meg a [adategység kódolása](media-services-dotnet-encode-with-media-encoder-standard.md) témakör, amely tartalmazza az adategység kódolása a Media Encoder Standard használatával.

