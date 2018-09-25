---
title: Az Azure bejárati ajtajának Service – metrikák és naplózás |} A Microsoft Docs
description: Ez a cikk segít megérteni a különböző metrikák és az Azure bejárati ajtajának szolgáltatás támogatja a naplók elérése
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997998"
---
# <a name="monitoring-metrics-for-front-door"></a>Figyelési metrikák bejárati ajtajának

Azure bejárati ajtajának szolgáltatást használja, a fő mérőszámok segítségével ellenőrizze a bejárati ajtajának konfigurációját, használati, állapotát, és a teljesítmény vagy a bejárati ajtajának együtt lehet figyelni.

## <a name="metrics"></a>Mérőszámok

Metrikák egyik újdonsága az egyes Azure-erőforrásokhoz, ahol megtekintheti teljesítményszámlálók a portálon. Bejárati ajtajának a következő metrikák érhetők el:

| Metrika | Metrika megjelenített neve | Unit (Egység) | Dimenziók | Leírás |
| --- | --- | --- | --- | --- |
| RequestCount | A kérelmek száma | Darabszám | válaszállapotok összege http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Bejárati ajtajának által kiszolgált ügyfélkérések száma.  |
| RequestSize | Kérés mérete | Bájt | válaszállapotok összege http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelek által az bejárati ajtajának kérések küldött bájtok száma. |
| ResponseSize | Válasz mérete | Bájt | válaszállapotok összege http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelek számára a bejárati ajtajának válaszokat, küldött bájtok száma. |
| TotalLatency | Teljes késés | Ezredmásodperc | válaszállapotok összege http</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az az időpont alapján számítja ki, amikor az ügyfél kérést kapott bejárati ajtajának mindaddig, amíg az ügyfél arra vonatkozik, a bejárati ajtajának válasz utolsó bájtját. |
| BackendRequestCount | Háttér-kérések száma | Darabszám | válaszállapotok összege http</br>HttpStatusGroup</br>Háttérszolgáltatás | A bejárati ajtajának háttérkomponenseinek küldött kérelmek száma. |
| BackendRequestLatency | Háttérbeli késleltetése | Ezredmásodperc | Háttérszolgáltatás | Az az időpont alapján számítja ki, ha a kérés által küldött bejárati ajtó a háttérrendszerhez mindaddig, amíg a bejárati ajtajának kapott válasz utolsó bájtját a háttérrendszerből. |
| BackendHealthPercentage | Háttérkiszolgáló állapotadatainak százalékos aránya | Százalék | Háttérszolgáltatás</br>Háttérkészletek | Sikeres állapotjelentés aránya mintavételek a bejárati ajtajának háttérkomponenseinek. |
| WebApplicationFirewallRequestCount | Web Application Firewall kérések száma | Darabszám | PolicyName</br>RuleName</br>Műveletek | Az alkalmazás réteg biztonsági bejárati ajtó által feldolgozott ügyfél-kérések száma. |


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
- Ismerje meg, [bejárati ajtajának működése](front-door-routing-architecture.md).
