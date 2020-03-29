---
title: A médiafeldolgozás méretezése kódolási egységek hozzáadásával - Azure |  Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan adhat hozzá kódolási egységeket az Azure Media Services .NET szolgáltatással.
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
ms.openlocfilehash: 86fd923c121b9d46109529f75bc3d0d040f1a7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887288"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>A kódolás méretezése a .NET SDK használatával
> [!div class="op_single_selector"]
> * [Portál](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Többi](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Győződjön meg arról, hogy tekintse át az [áttekintést,](media-services-scale-media-processing-overview.md) hogy további információt kapjon a médiafeldolgozás méretezéséről.
> 
> 

A fenntartott egységtípus és a .NET SDK használatával fenntartott kódolási egységek számának módosításához tegye a következőket:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Támogatási jegy megnyitása

Alapértelmezés szerint minden Media Services-fiók legfeljebb 10 S2 vagy S3 media reserved units (MRUs) vagy 25 S1 MRUs és 5 igény szerinti streamelési fenntartott egységre skálázható. Magasabb korlátot kérhet egy támogatási jegy megnyitásával.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

