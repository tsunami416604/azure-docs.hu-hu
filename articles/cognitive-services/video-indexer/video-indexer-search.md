---
title: Az Azure Video Indexer segítségével videókban pontos időpontok keresése |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan videókban pontos időpontok keresése.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 72f110c0c98e6fb6ee5fca684fdc74c6986e032c
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541407"
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
