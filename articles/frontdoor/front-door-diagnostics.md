---
title: Metrikák és naplók figyelése az Azure Bejárati ajtajában| Microsoft dokumentumok
description: Ez a cikk ismerteti a különböző metrikák és hozzáférési naplók, amelyek az Azure Front Door támogatja
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471727"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Metrikák és naplók figyelése az Azure bejárati ajtajában

Az Azure Front Door használatával az alábbi módokon figyelheti az erőforrásokat:

- **Mérőszámok**. Az Azure Front Door jelenleg hét metrikával rendelkezik a teljesítményszámlálók megtekintéséhez.
- **Naplók**. A tevékenység- és diagnosztikai naplók lehetővé teszik a teljesítmény, a hozzáférés és más adatok mentését vagy felhasználását egy erőforrásból figyelési célokra.

### <a name="metrics"></a>Mérőszámok

Metrikák egy olyan funkció, bizonyos Azure-erőforrások, amelyek lehetővé teszik a teljesítményszámlálók megtekintéséhez a portálon. A bejárati ajtó mutatói a következők:

| Metrika | Metrikus megjelenítendő név | Unit (Egység) | Dimenziók | Leírás |
| --- | --- | --- | --- | --- |
| Kérésszáma | Kérelmek száma | Darabszám | httpállapota</br>httpstatuscsoport</br>Ügyfélterület</br>Ügyfélország | A Bejárati ajtó által kiszolgált ügyfélkérelmek száma.  |
| Kérésméretet | A kérelem mérete | Bájt | httpállapota</br>httpstatuscsoport</br>Ügyfélterület</br>Ügyfélország | Az ügyfelektől a Bejárati ajtónak kérésként küldött bájtok száma. |
| Válaszméret | Válasz mérete | Bájt | httpállapota</br>httpstatuscsoport</br>Ügyfélterület</br>Ügyfélország | A bejárati ajtóból az ügyfeleknek válaszként küldött bájtok száma. |
| Összesen Késedelmiség | Teljes késés | Ezredmásodperc | httpállapota</br>httpstatuscsoport</br>Ügyfélterület</br>Ügyfélország | A Bejárati ajtó által fogadott ügyfélkérésből számított idő, amíg az ügyfél nem nyugtázta az utolsó válaszbájtot a Bejárati ajtóból. |
| Háttérvégpontkérése | Háttér-kérelem száma | Darabszám | httpállapota</br>httpstatuscsoport</br>Háttér | A bejárati ajtóból a háttérrendszereknek küldött kérelmek száma. |
| HáttérendKérelem Latency | Háttér-kérelem késése | Ezredmásodperc | Háttér | A bejárati ajtó által a háttérrendszernek küldött kérelem számításának időpontja, amíg a Bejárati ajtó meg nem kapta az utolsó válaszbájtot a háttérrendszertől. |
| BackendHealthPercentage | Háttér-állapot százalékos aránya | Százalék | Háttér</br>Háttérkészlet | A sikeres állapotpróbák százalékos aránya a bejárati ajtótól a háttérrendszerekig. |
| WebApplicationFirewallRequestCount | Webalkalmazás tűzfalának kérelmek száma | Darabszám | PolicyName</br>Szabályneve</br>Műveletek | A Bejárati ajtó alkalmazásréteg-biztonsága által feldolgozott ügyfélkérelmek száma. |

## <a name="activity-logs"></a><a name="activity-log"></a>Tevékenységnaplók

A tevékenységnaplók a Bejárati ajtón végzett műveletekről nyújtanak információt. Azt is meghatározzák, hogy mit, ki és mikor bármilyen írási műveletek (fel, posta, vagy törölje) hozott bejárati ajtó.

>[!NOTE]
>A tevékenységnaplók nem tartalmaznak olvasási (beolvasási) műveleteket. Emellett nem tartalmazzák az Azure Portal vagy az eredeti Felügyeleti API használatával végrehajtott műveleteket.

A tevékenységnaplók elérése a bejárati ajtajában vagy az Azure-erőforrások összes naplójában az Azure Monitorban. A tevékenységnaplók megtekintése:

1. Válassza ki a bejárati ajtó példányát.
2. Válassza **a Tevékenységnapló lehetőséget.**

    ![Tevékenységnapló](./media/front-door-diagnostics/activity-log.png)

3. Válasszon egy szűrési hatókört, majd válassza **az Alkalmaz**lehetőséget.

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnosztikai naplók
A diagnosztikai naplók gazdag információkat nyújtanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A diagnosztikai naplók eltérnek a tevékenységnaplóktól.

A tevékenységnaplók betekintést nyújtanak az Azure-erőforrásokon végzett műveletekbe. A diagnosztikai naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekbe. További információ: [Azure Monitor diagnosztikai naplók.](../azure-monitor/platform/platform-logs-overview.md)

![Diagnosztikai naplók](./media/front-door-diagnostics/diagnostic-log.png)

Diagnosztikai naplók konfigurálása a bejárati ajtóhoz:

1. Válassza ki az Azure bejárati ajtaját.

2. Válassza **a Diagnosztikai beállítások lehetőséget**.

3. Válassza **a Diagnosztika bekapcsolása**lehetőséget. Archiválja a diagnosztikai naplókat és a metrikákat egy tárfiókba, továbbítsa őket egy eseményközpontba, vagy küldje el őket az Azure Monitor naplóiba.

Bejárati ajtó jelenleg diagnosztikai naplók (óránként kötegelve). A diagnosztikai naplók minden egyes bejegyzéshez egyedi API-kérelmeket biztosítanak, amelyek a következő sémával rendelkeznek:

| Tulajdonság  | Leírás |
| ------------- | ------------- |
| BackendHostname | Ha a kérést háttérkiszolgálóra továbbították, ez a mező a háttérkiszolgáló állomásnevét jelöli. Ez a mező üres lesz, ha a kérelmet átirányították vagy egy regionális gyorsítótárba irányították (ha a gyorsítótárazás engedélyezve van a művelettervi szabályhoz). |
| Gyorsítótár állapota | Gyorsítótárazási esetekesetén ez a mező határozza meg a POP gyorsítótár-találatot/tévesztést |
| ClientIp (Ügyfél IP-címe) | A kérést küldő ügyfél IP-címe. Ha a kérelemben x-forwarded-for fejléc volt, akkor az ügyfél IP-címe ugyanabból lesz kiválasztva. |
| Ügyfélport | A kérést küldő ügyfél IP-portja. |
| HttpMódszer | A kérelem által használt HTTP-módszer. |
| httpstatuskód | A proxytól visszaadott HTTP-állapotkód. |
| httpStatusDetails | A kérelem eredményül kapott állapota. Ennek a karakterlánc-értéknek a jelentése egy Állapothivatkozás-táblában található. |
| HttpVerzió | A kérelem vagy kapcsolat típusa. |
| POP | Annak az élnek a rövid neve, ahol a kérelem landolt. |
| Kérésbájt | A HTTP-kérelem üzenet mérete bájtban, beleértve a kérelem fejlécek és a kérelem törzse. |
| RequestUri (Kérés) | A fogadott kérelem URI-ja. |
| Válaszbájt | A háttérkiszolgáló által válaszként küldött bájtok.  |
| Útválasztási szabály neve | A kérelem által egyeztetett útválasztási szabály neve. |
| SecurityProtocol (Biztonsági protokoll) | A kérés által használt TLS/SSL protokollverzió, vagy null, ha nincs titkosítás. |
| SentToOriginPajzs | Logikai mező, amely azt jelöli, hogy az első környezetben hiányzott-e a gyorsítótár, és a kérés a regionális gyorsítótárba lett-e elküldve. Hagyja figyelmen kívül ezt a mezőt, ha a művelettervszabály átirányítás, vagy ha nincs engedélyezve a gyorsítótárazás. |
| Időtaken | A művelet időtartama ezredmásodpercben. |
| Követési hivatkozás | Az egyedi hivatkozási karakterlánc, amely azonosítja a Bejárati ajtó által kiszolgált kérelmet, amelyet X-Azure-Ref fejlécként is küldött az ügyfélnek. Egy adott kérelem hozzáférési naplóiban a részletek kereséséhez szükséges. |
| Useragent | Az ügyfél által használt böngészőtípusa. |

**Megjegyzés:** A különböző útválasztási konfigurációk és a forgalmi viselkedések, néhány mező, mint a backendHostname, cacheStatus, sentToOriginShield, és a POP mező válaszolhat különböző értékeket. Az alábbi táblázat ismerteti a különböző értékeket, ezeket a mezőket különböző forgatókönyvekhez:

| Forgatókönyvek | Naplóbejegyzések száma | POP | BackendHostname | SentToOriginPajzs | Gyorsítótár állapota |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| Útválasztási szabály gyorsítótárazás nélkül | 1 | Élű POP-kód | Háttértartalék, ahol a kérelem továbbításra került | False (Hamis) | CONFIG_NOCACHE |
| Útválasztási szabály, amelyen engedélyezve van a gyorsítótárazás. Gyorsítótár-találat a POP szélén | 1 | Élű POP-kód | Üres | False (Hamis) | Megüt |
| Útválasztási szabály, amelyen engedélyezve van a gyorsítótárazás. Gyorsítótár-tévesztés a pop-pszélnél, de a gyorsítótár lecsapott a szülőgyorsítótár POP-ra | 2 | 1. Edge POP-kód</br>2. Szülő gyorsítótár POP-kódja | 1. Szülőgyorsítótár POP-állomásneve</br>2. Üres | 1. Igaz</br>2. Hamis | 1. HIÁNYZIK</br>2. PARTIAL_HIT |
| Útválasztási szabály, amelyen engedélyezve van a gyorsítótárazás. Gyorsítótár-tévesztés a szélen és a szülő gyorsítótárpop-ban is | 2 | 1. Edge POP-kód</br>2. Szülő gyorsítótár POP-kódja | 1. Szülőgyorsítótár POP-állomásneve</br>2. Háttérrendszer, amely segít a gyorsítótár feltöltésén | 1. Igaz</br>2. Hamis | 1. HIÁNYZIK</br>2. KISASSZONY |


## <a name="next-steps"></a>További lépések

- [Bejárati ajtó profil létrehozása](quickstart-create-front-door.md)
- [A bejárati ajtó működése](front-door-routing-architecture.md)
