---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399923"
---
:::row:::
    :::column span="3":::
        A JavaScript Speech SDK érhető el npm csomagként, lásd a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> és a github-tárház <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">kognitív-szolgáltatások-speech-sdk-js. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bár a JavaScript Speech SDK érhető el npm csomagként, így mind az ügyfél böngészők és node.js is fogyasztanak , hogy - fontolja meg a különböző építészeti vonatkozásait az egyes környezetekben. A <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">dokumentumobjektum-modell (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> például nem érhető el a kiszolgálóoldali alkalmazások számára, ahogy a <a href="https://nodejs.org/api/fs.html" target="_blank">fájlrendszer <span class="docon docon-navigate-external x-hidden-focus"></span> </a> sem érhető el az ügyféloldali alkalmazások számára.

### <a name="nodejs-package-manager-npm"></a>Node.js csomagkezelő (NPM)

A JavaScript Speech SDK telepítéséhez `npm install` futtassa az alábbi parancsot.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-parancsfájlcímke

Másik lehetőségként közvetlenül `<script>` is felvehet egy címkét a HTMLs `<head>` elembe, amely a <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM szindikátusra <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>támaszkodik.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

További információt a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">webböngésző beszédfelismerési sdk-rövid útmutatójában talál. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
