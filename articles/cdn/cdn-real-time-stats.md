---
title: Valós idejű statisztikák az Azure CDN-ben | Microsoft dokumentumok
description: A valós idejű statisztikák valós idejű adatokat szolgáltatnak az Azure CDN teljesítményéről, amikor tartalmat szállítanak ügyfeleinek.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d56007e5a196a0857f3b69ac51f5e3b5a88c4f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593490"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Valós idejű statisztikák a Microsoft Azure CDN-ben
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a valós idejű statisztikákat a Microsoft Azure CDN-ben.  Ez a funkció valós idejű adatokat biztosít, például sávszélességet, gyorsítótár-állapotokat és egyidejű kapcsolatokat a CDN-profillal, amikor tartalmat szállít az ügyfeleknek. Ez lehetővé teszi a szolgáltatás állapotának folyamatos figyelését bármikor, beleértve az élő eseményeket is.

A következő grafikonok állnak rendelkezésre:

* [Sávszélesség](#bandwidth)
* [Állapotkódok](#status-codes)
* [Gyorsítótár állapotai](#cache-statuses)
* [Kapcsolatok](#connections)

## <a name="accessing-real-time-stats"></a>Hozzáférés valós idejű statisztikák
1. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját.
   
    ![CDN profilpanel](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. A CDN-profilpanelen kattintson a **Kezelés** gombra.
   
    ![CDN profilpanel kezelőgombja](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
3. Mutasson az **Analytics** fülre, majd mutasson a **Valós idejű statisztika** úszó panelre.  Kattintson a **HTTP Large Object elemre.**
   
    ![CDN felügyeleti portál](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    A valós idejű statisztika grafikonok jelennek meg.

Mindegyik grafikon valós idejű statisztikát jelenít meg a kiválasztott időtartamról, az oldal betöltésétől kezdve.  A grafikonok néhány másodpercenként automatikusan frissülnek.  A **Diagram frissítése** gomb ( ha van) törli a grafikont, majd csak a kijelölt adatokat jeleníti meg.

## <a name="bandwidth"></a>Sávszélesség
![Sávszélesség grafikon](./media/cdn-real-time-stats/cdn-bandwidth.png)

A **Sávszélesség** grafikon az aktuális platformhoz a kiválasztott időtartam alatt felhasznált sávszélesség mennyiségét jeleníti meg. A diagram árnyékolt része a sávszélesség-használatot jelzi. A jelenleg használt sávszélesség pontos mennyisége közvetlenül a vonaldiagram alatt jelenik meg.

## <a name="status-codes"></a>Állapotkódok
![Állapotkód-grafikon](./media/cdn-real-time-stats/cdn-status-codes.png)

Az **Állapotkódok** grafikon azt jelzi, hogy bizonyos HTTP-válaszkódok milyen gyakran fordulnak elő a kiválasztott időtartam alatt.

> [!TIP]
> Az egyes HTTP-állapotkódok leírását az [Azure CDN HTTP-állapotkódok ban](/previous-versions/azure/mt759238(v=azure.100))található.
> 
> 

A HTTP-állapotkódok listája közvetlenül a grafikon felett jelenik meg. Ez a lista minden olyan állapotkódot jelez, amely szerepelhet a sordiagramban, és az adott állapotkód hoz másodpercenkénti előfordulások számát. Alapértelmezés szerint a diagramon minden egyes állapotkódhoz megjelenik egy sor. Azonban dönthet úgy, hogy csak a CDN-konfiguráció szempontjából különleges jelentőséggel bíró állapotkódokat figyeli. Ehhez ellenőrizze a kívánt állapotkódokat, törölje az összes többi beállítást, majd kattintson **a Diagram frissítése**gombra. 

Egy adott állapotkód naplózott adatait ideiglenesen elrejtheti.  A közvetlenül a diagram alatti jelmagyarázatból kattintson az elrejteni kívánt állapotkódra. Az állapotkód azonnal el lesz rejtve a grafikonról. Ha ismét az állapotkódra kattint, az a beállítás ismét megjelenik.

## <a name="cache-statuses"></a>Gyorsítótár állapotai
![Gyorsítótár-állapotok grafikonja](./media/cdn-real-time-stats/cdn-cache-status.png)

A **gyorsítótár állapotai** grafikon azt jelzi, hogy bizonyos típusú gyorsítótár-állapotok milyen gyakran fordulnak elő a kiválasztott időtartam alatt. 

> [!TIP]
> Az egyes gyorsítótár-állapotkódok leírását az [Azure CDN-gyorsítótár állapotkódjai ban](/previous-versions/azure/mt759237(v=azure.100))található.
> 
> 

A gyorsítótár állapotkódjainak listája közvetlenül a grafikon felett jelenik meg. Ez a lista minden olyan állapotkódot jelez, amely szerepelhet a sordiagramban, és az adott állapotkód hoz másodpercenkénti előfordulások számát. Alapértelmezés szerint a diagramon minden egyes állapotkódhoz megjelenik egy sor. Azonban dönthet úgy, hogy csak a CDN-konfiguráció szempontjából különleges jelentőséggel bíró állapotkódokat figyeli. Ehhez ellenőrizze a kívánt állapotkódokat, törölje az összes többi beállítást, majd kattintson **a Diagram frissítése**gombra. 

Egy adott állapotkód naplózott adatait ideiglenesen elrejtheti.  A közvetlenül a diagram alatti jelmagyarázatból kattintson az elrejteni kívánt állapotkódra. Az állapotkód azonnal el lesz rejtve a grafikonról. Ha ismét az állapotkódra kattint, az a beállítás ismét megjelenik.

## <a name="connections"></a>Kapcsolatok
![Kapcsolatok grafikon](./media/cdn-real-time-stats/cdn-connections.png)

Ez a grafikon azt jelzi, hogy hány kapcsolat jött létre a peremhálózati kiszolgálókhoz. A CDN-n áthaladó eszközre vonatkozó minden egyes kérelem kapcsolathoz vezet.

## <a name="next-steps"></a>Következő lépések
* Értesítést kaphat [valós idejű riasztásokról az Azure CDN-ben](cdn-real-time-alerts.md)
* Mélyebbre áshat a [speciális HTTP-jelentésekkel](cdn-advanced-http-reports.md)
* [Használati minták](cdn-analyze-usage-patterns.md) elemzése

