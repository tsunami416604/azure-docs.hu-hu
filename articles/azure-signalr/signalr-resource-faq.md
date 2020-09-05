---
title: Az Azure Signaler szolgáltatással kapcsolatos gyakori kérdések
description: Választ kaphat az Azure Signaler szolgáltatással kapcsolatos gyakori kérdésekre, beleértve a hibaelhárítást és a jellemző használati forgatókönyveket.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489561"
---
# <a name="azure-signalr-service-faq"></a>Azure Signaler szolgáltatás – gyakori kérdések

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Az Azure Signaler szolgáltatás készen áll éles használatra?

Igen.
Az általános elérhetőség bejelentését lásd: az [Azure signaler szolgáltatás már általánosan elérhető](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

A [ASP.net Core jelző](https://docs.microsoft.com/aspnet/core/signalr/introduction) teljes mértékben támogatott.

A ASP.NET-jelző támogatása még *nyilvános előzetes*verzióban érhető el. [Íme egy példa a kódra](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Az ügyfél-csatlakozás a "nincs elérhető kiszolgáló" hibaüzenettel zárul. Mit jelent?

Ez a hiba csak akkor fordul elő, ha az ügyfelek üzeneteket küldenek az Azure Signaler szolgáltatásnak.

Ha nem rendelkezik alkalmazás-kiszolgálóval, és csak az Azure Signaler szolgáltatás REST API használja, ez a viselkedés a *tervezés szerint*történik.
A kiszolgáló nélküli architektúrában az ügyfélkapcsolatok *figyelés* módban vannak, és nem küldenek üzeneteket az Azure Signaler szolgáltatásnak.
[További információ a REST APIról](./signalr-quickstart-rest-api.md).

Ha az alkalmazás-kiszolgálókkal rendelkezik, ez a hibaüzenet azt jelenti, hogy egyetlen alkalmazáskiszolgáló sem csatlakozik az Azure Signaler szolgáltatás példányához.

A lehetséges okok a következők:
- Egyetlen alkalmazáskiszolgáló sem csatlakozik az Azure Signaler szolgáltatáshoz. A lehetséges csatlakoztatási hibákért keresse fel az alkalmazáskiszolgáló naplóit. Ez az eset ritka egy olyan magas rendelkezésre állási beállítás esetében, amelynek több alkalmazáskiszolgáló is van.
- Az Azure Signaler szolgáltatás példányaihoz kapcsolódási problémák léptek fel. Ez a probléma átmeneti, és a példányok automatikusan helyreállítva lesznek.
Ha több mint egy órát tart fenn, nyisson meg [egy problémát a githubon](https://github.com/Azure/azure-signalr/issues/new) , vagy [hozzon létre egy támogatási kérést az Azure-ban](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Ha több alkalmazás-kiszolgáló is van, az összes kiszolgálónak vagy csak az egyiknek küldi az üzeneteket?

Az ügyfél és az alkalmazáskiszolgáló között egy-az-egyhez típusú hozzárendelés található. Az egyik ügyfélről érkező üzeneteket a rendszer mindig ugyanarra az alkalmazási kiszolgálóra továbbítja.

A leképezés mindaddig fenn van tartva, amíg az ügyfél vagy az alkalmazáskiszolgáló nem bontja a kapcsolatot.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Ha az egyik alkalmazás-kiszolgálóm nem érhető el, Hogyan találhatom meg és kaphatok értesítést?

Az Azure Signaler szolgáltatás figyeli a szívveréseket az alkalmazás-kiszolgálókról.
Ha a szívverések nem érkeznek meg egy megadott ideig, az alkalmazáskiszolgáló offline állapotba kerül. A rendszer leválasztja az ehhez az alkalmazáskiszolgáló számára leképezett összes ügyfélkapcsolatot.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Miért van az egyéni `IUserIdProvider` kivételem, ha ASP.net Core Signal SDK-ról az Azure signaler Service SDK-ra váltok?

A paraméter `HubConnectionContext context` különbözik a ASP.net Core signaler SDK és az Azure signaler Service SDK között, ha a `IUserIdProvider` hívása megtörténik.

ASP.NET Core a jelzőben a `HubConnectionContext context` fizikai ügyfélkapcsolat környezete az összes tulajdonság érvényes értékeivel.

Az Azure Signaler Service SDK-ban a `HubConnectionContext context` logikai ügyfél-kapcsolatok kontextusa. A fizikai ügyfél csatlakozik az Azure Signaler szolgáltatás példányához, így csak korlátozott számú tulajdonság van megadva.

Egyelőre csak a `HubConnectionContext.GetHttpContext()` és a `HubConnectionContext.User` hozzáféréshez érhető el.
[A forráskódot](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)megtekintheti.

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Konfigurálható az Azure Signaler szolgáltatásban elérhető átvitelek a kiszolgálóoldali oldalon ASP.NET Core jelzővel? Le lehet tiltani például a WebSocket-átvitelt?

Nem.

Az Azure Signaler szolgáltatás mindhárom olyan átvitelt biztosít, amelyet a ASP.NET Core a szignáló alapértelmezés szerint támogat. Nem konfigurálható. Az Azure Signaler szolgáltatás minden ügyfélkapcsolat esetében kezeli a kapcsolatokat és a szállításokat.

Az ügyféloldali átviteleket [ASP.net Core jelző konfigurációjában](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)dokumentált módon konfigurálhatja.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Mit jelent a metrikák, például az üzenetek száma vagy a Azure Portalban látható kapcsolatok száma? Milyen típusú összesítési típust válasszon?

A metrikák kiszámításának részleteit az [Azure signaler szolgáltatás üzeneteiben és kapcsolataiban](signalr-concept-messages-and-connections.md)tekintheti meg.

Az Azure Signaler szolgáltatás erőforrásainak áttekintés paneljén már kiválasztottuk a megfelelő összesítési típust. Ha a metrikák ablaktáblára lép, az összesítési típust használhatja hivatkozásként az [Azure Signal Service-ben lévő üzenetekhez és kapcsolatokhoz](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) .

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Mit jelent a `Default` , a és a `Serverless` `Classic` szolgáltatás mód? Hogyan választhatok?

A következő információk szerepelnek a módokon:
* `Default` a módnak központi kiszolgálónak *kell lennie* . Ebben a módban az Azure Signaler szolgáltatás átirányítja az ügyfél forgalmát a csatlakoztatott hub-kiszolgáló kapcsolataira. Az Azure Signaler szolgáltatás ellenőrzi a csatlakoztatott központ kiszolgálóját. Ha a szolgáltatás nem talál csatlakoztatott központi kiszolgálót, elutasítja a bejövő ügyfélkapcsolatokat. Ebben a módban a *felügyeleti API* -t is használhatja a csatlakoztatott ügyfelek közvetlen felügyeletéhez az Azure Signaler szolgáltatáson keresztül.
* `Serverless` a Mode *nem* engedélyezi a kiszolgálói kapcsolatokat. Ez azt eredményezi, hogy elutasítja az összes kiszolgálói kapcsolatot. Minden ügyfélnek kiszolgáló nélküli módban kell lennie. Az ügyfelek az Azure Signaler szolgáltatáshoz csatlakoznak, és a felhasználók általában kiszolgáló nélküli technológiákat használnak, például a *Azure functions* a hub-logika kezelésére. [Tekintse meg a](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) kiszolgáló nélküli üzemmódot használó egyszerű példát az Azure Signaler szolgáltatásban.
* `Classic` a mód vegyes állapotú. Ha egy hubhoz kiszolgálói csatlakozás van, az új ügyfél egy központi kiszolgálóra lesz irányítva. Ha nem, akkor az ügyfél kiszolgáló nélküli módba lép. 

  Ez problémát okozhat. Ha például egy adott pillanatban az összes kiszolgálói kapcsolat elvész, egyes ügyfelek kiszolgáló nélküli módba lépnek a központi kiszolgálóra való útválasztás helyett.

Íme néhány útmutató a mód kiválasztásához:
- Ha nincs központi kiszolgáló, válassza a lehetőséget `Serverless` .
- Ha az összes hubhoz van központi kiszolgáló, válassza a lehetőséget `Default` .
- Ha egyes hubok rendelkeznek hub-kiszolgálókkal, de mások nem, akkor választhat `Classic` , de ez problémát okozhat. A jobb módszer két példány létrehozása: az egyik `Serverless` , a másik pedig a `Default` .

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Az Azure Signaler szolgáltatás az ASP.NET-jelzővel való használatának eltérései vannak?
Az Azure Signaler szolgáltatás használatakor a ASP.NET-jelző egyes API-jai és funkciói nem támogatottak:
- Az ügyfelek és a központ (gyakran nevezik) tetszőleges állapotba való továbbításának lehetősége `HubState` nem támogatott.
- Az `PersistentConnection` osztály nem támogatott.
- A *Forever frame Transport* nem támogatott.
- Az Azure Signaler szolgáltatás már nem játssza le az ügyfélnek a kapcsolat nélküli üzemmódban küldött üzeneteket.
- Az Azure Signaler szolgáltatás használatakor a rendszer mindig az egyik ügyfél-kapcsolatok forgalmát irányítja át (más néven *Sticky*) az egyik app Server-példányra a csatlakozás időtartamára.

A ASP.NET-jelző támogatása a kompatibilitásra összpontosít, így nem minden új funkció támogatott a ASP.NET Core-jelzővel. A *MessagePack* és a *Streaming* például csak ASP.net Core signaler-alkalmazásokhoz érhető el.

Az Azure Signaler szolgáltatás különböző szolgáltatási módokhoz konfigurálható: `Classic` , `Default` és `Serverless` . A `Serverless` mód nem támogatott a ASP.net esetében. Az adatsík REST API is nem támogatott.

## <a name="where-does-my-data-reside"></a>Hol találhatók az adataim?

Az Azure Signaler szolgáltatás adatfeldolgozó szolgáltatásként működik. Nem tárol semmilyen vásárlói tartalmat, és az adattárolási szolgáltatás a kialakítás részét képezi. Ha az Azure Signaler szolgáltatást más Azure-szolgáltatásokkal, például az Azure Storage for Diagnostics szolgáltatással együtt használja, tekintse meg [ezt](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) a tanulmányt, amely útmutatást nyújt az adattárolás Azure-régiókban való megőrzéséhez.
