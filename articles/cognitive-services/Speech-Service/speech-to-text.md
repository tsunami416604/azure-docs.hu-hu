---
title: Beszéd – szöveg – beszéd szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési funkció lehetővé teszi a hangadatfolyamok valós idejű átírását szöveggé. Alkalmazásait, eszközeit és eszközeit felhasználhatják, megjeleníthetik, és műveleteket végezhetnek ezen a szövegbevitelen. Ez a szolgáltatás zökkenőmentesen működik a szöveg-beszéd (Speech szintézis) és a beszédfelismerési funkciók használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: e0aea7e55381e9571b156701699f5f45315bb384
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399710"
---
# <a name="what-is-speech-to-text"></a>Mi az a diktálás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A beszédfelismerési szolgáltatásból, más néven beszédfelismerésből származó beszéd – szöveg lehetővé teszi a hangadatfolyamok valós idejű átírását szöveggé. Alkalmazásai, eszközei vagy eszközei felhasználhatják, megjeleníthetik és megtehetik ezt a szöveget a parancs bemenetként. Ezt a szolgáltatást a Microsoft a Cortana és az Office-termékekhez használt felismerési technológiával látja el. Zökkenőmentesen együttműködik a <a href="./speech-translation.md" target="_blank">fordítási <span class="docon docon-navigate-external x-hidden-focus"></span> </a> és <a href="./text-to-speech.md" target="_blank">szöveg- <span class="docon docon-navigate-external x-hidden-focus"></span> beszéd</a> szolgáltatási ajánlatokkal. Az elérhető beszéd – szöveg nyelvek teljes listáját a [támogatott nyelvek](language-support.md#speech-to-text)című részben tekintheti meg.

A beszédfelismerési szolgáltatás alapértelmezés szerint az univerzális nyelvi modellt használja. Ez a modell a Microsoft tulajdonában lévő adataival lett betanítva, és a felhőben van üzembe helyezve. Ez optimális a társalgási és a diktálási forgatókönyvek esetében. A beszédfelismerés és az átiratok egyedi környezetben való használatakor egyéni akusztikai, nyelvi és kiejtési modelleket hozhat létre és állíthat be. A Testreszabás hasznos lehet a környezeti zaj vagy az iparágra jellemző szókincs kezelése során.

> [!NOTE]
> A Bing Speech 2019. október 15-én lett leszerelve. Ha alkalmazásai, eszközei vagy termékei a Bing Speech API-kat használják, a beszédfelismerési szolgáltatásba való Migrálás megkönnyítéséhez útmutatókat hoztunk létre.
> - [Áttelepítés Bing Speechról a beszédfelismerési szolgáltatásba](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Ismerkedés a beszédfelismerési szöveggel

A Speech-Text szolgáltatás a [SPEECH SDK](speech-sdk.md)-n keresztül érhető el. Számos gyakori forgatókönyv érhető el, különböző nyelveken és platformokon:

 - [Gyors útmutató: beszéd felismerése mikrofonos bevitelsel](quickstarts/speech-to-text-from-microphone.md)
 - [Rövid útmutató: beszéd felismerése fájlból](quickstarts/speech-to-text-from-file.md)
 - [Gyors útmutató: a blob Storage-ban tárolt beszéd felismerése](quickstarts/from-blob.md)

Ha inkább a beszéd-szöveg REST-szolgáltatást szeretné használni, lásd: [REST API](rest-speech-to-text.md)-k.

## <a name="tutorials-and-sample-code"></a>Oktatóanyagok és mintakód

A beszédfelismerési szolgáltatás használatának lehetősége után próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédfelismerési szándékokat a Speech SDK és a LUIS használatával.

- [Oktatóanyag: beszédfelismerési szándékok felismerése a Speech SDK-val és a LUIS-mel a C használatával #](how-to-recognize-intents-from-speech-csharp.md)

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata.

- [Beszéd – szöveg minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Testreszabás

A standard Speech Service-modellen kívül egyéni modelleket is létrehozhat. A testreszabással leküzdheti a beszédfelismerési akadályokat, például a beszéd stílusát, a szókincset és a háttérzajt, lásd: [Custom Speech](how-to-custom-speech.md). A testreszabási lehetőségek nyelv/területi beállítás szerint változnak, a támogatás ellenőrzéséhez lásd a [támogatott nyelveket](supported-languages.md) .

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>További lépések

- [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
- [A Speech SDK beszerzése](speech-sdk.md)
