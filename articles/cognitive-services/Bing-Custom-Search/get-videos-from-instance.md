---
title: Videók kérhet az egyéni nézet – Bing egyéni keresés
titlesuffix: Azure Cognitive Services
description: Összefoglaló jellegű áttekintést nyújt a videók lekérése a webes egyéni nézetének Bing Custom Search használatával.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5394f86e8a5d167830cb4061800f4cbf9a521713
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816410"
---
# <a name="get-videos-from-your-custom-view"></a>Videók kérhet az egyéni nézet

Bing – egyéni videók keresés lehetővé teszi, hogy egyéni keresés használata során szerzett tapasztalataival videók gazdagabbá teheti. Hasonló webes találatokat, egyéni keresés támogatja a videók a webhelyek listája a példány keresése. A videókat a Bing egyéni videók keresési API-val beolvasása, vagy a tárolt felhasználói felületen funkció. A felhasználói felületi üzemeltetett szolgáltatás használata könnyen használható és a keresési funkciót és röviden rendelés ajánlott. A tárolt felhasználói felület tartalmazza a videók konfigurálásával kapcsolatos további információkért lásd: [konfigurálása a központi felhasználói felület](hosted-ui.md).

Ha szeretné jobban szabályozhatja a keresési eredményekkel, használhatja az egyéni videók keresési Bing-API. Mivel az API meghívása hasonlít a Bing Video Search API hívásakor a checkout [Bing Video Search](../Bing-Video-Search/search-the-web.md) példák az API-t hívná. Azonban mielőtt ezt megtenné, ismerje meg az a [egyéni videók keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference) tartalmat. A legfontosabb különbségek a következők: a támogatott lekérdezési paraméterek (meg kell adni a customConfig lekérdezési paraméter) és a végpont kéréseket küld.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->