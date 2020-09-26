---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327132"
---
A beszédfelismerési szolgáltatás egyik fő funkciója az emberi beszéd (más néven beszéd – szöveg) felismerése és átírása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech CLI-t az alkalmazásaiban és termékeiben a kiváló minőségű beszéd-szöveg átalakításhoz.

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Beszéd – szöveg a mikrofonból

Csatlakoztassa a számítógépet, és kapcsolja be a PC-mikrofont, és kapcsolja ki a mikrofont is használó alkalmazásokat. Egyes számítógépek beépített mikrofonnal rendelkeznek, míg mások a Bluetooth-eszközök konfigurációját igénylik.

Most már készen áll a Speech CLI futtatására, hogy felismerje a beszédet a mikrofonból. A parancssorban váltson arra a könyvtárra, amely tartalmazza a parancssori felület bináris fájlját, és futtassa a következő parancsot.

```bash
spx recognize --microphone
```

> [!NOTE]
> A Speech CLI alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
> Adja meg például a következőt: `--source de-DE` a német beszéd felismerése.

Beszéljen a mikrofonba, és valós időben megtekintheti a szavainak szövegbe való átírását. A Speech CLI egy adott időszak után leáll, vagy a CTRL-C billentyűkombináció lenyomása után.

## <a name="speech-to-text-from-audio-file"></a>Beszéd és szöveg közötti hangfájlból

A Speech CLI számos fájlformátumban és természetes nyelven képes felismerni a beszédet. Ebben a példában bármilyen WAV-fájlt használhat (16kHz vagy 8kHz, 16 bites és monó PCM), amely angol nyelvű beszédet tartalmaz. Ha szeretne egy gyors mintát, töltse le a <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a> fájlt, és másolja a Speech CLI bináris fájllal megegyező könyvtárba.

Most már készen áll arra, hogy az alábbi parancs futtatásával felismerje a hangfájlban talált beszédet.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> A Speech CLI alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
> Adja meg például a következőt: `--source de-DE` a német beszéd felismerése.

A Speech CLI a képernyőn megjelenő beszéd szövegének átírását jeleníti meg.