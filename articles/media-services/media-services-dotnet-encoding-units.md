---
title: "Kódolási egységeket - Azure általi feldolgozás media méretezése |}  Microsoft Docs"
description: "Megtudhatja, hogyan kell a .NET kódolási egység hozzáadása"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 72a8729d22a9e76c8076d7a3347619a2163e4f09
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>A kódolás méretezése a .NET SDK használatával
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Mindenképpen tekintse át a [áttekintése](media-services-scale-media-processing-overview.md) témakör feldolgozása media méretezésével kapcsolatos további információkat a témakör.
> 
> 

A fenntartott egységnek típusának és a kódoláshoz fenntartott egység .NET SDK használatával számának módosításához tegye a következőket:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Támogatási jegy megnyitása
Alapértelmezés szerint minden Media Services-fiók méretezhető legfeljebb 25 kódolás és 5 igény, folyamatos átvitelhez fenntartott egységek. Magasabb határérték kérhet egy támogatási jegy megnyitásával.

### <a name="open-a-support-ticket"></a>Támogatási jegy megnyitása
Nyissa meg a támogatási jegy tegye a következőket:

1. Kattintson a [segítségre van szüksége](https://manage.windowsazure.com/?getsupport=true). Ha nem jelentkezett be, kérni fogja a hitelesítő adatok megadását.
2. Válassza ki előfizetését.
3. A támogatási típusa válassza a "Műszaki".
4. Kattintson az "A jegy létrehozása".
5. Válassza ki az "Azure Media Services" a termék listájában jelenik meg a következő oldalon.
6. Válassza ki a "probléma típusa" megfelelő a problémát.
7. Kattintson a Tovább gombra.
8. Kövesse az utasításokat a következő oldalon, és adja meg a probléma részleteit.
9. Kattintson a hibajegyet nyújt.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

