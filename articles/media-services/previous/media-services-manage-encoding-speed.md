---
title: Sebesség és az Azure Media Services kódolási párhuzamossági kezelése |} Microsoft Docs
description: Ez a cikk hogyan kezelheti a sebesség és a kódolási feladatok/feladatokat az Azure Media Services párhuzamossági rövid áttekintést nyújt.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: d7e3d6d0c176d0a903c3027ab4feddb332557566
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788276"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Sebesség és a kódolási párhuzamossági kezelése

Ez a cikk hogyan kezelheti a sebesség és a egyidejűségi beállítása pedig a kódolási feladatok feladatok rövid áttekintést nyújt.

## <a name="overview"></a>Áttekintés

A Media Services szolgáltatásban a **fenntartott egységtípus** határozza meg a sebesség, amellyel a feladatok feldolgozása media feldolgozása. A következő Fenntartott egység típusok közül választhat: **S1**, **S2** vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. A [kódolási egységek méretezése](media-services-scale-media-processing-overview.md) témakör, amely segít a döntést között különböző kódolási sebességű kiválasztásakor táblázatát jeleníti meg.

Kívül fenntartott egységnek típusának megadásával, a fiók kiépítése megadhat **fenntartott egységek**. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Például ha a fiók nem rendelkezik öt fenntartott egységek, majd öt media feladatokat futtatni egyidejűleg hosszú, amennyi a feldolgozandó feladatok. A hátralévő műveletekkel a várólistában, és egymás után feldolgozásához, amikor egy futó feladat befejezése után fog beolvasása felvételre. Ha a fiók nem rendelkezik bármely fenntartott egységek kiosztása, majd feladatok fog felvételre egymás után. Ebben az esetben egy feladat befejeződik, és ezután egy kezdő között a várakozási idő függ a rendelkezésre álló erőforrások a rendszerben.

Részletes információkat és kódolási egységek méretezése példák: [ez](media-services-scale-media-processing-overview.md) témakör.

## <a name="next-step"></a>Következő lépés

[Kódolási méretezési egységek](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

