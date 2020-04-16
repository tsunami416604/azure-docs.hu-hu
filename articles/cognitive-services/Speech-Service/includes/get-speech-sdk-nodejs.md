---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6dd4dfd0edd334005cc2af51a46f9ca2e634272f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399903"
---
:::row:::
    :::column span="3":::
        A JavaScript Speech SDK érhető el npm csomagként, lásd a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices-speech-sdk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> és a github-tárház <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">kognitív-szolgáltatások-speech-sdk-js. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Bár a JavaScript Speech SDK érhető el npm csomagként, így mind a Node.js és az ügyfél webböngészők is fogyasztanak , hogy - fontolja meg a különböző építészeti vonatkozásait az egyes környezetekben. A <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">dokumentumobjektum-modell (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> például nem érhető el a kiszolgálóoldali alkalmazások számára, ahogy a <a href="https://nodejs.org/api/fs.html" target="_blank">fájlrendszer <span class="docon docon-navigate-external x-hidden-focus"></span> </a> sem érhető el az ügyféloldali alkalmazások számára.

### <a name="nodejs-package-manager-npm"></a>Node.js csomagkezelő (NPM)

A JavaScript Speech SDK telepítéséhez `npm install` futtassa az alábbi parancsot.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

További információt a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js beszédfelismerési SDK rövidútmutatója tartalmaz. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
