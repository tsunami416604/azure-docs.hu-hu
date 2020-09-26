---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332470"
---
Ebben a rövid útmutatóban megtudhatja, hogyan végezheti el a szöveg-beszéd szintézist a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

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