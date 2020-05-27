---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806486"
---
## <a name="enable-microphone"></a>Mikrofon engedélyezése

Csatlakoztassa a számítógépet, és kapcsolja be a PC-mikrofont, és kapcsolja ki a mikrofont is használó alkalmazásokat. Egyes számítógépek beépített mikrofonnal rendelkeznek, míg mások a Bluetooth-eszközök konfigurációját igénylik.

## <a name="run-the-speech-cli"></a>A Speech CLI futtatása

Most már készen áll a Speech CLI futtatására, hogy felismerje a beszédet a mikrofonból.

1. **Indítsa el az alkalmazást** – a parancssorból váltson arra a könyvtárra, amely tartalmazza a Speech CLI bináris fájlt, és írja be a következőt:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > A Speech CLI alapértelmezett értéke az angol. [A beszéd-szöveg táblázatból](../../../../language-support.md)más nyelvet is választhat.
    > Adja meg például a következőt: `--source de-DE` a német beszéd felismerése.

2. **Felismerés elindítása** – beszéljen a mikrofonba. A szavak átírása valós időben szövegként jelenik meg. A Speech CLI egy adott időszak után leáll, vagy a CTRL-C billentyűkombináció lenyomása után.
