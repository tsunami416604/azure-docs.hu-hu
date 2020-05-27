---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 4d548c1b7614503919de2725acb02d7f808ceb93
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806248"
---
## <a name="run-the-speech-cli"></a>A Speech CLI futtatása

Most már készen áll a Speech CLI futtatására, hogy más nyelven szövegbe fordítsa a szöveget.

A parancssorban váltson arra a könyvtárra, amely a parancssori felület bináris fájlját tartalmazza, és írja be a következőt:

```bash
spx translate --microphone --target de-DE
```

A beszédfelismerési parancssori felület nyelvén német nyelven kinyomtatott szöveg jelenik meg.
Az eszköz leállításához nyomja le az ENTER billentyűt.

> [!NOTE]
> A Speech CLI alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
> Adja meg például a következőt: `--source ja-JP` a japán beszéd felismerése.
