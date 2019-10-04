---
title: 'Gyors útmutató: Beszédfelismerési, Node. js-Speech szolgáltatás felismerése'
titleSuffix: Azure Cognitive Services
description: Az útmutató segítségével a Node. js-hez készült Speech SDK használatával hozhat létre egy beszéd-szöveg típusú alkalmazást. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 1a3d1bc62e995aff43d9538f49b436a7de16f1e9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554072"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Gyors útmutató: Beszéd felismerése a Node. js-hez készült Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy Node. js-projektet az Azure-hoz készült Speech SDK JavaScript-kötésével Cognitive Services a beszéd szövegre való átírásához.
Az alkalmazás a JavaScripthez készült [SPEECH SDK](https://aka.ms/csspeech/npmpackage)-ra épül.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](get-started.md).
* A [Node. js](https://nodejs.org)aktuális verziója.

## <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon létre egy új mappát, és inicializálja a projektet:

```sh
npm init -f
```

Ez a parancs az alapértelmezett értékekkel inicializálja a **Package. JSON** fájlokat. Valószínűleg később szerkeszteni szeretné ezt a fájlt.

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Adja hozzá a Speech SDK-t a Node. js-projekthez:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Ez a parancs letölti és telepíti a Speech SDK legújabb verzióját és a szükséges előfeltételeket a **npmjs**-től. Az SDK a Project mappában `node_modules` található könyvtárba települ.

## <a name="use-the-speech-sdk"></a>A Speech SDK használata

Hozzon létre egy új fájlt a nevű `index.js`mappában, és nyissa meg a fájlt egy szövegszerkesztővel.

> [!NOTE]
> A Node. js-ben a Speech SDK nem támogatja a mikrofont vagy a **fájl** adattípust. Mindkettő csak böngészőkön támogatott. Ehelyett használja az **adatfolyam** -felületet a Speech SDK- `AudioInputStream.createPushStream()` hoz a vagy `AudioInputStream.createPullStream()`a használatával.

Ebben a példában a `PushAudioInputStream` felületet használjuk.

Adja hozzá ezt a JavaScript-kódot:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Minta futtatása

Az alkalmazás megnyitásához, az `YourSubscriptionKey`igazításhoz `YourServiceRegion`, `YourAudioFile.wav` és a konfigurációhoz. Ezután futtassa a parancsot a következő parancs meghívásával:

```sh
node index.js
```

Elindít egy felismerést a megadott fájlnév használatával. És megjeleníti a kimenetet a konzolon.

Ez a minta az előfizetési kulcs frissítése `index.js` és a fájl `whatstheweatherlike.wav`használata után futtatott kimenet.

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

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>A Speech SDK és a Visual Studio Code telepítése és használata

A mintát a Visual Studio Code-ból is futtathatja. A rövid útmutató telepítéséhez, megnyitásához és futtatásához kövesse az alábbi lépéseket:

1. Indítsa el a Visual Studio Code-ot. Válassza a **mappa megnyitása**lehetőséget. Ezután keresse meg a Gyorsindítás mappát.

   ![Mappa megnyitása](media/sdk/qs-js-node-01-open_project.png)

1. Nyisson meg egy terminált a Visual Studio Code-ban.

   ![A terminálablak](media/sdk/qs-js-node-02_open_terminal.png)

1. A `npm` függőségek telepítéséhez futtassa a parancsot.

   ![NPM telepítése](media/sdk/qs-js-node-03-npm_install.png)

1. Most már készen áll a `index.js`töréspont megnyitására és beállítására.

   ![index. js töréspontkal a 16. sorban](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. A hibakeresés elindításához válassza az F5 lehetőséget, vagy válassza a menü Hibakeresés **/Indítás hibakeresés** parancsát.

   ![A hibakeresés menü](media/sdk/qs-js-node-05-start_debugging.png)

1. A töréspontok találatakor megvizsgálhatja a hívási verem és a változók értékét.

   ![Hibakereső](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Bármely kimenet megjelenik a hibakeresési konzol ablakban.

   ![Hibakeresési konzol](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Node. js-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
