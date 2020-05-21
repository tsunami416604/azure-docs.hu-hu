---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9ff21105e61be59b0000d86455ee19ba6f0d1aeb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714673"
---
## <a name="find-a-file-that-contains-speech"></a>Beszédet tartalmazó fájl keresése

Az SPX eszköz számos fájlformátumban és természetes nyelven képes a beszéd felismerésére. Ebben a rövid útmutatóban olyan WAV-fájlt használhat (16kHz vagy 8kHz, 16 bites és monó PCM), amely angol nyelvű beszédet tartalmaz.

1. Töltse le a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. <span class="docon docon-download x-hidden-focus"></span> WAV fájlokat </a>.
2. Másolja a `whatstheweatherlike.wav` fájlt az SPX-eszköz bináris fájljával megegyező könyvtárba.

## <a name="run-the-spx-tool"></a>Az SPX eszköz futtatása

Most már készen áll az SPX eszköz futtatására a hangfájlban talált beszéd felismeréséhez.

A parancssorban váltson arra a könyvtárra, amely az SPX-eszköz bináris fájlját tartalmazza, és írja be a következőt:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Az SPX-eszköz alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
> Adja meg például a következőt: `--source de-DE` a német beszéd felismerése.

Az SPX eszköz a beszéd szövegének átírását jeleníti meg a képernyőn. Ezután az SPX-eszköz be lesz zárva.
