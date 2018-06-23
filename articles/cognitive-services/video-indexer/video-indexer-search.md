---
title: Használja az Azure videó indexelőt pontos pillanat videók belül található |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan pontos pillanat videók belül található.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/02/2017
ms.author: juliako
ms.openlocfilehash: 75afa4efa50191466f8850d9b1313cf89a83c0a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348966"
---
# <a name="find-exact-moments-within-videos"></a>Pontos pillanat videók belül található

Ez a témakör bemutatja a keresési beállítások, amelyek lehetővé teszik, hogy pontos pillanat videók belül található.

1. Jelentkezzen be a [videó indexelő](https://api-portal.videoindexer.ai/) fiók.
2. Keresse meg a fiók összes videók között.

    A következő példában azt a keresés Channel9 a Scott Hanselman által létrehozott összes videók.

    ![Keresés](./media/video-indexer-search/video-indexer-search01.png)
    
3. Keresse meg a videó röviden bemutatják azok feltárása.

    Is kereshet belül videó kattintva **lejátszása** meg a videót. Ezután úgy keresheti meg a videó belül kiválasztása a **keresési** fülre. Például azt a keresés kiterjed minden olyan hely, ahol a "identity protection" szöveget szolgál. 

    ![Keresés](./media/video-indexer-search/video-indexer-search02.png)

    Ha gombra kattint, az eredmények közül, a Windows Media player biztosít számára, hogy néhány percet a videó. Az alkalmazás érhet el a player/insights megtekintése és a szinkronizálás. További információkért lásd: [az alkalmazásba beágyazni videó indexelő widgeteket](video-indexer-embed-widgets.md). 

    
4. Keresse meg a részletes információkat a videó.

    Ha szeretne létrehozni a saját lebontása alapján a videót, talált, nyomja meg a **szerkesztése** gombra. Ezen a lapon látható a teljes körű információkat a videó. Csak a részletezést belül kereshet érdekli sorokat. További információkért lásd: [megtekintése és szerkesztése a videó indexelő insights](video-indexer-view-edit.md).

    Ebben a példában a "identity protection" szöveget keres azt. Is alkalmaztunk további szűrőket, az alábbi képernyőn látható módon.

    ![Keresés](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>További lépések 

Miután megtalálta a videót szeretne dolgozni, továbbra is a videó feldolgozása az alábbi témakörökben találja leírtak szerint: 

- [Hozzon létre új videó insights meglévő videó alapján](video-indexer-create-new.md)
- [Folyamatok tartalom videó indexelő REST API-hoz](video-indexer-use-apis.md)
- [Az alkalmazás visual widgeteket beágyazása](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[Az indexelő áttekintő videó](video-indexer-overview.md)
