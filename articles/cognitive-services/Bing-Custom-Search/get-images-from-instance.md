---
title: Képek beolvasása az egyéni nézetből – Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Magas szintű áttekintés a Bing Custom Search használatáról a webes egyéni nézetből származó képek beszerzéséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 886f04d2ea143b7c6c22aa2e746de179fcf7e7dd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090647"
---
# <a name="get-images-from-your-custom-view"></a>Képek beolvasása az egyéni nézetből

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](https://aka.ms/cogsvcs/bingmove)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

A Bing Egyéni rendszerképek keresése lehetővé teszi, hogy a képekkel gazdagítsa egyéni keresési élményét. A webes találatokhoz hasonlóan az egyéni keresés a képkeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. A rendszerképeket a Bing egyéni lemezképek keresési API-jával vagy a futtatott felhasználói felület funkció használatával szerezheti be. A kihelyezett felhasználói felület funkció használata egyszerűen használható, és ajánlott a keresési élmény rövid sorrendben történő futtatásához.  Az üzemeltetett felhasználói felület lemezképekkel való konfigurálásával kapcsolatos további információkért lásd: [az üzemeltetett felhasználói felületi élmény konfigurálása](hosted-ui.md).

Ha nagyobb mértékben szeretné szabályozni a keresési eredmények megjelenítését, használhatja a Bing egyéni lemezképek keresési API-ját. Mivel az API meghívása hasonló a Bing Image Search API meghívásához, az API-t hívó példákhoz a pénztár [Bing Image Search](../Bing-Image-Search/overview.md) . Azonban mielőtt ezt megtenné, ismerkedjen meg az [Egyéni képek keresése API-referenciával](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) . A fő különbségek a támogatott lekérdezési paraméterek (a customConfig lekérdezési paramétert is meg kell adni) és a végpontot, amelyhez kéréseket küld.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
