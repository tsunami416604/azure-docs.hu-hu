---
title: 'Gyors útmutató: A beszédfelismerést a JavaScript a Speech Service SDK-t használó Node.js-ben'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan beszédfelismerést a JavaScript a Speech Service SDK-t használó Node.js-ben
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 6dc482f8629bf8ca75af5f1643999ae896fa2435
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609414"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Gyors útmutató: A beszédfelismerést a JavaScript a Speech Service SDK-t használó Node.js-ben

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben megismerheti, hogyan hozhat létre egy Node.js-projektet a Cognitive Services beszédfelismerő SDK JavaScript kötése történő Diktálás lefényképezze lesz.
Az alkalmazás a Microsoft alapján [Cognitive Services beszédfelismerő SDK](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Előfeltételek

* Előfizetői azonosító a Speech Service-hez. Lásd [A Speech Service ingyenes kipróbálása](get-started.md) című cikket.
* Aktuális verziója [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Hozzon létre egy új projekt mappát

Hozzon létre egy új, üres mappát, és a egy új JavaScript és a Node.js-projektet, inicializálja.

```sh
npm init -f
```

Ez a package.json fájlok, az alapértelmezett értékeket fogja init. Érdemes ezt a fájlt később szerkesztheti.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>A beszédfelismerés SDK telepítése JavaScript ebbe a mappába

Adja hozzá a Speech SDK-n keresztül `npm install microsoft-cognitiveservices-speech-sdk` , a Node.js-projektet.

Ez letölti és npmjs telepítse a legújabb verziót a Speech SDK és a szükséges előfeltételeket. Az SDK lesznek telepítve a `node_modules` könyvtárban, a projektmappa fájllistájának belül.

## <a name="using-the-speech-sdk"></a>A beszédfelismerés SDK használatával

Hozzon létre egy új fájlt a mappában `index.js` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

> [!NOTE]
> Vegye figyelembe, hogy a node.js-ben a Speech SDK nem támogatja a mikrofon vagy a fájl adattípus. Mindkét csak a böngészők támogatottak. Ehelyett használja a Stream felületen, a beszéd SDK-val, keresztül `AudioInputStream.createPushStream()` vagy `AudioInputStream.createPullStream()`.

Ebben a példában használjuk a `PushAudioInputStream` felületet.

Adja hozzá a következő JavaScript-kódot:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>A minta futtatása a parancssorból

Indítsa el az alkalmazást, hogy alkalmazkodjon `YourSubscriptionKey`, `YourServiceRegion`, és `YourAudioFile.wav` a konfigurációhoz. Ezután hajthat végre, a következő parancs meghívásával:

```sh
node index.js
```

Ez aktiválja a felismerés, a megadott fájlnév használata, és megtalálható a kimenetet a konzolon.

Itt látható egy mintakimenet futtatásának `index.js` után az előfizetés-kulcsának frissítése, valamint a fájl használatával `whatstheweatherlike.wav`.

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="running-the-sample-from-visual-studio-code"></a>A minta futtatása a Visual Studio Code-ból

Futtathatja a mintát a Visual Studio Code-ból is. Kövesse az alábbi lépéseket kell telepíteni, nyissa meg, és hajtsa végre a rövid útmutató:

1. Indítsa el a Visual Studio Code-ot, és kattintson a "Mappa megnyitása", majd keresse meg a rövid útmutató mappát

   ![Képernyőkép a mappa megnyitása](media/sdk/qs-js-node-01-open_project.png)

1. Nyisson meg egy terminált a Visual Studio Code-ban

   ![A terminálablakban képernyőképe](media/sdk/qs-js-node-02_open_terminal.png)

1. Futtassa az npm segítségével való függőségek telepítése

   ![Az npm install képernyőképe](media/sdk/qs-js-node-03-npm_install.png)

1. Most már készen áll a nyissa meg a `index.js`és állítson be egy töréspontot

   ![Egy sor 16 töréspontot az index.js képernyőképe](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Hibakeresés elindításához nyomja le az F5 billentyűt vagy válassza ki a hibakeresési/Start-hibakeresés a menüből

   ![A Hibakeresés menü képernyőképe](media/sdk/qs-js-node-05-start_debugging.png)

1. Ha elérte a töréspontot, vizsgálhatja meg a hívási verem és a változók

   ![Képernyőkép a hibakereső](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. A hibakeresési konzolt ablakban megjelenő kimenetet

   ![Képernyőkép a hibakeresési konzolt](media/sdk/qs-js-node-07-debug_output.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
A jelen útmutatóban használt mintát a `quickstart/js-node` mappában találja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták letöltése](speech-sdk.md#get-the-samples)
