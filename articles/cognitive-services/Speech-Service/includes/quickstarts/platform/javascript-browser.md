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
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097048"
---
Ez az útmutató bemutatja, hogyan telepítheti a JavaScripthez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t weblapokkal való használatra.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Új webhelymappa létrehozása

Hozzon létre egy új, üres mappát. Abban az esetben, ha a mintát egy webkiszolgálón szeretné futtatni, győződjön meg arról, hogy a webkiszolgáló eléri a mappát.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>A JavaScripthez készült Speech SDK kicsomagolása a mappába

Töltse le a Speech SDK-t [.zip-csomagként](https://aka.ms/csspeech/jsbrowserpackage), és csomagolja ki az újonnan létrehozott mappába. Ennek eredményeképpen öt fájlt csomagol ki a rendszer:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` A Speech SDK egy olvasható verziója.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` Az SDK-kód hibakereséséhez használt Térkép-fájl.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` A géppel használható objektumok definíciói
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` A Speech SDK egy minified-verziója.
* `speech-processor.js` Bizonyos böngészők teljesítményének javításához szükséges kód.

## <a name="create-an-indexhtml-page"></a>Az index.html lap létrehozása

Hozzon létre egy új fájlt a mappában `index.html` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]