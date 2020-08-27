---
title: Az Azure Signaler szolgáltatással kapcsolatos gyakori kérdések
description: Gyorsan hozzáférhet az Azure Signaler szolgáltatással kapcsolatos gyakori kérdésekhez, a hibaelhárításról és a tipikus használati forgatókönyvekről.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 9aa510a055cb76b30508cb98a25cd9c919eb117d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928309"
---
# <a name="azure-signalr-service-faq"></a>Azure Signaler szolgáltatás – gyakori kérdések

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Az Azure Signaler szolgáltatás készen áll éles használatra?

Igen.
Az általános elérhetőségről az [Azure signaler szolgáltatás általánosan elérhetővé](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)tételét ismertető témakörben talál további információt. 

A [ASP.net Core jelző](https://docs.microsoft.com/aspnet/core/signalr/introduction) teljes mértékben támogatott.

A ASP.NET-jelző támogatása továbbra is a *nyilvános előzetes*verzióban érhető el. Íme egy [példa egy kódra](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Az ügyfél-csatlakozás a "nincs kiszolgáló érhető el" hibaüzenettel zárul. Mit jelent?

Ez a hiba csak akkor fordul elő, ha az ügyfelek üzeneteket küldenek a Signaler szolgáltatásnak.

Ha nem rendelkezik alkalmazás-kiszolgálóval, és csak a Signaler Service REST API használja, ez a viselkedés a **tervezés szerint**történik.
A kiszolgáló nélküli architektúrában az ügyfélkapcsolatok **figyelés** módban vannak, és nem küldenek üzeneteket a jelző szolgáltatásnak.
További információk: [REST API](./signalr-quickstart-rest-api.md).

Ha az alkalmazás-kiszolgálókkal rendelkezik, ez a hibaüzenet azt jelenti, hogy egyetlen alkalmazáskiszolgáló sincs csatlakoztatva a Signal Service-példányhoz.

A lehetséges okok a következők:
- Nem kapcsolódik alkalmazáskiszolgáló a Signaler szolgáltatáshoz. A lehetséges csatlakoztatási hibákért keresse fel az alkalmazáskiszolgáló naplóit. Ez az eset ritka a magas rendelkezésre állási beállításban egynél több alkalmazás-kiszolgálóval.
- A Signaler szolgáltatás példányaihoz kapcsolódási problémák léptek fel. Ez a probléma átmeneti, és a rendszer automatikusan helyreállítja.
Ha több mint egy órát tart fenn, nyisson meg [egy problémát a githubon](https://github.com/Azure/azure-signalr/issues/new) , vagy [hozzon létre egy támogatási kérést az Azure-ban](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Ha több alkalmazás-kiszolgáló is van, az összes kiszolgálónak vagy csak az egyiknek küldi az üzeneteket?

Ez egy-az-egyhez típusú hozzárendelés az ügyfél és az alkalmazáskiszolgáló között. Az egyik ügyfélről érkező üzeneteket a rendszer mindig ugyanarra az alkalmazási kiszolgálóra továbbítja.

Az ügyfél és az alkalmazáskiszolgáló közötti leképezés csak akkor marad fenn, ha az ügyfél vagy az alkalmazáskiszolgáló bontja a kapcsolatot.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Ha az egyik alkalmazás-kiszolgálóm nem érhető el, Hogyan találhatom meg és kaphatok értesítést?

A signaler szolgáltatás figyeli a szívveréseket az alkalmazás-kiszolgálókról.
Ha a szívverések nem érkeznek meg egy megadott ideig, az alkalmazáskiszolgáló offline állapotba kerül. A rendszer leválasztja az ehhez az alkalmazáskiszolgáló számára leképezett összes ügyfélkapcsolatot.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Miért van az egyéni `IUserIdProvider` dobási kivétel a ASP.net Core Signal SDK-ról az Azure signaler Service SDK-ra való váltáskor?

A paraméter `HubConnectionContext context` különbözik a ASP.net Core signaler SDK és az Azure signaler Service SDK között `IUserIdProvider` , ha a hívása megtörténik.

ASP.NET Core a jelzőben a `HubConnectionContext context` fizikai ügyfélkapcsolat környezete az összes tulajdonság érvényes értékeivel.

Az Azure Signaler Service SDK-ban `HubConnectionContext context` a logikai ügyfélkapcsolat kontextusa. A fizikai ügyfélkapcsolat a Signaler szolgáltatás példányához van csatlakoztatva, így csak korlátozott számú tulajdonság van megadva.

Egyelőre csak a `HubConnectionContext.GetHttpContext()` és a `HubConnectionContext.User` hozzáféréshez érhető el.
[Itt](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)megtekintheti a forráskódot.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Konfigurálható a Signaler szolgáltatásban elérhető átvitelek a kiszolgálóoldali ASP.NET Core jelzővel való konfigurálásához? Letilthatja például a WebSocket Transport szolgáltatást?

Nem.

Az Azure Signaler szolgáltatás mindhárom olyan átvitelt biztosít, amelyet a ASP.NET Core a szignáló alapértelmezés szerint támogat. Nem konfigurálható. A signaler szolgáltatás minden ügyfélkapcsolat esetében kezeli a kapcsolatokat és a szállításokat.

Az ügyféloldali átvitelek az [itt](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)dokumentált módon konfigurálhatók.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Mit jelent a mérőszámok, például az üzenetek száma vagy a kapcsolatok száma Azure Portalban? Milyen típusú összesítési típust válasszon?

[Itt](signalr-concept-messages-and-connections.md)találja a mérőszámok kiszámításának részleteit.

Az Azure Signal Service-erőforrások áttekintés paneljén már kiválasztottuk a megfelelő összesítési típust. Ha pedig a metrikák panelre lép, az összesítési [típust is használhatja](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) hivatkozásként.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Mit jelent a szolgáltatási mód `Default` / `Serverless` / `Classic` ? Hogyan választhatok?

Módok
* `Default` a mód használatához a központi kiszolgáló *szükséges* . Ebben a módban az Azure Signaler a csatlakoztatott hub-kiszolgáló kapcsolataira irányítja az ügyfél forgalmát. Az Azure Signal ellenőrzi a csatlakoztatott központi kiszolgálókat. Ha nem található csatlakoztatott hub-kiszolgáló, az Azure-jelző elutasítja a bejövő ügyfélkapcsolatokat. Ebben a módban a **Management API** -t is használhatja a csatlakoztatott ügyfelek közvetlen Azure-jelzővel való kezeléséhez.
* `Serverless` a Mode *nem* engedélyezi a kiszolgálók kapcsolatát, azaz elutasítja az összes kiszolgálói kapcsolatot. Minden ügyfélnek kiszolgáló nélküli módban kell lennie. Az ügyfelek csatlakoznak az Azure-jelzőhöz, és a felhasználók általában kiszolgáló nélküli technológiákat, például **Azure-függvényt** használnak a hub-logika kezelésére. Tekintse meg az Azure-jelző kiszolgáló nélküli üzemmódját használó [egyszerű példát](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) .
* `Classic` a mód vegyes állapotú. Ha egy hubhoz kiszolgálói csatlakozás van, az új ügyfél a központi kiszolgálóra lesz irányítva, ha nem, akkor az ügyfél kiszolgáló nélküli módba lép.

  Ez némi problémát okozhat, például az összes kiszolgálói kapcsolat elvész egy pillanatra, egyes ügyfelek kiszolgáló nélküli módba lépnek, nem a központi kiszolgálóra való továbbítás helyett.

Kiválasztása
1. Nincs központi kiszolgáló, válassza a lehetőséget `Serverless` .
1. Az összes hubhoz van hub-kiszolgáló, válassza a lehetőséget `Default` .
1. Egyes hubok rendelkeznek hub-kiszolgálókkal, mások nem, választhatnak, `Classic` de ez némi problémát okozhat, a jobb módszer két példány létrehozása, az egyik a `Serverless` másik a `Default` .

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Az Azure Signaler ASP.NET-jelzővel való használata során felmerülő bármilyen eltérés?
Az Azure-jelző használatakor a ASP.NET-jelző egyes API-jai és funkciói már nem támogatottak:
- Az Azure-jelző használata nem támogatott az ügyfelek és a központ (gyakran hívott) közötti tetszőleges állapotának `HubState` átadásához.
- `PersistentConnection` az osztály még nem támogatott az Azure-jelző használatakor
- A **Forever frame Transport** nem támogatott az Azure-jelző használata esetén
- Az Azure-jelző már nem játssza le az ügyfélnek küldött üzeneteket, amikor az ügyfél offline állapotban van
- Az Azure-jelző használatakor a rendszer mindig az egyik ügyfélkapcsolat forgalmát irányítja át (aka. **Sticky**) egy app Server-példányra a kapcsolódás időtartamára

A ASP.NET-jelző támogatása a kompatibilitásra összpontosít, így nem minden új funkció támogatott a ASP.NET Core-jelzővel. Például a **MessagePack**, a **streaming**stb., csak ASP.net Core signaler-alkalmazásokhoz érhetők el.

A signaler szolgáltatás különböző szolgáltatási módhoz konfigurálható: `Classic` / `Default` / `Serverles` s. Ebben a ASP.NET-támogatásban a `Serverless` mód nem támogatott. Az adatsík REST API is nem támogatott.

## <a name="where-do-my-data-reside"></a>Hol találhatók az adataim?

Az Azure Signaler szolgáltatás adatfeldolgozó szolgáltatásként működik. A terv nem tárolja az ügyfél tartalmát és az adattárolást. Ha az Azure Signaler szolgáltatást más Azure-szolgáltatásokkal, például az Azure Storage for Diagnostics szolgáltatással együtt használja, tekintse [meg az Azure](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) -régiókban tárolt adattárolással kapcsolatos útmutatást.
