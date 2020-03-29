---
title: Áttelepítés a Fordító beszédfelismerési API-ról a beszédfelismerési szolgáltatásra
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan telepítheti át alkalmazásait a Translator Speech API-ból a Speech service-be.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560898"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Áttelepítés a Fordító beszédfelismerési API-ról a beszédfelismerési szolgáltatásra

Ebből a cikkből áttelepítheti alkalmazásait a Microsoft Translator Speech API-ból a [Beszédfelismerés szolgáltatásba.](index.yml) Ez az útmutató ismerteti a különbségeket a Fordító beszédfelismerési API és a beszédfelismerési szolgáltatás között, és stratégiákat javasol az alkalmazások áttelepítéséhez.

> [!NOTE]
> A Translator Speech API-előfizetési kulcsot a beszédfelismerési szolgáltatás nem fogadja el. Új beszédszolgáltatás-előfizetést kell létrehoznia.

## <a name="comparison-of-features"></a>A funkciók összehasonlítása

| Szolgáltatás                                           | Translator Speech API                                  | Speech szolgáltatás | Részletek                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Szövegre fordítás                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Fordítás beszédre                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globális végpont                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | A beszédszolgáltatás nem kínál globális végpontot. A globális végpont automatikusan irányíthatja a forgalmat a legközelebbi regionális végpontra, csökkentve a késést az alkalmazásban.                                                    |
| Regionális végpontok                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Csatlakozási időkorlát                             | 90 perc                                               | Korlátlan az SDK-val. 10 perc WebSockets-kapcsolattal.                                                                                                                                                                                                                                                                                   |
| Hitelesítési kulcs a fejlécben                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Több nyelv egyetlen kérelemben lefordítva | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Rendelkezésre álló SDK-k                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Az elérhető SDK-kért tekintse meg a [beszédszolgáltatás dokumentációját.](index.yml)                                                                                                                                                    |
| WebSockets-kapcsolatok                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Nyelvek API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | A beszédfelismerési szolgáltatás támogatja a [Translator API-nyelvek referenciacikkben](../translator-speech/languages-reference.md) leírt nyelvek azonos tartományát. |
| Káromkodásszűrő és jelölő                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM bemenetként                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Egyéb fájltípusok bemenetként                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Részleges eredmények                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Időzítési információk                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelációs azonosító                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Egyéni beszédmodellek                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A beszédfelismerési szolgáltatás egyéni beszédmodelleket kínál, amelyek lehetővé teszik a beszédfelismerés testreszabását a szervezet egyedi szókincséhez.                                                                                                                                           |
| Egyéni fordítási modellek                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A Microsoft Szövegfordítás API-ra való feliratkozás lehetővé teszi, hogy az [Egyéni fordító](https://www.microsoft.com/translator/business/customization/) segítségével saját adatait használja a pontosabb fordítások hoz.                                                 |

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezet olyan alkalmazásokat fejlesztés vagy éles környezetben, amelyek a Translator Speech API-t használják, frissítenie kell őket a beszédfelismerési szolgáltatás használatához. Tekintse meg a [beszédszolgáltatás](index.yml) dokumentációját a rendelkezésre álló SDK-k, kódminták és oktatóanyagok. Áttelepítéskor vegye figyelembe az alábbiakat:

* A beszédszolgáltatás nem kínál globális végpontot. Határozza meg, hogy az alkalmazás hatékonyan működik-e, ha egyetlen regionális végpontot használ az összes forgalmához. Ha nem, használja a földrajzi helymeghatározást a leghatékonyabb végpont meghatározásához.

* Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem tudja használni a rendelkezésre álló SDK-kat, websocket-kapcsolatot használhat. A 10 perces időkorlát kezelése a megfelelő időpontokban történő újracsatlakozással.

* Ha az alkalmazás a Translator Text API-t és a Translator Speech API-t használja az egyéni fordítási modellek engedélyezéséhez, közvetlenül hozzáadhat kategóriaazonosítókat a Beszédszolgáltatás használatával.

* A Translator Speech API-val ellentétben a beszédfelismerési szolgáltatás egyetlen kérelemben több nyelvre is képes fordításokat végezni.

## <a name="next-steps"></a>További lépések

* [Próbálja ki ingyen a Beszédszolgáltatást](get-started.md)
* [Rövid útmutató: Beszédfelismerés egy UWP-alkalmazásban a beszédfelismerési SDK használatával](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Lásd még

* [Mi a beszédszolgáltatás](overview.md)
* [Beszédszolgáltatás és beszédsdka dokumentációja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
