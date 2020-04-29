---
title: Metrikák és naplók figyelése az Azure bejárati ajtóban | Microsoft Docs
description: Ez a cikk az Azure-előtérben támogatott különböző mérőszámokat és hozzáférési naplókat ismerteti
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
ms.openlocfilehash: b935355cce36a6e26b168db286ab40248f8f0f68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471727"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Metrikák és naplók figyelése az Azure-beli bejárati ajtón

Az Azure bejárati ajtót használva a következő módokon figyelheti az erőforrásokat:

- **Metrikák**. Az Azure bejárati ajtajának jelenleg hét mérőszáma van a teljesítményszámlálók megtekintéséhez.
- **Naplók**. A tevékenység-és diagnosztikai naplók lehetővé teszik a teljesítmény, hozzáférés és egyéb adatok mentését, illetve az erőforrásokból való felhasználását figyelés céljából.

### <a name="metrics"></a>Mérőszámok

A metrikák bizonyos Azure-erőforrások, amelyek lehetővé teszik a teljesítményszámlálók megtekintését a portálon. A következő elérhetők a bejárati ajtó metrikái:

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Dimenziók | Leírás |
| --- | --- | --- | --- | --- |
| RequestCount | Kérelmek száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | A bejárati ajtó által kiszolgált ügyfél-kérelmek száma.  |
| RequestSize | Kérelem mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelektől a befelé irányuló kérelmekként küldött bájtok száma. |
| ResponseSize | Válasz mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | A bejárati ajtótól az ügyfeleknek küldött válaszként küldött bájtok száma. |
| TotalLatency | Teljes késés | Ezredmásodpercben | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyféltől érkező kérelemből kiszámított idő, amíg az ügyfél el nem fogadta az utolsó válasz bájtját a bejárati ajtótól. |
| BackendRequestCount | Háttérbeli kérelmek száma | Darabszám | HttpStatus</br>HttpStatusGroup</br>Háttérrendszer | A bejárati ajtóról a háttérbe küldött kérések száma. |
| BackendRequestLatency | Háttérbeli kérelmek késése | Ezredmásodpercben | Háttérrendszer | A kérelemnek a háttérbe való beérkezése után kiszámított idő, amíg be nem érkezett az utolsó válasz bájtja a háttérből. |
| BackendHealthPercentage | Háttér állapotának százalékos aránya | Százalék | Háttérrendszer</br>Háttérkészletek | A sikeres állapot-mintavételek százalékos aránya a bejárati ajtóról a háttérbe. |
| WebApplicationFirewallRequestCount | Webalkalmazási tűzfalra vonatkozó kérelmek száma | Darabszám | PolicyName</br>RuleName</br>Műveletek | Az alkalmazási rétegben a bejárati ajtó biztonságával feldolgozott ügyfélalkalmazások száma |

## <a name="activity-logs"></a><a name="activity-log"></a>Tevékenységnaplók

A Tevékenységnaplók a bejárati ajtókon végzett műveletekről nyújtanak információt. Azt is meghatározzák, hogy mi, ki és mikor történik a bejáratkor végrehajtott írási műveletek (Put, post vagy DELETE).

>[!NOTE]
>A Tevékenységnaplók nem tartalmaznak olvasási (Get) műveleteket. Emellett nem tartalmazzák a Azure Portal vagy az eredeti felügyeleti API használatával végrehajtott műveleteket.

A hozzáférési tevékenység naplóit a bejárati ajtón vagy az Azure-erőforrások összes naplóján Azure Monitor. A tevékenységnaplók megtekintése:

1. Válassza ki az előtérben lévő példányt.
2. Válassza a **műveletnapló**lehetőséget.

    ![Tevékenységnapló](./media/front-door-diagnostics/activity-log.png)

3. Válasszon egy szűrési hatókört, majd kattintson az **alkalmaz**gombra.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnosztikai naplók
A diagnosztikai naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A diagnosztikai naplók eltérnek a tevékenység naplóitól.

A Tevékenységnaplók betekintést nyújtanak az Azure-erőforrásokon végzett műveletekre. A diagnosztikai naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekre. További információ: [Azure monitor diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md).

![Diagnosztikai naplók](./media/front-door-diagnostics/diagnostic-log.png)

Diagnosztikai naplók konfigurálása a bejárati ajtóhoz:

1. Válassza ki az Azure-bejárati ajtót.

2. Válassza a **diagnosztikai beállítások**lehetőséget.

3. Kattintson **a diagnosztika bekapcsolása**elemre. Archiválja a diagnosztikai naplókat, valamint mérőszámokat egy Storage-fiókba, továbbíthatja őket egy Event hubhoz, vagy elküldheti azokat Azure Monitor naplókba.

A bejárati ajtó jelenleg biztosít diagnosztikai naplókat (óránként kötegelt feldolgozással). A diagnosztikai naplók egyedi API-kérelmeket biztosítanak minden egyes bejegyzéshez a következő sémával:

| Tulajdonság  | Leírás |
| ------------- | ------------- |
| BackendHostname | Ha a kérést egy háttérbe továbbították, ez a mező a háttér állomásnevét jelöli. Ez a mező üresen marad, ha a kérés átirányítása vagy továbbítása regionális gyorsítótárba történik (ha a gyorsítótárazás engedélyezve van az útválasztási szabályhoz). |
| CacheStatus | Gyorsítótárazási forgatókönyvek esetén ez a mező a legördülő menüben definiált gyorsítótár-találatok és-kihagyás |
| ClientIp (Ügyfél IP-címe) | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. Ha a kérelemben a fejléchez X-továbbítás történt, akkor az ügyfél IP-címe azonos. |
| ClientPort | Az ügyfél IP-portja, amely a kérést elvégezte. |
| HttpMethod | A kérelem által használt HTTP-metódus. |
| HttpStatusCode | A proxy által visszaadott HTTP-állapotkód. |
| HttpStatusDetails | Az eredményül kapott állapot a kérelemben. A karakterlánc értékének jelentése az állapot-hivatkozási táblában található. |
| HttpVersion | A kérelem vagy a kapcsolatok típusa. |
| POP | A kérelem által kirakodott szegély rövid neve. |
| RequestBytes | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét. |
| RequestUri | A fogadott kérelem URI-ja. |
| ResponseBytes | A háttér-kiszolgáló által válaszként küldött bájtok.  |
| RoutingRuleName | Annak az útválasztási szabálynak a neve, amelyhez a kérelem illeszkedik. |
| SecurityProtocol | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás. |
| SentToOriginShield | Logikai mező, amely azt jelöli, hogy az első környezetben történt-e gyorsítótár-kihagyás, és a rendszer elküldte a kérelmet a regionális gyorsítótárnak. Hagyja figyelmen kívül ezt a mezőt, ha az útválasztási szabály egy átirányítás, vagy ha nincs engedélyezve a gyorsítótárazás. |
| Eltelt idő | A művelet végrehajtásának időtartama (ezredmásodpercben). |
| TrackingReference | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |
| UserAgent | Az ügyfél által használt böngésző típusa. |

**Megjegyzés:** A különböző útválasztási konfigurációk és a forgalom viselkedése érdekében egyes mezők, például a backendHostname, a cacheStatus, a sentToOriginShield és a POP mező különböző értékekkel is reagálhat. Az alábbi táblázat ismerteti a különböző értékeket, ezek a mezők különböző forgatókönyvekhez szükségesek:

| Forgatókönyvek | Naplóbejegyzések száma | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Útválasztási szabály engedélyezve gyorsítótárazás nélkül | 1 | Edge POP-kód | A kérés továbbítására szolgáló háttér | False (Hamis) | CONFIG_NOCACHE |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár-találat a peremhálózati POP-on | 1 | Edge POP-kód | Üres | False (Hamis) | HIT |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár-kihagyás az Edge POP-ban, de a gyorsítótár a szülő gyorsítótár POP-ban | 2 | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. szülő gyorsítótár POP-állomásnév</br>2. üres | 1. igaz</br>2. hamis | 1. MISS</br>2. PARTIAL_HIT |
| Útválasztási szabály engedélyezve gyorsítótárazással. Gyorsítótár-kihagyás az Edge és a szülő gyorsítótár POP-ban | 2 | 1. Edge POP-kód</br>2. szülő gyorsítótár POP-kódja | 1. szülő gyorsítótár POP-állomásnév</br>2. a gyorsítótár feltöltését segítő háttérrendszer | 1. igaz</br>2. hamis | 1. MISS</br>2. MISS |


## <a name="next-steps"></a>További lépések

- [Bejárati ajtó profiljának létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)
