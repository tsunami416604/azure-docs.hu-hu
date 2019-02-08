---
title: A beszédfelismerési szolgáltatás át a Translator Speech API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan az alkalmazások áttelepíthetők a Translator Speech API a Speech Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: de9727df9255fb880403e0409055b73db240e882
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868113"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>A beszédfelismerési szolgáltatás át a Translator Speech API

Ez a cikk segítségével az alkalmazások át a Microsoft Translator Speech API a [beszédszolgáltatás](index.yml). Ez az útmutató a Translator Speech API és a Speech Service közötti különbségeket ismerteti, és az alkalmazások áttelepítéséhez stratégiák javasol.

> [!NOTE]
> A Translator Speech API-előfizetési kulcs nem fogadja el a Speech Service. Beszédszolgáltatás új előfizetés indítása kell.

## <a name="comparison-of-features"></a>Szolgáltatások összehasonlítása

| Funkció                                           | Translator Speech API                                  | Beszédszolgáltatás | Részletek                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Szöveg fordítása                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| A beszédfelismerés fordítása                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globális végpontja                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | A beszédfelismerési szolgáltatás jelenleg nem kínál egy globális végpontja. Egy globális végpontja automatikusan irányíthatók a forgalmat a legközelebbi regionális végpontra, az alkalmazás a késés csökkentése.                                                    |
| Regionális végpontok                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kapcsolati időkorlát                             | 90 perc                                               | Korlátlan számú az SDK-val. a websockets protokoll kapcsolattal 10 perc.                                                                                                                                                                                                                                                                                   |
| Hitelesítési kulcs a fejléc                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Egyetlen kérelem több nyelv lefordítva | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK-k                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Tekintse meg a [Speech Service dokumentációjában](index.yml) számára elérhető SDK-kkal.                                                                                                                                                    |
| A websockets protokoll kapcsolatok                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Nyelvek API                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | A Speech Service támogatja a nyelvek ismertetett ugyanezt a porttartományt a [Translator API-nyelvek referencia](../translator-speech/languages-reference.md) cikk. |
| Profanitásszűrőjének és jelölő                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM bemenetként                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Más fájltípusok bemenetként                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Részleges eredményeket                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Időzítési adatai                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelációs azonosító                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Egyéni beszédfelismerési modelleket                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | A beszédfelismerési szolgáltatás egyéni beszédmodellekké, amelyek szabhatja testre a szervezet egyedi szókincsből eredőket beszédfelismerés kínál.                                                                                                                                           |
| Egyéni fordítási modellek                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Előfizetés a Microsoft fordítási API lehetővé teszi, hogy [egyéni a fordítót](https://www.microsoft.com/translator/business/customization/) pontosabb fordítások használandó a saját adatait.                                                 |

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezet rendelkezik a Translator Speech API-t használó alkalmazások fejlesztési vagy éles környezetben, frissítenie kell azokat a beszédfelismerési szolgáltatás használatára. Tekintse meg a [beszédszolgáltatás](index.yml) dokumentáció elérhető SDK-k, Kódminták és oktatóanyagok. Áttelepítésekor vegye figyelembe a következőket:

* A beszédfelismerési szolgáltatás jelenleg nem kínál egy globális végpontja. Határozza meg, ha ha használ egy regionális végpontot az összes, a forgalom hatékony működik-e az alkalmazás. Ha nem, földrajzi hely meghatározásának segítségével határozhatja meg, a leghatékonyabb végpontot.

* Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem használja az elérhető SDK-k, használhatja a websockets protokoll kapcsolatot. Újracsatlakozás a megfelelő időben kezelheti a 10 perces időtúllépési korlát.

* Ha az alkalmazás egyéni fordítási modellek engedélyezése a Translator Text API és a Translator Speech API-t használja, a kategória-azonosítók a Speech Service segítségével közvetlenül is hozzáadhat.

* A Translator Speech API eltérően a beszédfelismerési szolgáltatás egyetlen kérelem több nyelven fordítást hajthatja végre.

## <a name="next-steps"></a>További lépések

* [Speech Service ingyenes kipróbálása](get-started.md)
* [Rövid útmutató: A beszédfelismerés SDK használatával egy UWP-alkalmazás a beszédfelismerést](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Lásd még

* [Mi az a Speech Service](overview.md)
* [Beszédszolgáltatás és az SDK-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
