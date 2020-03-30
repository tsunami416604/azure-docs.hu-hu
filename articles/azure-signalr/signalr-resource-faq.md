---
title: Az Azure SignalR szolgáltatás gyakori kérdései
description: Gyorsan hozzáférhet az Azure SignalR-szolgáltatással kapcsolatos gyakori kérdésekhez, a hibaelhárításról és a tipikus használati forgatókönyvekről.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dde11b6097dddb1568f5adfea811606214a9759e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75891252"
---
# <a name="azure-signalr-service-faq"></a>Az Azure Signalr szolgáltatás gyaktorva

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Készen áll az Azure SignalR szolgáltatás éles használatra?

Igen.
Az általános elérhetőség bejelentését lásd az [Azure SignalR szolgáltatás ban, amely most már általánosan elérhető.](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) teljes mértékben támogatott.

A ASP.NET SignalR támogatása továbbra is nyilvános *előzetes verzióban*érhető el. Itt van egy [kód példa](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Az ügyfélkapcsolat a "Nincs elérhető kiszolgáló" hibaüzenettel zárul. Mit jelentsen ez?

Ez a hiba csak akkor fordul elő, ha az ügyfelek üzeneteket küldenek a SignalR szolgáltatásnak.

Ha nem rendelkezik alkalmazáskiszolgálóval, és csak a SignalR Service REST API-t használja, ez a viselkedés **szándékosan .**
A kiszolgáló nélküli architektúrában az ügyfélkapcsolatok **LISTEN** módban vannak, és nem küldenek üzeneteket a SignalR szolgáltatásnak.
További információ a [REST API-ról.](./signalr-quickstart-rest-api.md)

Ha alkalmazáskiszolgálói vannak, ez a hibaüzenet azt jelenti, hogy nincs alkalmazáskiszolgáló csatlakoztatva a SignalR szolgáltatáspéldányhoz.

A lehetséges okok a következők:
- Nincs alkalmazáskiszolgáló csatlakoztatva a SignalR szolgáltatáshoz. Ellenőrizze az alkalmazáskiszolgáló naplóiban a lehetséges csatlakozási hibákat. Ez az eset ritka, ha egynél több alkalmazáskiszolgálóval van magas rendelkezésre állású környezetben.
- Kapcsolati problémák vannak a SignalR service-példányokkal. Ez a probléma átmeneti, és automatikusan helyreáll.
Ha egy óránál tovább marad, [nyisson meg egy problémát a GitHubon,](https://github.com/Azure/azure-signalr/issues/new) vagy [hozzon létre egy támogatási kérelmet az Azure-ban.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Ha több alkalmazáskiszolgáló van, az ügyfélüzenetek et az összes kiszolgálónak vagy csak az egyiknek küldik?

Ez egy-az-egyhez hozzárendelés az ügyfél és az alkalmazáskiszolgáló között. Az egyik ügyféltől érkező üzenetek mindig ugyanannak az alkalmazáskiszolgálónak lesznek elküldve.

Az ügyfél- és alkalmazáskiszolgáló közötti hozzárendelés mindaddig megmarad, amíg az ügyfél- vagy alkalmazáskiszolgáló le nem bontja a kapcsolatot.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Ha az egyik alkalmazásszerverem nem működik, hogyan találhatom meg, és kaphatok értesítést?

A SignalR szolgáltatás az alkalmazáskiszolgálók szívverését figyeli.
Ha a szívverések egy adott ideig nem érkeznek meg, az alkalmazáskiszolgáló offline állapotban lesz. Az alkalmazáskiszolgálóra leképezett összes ügyfélkapcsolat le lesz választva.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Miért az `IUserIdProvider` egyéni dobáskivétel, amikor ASP.NET Core SignalR SDK-ról az Azure SignalR Service SDK-ra vált?

A `HubConnectionContext context` paraméter a hívott ASP.NET Core SignalR SDK és `IUserIdProvider` az Azure SignalR Service SDK között eltérő.

A Core SignalR `HubConnectionContext context` ASP.NET a fizikai ügyfélkapcsolat ból származó környezet érvényes értékekkel rendelkezik az összes tulajdonságra vonatkozóan.

Az Azure SignalR Service `HubConnectionContext context` SDK, a logikai ügyfélkapcsolat környezete. A fizikai ügyfélkapcsolat a SignalR szolgáltatáspéldányhoz csatlakozik, így csak korlátozott számú tulajdonság áll rendelkezésre.

Most, csak `HubConnectionContext.GetHttpContext()` `HubConnectionContext.User` és elérhető a hozzáférést.
A forráskódot [itt](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)ellenőrizheti.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Konfigurálhatom a SignalR szolgáltatásban elérhető transzportálásokat úgy, hogy a kiszolgáló oldalon konfigurálják ASP.NET Core SignalR segítségével? Letiltja például a WebSocket átvitelt?

Nem.

Az Azure SignalR Service biztosítja mind a három átvitelt, amelyet ASP.NET Core SignalR alapértelmezés szerint támogat. Nem konfigurálható. A SignalR Service kezeli az összes ügyfélkapcsolat kapcsolatait és átvitelét.

Az ügyféloldali szállításokat [itt](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)dokumentáltként állíthatja be.
