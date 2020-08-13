---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173126"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Beszédszintézis hangszóróra

Most már készen áll a Speech CLI futtatására a beszéd szövegből való szintéziséhez. A parancssorban váltson arra a könyvtárra, amely a Speech CLI bináris fájlját tartalmazza. Ezután futtassa a következő parancsot.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

A beszédfelismerési CLI a számítógép hangszóróján belül természetes nyelvet hoz létre angol nyelven.

## <a name="synthesize-speech-to-a-file"></a>Beszéd készítése fájlba

A következő parancs futtatásával módosíthatja a hangszórók kimenetét egy `.wav` fájlba.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

A Speech CLI a természetes nyelvet angol nyelven hozza létre a `greetings.wav` hangfájlba.
A Windowsban a hangfájlt a beírásával is lejátszhatja `start greetings.wav` .