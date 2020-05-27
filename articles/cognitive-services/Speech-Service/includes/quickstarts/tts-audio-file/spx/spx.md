---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806523"
---
## <a name="run-the-speech-cli"></a>A Speech CLI futtatása

Most már készen áll a Speech CLI futtatására a beszéd szövegből egy új hangfájlba való szintéziséhez.

A parancssorban váltson arra a könyvtárra, amely a parancssori felület bináris fájlját tartalmazza, és írja be a következőt:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A Speech CLI a természetes nyelvet angol nyelven hozza létre a `greetings.wav` hangfájlba.
A Windowsban a hangfájlt a beírásával is lejátszhatja `start greetings.wav` .
