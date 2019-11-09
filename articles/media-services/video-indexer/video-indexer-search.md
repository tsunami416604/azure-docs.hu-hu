---
title: Pontos pillanatok a videókon belül – Video Indexer
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan találhatja meg a videókon belüli pontos pillanatokat Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833796"
---
# <a name="find-exact-moments-within-videos"></a>Pontos időpontok keresése videókban

Ebből a témakörből megtudhatja, hogy milyen keresési lehetőségekkel lehet pontos pillanatokat találni a videókon belül.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Keresés a fiókban található összes videó között.

    Az alábbi példában megkeresjük az összes olyan videót, amely a biztonsággal kapcsolatos, és amelyekben a kijelzés jelenik meg,

    ![Keresés](./media/video-indexer-search/video-indexer-search01.png)
3. Keresse meg a videó összegzett elemzéseit.

    A videóban a **Lejátszás** gombra kattintva kereshet a videón belül. Ezután kereshet a videóban a **Keresés** lapon. 

    A következő példában a "biztonság" kifejezésre keresünk a kiválasztott videóban.

    ![Keresés](./media/video-indexer-search/video-indexer-search02.png)

    Ha az egyik találatra kattint, a lejátszó erre a pillanatra viszi a videót. Az alkalmazásban elérheti a lejátszó/betekintő nézetet és a szinkronizálást. További információ: [video Indexer widgetek beágyazása az alkalmazásba](video-indexer-embed-widgets.md). 
4. Keresse meg a videó részletes részletezését.
    
    Ha saját klipet szeretne létrehozni a talált videó alapján, kattintson a **Szerkesztés** gombra. Ezen az oldalon megtekintheti a videókat és a hozzájuk tartozó bepillantást. További információ: [video Indexer-információk megtekintése és szerkesztése](video-indexer-view-edit.md). 

    A videóban kereshet úgy, hogy csak azokat a sorokat jelenítse meg, amelyek érdeklik, és az oldalsó bepillantást használva szűrheti a megtekinteni kívánt részeket. Ha elkészült, megtekintheti a klipet, és a **Közzététel** gombra kattintva létrehozhatja a gyűjteményben megjelenő új klipet.
    
    A következő példában a "vegyes valóság" szövegre kerestünk rá. További szűrőket is alkalmazunk, ahogy az alábbi képernyőn is látható.
    
    ![Keresés](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>További lépések 

Miután megtalálta a használni kívánt videót, folytathatja a videó feldolgozását a következő témakörökben leírtak szerint: 

- [A videók mélyreható bepillantást készíthet](use-editor-create-project.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még:

[A Video Indexer áttekintése](video-indexer-overview.md)
