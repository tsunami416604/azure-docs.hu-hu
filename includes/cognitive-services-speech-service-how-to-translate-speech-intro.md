---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 4b823a8dc15d33e0537fae348b34c3a3fee84558
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330849"
---
<!-- N.B. no header, language-agnostic -->

A [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) biztosítja a legegyszerűbb módja a beszédalapú fordítási használhatja az alkalmazásban.
Az SDK szolgáltatás összes funkcióját biztosítja.
Az alapvető folyamat beszédalapú fordítási végrehajtásához a következő lépésekből áll:

1. Biztosít egy beszéd service előfizetési kulcsot speech-előállító létrehozásához és a egy [régió](~/articles/cognitive-services/speech-service/regions.md).
   Emellett a forrás és cél szövegfordítási nyelv, valamint adja meg, hogy kívánja-e a szöveg és beszéd kimeneti konfigurálása.

1. A beszédfelismerés gyári egy fordítási felismerő le.
   Nincsenek fordítási felismerő használ hangforrásról alapján különböző változataira jellemző.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges.
   A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha.
   Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

1. Indítsa el a felismerés és a fordítás.

Az alábbiakban bemutatjuk, beszéd fordítási forgatókönyvet, a beszéd SDK-val számos kódrészleteket.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
