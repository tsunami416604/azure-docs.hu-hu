---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 161138ba09baacdd2a3d18d35c930c645ee78103
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715169"
---
## <a name="run-the-spx-tool"></a>Az SPX eszköz futtatása

Most már készen áll az SPX eszköz futtatására a beszéd szövegből egy új hangfájlba való szintetizálása céljából.

A parancssorban váltson arra a könyvtárra, amely az SPX-eszköz bináris fájlját tartalmazza, és írja be a következőt:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Az SPX-eszköz természetes nyelvet hoz létre angol nyelven a `greetings.wav` hangfájlba.
A Windowsban a hangfájlt a beírásával is lejátszhatja `start greetings.wav` .
