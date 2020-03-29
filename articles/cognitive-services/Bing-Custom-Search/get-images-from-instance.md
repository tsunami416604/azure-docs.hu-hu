---
title: Képek beszereznie az egyéni nézetből – Bing egyéni keresés
titleSuffix: Azure Cognitive Services
description: Magas szintű áttekintés arról, hogy a Bing egyéni keresés használatával milyen képeket kaphat az egyéni webes nézetből.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390332"
---
# <a name="get-images-from-your-custom-view"></a>Képek beszerezése az egyéni nézetből

A Bing egyéni képek keresése lehetővé teszi az egyéni keresési élmény képekkel való gazdagítását. A webes találatokhoz hasonlóan az egyéni keresés a képkeresést is támogatja azokon a webhelyeken, amelyek a keresési példányhoz engedélyezve vannak. A képeket a Bing Egyéni képek keresési API-jával vagy a Hosztott felhasználói felület funkciójával szerezheti be. A Hosted ui funkció használata egyszerű, és ajánlott a keresési élmény rövid időn belül történő felfuttatásához.  A szolgáltatott felhasználói felület képek rekonfigurálásáról a [Hosztolt felhasználói felület konfigurálása című](hosted-ui.md)témakörben talál.

Ha szeretné, hogy jobban kézben jelenítse meg a keresési eredményeket, használhatja a Bing Egyéni képek keresési API-ját. Mivel az API-hívás hasonló a Bing Image Search API hívásához, a [Bing Image Search](../Bing-Image-Search/overview.md) kivételezése példákat az API-t. De mielőtt ezt megtenné, ismerkedjen meg az [Egyéni képek keresés API referenciatartalommal.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) A fő különbségek a támogatott lekérdezési paraméterek (tartalmaznia kell a customConfig lekérdezési paraméter) és a végpont, amelynek kéréseket küld.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
