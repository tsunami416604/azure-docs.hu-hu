---
title: Videók – Video Indexer belül pontos időpontok keresése
titlesuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan belül a Video Indexer használatával kíván videókat pontos időpontok keresése.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6c93c0228425b2ff020e5b4301e68eae5b1062a3
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807830"
---
# <a name="find-exact-moments-within-videos"></a>Pontos időpontok keresése videókban

Ez a témakör bemutatja, hogy a keresési lehetőségeket, amelyek lehetővé teszik, hogy a videókban pontos időpontok keresése.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Keressen rá a fiókban lévő összes videó között.

    A következő példában azt kommunikálhatnak biztonságával kapcsolatban, és amely Satya jelenik meg, az összes videó keresése

    ![Keresés](./media/video-indexer-search/video-indexer-search01.png)
3. A videó összefoglaló elemzések keresése.

    Majd videón belüli kattintva kereshet **lejátszása** videó. Ezt követően is végezhető keresés a videó kiválasztásával a **keresési** fülre. 

    A következő példában azt keresse meg a "biztonságos" a kijelölt videó belül.

    ![Keresés](./media/video-indexer-search/video-indexer-search02.png)

    Kattint az eredmények közül, ha a Windows Media player méretezhetőséget kínál, hogy a videóban. A player/elemzések megtekintése és a szinkronizálás érheti el az alkalmazásban. További információkért lásd: [az alkalmazásba való beágyazása a Video Indexer widgetek](video-indexer-embed-widgets.md). 
4. Keresés a videó részletes áttekintését.

    Ha szeretne létrehozni a saját lebontása a videót, a megtalált alapján, nyomja le az **szerkesztése** gombra. Ez az oldal megjeleníti egy videó teljes lebontását. Csak a részletezést belül kereshet az Önt érdeklő sorokat. További információkért lásd: [megtekintése és szerkesztése a Videóindexelő-elemzések](video-indexer-view-edit.md).

    Ebben a példában a "identity protection" szöveget keres azt. Azt is alkalmazza a rendszer további szűrőket, az alábbi képernyőn látható módon.

    ![Keresés](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>További lépések 

Miután megtalálta a videót, amelyben dolgozni szeretne, továbbra is a videó feldolgozása ezek témakörökben ismertetett módon: 

- [A meglévő videóhoz alapján új videóelemzések létrehozása](video-indexer-create-new.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)
