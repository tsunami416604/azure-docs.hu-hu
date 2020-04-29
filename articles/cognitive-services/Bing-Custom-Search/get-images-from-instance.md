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
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66390332"
---
# <a name="get-images-from-your-custom-view"></a>Képek beolvasása az egyéni nézetből

A Bing Egyéni rendszerképek keresése lehetővé teszi, hogy a képekkel gazdagítsa egyéni keresési élményét. A webes találatokhoz hasonlóan az egyéni keresés a képkeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. A rendszerképeket a Bing egyéni lemezképek keresési API-jával vagy a futtatott felhasználói felület funkció használatával szerezheti be. A kihelyezett felhasználói felület funkció használata egyszerűen használható, és ajánlott a keresési élmény rövid sorrendben történő futtatásához.  Az üzemeltetett felhasználói felület lemezképekkel való konfigurálásával kapcsolatos további információkért lásd: [az üzemeltetett felhasználói felületi élmény konfigurálása](hosted-ui.md).

Ha nagyobb mértékben szeretné szabályozni a keresési eredmények megjelenítését, használhatja a Bing egyéni lemezképek keresési API-ját. Mivel az API meghívása hasonló a Bing Image Search API meghívásához, az API-t hívó példákhoz a pénztár [Bing Image Search](../Bing-Image-Search/overview.md) . Azonban mielőtt ezt megtenné, ismerkedjen meg az [Egyéni képek keresése API-referenciával](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) . A fő különbségek a támogatott lekérdezési paraméterek (a customConfig lekérdezési paramétert is meg kell adni) és a végpontot, amelyhez kéréseket küld.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
