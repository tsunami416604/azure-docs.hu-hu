---
title: 'Gyors útmutató: Recognize speech a JavaScript a Cognitive Services beszédfelismerő SDK használatával'
titleSuffix: Microsoft Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést a JavaScript a Cognitive Services beszédfelismerő SDK használatával
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 9ac48bd77087a8dc050ccd1ee8fe63ec5c25a643
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018384"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>Gyors útmutató: Recognize speech a JavaScript a Cognitive Services beszédfelismerő SDK használatával

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebből a cikkből megismerheti, hogyan hozható létre webhely a beszédfelismerés lefényképezze JavaScript kötése a Cognitive Services beszédfelismerő SDK használatával fogjuk.
Az alkalmazás a Microsoft Cognitive Services beszédfelismerő SDK alapul ([1.0.0-s verziójának letöltése](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy előfizetési kulcsot. Lásd: [próbálja ki ingyenesen a speech service](get-started.md).
* PC vagy Mac működő mikrofonnal.
* Egy szövegszerkesztőben.
* Chrome-ban vagy a Microsoft Edge aktuális verzióját.
* Szükség esetén egy webkiszolgálót, amely támogatja tároló PHP-parancsfájlok.

## <a name="create-a-new-website-folder"></a>Hozzon létre egy új webhely mappát

Hozzon létre egy új, üres mappát. Abban az esetben, ha meg szeretné futtatni a mintát egy webkiszolgálón, győződjön meg arról, hogy a webkiszolgáló a mappa elérésére.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>A beszédfelismerés SDK kicsomagolása JavaScript ebbe a mappába

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Töltse le, a beszéd SDK egy [.zip-csomagjából](https://aka.ms/csspeech/jsbrowserpackage) , és csomagolja ki az újonnan létrehozott mappába. Ennek eredménye mappastruktúrájának kicsomagolása zajlik, azaz a két fájlt `microsoft.cognitiveservices.speech.sdk.bundle.js` és `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Az utóbbi fájl nem kötelező, és a használt SDK kódjába hibakeresés érdekében, ha szükséges.

## <a name="create-an-indexhtml-page"></a>Hozzon létre egy index.html lap

Hozzon létre egy új fájlt a mappába, nevű `index.html` , és nyissa meg ezt a fájlt egy szövegszerkesztőben.

1. Hozza létre a következő HTML vázat:

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

1. Adja hozzá a következő felhasználói felület kódot a fájlba, az első Megjegyzés alatt:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Adjon hozzá egy hivatkozást a Speech SDK-val

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Beállítani a gomb recognition, felismerés eredményét és az előfizetés kezelőkkel határozzák meg a felhasználói felület kód kapcsolódó mezők:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>A token forrás létrehozása (opcionális)

Abban az esetben, ha meg szeretné futtatni a weblap, egy webkiszolgálón, a bemutató alkalmazás opcionálisan megadhat egy token forrás.
Ezzel a módszerrel az előfizetési kulcs soha nem hagyja a kiszolgáló miközben lehetővé teszi a felhasználók maguk engedélyezési kód megadása nélkül speech képességeinek használata érdekében.

1. Hozzon létre egy új fájlt `token.php` néven. Ebben a példában feltételezzük, a webkiszolgáló támogatja a PHP-programozási nyelv. Adja meg a következő kódot:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Szerkessze a `index.html` fájlt, és adja hozzá a következő kódot a fájlhoz:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Hitelesítési jogkivonatok élettartama korlátozható az csak kell.
> Ebben a példában egyszerűsített nem jeleníti meg a hitelesítési tokenek automatikus frissítése. Egy felhasználó nevében is manuálisan frissítse az oldalt, vagy nyomja le az F5 billentyűt.

## <a name="build-and-run-the-sample-locally"></a>Hozhat létre, és a minta futtatása helyben

Indítsa el az alkalmazást, kattintson duplán az index.html fájlt, vagy nyissa meg a index.html a kedvenc webböngészőjét. Azt, megjelenik egy egyszerű grafikus felhasználói felület lehetővé teszi, hogy adja meg az előfizetési kulcs és [régió](regions.md) és a egy felismerés, a mikrofon használatával aktiválásához.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Hozza létre és futtassa a mintát keresztül egy webkiszolgáló

Indítsa el az alkalmazást, nyissa meg a kedvenc webböngészőjét, és mutasson a nyilvános URL-címre, amely az működtetni a mappát, adja meg a [régió](regions.md), és a egy felismerés, a mikrofon használatával aktiválásához. Ha konfigurálta, azt fogják beszerezni egy jogkivonatot a jogkivonat forrásból.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg az ehhez a mintához a `quickstart/js-browser` mappát.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A minták letöltése](speech-sdk.md#get-the-samples)
