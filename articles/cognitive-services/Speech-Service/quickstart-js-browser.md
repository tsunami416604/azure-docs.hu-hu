---
title: 'Gyors útmutató: Recognize speech, Javascript (böngésző) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan végezhet beszédfelismerést a JavaScripttel böngészőben a Speech Service SDK használatával
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: f8b6f4d39ce4dc65d691ec6a0e3d0734abb19734
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087915"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-service-sdk"></a>Rövid útmutató: Beszédfelismerés a JavaScripttel böngészőben a Speech Service SDK használatával

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk azt mutatja be, hogyan hozhat létre egy webhelyet, amely a Cognitive Services Speech SDK JavaScript-kötésével írja át a beszédet szöveggé.
Az alkalmazás a Microsoft Cognitive Services Speech SDK-n alapul ([Az 1.1.0-s verzió letöltése](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Előfeltételek

* Előfizetői azonosító a Speech Service-hez. Lásd a [Speech Service ingyenes kipróbálását](get-started.md) ismertető részt.
* Egy PC vagy Mac számítógép egy működő mikrofonnal.
* Egy szövegszerkesztő.
* A Chrome vagy a Microsoft Edge egy aktuális verziója.
* Egy webkiszolgáló, amely támogatja a PHP-szkriptek futtatását (nem kötelező).

## <a name="create-a-new-website-folder"></a>Új webhelymappa létrehozása

Hozzon létre egy új, üres mappát. Abban az esetben, ha a mintát egy webkiszolgálón szeretné futtatni, győződjön meg arról, hogy a webkiszolgáló eléri a mappát.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>A JavaScripthez készült Speech SDK kicsomagolása a mappába

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Töltse le a Speech SDK-t [.zip-csomagként](https://aka.ms/csspeech/jsbrowserpackage), és csomagolja ki az újonnan létrehozott mappába. Ekkor a rendszernek elvileg két fájlt kell kicsomagolnia, a `microsoft.cognitiveservices.speech.sdk.bundle.js` és a `microsoft.cognitiveservices.speech.sdk.bundle.js.map` fájlt.
Az utóbbi fájl használata nem kötelező; az SDK kódjában való hibakeresés segítésére szolgál szükség esetén.

## <a name="create-an-indexhtml-page"></a>Az index.html lap létrehozása

Hozzon létre egy új fájlt a mappában `index.html` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

1. Hozza létre a következő HTML-vázat:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Szúrja be a következő felhasználói felületi kódot a fájlba, az első megjegyzés alá:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. A Speech SDK hivatkozásának hozzáadása

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Kösse be a felismerési gomb, a felismerési eredmény és az előfizetéshez kapcsolódó mezők a felhasználói felületi kódban definiált kezelőit:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>A jogkivonatforrás létrehozása (nem kötelező)

Ha egy webkiszolgálón szeretné futtatni a weblapot, opcionálisan megadhat egy jogkivonatforrást a bemutató alkalmazáshoz.
Így az előfizetői azonosítója soha nem fog kikerülni a kiszolgálóról, közben viszont a felhasználóknak úgy teheti lehetővé a beszédfelismerési képességek használatát, hogy ők maguk semmilyen engedélyezési kódot nem adnak meg.

1. Hozzon létre egy új fájlt `token.php` néven. Ebben a példában feltételezzük, hogy a webkiszolgáló támogatja a PHP parancsnyelv használatát. Írja be a következő kódot:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Szerkessze az `index.html` fájlt, és szúrja be a következő kódot a fájlba:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Az engedélyezési jogkivonatok csupán korlátozott élettartammal rendelkeznek.
> Ez az egyszerűsített példa nem mutatja be, hogyan lehet frissíteni az engedélyezési jogkivonatokat. Felhasználóként manuálisan betöltheti az oldalt újból, vagy megnyomhatja az F5 billentyűt a frissítéshez.

## <a name="build-and-run-the-sample-locally"></a>A minta helyi buildelése és futtatása

Az alkalmazás elindításához kattintson duplán az index.html fájlra, vagy nyissa meg az index.html fájlt kedvenc webböngészőjében. Egy egyszerű grafikus felhasználói felület fog megjelenni, amelyen megadhatja az előfizetői azonosítót és a [régiót](regions.md), majd a mikrofon használatával aktiválhatja a felismerést.

## <a name="build-and-run-the-sample-via-a-web-server"></a>A minta buildelése és futtatása webkiszolgáló használatával

Az alkalmazás elindításához nyissa meg kedvenc webböngészőjét, és mutasson a nyilvános URL-címre, amelyen a mappát tárolja, adja meg a [régiót](regions.md), majd a mikrofon használatával aktiválja a felismerést. Ha konfigurálva van, az beszerez egy jogkivonatot a jogkivonatforrásból.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/js-browser` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták letöltése](speech-sdk.md#get-the-samples)
