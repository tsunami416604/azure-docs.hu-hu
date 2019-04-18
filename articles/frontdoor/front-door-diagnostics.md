---
title: Figyelési metrikák és naplók az Azure bejárati ajtajának Service |} A Microsoft Docs
description: Ez a cikk ismerteti a különböző metrikák és az Azure bejárati ajtajának szolgáltatás támogatja a naplók elérése
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
ms.openlocfilehash: 98aabf5330589bf80f1653bb2882c015a4bc133c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862114"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Figyelési metrikák és naplók Azure bejárati ajtajának Service-ben

Azure bejárati ajtajának Service használatával figyelemmel kísérheti az erőforrásokat a következőképpen:

- **Metrikák**. Az Application Gateway jelenleg rendelkezik hét metrikák megtekintéséhez a teljesítményszámlálókat.
- **Naplók**. Tevékenység és a diagnosztikai naplók lehetővé teszi a teljesítmény, a hozzáférési és egyéb adatok mentése vagy figyelési célból az erőforrásból felhasznált.

### <a name="metrics"></a>Mérőszámok

Metrikák egyik újdonsága az egyes Azure-erőforrásokhoz, amelyek lehetővé teszik, hogy a teljesítményszámlálók megtekintése a portálon. Rendelkezésre álló bejárati ajtajának metrikák a következők:

| Metrika | Metrika megjelenített neve | Unit (Egység) | Dimenziók | Leírás |
| --- | --- | --- | --- | --- |
| RequestCount | Kérelmek száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Bejárati ajtajának által kiszolgált ügyfélkérések száma.  |
| RequestSize | Kérés mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelek által az bejárati ajtajának kérések küldött bájtok száma. |
| ResponseSize | Válasz mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelek számára a bejárati ajtajának válaszokat, küldött bájtok száma. |
| TotalLatency | Teljes késés | Ezredmásodperc | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az idő alapján az ügyfél kérése bejárati ajtajának által fogadott, mindaddig, amíg az ügyfél arra vonatkozik, a bejárati ajtajának válasz utolsó bájtját számítja ki. |
| BackendRequestCount | Háttér-kérések száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>Háttérszolgáltatás | A bejárati ajtajának háttérkomponenseinek küldött kérelmek száma. |
| BackendRequestLatency | Háttérbeli késleltetése | Ezredmásodperc | Háttérszolgáltatás | Az az időpont alapján számítja ki, ha a kérés által küldött bejárati ajtó a háttérrendszerhez mindaddig, amíg a bejárati ajtajának kapott válasz utolsó bájtját a háttérrendszerből. |
| BackendHealthPercentage | Háttérkiszolgáló állapotadatainak százalékos aránya | Százalék | Háttérszolgáltatás</br>BackendPool | Sikeres állapotjelentés aránya mintavételek a bejárati ajtajának háttérkomponenseinek. |
| WebApplicationFirewallRequestCount | Web Application Firewall kérések száma | Darabszám | PolicyName</br>RuleName</br>Műveletek | Az alkalmazás réteg biztonsági bejárati ajtó által feldolgozott ügyfél-kérések száma. |

## <a name="activity-log"></a>Tevékenységnaplók

A Tevékenységnaplók a bejárati ajtajának szolgáltatásban végzett műveleteket ismertetik. Akkor is meghatározza, mit, ki és mikor minden írási műveletek (put, post és a delete) bejárati ajtajának szolgáltatásban végzett.

>[!NOTE]
>Tevékenységnaplók az olvasási (get) műveleteket nem tartalmazzák. Nem is, az Azure Portalon vagy az eredeti felügyeleti API használatával végrehajtott műveletek.

Access-Tevékenységnaplók a bejárati ajtajának szolgáltatásban vagy az Azure-erőforrások az Azure monitorban a naplókat. A tevékenységnaplók megtekintése:

1. Válassza ki a bejárati ajtajának példányt.
2. Válassza ki **tevékenységnapló**.

    ![Tevékenységnapló](./media/front-door-diagnostics/activity-log.png)

3. Válassza ki a szűrési hatókört, és válassza ki **alkalmaz**.

## <a name="diagnostic-logging"></a>Diagnosztikai naplók
Diagnosztikai naplók rengeteg információt kapcsolatos műveletek és hibák naplózása és a hibaelhárítás fontos tartalmaznak. Diagnosztikai naplók különböznek a tevékenységnaplóktól.

A Tevékenységnaplók az Azure-erőforrások kész műveletekkel kapcsolatos információkat. Diagnosztikai naplók adja meg az erőforrás által végrehajtott műveletekkel kapcsolatos információk. További információkért lásd: [diagnosztikai naplók az Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md).

![Diagnosztikai naplók](./media/front-door-diagnostics/diagnostic-log.png)

Diagnosztikai naplók konfigurálása a bejárati ajtajának szolgáltatáshoz:

1. Válassza ki az Azure API Management szolgáltatást.

2. Válasszon **diagnosztikai beállítások**.

3. Válassza ki **diagnosztika bekapcsolása**. Diagnosztikai naplókat és mérőszámokat tárfiókba archiválhatja, azok streamelésére az eseményközpontok felé, vagy küldhet nekik az Azure Monitor naplóira.

Bejárati ajtajának szolgáltatás jelenleg biztosít a diagnosztikai naplók (kötegelt óránként). Diagnosztikai naplók egyéni API-kérések adja meg a bejegyzései a következő mintát követik:

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

- [Bejárati ajtajának profil létrehozása](quickstart-create-front-door.md)
- [Hogyan működik a bejárati ajtó](front-door-routing-architecture.md)
