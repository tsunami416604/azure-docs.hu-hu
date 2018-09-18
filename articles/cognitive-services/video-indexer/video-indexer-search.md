---
title: Videók – Video Indexer belül pontos időpontok keresése
titlesuffix: Azure Cognitive Services
description: Ez a témakör bemutatja, hogyan belül a Video Indexer használatával kíván videókat pontos időpontok keresése.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 9806f9c3f22429d61ac765773e4b6971d683a6b1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984508"
---
# <a name="find-exact-moments-within-videos"></a>Pontos időpontok keresése videókban

Ez a témakör bemutatja, hogy a keresési lehetőségeket, amelyek lehetővé teszik, hogy a videókban pontos időpontok keresése.

1. Keresse meg a [Video Indexer](https://www.videoindexer.ai/) webhelyet, és jelentkezzen be.
2. Keressen rá a fiókban lévő összes videó között.

    A következő példában azt keres az összes videó channel9-on, Scott Hanselman videosorozatának e által létrehozott is.

    ![Keresés](./media/video-indexer-search/video-indexer-search01.png)
    
3. A videó összefoglaló elemzések keresése.

    Majd videón belüli kattintva kereshet **lejátszása** videó. Ezt követően is végezhető keresés a videó kiválasztásával a **keresési** fülre. Például hogy keresni minden olyan hely, ahol az "identity protection" szöveget használják. 

    ![Keresés](./media/video-indexer-search/video-indexer-search02.png)

    Kattint az eredmények közül, ha a Windows Media player méretezhetőséget kínál, hogy a videóban. A player/elemzések megtekintése és a szinkronizálás érheti el az alkalmazásban. További információkért lásd: [az alkalmazásba való beágyazása a Video Indexer widgetek](video-indexer-embed-widgets.md). 

    
4. Keresés a videó részletes áttekintését.

    Ha szeretne létrehozni a saját lebontása a videót, a megtalált alapján, nyomja le az **szerkesztése** gombra. Ezen a lapon látható a videó teljes áttekintését. Csak a részletezést belül kereshet az Önt érdeklő sorokat. További információkért lásd: [megtekintése és szerkesztése a Videóindexelő-elemzések](video-indexer-view-edit.md).

    Ebben a példában a "identity protection" szöveget keres azt. Azt is alkalmazza a rendszer további szűrőket, az alábbi képernyőn látható módon.

    ![Keresés](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>További lépések 

Miután megtalálta a videót, amelyben dolgozni szeretne, továbbra is a videó feldolgozása ezek témakörökben ismertetett módon: 

- [A meglévő videóhoz alapján új videóelemzések létrehozása](video-indexer-create-new.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[Video Indexer – áttekintés](video-indexer-overview.md)
