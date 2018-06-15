---
title: Megtekintéséhez és szerkesztéséhez Azure videó indexelő insights |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan megtekintése és szerkesztése a videó indexelő insights.
services: cognitive services
documentationcenter: ''
author: juliako
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: df4e2152f0f3ff0a2cc6a4dbb8c46478d5db7dc7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349870"
---
# <a name="view-and-edit-video-indexer-insights"></a>Megtekintése és szerkesztése a videó indexelő insights

Ez a témakör bemutatja, hogyan megtekintése és szerkesztése a videó indexelő insights egy videó.

1. Jelentkezzen be a [videó indexelő](https://api-portal.videoindexer.ai/) fiók.
2. Videó, amelyből el kívánja létrehozni az videó indexelő elemzések keresése. További információkért lásd: [pontos pillanat videók belül található](video-indexer-search.md).
3. Nyomja le az **lejátszása**.

    A lapon látható a videó röviden bemutatják azok insights. 

    ![Insights](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. Az összesített insights videó megtekintése. 

    Összesített insights az adatok egy összevont nézetének megjelenítése: lapokat, kulcsszavakat, hangulati elemek. Például láthatja a személyek és jelennek meg minden lapot időtartományhoz felületei és a %-ában megjelenő.

    A Windows Media player és az elemzések szinkronizálva van. Például ha kulcsszó vagy a Beszélgetés szövegének sor gombra kattint, a Windows Media player biztosít számára, hogy néhány percet a videó. Az alkalmazás érhet el a player/insights megtekintése és a szinkronizálás. További információkért lásd: [az alkalmazásba beágyazni Azure indexelő widgeteket](video-indexer-embed-widgets.md). 

3. A videó indexelő insights szerkesztése.

    Nyomja meg a Szerkesztés alatt a videót. A teljes körű információkat a videó bemutatja, hogy a lap jelenik meg. A részletes információkat blokkokra megszakad. Blokkok jelenleg itt könnyebb halad át az adatokat. Például blokk előfordulhat, hogy kell bontani alapján amikor hangszórók változnak, vagy hosszú szünetet. Létrehozhat saját kívánt sorokat tartalmazó lista. Videó megjelenítése a forrás csak meghatározott részeit, témakörök/kulcsszavak, hangulati elemek, személyek, hangszórók szerint szűrheti. Ha szeretné, csak megtekinteni a videó Beszélgetés szövegének vagy OCR.  

    ![Insights](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>További lépések

[Megtudhatja, hogyan videó indexelő adatmodellünk saját néhány más videó alapján](video-indexer-create-new.md).

## <a name="see-also"></a>Lásd még

[Az indexelő áttekintő videó](video-indexer-overview.md)

