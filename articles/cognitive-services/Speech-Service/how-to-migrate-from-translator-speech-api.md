---
title: A beszédfelismerési szolgáltatás át a Translator Speech API
titleSuffix: Azure Cognitive Services
description: Ez a témakör segítségével az alkalmazások át a Translator Speech API a beszédfelismerési szolgáltatás.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: 55303b38a5fe8890cddc2336b4bc47a23fc03745
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239945"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>A beszédfelismerési szolgáltatás át a Translator Speech API

Ez a cikk segítségével az alkalmazások át a Microsoft Translator Speech API a [beszédszolgáltatás](index.yml). Ez az útmutató a Translator Speech API és a Speech Service közötti különbségeket ismerteti, és az alkalmazások áttelepítéséhez stratégiák javasol.

> [!NOTE]
> A Translator Speech API-előfizetési kulcs nem fogadja el a Speech Service. Beszédszolgáltatás új előfizetés indítása kell.

## <a name="comparison-of-features"></a>Szolgáltatások összehasonlítása

| Szolgáltatás                                           | Translator Speech API                                  | Beszédszolgáltatás | Részletek                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Szöveg fordítása                               | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| A beszédfelismerés fordítása                             | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globális végpontja                                   | : heavy_check_mark:                                              | : heavy_minus_sign:                 | A beszédfelismerési szolgáltatás jelenleg nem kínál egy globális végpontja. Egy globális végpontja automatikusan irányíthatók a forgalmat a legközelebbi regionális végpontra, az alkalmazás a késés csökkentése.                                                    |
| Regionális végpontok                                | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kapcsolati időkorlát                             | 90 perc                                               | Korlátlan számú az SDK-val. Websocket-kapcsolattal 10 perc                                                                                                                                                                                                                                                                                   |
| Hitelesítési kulcs a fejléc                                | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Egyetlen kérelem több nyelv lefordítva | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK-k                                    | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Tekintse meg a [Speech Service dokumentációjában](index.yml) számára elérhető SDK-kkal.                                                                                                                                                    |
| Websocket-kapcsolatok                             | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Nyelvek API                                     | : heavy_check_mark:                                              | : heavy_minus_sign:                 | A Speech Service támogatja a nyelvek ismertetett ugyanezt a porttartományt a [Translator API-nyelvek referencia](../translator-speech/languages-reference.md) cikk. |
| Profanitásszűrőjének és jelölő                       | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM bemenetként                                 | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Más fájltípusok bemenetként                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Részleges eredményeket                                   | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Időzítési adatai                                       | : heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelációs azonosító                                    | : heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Egyéni beszédfelismerési modelleket                              | : heavy_minus_sign:                                              | : heavy_check_mark:                 | A beszédfelismerési szolgáltatás egyéni beszédmodellekké, amelyek szabhatja testre a szervezet egyedi szókincsből eredőket beszédfelismerés kínál.                                                                                                                                           |
| Egyéni fordítási modellek                         | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Előfizetés a Microsoft fordítási API lehetővé teszi, hogy [egyéni a fordítót](https://www.microsoft.com/translator/business/customization/) (jelenleg előzetes verzió) használata a saját adatok pontosabb fordítások.                                                 |

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezet rendelkezik a Translator Speech API-t használó alkalmazások fejlesztési vagy éles környezetben, frissítenie kell azokat a beszédfelismerési szolgáltatás használatára. Tekintse meg a [beszédszolgáltatás](index.yml) dokumentáció elérhető SDK-k, Kódminták és oktatóanyagok. Az alábbiakban néhány dolgot figyelembe kell venni áttelepítése:

* A beszédfelismerési szolgáltatás jelenleg nem kínál egy globális végpontja. Határozza meg, ha az alkalmazás működik hatékonyan navigálhat az összes hozzá tartozó forgalmat egy regionális végpontot kell. Nem fog, ha szüksége lesz a leghatékonyabb végpont meghatározásához használja a földrajzi hely meghatározása.

* Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem használja az elérhető SDK-k, egyetlen websocket kapcsolaton keresztül, és kezelheti a 10 perces időtúllépési korlát újracsatlakozás a megfelelő időben.

* Ha az alkalmazás a Translator Text API és a Translator Speech API engedélyezése egyéni fordítási modellek, lesz közvetlenül a Speech szolgáltatással "Kategória" azonosítók adhat hozzá.

* A beszédfelismerési szolgáltatás egyetlen kérelem, ellentétben a Translator Speech API több nyelven fordítást hajthatja végre.

## <a name="next-steps"></a>További lépések

* [Speech Service ingyenes kipróbálása](get-started.md)
* [Gyors útmutató: Recognize speech, a beszéd SDK használatával egy UWP-alkalmazás](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Lásd még

* [Mi az a Speech service](overview.md)
* [Beszédszolgáltatás és az SDK-dokumentáció](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
