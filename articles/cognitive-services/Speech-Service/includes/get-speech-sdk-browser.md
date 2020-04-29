---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399923"
---
:::row:::
    :::column span="3":::
        A JavaScript Speech SDK NPM-csomagként érhető el: <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> , és ez a Companion GitHub-tárház <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">kognitív-Services-Speech-SDK- <span class="docon docon-navigate-external x-hidden-focus"> </span>js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bár a JavaScript Speech SDK NPM-csomagként érhető el, így az ügyfél-webböngészők és a Node. js is felhasználható – vegye figyelembe az egyes környezetek különböző építészeti következményeit. Például a dokumentum- <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">objektummodell (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nem érhető el kiszolgálóoldali alkalmazásokhoz, ugyanúgy, mint a <a href="https://nodejs.org/api/fs.html" target="_blank">fájlrendszer <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nem érhető el az ügyféloldali alkalmazások számára.

### <a name="nodejs-package-manager-npm"></a>Node. js csomagkezelő (NPM)

A JavaScript Speech SDK telepítéséhez futtassa az alábbi `npm install` parancsot az alábbi paranccsal.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-parancsfájl címkéje

Azt is megteheti, hogy `<script>` közvetlenül tartalmaz egy címkét `<head>` a html-elemben, amely a <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM szindikátusra <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>támaszkodik.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

További információ: <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">webböngésző SPEECH SDK <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>– rövid útmutató.
