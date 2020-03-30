---
title: Diagnosztikai naplók az Azure SignalR szolgáltatáshoz
description: Ismerje meg, hogyan állíthat be diagnosztikai naplókat az Azure SignalR-szolgáltatáshoz, és hogyan használhatja azt önhibaelhárításhoz.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536734"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Diagnosztikai naplók az Azure SignalR szolgáltatáshoz

Ez az oktatóanyag ismerteti, hogy mik az Azure SignalR-szolgáltatás diagnosztikai naplói, és hogyan állíthatja be a diagnosztikai naplókat, és hogyan háríthatja el a hibaelhárítást a diagnosztikai naplókkal.

## <a name="prerequisites"></a>Előfeltételek
A diagnosztikai naplók engedélyezéséhez valahol a naplóadatok tárolásához kell. Ez az oktatóanyag az Azure Storage és a Log Analytics szolgáltatást használja.

* [Azure storage](../azure-monitor/platform/resource-logs-collect-storage.md) – megőrzi a házirend-naplózás, a statikus elemzés vagy a biztonsági mentés diagnosztikai naplóit.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) – Egy rugalmas naplókeresési és -elemzési eszköz, amely lehetővé teszi az Azure-erőforrások által létrehozott nyers naplók elemzését.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Diagnosztikai naplók beállítása egy Azure SignalR-szolgáltatáshoz

Megtekintheti az Azure SignalR Szolgáltatás diagnosztikai naplóit. Ezek a naplók gazdagabb képet nyújtanak az Azure SignalR Service-példányhoz való kapcsolódásról. A diagnosztikai naplók részletes információkat nyújtanak minden kapcsolatról. Például a kapcsolat alapvető információi (felhasználói azonosító, kapcsolatazonosító és átviteli típus és így tovább) és eseményinformációk (csatlakozás, kapcsolat bontása és megszakítása, és így tovább). A diagnosztikai naplók a probléma azonosítására, a kapcsolatnyomon követésére és elemzésére használhatók.

### <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)a **Figyelés**csoportban kattintson a **Diagnosztikai beállítások**elemre.

    ![Ablaktábla-navigáció a diagnosztikai beállításokhoz](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Ezután kattintson **a Diagnosztikai beállítás hozzáadása gombra.**

    ![Diagnosztikai naplók hozzáadása](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Állítsa be a kívánt archív célt. Jelenleg támogatjuk **az Archiválást egy tárfiókba** és **a Küldés a Log Analytics szolgáltatásba.**

1. Jelölje ki az archiválni kívánt naplókat.

    ![Diagnosztikai beállítások ablaktábla](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Mentse az új diagnosztikai beállításokat.

Az új beállítások körülbelül 10 perc múlva lépnek érvénybe. Ezt követően a naplók megjelennek a konfigurált archiválási célban, a **Diagnosztikai naplók** ablaktáblában.

A diagnosztika konfigurálásáról az [Azure diagnosztikai naplóinak áttekintése](../azure-monitor/platform/platform-logs-overview.md)című témakörben olvashat bővebben.

### <a name="diagnostic-logs-categories"></a>Diagnosztikai naplók kategóriái

Az Azure SignalR szolgáltatás egy kategóriában rögzíti a diagnosztikai naplókat:

* **Minden napló:** Nyomon követheti az Azure SignalR szolgáltatáshoz csatlakozó kapcsolatokat. A naplók információt nyújtanak a csatlakozás/leválasztás, hitelesítés és szabályozás. További információt a következő szakaszban talál.

### <a name="archive-to-a-storage-account"></a>Archiválás tárfiókba

A naplók a Diagnosztikai naplók ablaktáblában konfigurált tárfiókban **tárolódnak.** A rendszer `insights-logs-alllogs` automatikusan létrehozza a megnevezett tárolót a diagnosztikai naplók tárolására. A tárolóban a naplók a fájlban `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`tárolódnak. Alapvetően, az út `resource ID` kombinálja és `Date Time`. A naplófájlokat `hour`a részre bontja. Ezért a perc `m=00`mindig .

Minden napló JavaScript Object Notation (JSON) formátumban van tárolva. Minden bejegyzéshez olyan karakterláncmezők vannak, amelyek a következő szakaszokban leírt formátumot használják.

Az archív napló JSON-karakterláncai az alábbi táblázatokban felsorolt elemeket tartalmaznak:

**Formátum**

Név | Leírás
------- | -------
time | Esemény időnek naplózása
szint | Eseményszint naplózása
resourceId | Az Azure SignalR-szolgáltatás erőforrásazonosítója
location | Az Azure SignalR szolgáltatás helye
category | A naplóesemény kategóriája
operationName | Az esemény műveletének neve
callerIpAddress | A kiszolgáló/ügyfél IP-címe
properties | A naplóeseményhez kapcsolódó részletes tulajdonságok. További részletekért tekintse meg az alábbi tulajdonságok táblázatot.

**Tulajdonságok tábla**

Név | Leírás
------- | -------
type | A naplóesemény típusa. Jelenleg az Azure SignalR szolgáltatással való kapcsolattal kapcsolatos információkat biztosítunk. Csak `ConnectivityLogs` típus érhető el
Gyűjtemény | A naplóesemény gyűjteménye. Az engedélyezett `Connection`értékek `Authorization` a következők: , és`Throttling`
connectionId | A kapcsolat azonosítása
transportType (transportType) | A kapcsolat átviteli típusa. Az engedélyezett `Websockets` \| `ServerSentEvents` \| értékek a következők:`LongPolling`
connectionType típusú | A kapcsolat típusa. Az engedélyezett `Server` \| `Client`értékek a következők: . `Server`: kapcsolat a kiszolgálóoldalról; `Client`: kapcsolat az ügyféloldalról
userId | A felhasználó identitása
message | A naplóesemény részletes üzenete

A következő kód egy példa egy archív napló JSON-karakterláncra:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Archiválási naplók sémája a Log Analytics szolgáltatáshoz

A diagnosztikai naplók megtekintéséhez kövesse az alábbi lépéseket:

1. Kattintson `Logs` a célnapló-elemzésre.

    ![Log Analytics menüpont](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Adja `SignalRServiceDiagnosticLogs` meg és válassza ki a diagnosztikai naplók lekérdezéséhez megadott időtartományt. A speciális lekérdezések ről a [Log Analytics az Azure Monitorban – Első lépések az Azure Monitorban](../azure-monitor/log-query/get-started-portal.md)

    ![Lekérdezési napló a Log Analytics szolgáltatásban](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Az archív naplóoszlopok az alábbi táblázatban felsorolt elemeket tartalmazzák:

Név | Leírás
------- | ------- 
TimeGenerated | Esemény időnek naplózása
Gyűjtemény | A naplóesemény gyűjteménye. Az engedélyezett `Connection`értékek `Authorization` a következők: , és`Throttling`
OperationName | Az esemény műveletének neve
Hely | Az Azure SignalR szolgáltatás helye
Szint | Eseményszint naplózása
CallerIpAddress cím | A kiszolgáló/ügyfél IP-címe
Üzenet | A naplóesemény részletes üzenete
UserId (Felhasználóazonosító) | A felhasználó identitása
ConnectionId azonosító | A kapcsolat azonosítása
ConnectionType típusú | A kapcsolat típusa. Az engedélyezett `Server` \| `Client`értékek a következők: . `Server`: kapcsolat a kiszolgálóoldalról; `Client`: kapcsolat az ügyféloldalról
TransportType (Szállítási típus) | A kapcsolat átviteli típusa. Az engedélyezett `Websockets` \| `ServerSentEvents` \| értékek a következők:`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Hibaelhárítás diagnosztikai naplókkal

Az Azure SignalR-szolgáltatás hibaelhárításához engedélyezheti a kiszolgáló/ügyfél oldali naplók at a hibák rögzítéséhez. Jelenleg az Azure SignalR service diagnosztikai naplókat tesz elérhetővé, a szolgáltatásoldalon is engedélyezheti a naplókat.

Ha nem várt növekvő vagy eldobási helyzettel találkozik a kapcsolattal, a diagnosztikai naplók segítségével elháríthatja a hibákat.

A tipikus problémák gyakran a kapcsolatok váratlan mennyiségi változásaival, a kapcsolatok a kapcsolati korlátok elérésével és az engedélyezési hibával kapcsolatosak. Tekintse meg a hibaelhárításról szóló következő szakaszokat.

#### <a name="unexpected-connection-number-changes"></a>Nem várt kapcsolatszám-módosítások

##### <a name="unexpected-connection-dropping"></a>Váratlan kapcsolat-visszadobás

Ha váratlan kapcsolatcsökkenést tapasztal, először engedélyezze a naplókat a kiszolgáló, a kiszolgáló és az ügyféloldalon.

Ha a kapcsolat bontja a kapcsolatot, a diagnosztikai naplók `ConnectionAborted` rögzítik ezt a leválasztó eseményt, megjelenik, vagy `ConnectionEnded` a alkalmazásban jelenik `operationName`meg.

A `ConnectionAborted` különbség, `ConnectionEnded` és `ConnectionEnded` ez a várható leválasztás, amely által kiváltott ügyfél vagy kiszolgáló oldalon. Míg `ConnectionAborted` a általában egy váratlan kapcsolat eldobása esemény, `message`és megszakítja okát fogja biztosítani a .

A megszakítások okait az alábbi táblázat sorolja fel:

Ok | Leírás
------- | ------- 
A kapcsolatok száma eléri a korlátot | A kapcsolatok száma eléri az aktuális árszint korlátját. Fontolja meg a felskálázási szolgáltatás egységét
Az alkalmazáskiszolgáló bezárta a kapcsolatot | App szerver elindítja az abortuszt. Úgy tekinthető, mint a várható abortusz
Csatlakozási ping időtúllépés | Általában ez okozza a hálózati probléma. Fontolja meg az alkalmazáskiszolgáló elérhetőségének ellenőrzését az internetről
Szolgáltatás újratöltése, újracsatlakozás | Az Azure SignalR szolgáltatás újratöltődik. Az Azure SignalR támogatja az automatikus újracsatlakozást, megvárhatja, amíg újra csatlakozik, vagy manuálisan csatlakozik az Azure SignalR szolgáltatáshoz
Belső kiszolgálóátmeneti hiba | Átmeneti hiba történik az Azure SignalR szolgáltatásban, automatikusan helyre kell állni
A kiszolgálókapcsolat megszakadt | A kiszolgálói kapcsolat ismeretlen hibával csökken, először fontolja meg az önhibaelhárítást a szerviz/kiszolgáló/ügyféloldali naplóval. Próbálja meg kizárni az alapvető problémákat (pl. hálózati probléma, alkalmazáskiszolgálói probléma és így tovább). Ha a probléma nem oldódott meg, további segítségért forduljon hozzánk. További információt a [Súgó beszerezése](#get-help) című témakörben talál. 

##### <a name="unexpected-connection-growing"></a>Váratlan kapcsolat növekszik

A váratlan kapcsolatnövekedés selejtezésével kapcsolatos hibaelhárításhoz az első dolog, amit meg kell tennie, hogy kiszűri az extra kapcsolatokat. A tesztügyfél-kapcsolathoz egyedi tesztfelhasználói azonosítót adhat hozzá. Ezután ellenőrizze, hogy a diagnosztikai naplók, ha azt látja, több ügyfélkapcsolatok azonos teszt felhasználói azonosító vagy IP, akkor valószínű, hogy az ügyféloldali létrehozása és létrehozása több kapcsolatot, mint a várakozás. Ellenőrizze az ügyféloldalát.

#### <a name="authorization-failure"></a>Engedélyezési hiba

Ha 401 jogosulatlan visszaküldött ügyfélkérelmek, ellenőrizze a diagnosztikai naplók. Ha találkozik, `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`az azt jelenti, hogy a hozzáférési jogkivonat összes közönsége érvénytelen. Próbálja meg használni a naplóban javasolt érvényes közönségeket.


#### <a name="throttling"></a>Throttling

Ha úgy találja, hogy nem tud SignalR-ügyfélkapcsolatokat létesíteni az Azure SignalR Szolgáltatással, ellenőrizze a diagnosztikai naplókat. Ha a `Connection count reaches limit` diagnosztikai naplóban találkozik, túl sok kapcsolatot hoz létre a SignalR szolgáltatással, amely eléri a kapcsolatszám korlátját. Fontolja meg a SignalR szolgáltatás bővítését. Ha a `Message count reaches limit` diagnosztikai naplóban találkozik, az azt jelenti, hogy ingyenes szintet használ, és felhasználja az üzenetek kvótáját. Ha további üzeneteket szeretne küldeni, fontolja meg a SignalR szolgáltatás standard szintre való módosítását további üzenetek küldéséhez. További információ: [Azure SignalR service pricing](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Segítségkérés

Először azt javasoljuk, hogy a hibaelhárítást először egyedül tegye. A legtöbb problémát az alkalmazáskiszolgálóval vagy a hálózati problémákkal kapcsolatos problémák okozzák. Kövesse [a hibaelhárítási útmutatót a diagnosztikai naplóval](#troubleshooting-with-diagnostic-logs) és az [alapvető hibaelhárítási útmutatóval](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) a kiváltó ok megkereséséhez.
Ha a probléma továbbra sem oldható meg, fontolja meg egy probléma megnyitását a GitHubon, vagy hozzon létre jegyet az Azure Portalon.
Adja meg:
1. A probléma bekövetkezésekor körülbelül 30 perces időtartomány
2. Az Azure SignalR szolgáltatás erőforrás-azonosítója
3. A probléma részletei a lehető legpontosabbak: Például az appserver nem küld üzeneteket, az ügyfélkapcsolat megszakad, és így tovább
4. A kiszolgáló/ügyfél oldalról gyűjtött naplók és egyéb, hasznos anyagok
5. [Nem kötelező] Repro kód

> [!NOTE]
> Ha a Problémát a GitHubon nyitja meg, a bizalmas adatokat (például erőforrás-azonosítót, kiszolgáló-/ügyfélnaplókat) csak a Microsoft-szervezet tagjainak küldje el.