---
title: A kódolás gyorsaságának és egyidejűssének kezelése az Azure Media Services szolgáltatással | Microsoft dokumentumok
description: Ez a cikk rövid áttekintést nyújt arról, hogyan kezelheti a kódolási feladatok/feladatok sebességét és egyidejűségét az Azure Media Services szolgáltatással.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463752"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>A kódolás sebességének és egyidejűségének kezelése  

Ez a cikk rövid áttekintést nyújt arról, hogyan kezelheti a kódolási feladatok/feladatok sebességét és egyidejűségét.

## <a name="overview"></a>Áttekintés

A Media Services szolgáltatásban **a fenntartott egységtípus** határozza meg a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egységtípusok közül választhat: **S1,** **S2**vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. A [skálázási kódolási egységek](media-services-scale-media-processing-overview.md) témakör egy táblázatot jelenít meg, amely segít a döntésmeghozatalában a különböző kódolási sebességek közötti választáskor.

A fenntartott egység típusának megadása mellett megadhatja, hogy a fiók a Fenntartott egységek segítségével is **kiépítse a fiókot.** A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Ha például a fiók öt fenntartott egységgel rendelkezik, akkor öt médiafeladat fog egyidejűleg futni, amíg vannak feldolgozandó feladatok. A fennmaradó feladatok a várólistában várnak, és a futó feladat befejezésekor egymás után kerülnek feldolgozásra. Ha egy fióknem rendelkezik kiépített fenntartott egységekkel, akkor a feladatok at egymás után veszi késve. Ebben az esetben az egyik tevékenység befejezése és a következő indítás közötti várakozási idő a rendszerben lévő erőforrások rendelkezésre állásától függ.

A kódolási egységek méretezését bemutató részletes információkat és példákat [ebben a](media-services-scale-media-processing-overview.md) témakörben talál.

## <a name="next-step"></a>Következő lépés

[Kódolási egységek méretezése](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

