---
title: 'Rövid útmutató: a JavaScripthez készült Speech SDK (böngésző) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot a JavaScript (böngésző) használatára a Speech Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: fa8bf79c047911ca283bf60261bba0cbdd6816a7
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980145"
---
Ez az útmutató bemutatja, hogyan telepítheti a JavaScripthez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t weblapokkal való használatra.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Új webhelymappa létrehozása

Hozzon létre egy új, üres mappát. Abban az esetben, ha a mintát egy webkiszolgálón szeretné futtatni, győződjön meg arról, hogy a webkiszolgáló eléri a mappát.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>A JavaScripthez készült Speech SDK kicsomagolása a mappába

Töltse le a Speech SDK-t [.zip-csomagként](https://aka.ms/csspeech/jsbrowserpackage), és csomagolja ki az újonnan létrehozott mappába. Ez a következő négy fájl kicsomagolását eredményezi:
* `microsoft.cognitiveservices.speech.sdk.bundle.js`A Speech SDK egy olvasható verziója.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map`Az SDK-kód hibakereséséhez használt Térkép-fájl.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts`A géppel használható objektumok definíciói
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js`A Speech SDK egy minified-verziója.

## <a name="create-an-indexhtml-page"></a>Az index.html lap létrehozása

Hozzon létre egy új fájlt a mappában `index.html` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]