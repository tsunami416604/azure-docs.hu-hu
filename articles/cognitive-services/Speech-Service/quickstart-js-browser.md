---
title: 'Gyors útmutató: Beszédfelismerést, JavaScript (böngésző) – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést a JavaScript egy böngészőben, a beszéd SDK-val
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: a418966ce72876d58d39b939ecb7dd5a05e41b9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008986"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Gyors útmutató: A JavaScript beszédfelismerést egy böngészőben, a beszéd SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk azt mutatja be, hogyan hozhat létre egy webhelyet, amely a Cognitive Services Speech SDK JavaScript-kötésével írja át a beszédet szöveggé.
Az alkalmazás JavaScript alapul a Speech SDK ([letöltési verzióra 1.4.0-s](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Előfeltételek

* Előfizetői azonosító a Speech Service-hez. Lásd: [próbálja ki ingyenesen a beszédszolgáltatások](get-started.md).
* Egy PC vagy Mac számítógép egy működő mikrofonnal.
* Egy szövegszerkesztő.
* Chrome, a Microsoft Edge vagy a Safari aktuális verzióját.
* Egy webkiszolgáló, amely támogatja a PHP-szkriptek futtatását (nem kötelező).

## <a name="create-a-new-website-folder"></a>Új webhelymappa létrehozása

Hozzon létre egy új, üres mappát. Abban az esetben, ha a mintát egy webkiszolgálón szeretné futtatni, győződjön meg arról, hogy a webkiszolgáló eléri a mappát.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>A JavaScripthez készült Speech SDK kicsomagolása a mappába

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Töltse le a Speech SDK-t [.zip-csomagként](https://aka.ms/csspeech/jsbrowserpackage), és csomagolja ki az újonnan létrehozott mappába. Ennek hatására a mappastruktúrájának kicsomagolása zajlik, két fájlokat `microsoft.cognitiveservices.speech.sdk.bundle.js` és `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Az utóbbi fájl nem kötelező, és akkor hasznos, ha az SDK-kódjába hibakeresés.

## <a name="create-an-indexhtml-page"></a>Az index.html lap létrehozása

Hozzon létre egy új fájlt a mappában `index.html` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

1. Hozza létre a következő HTML-vázat:

   ```html
   <html>
   <head>
      <title>Speech SDK JavaScript Quickstart</title>
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

1. A felismerés gomb, felismerés eredményét és előfizetéshez kapcsolódó mezőket a felhasználói felület kód által meghatározott kezelőkkel beállítani:

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

> [!NOTE]
> Ez a módszer a Safari böngészőben nem működik.
> A Safari a minta weboldal kell üzemeltetését a webkiszolgálón; A Safari nem teszi lehetővé a mikrofon használatára egy helyi fájlból betöltött webhelyeket.

## <a name="build-and-run-the-sample-via-a-web-server"></a>A minta buildelése és futtatása webkiszolgáló használatával

Az alkalmazás elindításához nyissa meg kedvenc webböngészőjét, és mutasson a nyilvános URL-címre, amelyen a mappát tárolja, adja meg a [régiót](regions.md), majd a mikrofon használatával aktiválja a felismerést. Ha konfigurálva van, az beszerez egy jogkivonatot a jogkivonatforrásból.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [JavaScript-minták megtekintése a Githubon](https://aka.ms/csspeech/samples)
