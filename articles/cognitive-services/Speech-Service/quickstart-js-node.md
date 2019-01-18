---
title: 'Gyors útmutató: Beszédfelismerést, node.js-szel – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató segítségével hozzon létre egy hang-szöveg transzformációs konzolalkalmazást az a Speech SDK for node.js használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: e0ae916687ca32835dd8daf6e5059b8f6eea0ff6
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382169"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Gyors útmutató: NODE.js-hez készült beszédfelismerést a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ebben a cikkben megismerheti, hogyan hozhat létre egy Node.js-projektet a Cognitive Services beszédfelismerő SDK JavaScript kötése történő Diktálás lefényképezze lesz.
Az alkalmazás a Microsoft alapján [Cognitive Services beszédfelismerő SDK](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).
* Aktuális verziója [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon létre egy új mappát, és a projekt inicializálása.

```sh
npm init -f
```

Ez a package.json fájlok, az alapértelmezett értékeket fogja init. Érdemes ezt a fájlt később szerkesztheti.

## <a name="install-the-speech-sdk"></a>A beszédfelismerés SDK telepítése

A beszédfelismerés SDK-t adhat a Node.js-projektet.

```
npm install microsoft-cognitiveservices-speech-sdk
```

Ez letölti és telepíti a legújabb verziót, a beszéd SDK és a szükséges előfeltételeket a npmjs. Az SDK lesznek telepítve a `node_modules` könyvtárban, a projektmappa fájllistájának belül.

## <a name="use-the-speech-sdk"></a>A beszéd SDK használata

Hozzon létre egy új fájlt a mappában `index.js` néven, és nyissa meg a fájlt egy szövegszerkesztőben.

> [!NOTE]
> Vegye figyelembe, hogy a node.js-ben a Speech SDK nem támogatja a mikrofon vagy a fájl adattípus. Mindkét csak a böngészők támogatottak. Ehelyett használja a Stream felületen, a beszéd SDK-val, keresztül `AudioInputStream.createPushStream()` vagy `AudioInputStream.createPullStream()`.

Ebben a példában használjuk a `PushAudioInputStream` felületet.

Adja hozzá a következő JavaScript-kódot:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Minta futtatása

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

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Telepítse, és a Speech SDK használata a Visual Studio Code használatával

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Node.js-minták a Githubon](https://aka.ms/csspeech/samples)
