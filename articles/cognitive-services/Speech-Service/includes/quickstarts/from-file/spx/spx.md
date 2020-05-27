---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806262"
---
## <a name="find-a-file-that-contains-speech"></a>Beszédet tartalmazó fájl keresése

A Speech CLI számos fájlformátumban és természetes nyelven képes felismerni a beszédet. Ebben a rövid útmutatóban olyan WAV-fájlt használhat (16kHz vagy 8kHz, 16 bites és monó PCM), amely angol nyelvű beszédet tartalmaz.

1. Töltse le a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. <span class="docon docon-download x-hidden-focus"></span> WAV fájlokat </a>.
2. Másolja a `whatstheweatherlike.wav` fájlt a SPEECH CLI bináris fájllal megegyező könyvtárba.

## <a name="run-the-speech-cli"></a>A Speech CLI futtatása

Most már készen áll a Speech CLI futtatására a hangfájlban található beszéd felismeréséhez.

A parancssorban váltson arra a könyvtárra, amely a parancssori felület bináris fájlját tartalmazza, és írja be a következőt:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> A Speech CLI alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
> Adja meg például a következőt: `--source de-DE` a német beszéd felismerése.

A Speech CLI a képernyőn megjelenő beszéd szövegének átírását jeleníti meg. Ekkor a parancssori felület le lesz zárva.
