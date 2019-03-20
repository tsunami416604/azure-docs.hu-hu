---
title: Sebességének és egyidejűségének az Azure Media Services encoding kezelése |} A Microsoft Docs
description: Ez a cikk röviden bemutatja, hogyan kezelheti sebességének és egyidejűségének a kódolási feladatok és tevékenységek az Azure Media Services biztosítja.
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
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188628"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Sebességének és egyidejűségének a kódolás kezelése  

Ez a cikk röviden bemutatja, hogyan kezelheti sebességének és egyidejűségének a kódolási feladatok feladatokat biztosít.

## <a name="overview"></a>Áttekintés

A Media Services szolgáltatásban egy **fenntartott egység típussal** meghatározza a médiafeldolgozási feladatok feldolgozásának sebességét. A következő fenntartott egység típusok közül választhat: **S1**, **S2**, vagy **S3**. Ugyanaz a kódolási feladat például gyorsabban fut, amikor az **S2** Fenntartott egység típust használja az **S1** típus helyett. A [kódolási egységek méretezése](media-services-scale-media-processing-overview.md) témakör, amely segítséget nyújt a különböző kódolási sebességű közötti kiválasztásakor döntéseket táblázatát jeleníti meg.

A szolgáltatás számára fenntartott egység típusának meghatározása, mellett is megadhat a fiók kiépítése **szolgáltatás számára fenntartott egységek**. A megadott Fenntartott egységek száma határozza meg az egy adott fiókon egy időben feldolgozható médiafeladatok számát. Például ha a fiókja rendelkezik öt fenntartott egységet, majd öt feldolgozható médiafeladatok fog futni, amíg egy időben, amennyi a feldolgozandó feladatok. A hátralévő feladatok a várólistában várakozik, és feldolgozására egymás után futtatott feladat végeztével lesznek első mértékének. Ha a fiók nem rendelkezik minden üzembe helyezett szolgáltatás számára fenntartott egységeket, majd feladatok fog felvenni, egymás után. Ebben az esetben egy feladat befejeződik, és a következő egy kezdő között a várakozási idő függ az erőforrások rendelkezésre állása a rendszerben.

Részletes információk és példák azt mutatják be, hogyan skálázható kódolási egységek: [ez](media-services-scale-media-processing-overview.md) témakör.

## <a name="next-step"></a>Következő lépés

[Kódolási egységek méretezése](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

