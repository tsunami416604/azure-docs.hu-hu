---
title: Az Azure CDN valós idejű statisztikák |} A Microsoft Docs
description: Valós idejű statisztikák biztosít az Azure CDN teljesítményének valós idejű adatokat, az ügyfeleknek történő tartalomkézbesítésről.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: eb20630533735fb46ea7743be75448329281938a
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916563"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>A Microsoft Azure CDN Szolgáltatásban valós idejű statisztikák
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a Microsoft Azure CDN Szolgáltatásban valós idejű statisztikák.  Ez a funkció biztosítja a valós idejű adatok, például sávszélesség, gyorsítótárak állapota és egyidejű kapcsolódások a CDN-profil az ügyfeleknek történő tartalomkézbesítésről. Ez lehetővé teszi a folyamatos figyelés állapota, a szolgáltatás bármikor, beleértve az éles eseményeket.

A következő diagramok érhetők el:

* [Bandwidth](#bandwidth)
* [Állapotkódok](#status-codes)
* [Gyorsítótárak Allapota](#cache-statuses)
* [Kapcsolatok](#connections)

## <a name="accessing-real-time-stats"></a>Valós idejű statisztikák elérése
1. Az a [az Azure Portal](https://portal.azure.com), tallózással keresse meg a CDN-profilra.
   
    ![CDN-profil panelje](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. A CDN-profil panelje, kattintson a **kezelés** gombra.
   
    ![CDN-profil panelje kezelése gomb](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.
3. A kurzort a **Analytics** lapfülre, majd mutasson a **valós idejű statisztikák** úszó menü.  Kattintson a **HTTP nagy objektum**.
   
    ![CDN felügyeleti portálját](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    A valós idejű statisztikák grafikonok jelennek meg.

A gráfok mindegyike megjeleníti a kiválasztott időtartomány, amikor az oldal betöltött valós idejű statisztikák.  A gráfok néhány másodpercenként automatikusan frissül.  A **frissítése Graph** gombra, ha van ilyen, törli a gráf, amely után, csak azokat jeleníti meg a kijelölt adatokat.

## <a name="bandwidth"></a>Bandwidth
![A sávszélesség-grafikon](./media/cdn-real-time-stats/cdn-bandwidth.png)

A **sávszélesség** grafikonon jeleníti meg az adott időtartam a jelenlegi platform használt sávszélesség mennyiségét. A gráf árnyékolt részének azt jelzi, hogy a sávszélesség-használat. A jelenleg használt sávszélesség pontos mennyisége közvetlenül a vonaldiagram alatt jelenik meg.

## <a name="status-codes"></a>Állapotkódok
![Kód állapotdiagram](./media/cdn-real-time-stats/cdn-status-codes.png)

A **állapotkódok** graph azt jelzi, hogy milyen gyakran fordul elő bizonyos HTTP-válaszkódot az adott időtartam.

> [!TIP]
> Az egyes HTTP-állapot kód lehetőségek ismertetését lásd: [Azure CDN HTTP-állapotkódok](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

HTTP-állapotkódok listája közvetlenül a diagram fölött jelenik meg. Ebben a listában, amelyek hozzáadhatók a vonaldiagram és a másodpercenként, állapotkód: az előfordulások száma az egyes állapotkód jelzi. Alapértelmezés szerint egy sor minden a gráf ezek állapotkódok jelenik meg. Azonban csak figyelése a CDN-konfiguráció számára fontos szerepük van az állapotkódokkal választhat. Ehhez tekintse meg a kívánt állapotkódok és törölje az összes többi beállítást, majd kattintson **frissítése Graph**. 

Egy adott állapotkód: a naplózott adatok átmenetileg elrejthetők.  A jelmagyarázat közvetlenül a diagram alá kattintson a elrejteni kívánt állapotkódot. Az állapotkód azonnal rejtett a diagramon. Kattintson ismét a állapotkód okoz, ismét megjelenik ez a lehetőség.

## <a name="cache-statuses"></a>Gyorsítótárak Allapota
![Gyorsítótár-állapotok graph](./media/cdn-real-time-stats/cdn-cache-status.png)

A **gyorsítótárak Allapota** graph azt jelzi, hogy milyen gyakran fordul elő bizonyos típusú, gyorsítótárak állapota az adott időtartam. 

> [!TIP]
> Minden egyes gyorsítótár állapotát kódot beállítás leírását lásd: [Azure CDN gyorsítótára állapotkódok](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Gyorsítótár állapotkódok listája közvetlenül a diagram fölött jelenik meg. Ebben a listában, amelyek hozzáadhatók a vonaldiagram és a másodpercenként, állapotkód: az előfordulások száma az egyes állapotkód jelzi. Alapértelmezés szerint egy sor minden a gráf ezek állapotkódok jelenik meg. Azonban csak figyelése a CDN-konfiguráció számára fontos szerepük van az állapotkódokkal választhat. Ehhez tekintse meg a kívánt állapotkódok és törölje az összes többi beállítást, majd kattintson **frissítése Graph**. 

Egy adott állapotkód: a naplózott adatok átmenetileg elrejthetők.  A jelmagyarázat közvetlenül a diagram alá kattintson a elrejteni kívánt állapotkódot. Az állapotkód azonnal rejtett a diagramon. Kattintson ismét a állapotkód okoz, ismét megjelenik ez a lehetőség.

## <a name="connections"></a>Kapcsolatok
![Kapcsolatok diagram](./media/cdn-real-time-stats/cdn-connections.png)

Ez a diagram azt jelzi, hogy hány kapcsolat jött létre a peremhálózati kiszolgálókon. Minden egyes kérelem egy eszköz, amely áthalad a kapcsolatot a CDN eredményez.

## <a name="next-steps"></a>További lépések
* Értesítést kap [valós idejű riasztások az Azure CDN-ben](cdn-real-time-alerts.md)
* Ásson mélyebbre [speciális HTTP-jelentések](cdn-advanced-http-reports.md)
* Elemezheti [használati minták](cdn-analyze-usage-patterns.md)

