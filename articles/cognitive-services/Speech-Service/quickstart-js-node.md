---
title: 'Gyors útmutató: Beszédfelismerést, node.js-szel – beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató segítségével egy hang-szöveg transzformációs Konzolalkalmazás létrehozása a node.js-ben a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: 99938f8b64339556c0ee4b9caa9124c609159af6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747272"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Gyors útmutató: NODE.js-hez készült beszédfelismerést a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy Node.js-projektet a beszédfelismerés lefényképezze a JavaScript-kötés az Azure Cognitive Services beszédfelismerő SDK használatával.
Az alkalmazás alapján a [Speech SDK JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).
* Aktuális verziója [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Új projekt létrehozása

Hozzon létre egy új mappát, és a projekt inicializálása:

```sh
npm init -f
```

Ez a parancs inicializálja a **package.json** fájlok alapértelmezett értékekkel. Érdemes ezt a fájlt később szerkesztheti.

## <a name="install-the-speech-sdk"></a>A beszédfelismerés SDK telepítése

A beszédfelismerés SDK-t adhat a Node.js-projektet:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Ez a parancs letölti és telepíti a legújabb verziót a Speech SDK és a szükséges előfeltételeket **npmjs**. Az SDK-t telepíti a `node_modules` könyvtárban, a projektmappa fájllistájának belül.

## <a name="use-the-speech-sdk"></a>A beszéd SDK használata

Hozzon létre egy új fájlt a mappába, nevű `index.js`, és nyissa meg ezt a fájlt egy szövegszerkesztőben.

> [!NOTE]
> A node.js-ben, a beszéd SDK nem támogatja a mikrofon vagy a **fájl** adattípus. Mindkét csak a böngészők támogatottak. Ehelyett használja a **Stream** felület, a beszéd SDK-val, keresztül `AudioInputStream.createPushStream()` vagy `AudioInputStream.createPullStream()`.

Ebben a példában használjuk a `PushAudioInputStream` felületet.

Adja hozzá a JavaScript-kódot:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Minta futtatása

Nyissa meg az alkalmazást, hogy alkalmazkodjon `YourSubscriptionKey`, `YourServiceRegion`, és `YourAudioFile.wav` a konfigurációhoz. Ez a parancs meghívásával ezután futtassa azt:

```sh
node index.js
```

Elismerés elindítja a megadott fájlnév használatával. És a kimeneti azt jeleníti meg a konzolon.

Ez a minta képezi a kimeneti futtatásakor `index.js` után frissítse az előfizetési kulcsot, és használhatják a fájlt `whatstheweatherlike.wav`:

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

A mintát a Visual Studio Code-ból is futtathatja. Kövesse az alábbi lépéseket a telepítés, nyissa meg és futtassa a rövid útmutató:

1. Indítsa el a Visual Studio Code-ot. Válassza ki **nyissa meg a mappa**. Majd keresse meg a rövid útmutató mappát.

   ![Mappa megnyitása](media/sdk/qs-js-node-01-open_project.png)

1. A Visual Studio Code-ban nyisson meg egy terminált.

   ![A terminálablakban](media/sdk/qs-js-node-02_open_terminal.png)

1. Futtatás `npm` függőségek telepítéséhez.

   ![az npm install](media/sdk/qs-js-node-03-npm_install.png)

1. Most már készen megnyitásához `index.js`és állítson be egy töréspontot.

   ![egy sor 16 töréspontot az index.js](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. A hibakeresés, vagy válassza az F5 vagy **hibakeresési/Start-hibakeresés** a menüből.

   ![A hibakeresés menüből](media/sdk/qs-js-node-05-start_debugging.png)

1. Ha elérte a töréspontot, vizsgálhatja és a változókat.

   ![Ladicí program](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. A hibakeresési konzolt ablakban látható kimenetet.

   ![Konzol hibakeresése](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Node.js-minták a Githubon](https://aka.ms/csspeech/samples)
