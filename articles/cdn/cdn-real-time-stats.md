---
title: "Az Azure CDN valós idejű statisztikák |} Microsoft Docs"
description: "Valós idejű statisztikák Azure CDN teljesítményének valós idejű adatokat biztosít, amikor a tartalom továbbítása az ügyfelek számára."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>A Microsoft Azure CDN valós idejű statisztikák
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a Microsoft Azure CDN valós idejű statisztikák.  Ezt a funkciót biztosít a valós idejű adatok, például a sávszélesség, a gyorsítótár állapotok és a CDN-profil létesített egyidejű kapcsolatok, amikor a tartalom továbbítása az ügyfelek számára. Ez lehetővé teszi, hogy folyamatosan figyelje a a szolgáltatás bármikor, beleértve az éles események.

A következő diagramokon érhetők el:

* [Sávszélesség](#bandwidth)
* [Állapotkódok](#status-codes)
* [Gyorsítótár-állapotok](#cache-statuses)
* [Kapcsolatok](#connections)

## <a name="accessing-real-time-stats"></a>Valós idejű statisztikák elérése
1. Az a [Azure Portal](https://portal.azure.com), keresse meg a CDN-profilt.
   
    ![CDN-profil panelje](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. A CDN-profil panelje, kattintson a **kezelése** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
3. Vigye a **Analytics** lapra, és vigye a **valós idejű statisztikák** menü.  Kattintson a **HTTP nagy objektum**.
   
    ![CDN-felügyeleti portálon](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    A valós idejű statisztikák diagramokon jelennek meg.

A diagramokon mindegyikének jeleníti meg valós idejű statisztikák megjelentése a kijelölt időtartama, indítása az oldal betöltésekor.  A diagramokon automatikus frissítés minden néhány másodpercben.  A **frissítése Graph** gombra, ha van ilyen, törölni fogja a diagramot, amely után az csak jeleníti meg a kijelölt adatokat.

## <a name="bandwidth"></a>Sávszélesség
![Sávszélesség-grafikon](./media/cdn-real-time-stats/cdn-bandwidth.png)

A **sávszélesség** graph a kijelölt időtartam a jelenlegi platform használt sávszélességet jelenít meg. A grafikon árnyékolt része azt jelzi, hogy a sávszélesség-használat. A jelenleg használt sávszélesség pontos mennyisége közvetlenül a vonaldiagram alább látható.

## <a name="status-codes"></a>Állapotkódok
![Kód állapotdiagram](./media/cdn-real-time-stats/cdn-status-codes.png)

A **állapotkódok** grafikon azt jelzi, hogy milyen gyakran bizonyos HTTP válaszkódot a kijelölt időtartama alatt is megjelenhetnek.

> [!TIP]
> Az egyes HTTP-állapot kód lehetőségek ismertetését lásd: [Azure CDN HTTP-állapotkódok](https://msdn.microsoft.com/library/mt759238.aspx).
> 
> 

A HTTP-állapotkódok listáját közvetlenül a diagram felett jelenik meg. Ez a lista azt jelzi, hogy minden állapotkód is szerepelhet a vonaldiagram és eseményeket adott állapotkód másodpercenkénti száma. Alapértelmezés szerint egy sor minden ezen a diagramon állapotkódok jelenik meg. Azonban ha szeretné, csak figyelheti az állapot kód, amelyek különleges jelentőséggel a CDN-konfigurációhoz. Ehhez ellenőrizze a kívánt állapotkódok és törölje, majd kattintson **frissítése Graph**. 

Egy adott állapotkód: az a naplózott adatok ideiglenesen elrejthetők.  A jelmagyarázat közvetlenül a diagram alatt kattintson az elrejteni kívánt állapotkódot. Az állapotkód: azonnal a elől elrejtett a diagramon. Kattintson ismét az adott állapotkód, akkor ezt a beállítást, ismét megjelenik.

## <a name="cache-statuses"></a>Gyorsítótár-állapotok
![Gyorsítótár-állapotok diagramhoz](./media/cdn-real-time-stats/cdn-cache-status.png)

A **gyorsítótár állapotok** grafikon azt jelzi, hogy milyen gyakran bizonyos típusú gyorsítótár állapotok a kijelölt időtartama alatt is megjelenhetnek. 

> [!TIP]
> Az egyes gyorsítótár állapot kód lehetőségek ismertetését lásd: [Azure CDN gyorsítótár állapotkódok](https://msdn.microsoft.com/library/mt759237.aspx).
> 
> 

A gyorsítótár állapotkódok listáját közvetlenül a diagram felett jelenik meg. Ez a lista azt jelzi, hogy minden állapotkód is szerepelhet a vonaldiagram és eseményeket adott állapotkód másodpercenkénti száma. Alapértelmezés szerint egy sor minden ezen a diagramon állapotkódok jelenik meg. Azonban ha szeretné, csak figyelheti az állapot kód, amelyek különleges jelentőséggel a CDN-konfigurációhoz. Ehhez ellenőrizze a kívánt állapotkódok és törölje, majd kattintson **frissítése Graph**. 

Egy adott állapotkód: az a naplózott adatok ideiglenesen elrejthetők.  A jelmagyarázat közvetlenül a diagram alatt kattintson az elrejteni kívánt állapotkódot. Az állapotkód: azonnal a elől elrejtett a diagramon. Kattintson ismét az adott állapotkód, akkor ezt a beállítást, ismét megjelenik.

## <a name="connections"></a>Kapcsolatok
![Kapcsolatok diagramhoz](./media/cdn-real-time-stats/cdn-connections.png)

Ehhez a diagramhoz azt jelzi, hogy hány kapcsolat jött létre a peremhálózati kiszolgálóra. Minden egyes kérelem egy eszköz, amely áthalad a CDN-eredmények kapcsolaton keresztül.

## <a name="next-steps"></a>Következő lépések
* Az értesítés [valós idejű riasztások az Azure CDN szolgáltatás használata](cdn-real-time-alerts.md)
* Mélyebb a Dig [speciális HTTP-jelentések](cdn-advanced-http-reports.md)
* Elemezze [használati minták](cdn-analyze-usage-patterns.md)

