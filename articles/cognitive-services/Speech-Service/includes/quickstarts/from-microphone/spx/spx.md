---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9f1da13efc9f75d14fdf2d158e8b7547d4a9fa94
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715305"
---
## <a name="enable-microphone"></a>Mikrofon engedélyezése

Csatlakoztassa a számítógépet, és kapcsolja be a PC-mikrofont, és kapcsolja ki a mikrofont is használó alkalmazásokat. Egyes számítógépek beépített mikrofonnal rendelkeznek, míg mások a Bluetooth-eszközök konfigurációját igénylik.

## <a name="run-the-spx-tool"></a>Az SPX eszköz futtatása

Most már készen áll az SPX eszköz futtatására, hogy felismerje a beszédet a mikrofonból.

1. **Indítsa el az alkalmazást** – a parancssorból váltson arra a könyvtárra, amely az SPX-eszköz bináris fájlját tartalmazza, majd írja be a következőt:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Az SPX-eszköz alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
    > Adja meg például a következőt: `--source de-DE` a német beszéd felismerése.

2. **Felismerés elindítása** – beszéljen a mikrofonba. A szavak átírása valós időben szövegként jelenik meg. Az SPX-eszköz egy adott időtartam után leáll, vagy a CTRL-C billentyűkombináció lenyomása után.
