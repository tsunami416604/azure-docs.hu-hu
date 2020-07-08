---
title: Adathordozó-feldolgozás skálázása kódolási egységek hozzáadásával – Azure |  Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá kódolási egységeket Azure Media Services .NET-tel.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milangada
ms.openlocfilehash: dd2a94a1d10e4c8078e5437959bf7e101b3c6dd7
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964770"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>A kódolás méretezése a .NET SDK használatával
> [!div class="op_single_selector"]
> * [Portál](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Tekintse át az [áttekintést](media-services-scale-media-processing-overview.md) , hogy további információkat kapjon a média-feldolgozás méretezéséről.
> 
> 

A fenntartott egység típusának és a kódoláshoz fenntartott egységek számának a .NET SDK használatával történő módosításához tegye a következőket:

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>Támogatási jegy megnyitása

Alapértelmezés szerint minden Media Services fiók legfeljebb 10 S2 vagy S3 Media szolgáltatás számára fenntartott egységre (MRUs) vagy 25 S1 MRUs, valamint 5 igény szerinti folyamatos átvitelre fenntartott egységre méretezhető. A támogatási jegy megnyitásával magasabb korlátot kérhet.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

