---
title: Az Azure SignalR Service – gyakori kérdések
description: Az Azure SignalR Service – gyakori kérdések.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 09788f4ded66b43fd5ecae20301a28cd01d77320
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554524"
---
# <a name="azure-signalr-service-faq"></a>Az Azure SignalR Service – gyakori kérdések

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Az Azure SignalR Service éles használatra kész van?

Igen.
Az általános elérhetőség közleményt, lásd: [már általánosan elérhető az Azure SignalR Service](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/). 

[Az ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) teljes mértékben támogatott.

Az ASP.NET SignalR támogatása továbbra is van a *nyilvános előzetes verzióban*. Íme egy [példakód](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>Az ügyfél kapcsolata bezárul a "Nincs elérhető kiszolgáló". Mit jelent?

Ez a hiba akkor fordul elő, csak akkor, ha az ügyfelek üzeneteket küldenek a SignalR-szolgáltatáshoz.

Ha nem rendelkezik minden olyan alkalmazáskiszolgáló, és csak a SignalR Service REST API-val, van-e ez a viselkedés **elvárt**.
Kiszolgáló nélküli architektúra, az ügyfélkapcsolatok vannak **FIGYELÉSÉRE** mód és a nem küldése a SignalR Service küldött üzeneteket fogja.
További tudnivalók a [REST API-val](./signalr-quickstart-rest-api.md).

Ha az alkalmazáskiszolgálókat, ez a hibaüzenet azt jelenti, hogy nincs alkalmazás kiszolgáló csatlakoztatva van a SignalR Service-példányt.

A lehetséges okok a következők:
- Nincs alkalmazás kiszolgáló csatlakoztatva van a SignalR Service. Ellenőrizze a kiszolgáló alkalmazásnaplókat a lehetséges csatlakozási hibákat. Ebben az esetben a rendszer egynél több alkalmazás-kiszolgálók magas rendelkezésre állású Setting ritka.
- Nincsenek kapcsolódási problémák SignalR Service példányaival. Ez a probléma átmeneti, és automatikusan helyre.
Ha nem szűnik meg, a több mint egy óráig [nyisson egy problémát a Githubon](https://github.com/Azure/azure-signalr/issues/new) vagy [hozzon létre egy támogatási kérést az Azure-ban](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Ha több, ügyfél-üzenetek érkeznek minden kiszolgáló, vagy csak az egyik?

Egy az egyhez típusú hozzárendelés az ügyfelek és alkalmazáskiszolgálók között. Egy ügyfél mindig üzenetküldés alkalmazás ugyanarra a kiszolgálóra.

Az ügyfelek és alkalmazáskiszolgálók közötti leképezést fenn kell tartani, amíg az ügyfél vagy az alkalmazás kiszolgáló bontja a kapcsolatot.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Ha az alkalmazás-kiszolgálók egyike le, hogyan keresse meg, és lekérése az értesítés?

SignalR Service alkalmazáskiszolgálók érkező szívveréseket figyeli.
A szívverések nem érkezik a megadott időtartam elteltéig, ha a kiszolgáló offline állapotúnak tekinti. Az alkalmazáskiszolgáló leképezve az összes ügyfélkapcsolatok le lesz választva.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Miért érdemes a saját egyéni does `IUserIdProvider` throw kivétel, amikor átvált az ASP.NET Core SDK-t a SignalR Azure SignalR Service SDK-t?

A paraméter `HubConnectionContext context` eltér az ASP.NET Core SDK-t a SignalR és az Azure SignalR Service SDK amikor `IUserIdProvider` nevezzük.

Az ASP.NET Core SignalR `HubConnectionContext context` az összes tulajdonság érvényes értékei a fizikai ügyfél-kapcsolatot a környezet.

Az Azure SignalR Service SDK-t `HubConnectionContext context` a környezet a logikai ügyfélkapcsolat. A fizikai ügyfélkapcsolat csatlakozik a SignalR Service-példányhoz, így csak korlátozott számú tulajdonságok vannak megadva.

Egyelőre csak `HubConnectionContext.GetHttpContext()` és `HubConnectionContext.User` hozzáférés érhetők el.
Ellenőrizheti a forráskód [Itt](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Konfigurálhatom úgy a SignalR Service-ben elérhető átvitelek, konfigurálja úgy a kiszolgálói oldalon az ASP.NET Core SignalR? Például letiltja a WebSocket átviteli?

Nem.

Az Azure SignalR Service minden három szállítások, amely alapértelmezés szerint támogatja az ASP.NET Core SignalR biztosít. Érték nem módosítható. SignalR Service és az összes ügyfél-kapcsolatok átvitel fogja kezelni.

Ügyféloldali átvitelek dokumentált módon konfigurálható [Itt](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
