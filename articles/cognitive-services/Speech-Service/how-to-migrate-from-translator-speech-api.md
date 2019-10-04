---
title: Migrálás a Translator Speech APIról a beszédfelismerési szolgáltatásba
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan migrálhatja alkalmazásait a Translator Speech APIról a Speech Service-be.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 7b61aef13b113d9b2502c24e3001da25fa186c76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559567"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrálás a Translator Speech APIról a beszédfelismerési szolgáltatásba

Ebből a cikkből megtudhatja, hogyan telepíthet alkalmazásokat a Microsoft Translator Speech APIból a [Speech szolgáltatásba](index.yml). Ez az útmutató ismerteti a Translator Speech API és a beszédfelismerési szolgáltatás közötti különbségeket, és javaslatot tesz az alkalmazások áttelepítésére vonatkozó stratégiákra.

> [!NOTE]
> A beszédfelismerési szolgáltatás nem fogadja el a Translator Speech API előfizetési kulcsát. Létre kell hoznia egy új Speech Services-előfizetést.

## <a name="comparison-of-features"></a>Funkciók összehasonlítása

| Funkció                                           | Translator Speech API                                  | Beszédszolgáltatások | Részletek                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fordítás szöveggé                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Fordítás a beszédbe                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globális végpont                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | A Speech Services nem biztosít globális végpontot. A globális végpontok automatikusan irányítják a forgalmat a legközelebbi regionális végpontra, és csökkentik a késést az alkalmazásban.                                                    |
| Regionális végpontok                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kapcsolatok időbeli korlátja                             | 90 perc                                               | Korlátlan az SDK-val. 10 perc WebSockets-kapcsolattal.                                                                                                                                                                                                                                                                                   |
| Hitelesítési kulcs a fejlécben                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Több nyelv lett lefordítva egyetlen kérelemben | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Elérhető SDK-k                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Tekintse meg az elérhető SDK-k [Speech Services](index.yml) -dokumentációját.                                                                                                                                                    |
| WebSocket-kapcsolatok                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Nyelvek API                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | A Speech Services a [Translator API languages Reference](../translator-speech/languages-reference.md) című cikkben ismertetett nyelvek széles körét támogatja. |
| Trágár szűrő és jelölő                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM bemenetként                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Egyéb fájltípusok bemenetként                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Részleges eredmények                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Időzítési információ                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelációs azonosító                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Egyéni beszédfelismerési modellek                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | A Speech Services olyan egyéni beszédfelismerési modelleket kínál, amelyek segítségével testre szabhatja a beszédfelismerést a szervezet egyedi szókincse számára.                                                                                                                                           |
| Egyéni fordítási modellek                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | A Microsoft Text Translation API-ra való feliratkozás lehetővé teszi, hogy [Egyéni Translator](https://www.microsoft.com/translator/business/customization/) használatával saját adatait használja a pontosabb fordításhoz.                                                 |

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezete rendelkezik olyan alkalmazásokkal, amelyeket a Translator Speech API használó fejlesztési vagy éles környezetben használ, frissítse őket a beszédfelismerési szolgáltatás használatára. Tekintse meg a [beszédfelismerési szolgáltatás](index.yml) dokumentációját, amely tartalmazza az elérhető SDK-kat, a kódot és az oktatóanyagokat. A Migrálás során vegye figyelembe a következőket:

* A Speech Services nem biztosít globális végpontot. Állapítsa meg, hogy az alkalmazás hatékonyan működik-e, ha egyetlen regionális végpontot használ az összes adatforgalmához. Ha nem, a leghatékonyabb végpont meghatározásához használja a térinformatikat.

* Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem tudja használni a rendelkezésre álló SDK-kat, használhat WebSockets-kapcsolatot. A 10 perces időtúllépési korlátot a megfelelő időpontokban történő újrakapcsolattal kezelheti.

* Ha az alkalmazás a Translator Text API és a Translator Speech API használatával engedélyezi az egyéni fordítási modelleket, a beszédfelismerési szolgáltatással közvetlenül is hozzáadhat kategóriá-azonosítókat.

* A Translator Speech APItól eltérően a Speech Services több nyelven is elvégezheti a fordítást egyetlen kérelemben.

## <a name="next-steps"></a>További lépések

* [Próbálja ki ingyenesen a Speech Services szolgáltatást](get-started.md)
* [Rövid útmutató: Beszédfelismerés felismerése UWP-alkalmazásban a Speech SDK használatával](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Lásd még

* [Mi az a Speech Service](overview.md)
* [A Speech Services és a Speech SDK dokumentációja](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
