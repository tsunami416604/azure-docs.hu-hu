---
title: Pontos pillanatok keresése videókban a Video Indexer segítségével
titleSuffix: Azure Media Services
description: További információ arról, hogyan kereshetsz pontos pillanatokat a videókban a Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127908"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Pontos pillanatok keresése videókban a Video Indexer segítségével

Ez a témakör azokat a keresési lehetőségeket mutatja be, amelyek lehetővé teszik a pontos pillanatok keresését a videókban.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Keresés a fiókjában lévő összes videó között.

    A következő példában megkeressük az összes olyan videót, amely a biztonságról szól, és amelyben Satya megjelenik.

    ![Videó keresése a Video Indexer ben](./media/video-indexer-search/video-indexer-search01.png)

3. Keress a videó összesített elemzési adatai között.

    Ezután a videólejátszás **lehetőség** kiválasztásával kereshet a videóban. Ezután a **Keresés** fülkiválasztásával megkeresheti a videó pontos pillanatait.

    A következő példában a "biztonságos" kifejezésre keresünk a kiválasztott videóban.

    ![Keresés egy videóban a Video Indexer segítségével](./media/video-indexer-search/video-indexer-search02.png)

    Ha kiválasztod az egyik eredményt, a játékos pontosan a videó adott pillanatához vezet. A lejátszó/elemzési nézet és a szinkronizálás az alkalmazásban érhető el. További információt a [Videoindexelő widgetek beágyazása az alkalmazásba című témakörben talál.](video-indexer-embed-widgets.md)

4. Keresd meg a videó részletes bontását.

    Ha a talált videó alapján szeretné létrehozni saját klipjét, válassza a **Szerkesztés** gombot. Ezen az oldalon a videó és az elemzési adatok szűrőként jelennek meg. További információt a [Video Indexer-elemzések megtekintése és szerkesztése című témakörben talál.](video-indexer-view-edit.md)

    A videóban pontos pillanatokat kereshetsz, hogy csak azokat a sorokat jelenítsd meg, amelyek érdekelnek. Az oldalelemzési adatok segítségével szűrheti a megtekinteni kívánt részeket. Ha végzett, megtekintheti a klip előnézetét, és a **Közzététel gombra** választva létrehozhatja a gyűjteményben megjelenő új klipet.

    A következő példában a "vegyes valóság" szöveget kerestük. További szűrőket is alkalmaztunk, amint az az alábbi képernyőn látható.

    ![Pontos pillanat keresése a Video Indexer ben](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>További lépések

Miután megtalálta a pontos pillanatot a videóban, amivel dolgozni szeretne, folytathatja a videó feldolgozását. További információkért tekintse át a következők témaköröket:

- [A videók mély elemzési adatainak használata](use-editor-create-project.md)
- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)
