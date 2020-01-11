---
title: Az Azure Signaler szolgáltatással kapcsolatos gyakori kérdések
description: Gyorsan hozzáférhet az Azure Signaler szolgáltatással kapcsolatos gyakori kérdésekhez, a hibaelhárításról és a tipikus használati forgatókönyvekről.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dde11b6097dddb1568f5adfea811606214a9759e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891252"
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

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Miért van az egyéni `IUserIdProvider` kivétel, ha ASP.NET Core Signal SDK-ról az Azure Signaler Service SDK-ra vált?

A (z) `HubConnectionContext context` paraméter különbözik a ASP.NET Core Signaler SDK és az Azure Signaler Service SDK között, ha a rendszer a `IUserIdProvider`t hívja meg.

ASP.NET Core-jelzőben a `HubConnectionContext context` a fizikai ügyfélkapcsolat környezete az összes tulajdonság érvényes értékeivel.

Az Azure Signaler Service SDK-ban `HubConnectionContext context` a logikai ügyfélkapcsolat kontextusa. A fizikai ügyfélkapcsolat a Signaler szolgáltatás példányához van csatlakoztatva, így csak korlátozott számú tulajdonság van megadva.

Egyelőre csak `HubConnectionContext.GetHttpContext()` és `HubConnectionContext.User` érhető el a hozzáféréshez.
[Itt](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)megtekintheti a forráskódot.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Konfigurálható a Signaler szolgáltatásban elérhető átvitelek a kiszolgálóoldali ASP.NET Core jelzővel való konfigurálásához? Letilthatja például a WebSocket Transport szolgáltatást?

Nem.

Az Azure Signaler szolgáltatás mindhárom olyan átvitelt biztosít, amelyet a ASP.NET Core a szignáló alapértelmezés szerint támogat. Nem konfigurálható. A signaler szolgáltatás minden ügyfélkapcsolat esetében kezeli a kapcsolatokat és a szállításokat.

Az ügyféloldali átvitelek az [itt](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)dokumentált módon konfigurálhatók.
