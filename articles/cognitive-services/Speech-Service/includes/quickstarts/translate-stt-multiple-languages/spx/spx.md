---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 94d50a15b0d5d7bed2bd43516952036044d7f0c0
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715100"
---
## <a name="run-the-spx-tool"></a>Az SPX eszköz futtatása

Most már készen áll az SPX eszköz futtatására, hogy két különböző nyelven szövegbe fordítsa a beszédet.

A parancssorban váltson arra a könyvtárra, amely az SPX-eszköz bináris fájlját tartalmazza, és írja be a következőt:

```bash
spx translate --microphone --target de-DE --target es-MX
```

Az SPX-eszköz lefordítja az angol nyelvű szöveget a német és (mexikói) spanyol nyelven kinyomtatott szövegre.
Az eszköz leállításához nyomja le az ENTER billentyűt.

> [!NOTE]
> Az SPX-eszköz alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
> Adja meg például a következőt: `--source ja-JP` a japán beszéd felismerése.
