---
title: Mi az Azure SignalR Service?
description: Az Azure SignalR Service áttekintése.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 198eb0ff6c9f8de311cc2d39ba8fb7c8b6ed3a11
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552085"
---
# <a name="what-is-azure-signalr-service"></a>Mi az Azure SignalR Service?

Az Azure SignalR szolgáltatás leegyszerűsíti a valós idejű webes funkciók hozzáadását az alkalmazásokhoz HTTP-n keresztül. A valós idejű funkció lehetővé teszi, hogy a szolgáltatás tartalomfrissítéseket küldjön le a csatlakoztatott ügyfeleknek, például az egyoldalas web- vagy mobilalkalmazásoknak. Így az ügyfelek a kiszolgáló lekérdezése nélkül frissülnek vagy küldenek frissítésekre vonatkozó új HTTP-kéréseket.

A cikk áttekintést nyújt az Azure SignalR szolgáltatásról.

## <a name="what-is-azure-signalr-service-used-for"></a>Mire használják az Azure SignalR szolgáltatást?

Számos alkalmazástípushoz szükségesek valós idejű tartalomfrissítések. A következő példák megfelelőek lehetnek az Azure SignalR szolgáltatás használatához:

* Alkalmazások, amelyek gyakori frissítéseket igényelnek a kiszolgálóról. Ilyenek többek között a játékok és a szavazó-, aukciós, térkép- és GPS-alkalmazások.
* Irányítópultok és monitorozási alkalmazások. Ide tartoznak például a vállalati irányítópultok és az azonnali értékesítési frissítések.
* Együttműködésen alapuló alkalmazások. Rajtábla-alkalmazások és csapatértekezleti szoftverek tartoznak az együttműködésen alapuló alkalmazások közé.
* Értesítéseket használó alkalmazások. A közösségi hálózatbeli, levelezési, csevegő-, játék-, utazási figyelmeztetési és számos egyéb alkalmazás használ értesítéseket.

A SignalR számos, valós idejű webalkalmazások létrehozására használt technológia absztrakciója. A [WebSocket](https://wikipedia.org/wiki/WebSocket) az ideális átviteli technológia, de más technológiákat (például [kiszolgáló által küldött eseményeket (SSE)](https://wikipedia.org/wiki/Server-sent_events) és hosszú lekérdezést) is használ a rendszer, ha egyéb lehetőségek nem érhetők el. A SignalR automatikusan észleli és inicializálja a megfelelő átvitelt, a kiszolgáló és az ügyfél által támogatott szolgáltatások alapján.

A SignalR emellett programozási modellt biztosít a valós idejű alkalmazások számára, amely lehetővé teszi, hogy a kiszolgáló üzeneteket küldjön az összes kapcsolatnak, vagy a kapcsolatok egy adott felhasználóhoz tartozó, vagy tetszőleges csoportba helyezett részének.

## <a name="how-to-use-azure-signalr-service"></a>Az Azure SignalR szolgáltatás használata

Jelenleg háromféleképpen lehet használni az Azure SignalR szolgáltatást:

- **[ASP.NET Core SignalR-alkalmazás skálázása](signalr-concept-scale-aspnet-core.md)** – Az Azure SignalR szolgáltatás integrálása egy ASP.NET Core SignalR-alkalmazással több százezer kapcsolatra való horizontális felskálázáshoz.
- **[Kiszolgáló nélküli, valós idejű alkalmazások létrehozása](signalr-concept-azure-functions.md)** – Az Azure SignalR szolgáltatással integrált Azure Functions használata kiszolgáló nélküli, valós idejű alkalmazások létrehozására, többek között JavaScript, C# és Java nyelven.
- **[Üzenetek küldése a kiszolgálóról az ügyfeleknek REST API-n keresztül](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – Az Azure SignalR szolgáltatás biztosít egy REST API-t, amely lehetővé teszi, hogy az alkalmazások bármilyen REST-kompatibilis programnyelven üzeneteket küldjenek a SignalR szolgáltatáshoz csatlakoztatott ügyfeleknek.