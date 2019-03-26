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
ms.openlocfilehash: 3097f4a1716718df5d67769e234562a234623cfe
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407028"
---
# <a name="monitoring-metrics-and-logs-for-front-door"></a>Figyelési metrikák és naplók számára a bejárati ajtó

Azure bejárati ajtajának Service használatával figyelemmel kísérheti az erőforrásokat a következőképpen:

* [Metrikák](#metrics): Az Application Gateway jelenleg rendelkezik hét metrikák megtekintéséhez a teljesítményszámlálókat.
* [Naplók](#diagnostic-logging): A teljesítmény, a hozzáférési és egyéb adatok mentése vagy figyelési célból az erőforrásból felhasznált naplóihoz.

## <a name="metrics"></a>Mérőszámok

Metrikák egyik újdonsága az egyes Azure-erőforrásokhoz, ahol megtekintheti teljesítményszámlálók a portálon. Bejárati ajtajának a következő metrikák érhetők el:

| Metrika | Metrika megjelenített neve | Unit (Egység) | Dimenziók | Leírás |
| --- | --- | --- | --- | --- |
| RequestCount | Kérelmek száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Bejárati ajtajának által kiszolgált ügyfélkérések száma.  |
| RequestSize | Kérés mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelek által az bejárati ajtajának kérések küldött bájtok száma. |
| ResponseSize | Válasz mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelek számára a bejárati ajtajának válaszokat, küldött bájtok száma. |
| TotalLatency | Teljes késés | Ezredmásodperc | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az az időpont alapján számítja ki, amikor az ügyfél kérést kapott bejárati ajtajának mindaddig, amíg az ügyfél arra vonatkozik, a bejárati ajtajának válasz utolsó bájtját. |
| BackendRequestCount | Háttér-kérések száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>Háttérszolgáltatás | A bejárati ajtajának háttérkomponenseinek küldött kérelmek száma. |
| BackendRequestLatency | Háttérbeli késleltetése | Ezredmásodperc | Háttérszolgáltatás | Az az időpont alapján számítja ki, ha a kérés által küldött bejárati ajtó a háttérrendszerhez mindaddig, amíg a bejárati ajtajának kapott válasz utolsó bájtját a háttérrendszerből. |
| BackendHealthPercentage | Háttérkiszolgáló állapotadatainak százalékos aránya | Százalék | Háttérszolgáltatás</br>BackendPool | Sikeres állapotjelentés aránya mintavételek a bejárati ajtajának háttérkomponenseinek. |
| WebApplicationFirewallRequestCount | Web Application Firewall kérések száma | Darabszám | PolicyName</br>RuleName</br>Műveletek | Az alkalmazás réteg biztonsági bejárati ajtó által feldolgozott ügyfél-kérések száma. |

## <a name="activity-log"></a>Tevékenységnaplók

A Tevékenységnaplók betekintést a bejárati ajtajának tartományvezérlőn végrehajtott műveletek. Tevékenységnaplók segítségével megadhatja, hogy a "mit, ki és mikor" írási műveletek (PUT, POST, DELETE) tett a bejárati ajtó a.

> [!NOTE]
> A tevékenységnaplók az olvasási (GET) műveleteket, illetve az Azure Portalon vagy az eredeti felügyeleti API-k használatával végzett műveleteket nem tartalmazzák.

A bejárati ajtó a vizsgálati naplók eléréséhez, vagy az Azure-erőforrások az Azure monitorban naplók elérése. 

A tevékenységnaplók megtekintése:

1. Válassza ki a bejárati ajtajának példányt.
2. Kattintson a **Tevékenységnapló** gombra.

    ![tevékenységnapló](./media/front-door-diagnostics/activity-log.png)

3. Válassza ki a kívánt szűrési hatókört, és kattintson az **Alkalmaz** elemre.

## <a name="diagnostic-logging"></a>Diagnosztikai naplók
A diagnosztikai naplók rengeteg információt tartalmaznak a műveletekkel és a hibákkal kapcsolatban, amelyek felülvizsgálati és hibaelhárítási célból egyaránt fontosak lehetnek. A diagnosztikai naplók különböznek a tevékenységnaplóktól. A tevékenységnaplók az Azure-erőforrásokon végrehajtott műveletekkel kapcsolatos információkat tartalmaznak. A diagnosztikai naplókban az erőforrás által végrehajtott műveletekkel kapcsolatos információk találhatók meg. Tudjon meg többet [diagnosztikai naplók az Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md). 

Diagnosztikai naplók számára a bejárati ajtajának konfigurálása:

1. Válassza ki az APIM-szolgáltatáspéldányt.
2. Kattintson a **Diagnosztikai beállítások** elemre.

    ![diagnosztikai naplók](./media/front-door-diagnostics/diagnostic-log.png)

3. Kattintson a **Diagnosztika bekapcsolása** elemre. Diagnosztikai naplókat és mérőszámokat tárfiókba archiválhatja, azok streamelésére az Eseményközpontok felé, vagy küldhet nekik az Azure Monitor naplóira. 

Azure bejárati ajtajának Service jelenleg lehetővé teszi a diagnosztikai naplók (óránként kötegelt) különálló API-kérést bejegyzései a következő mintát követik:

| Tulajdonság  | Leírás |
| ------------- | ------------- |
| ClientIp | A kérést leadó ügyfél IP-címét. |
| ClientPort | A kérést leadó ügyfél IP-portot. |
| HttpMethod | A kérelem által használt HTTP-metódus. |
| HttpStatusCode | A proxy a HTTP-állapotkódot adott vissza. |
| HttpStatusDetails | Eredményül kapott állapotának a kérésre. A karakterlánc értéke szerinti egy állapot referenciatábla tekinthet meg. |
| HttpVersion | A kérelem vagy a kapcsolat típusát. |
| RequestBytes | A HTTP kérésüzenetben bájt, beleértve a kérelemfejlécek és a kérelem törzsében mérete. |
| requestUri | A fogadott kérelem URI azonosítója. |
| ResponseBytes | A háttérkiszolgáló, a válaszban elküldött bájtok száma.  |
| RoutingRuleName | Az útválasztási szabályt, amely megfelel a kérelem neve. |
| SecurityProtocol | A kérelem vagy null, ha titkosítás nélkül használja a TLS/SSL protokoll verziója. |
| TimeTaken | Az, hogy mennyi ideig tartott a művelet (MS). |
| UserAgent | Az ügyfél által használt böngésző típusa |
| TrackingReference | Az egyedi hivatkozási karakterlánc, amely azonosítja a bejárati ajtó, által kiszolgált kérés is X-Azure-Ref fejléccé az ügyfélnek küldött. A szükséges részleteket egy adott kérés esetében a hozzáférési naplók keresése. |

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
