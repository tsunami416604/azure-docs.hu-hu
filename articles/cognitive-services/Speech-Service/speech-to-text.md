---
title: Beszéd-szöveg - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszéd-szöveg funkció lehetővé teszi a hangadatfolyamok valós idejű átírását a szövegbe. Az alkalmazások, eszközök vagy eszközök felemészthetik, megjeleníthetik és végrehajthatják a szövegbevitelt. Ez a szolgáltatás zökkenőmentesen működik a szövegfelolvasás (beszédszintézis) és a beszédfordítási funkciókkal.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052628"
---
# <a name="what-is-speech-to-text"></a>Mi az a diktálás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A beszédfelismerési szolgáltatásból származó beszéd-szöveg üzenet lehetővé teszi a hangadatfolyamok valós idejű átírását a szövegbe. Az alkalmazások, eszközök vagy eszközök parancsbemenetként felhasználhatók, megjeleníthetnek és végrehajthatnak műveleteket ezen a szövegen. Ezt a szolgáltatást ugyanaz a felismerési technológia működteti, amelyet a Microsoft a Cortanához és az Office-termékekhez használ. Ez zökkenőmentesen működik a <a href="./speech-translation.md" target="_blank">fordítás <span class="docon docon-navigate-external x-hidden-focus"></span> </a> és <a href="./text-to-speech.md" target="_blank">a text-to-speech <span class="docon docon-navigate-external x-hidden-focus"></span> </a> szolgáltatás kínálatát. Az elérhető beszéd-szöveg nyelvek teljes listáját a támogatott nyelvek című [témakörben](language-support.md#speech-to-text)található.

A beszéd-szöveg szolgáltatás alapértelmezés szerint az univerzális nyelvi modellt használja. Ez a modell a Microsoft tulajdonában lévő adatok használatával lett betanítva, és a felhőben van telepítve. Ez optimális társalgási és diktálási forgatókönyvek. Ha a beszéd-szöveg felismerés és átírás egy egyedi környezetben, hozhat létre, és a betanításegyéni akusztikai, nyelvi és kiejtési modellek. A testreszabás hasznos a környezeti zajok vagy az iparág-specifikus szókincs kezeléséhez.

> [!NOTE]
> A Bing Speech-t 2019. október 15-én leszerelték. Ha alkalmazásai, eszközei vagy termékei a Bing beszédfelismerési API-kat használják, útmutatókat hoztunk létre a beszédfelismerési szolgáltatásba való áttelepítéshez.
> - [Áttelepítés a Bing beszédfelismerésről a beszédfelismerési szolgáltatásra](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Ismerkedés a szöveggé bírja - beszédfelismerés

A beszéd-szöveg szolgáltatás a [Speech SDK-n](speech-sdk.md)keresztül érhető el. Számos gyakori forgatókönyv érhető el rövid útmutatóként, különböző nyelveken és platformokon:

 - [Rövid útmutató: Beszéd felismerése mikrofonbemenettel](quickstarts/speech-to-text-from-microphone.md)
 - [Rövid útmutató: Beszéd felismerése fájlból](quickstarts/speech-to-text-from-file.md)
 - [Rövid útmutató: A blobstorageban tárolt beszédfelismerés](quickstarts/from-blob.md)

Ha a beszédfelismerési REST-szolgáltatást szeretné használni, olvassa el a [REST API-k című témakört.](rest-speech-to-text.md)

## <a name="tutorials-and-sample-code"></a>Oktatóanyagok és mintakód

Miután lehetősége volt használni a beszédfelismerési szolgáltatást, próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédből származó szándékokat a beszédbeszédskés a LUIS használatával.

- [Oktatóanyag: A beszédbeszédSDK-vel és a LUIS-szal való leképezések felismerése c használatával #](how-to-recognize-intents-from-speech-csharp.md)

A beszédbeszéd SDK-hoz tartozó mintakód elérhető a GitHubon. Ezek a minták olyan gyakori forgatókönyveket fednek le, mint a hang fájlból vagy adatfolyamból történő olvasása, a folyamatos és az egyesélyes felismerés, valamint az egyéni modellek kezelése.

- [Beszéd-szöveg minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Tételtranszkripciós minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Testreszabás

A szabványos beszédszolgáltatás-modell mellett egyéni modelleket is létrehozhat. A testreszabás segít leküzdeni a beszédfelismerési akadályokat, például a beszédstílust, a szókincset és a háttérzajt, lásd: [Egyéni beszéd.](how-to-custom-speech.md) A testreszabási lehetőségek nyelvtől/területi beállítástól függően változnak, lásd a [támogatott nyelvek](supported-languages.md) ellenőrzését.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>További lépések

- [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
- [A beszédfelismerési SDK beolvasása](speech-sdk.md)
