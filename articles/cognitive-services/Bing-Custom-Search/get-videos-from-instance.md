---
title: Videók letöltése egyéni nézetből – Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Magas szintű áttekintés a Bing Custom Search használatáról a webes nézetből származó videók beszerzéséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: c0184d1765052510d42337b34094f66b2536c9ec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367539"
---
# <a name="get-videos-from-your-custom-view"></a>Videók letöltése egyéni nézetből

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](https://aka.ms/cogsvcs/bingmove)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

A Bing egyéni videók keresése funkcióval gazdagíthatja egyéni keresési élményét videókon. A webes találatokhoz hasonlóan az egyéni keresés a videókeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. A videók a Bing egyéni videók keresési API-jával vagy az üzemeltetett felhasználói felület funkció segítségével szerezhetők be. A kihelyezett felhasználói felület funkció használata egyszerűen használható, és ajánlott a keresési élmény rövid sorrendben történő futtatásához. Az üzemeltetett felhasználói felület videókra való konfigurálásával kapcsolatos további információkért lásd: [az üzemeltetett felhasználói felületi élmény konfigurálása](hosted-ui.md).

Ha nagyobb mértékben szeretné szabályozni a keresési eredmények megjelenítését, használhatja a Bing egyéni videók keresési API-ját. Mivel az API meghívása hasonló a Bing Video Search API meghívásához, az API-t hívó példákhoz a pénztár [Bing Video Search](../bing-video-search/overview.md) . Azonban mielőtt ezt megtenné, ismerkedjen meg az [Egyéni videók keresési API-referenciájának](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) tartalmával. A fő különbségek a támogatott lekérdezési paraméterek (a customConfig lekérdezési paramétert is meg kell adni) és a végpontot, amelyhez kéréseket küld.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->