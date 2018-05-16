---
title: Mi az az Azure SignalR? | Microsoft Docs
description: Az Azure SignalR szolgáltatás áttekintése.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: e24091b017a1c6c82cfe8d12873223b98c165c63
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-signalr-service"></a>Mi az az Azure SignalR szolgáltatás?

Az Azure SignalR szolgáltatás egy [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction)-alapú Azure-szolgáltatás. Az ASP.NET Core SignalR egy [nyílt forráskódú kódtár](https://github.com/aspnet/signalr), amely leegyszerűsíti valós idejű webes funkciók hozzáadását az alkalmazásokhoz HTTP-n keresztül. A valós idejű funkció lehetővé teszi, hogy a webkiszolgáló tartalomfrissítéseket küldjön le a csatlakoztatott ügyfeleknek. Így az ügyfelek a kiszolgáló lekérdezése nélkül frissülnek vagy küldenek frissítésekre vonatkozó új HTTP-kéréseket.

A cikk áttekintést nyújt az Azure SignalR szolgáltatásról. Ha meg szeretne ismerkedni a szolgáltatással, kezdje az [ASP.NET Core rövid útmutatójával](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>Mire használható a SignalR szolgáltatás? 

Számos alkalmazástípushoz szükségesek valós idejű tartalomfrissítések. A következő példaalkalmazás-típusok megfelelőek lehetnek az Azure SignalR szolgáltatás használatához:

* Alkalmazások, amelyek gyakori frissítéseket igényelnek a kiszolgálóról. Ilyenek többek között a játékok, a közösségi hálózati alkalmazások, valamint a szavazó-, aukciós, térkép- és GPS-alkalmazások.
* Irányítópultok és monitorozási alkalmazások. Ide tartoznak például a vállalati irányítópultok, az azonnali értékesítési frissítések vagy az utazási figyelmeztetések.
* Együttműködésen alapuló alkalmazások. Rajtábla-alkalmazások és csapatértekezleti szoftverek tartoznak az együttműködésen alapuló alkalmazások közé.
* Értesítéseket használó alkalmazások. A közösségi hálózatbeli, levelezési, csevegő-, játék-, utazási figyelmeztetési és számos egyéb alkalmazás használ értesítéseket.

A SignalR számos, valós idejű webalkalmazások létrehozására használt technológia absztrakciója. A [WebSocket](https://wikipedia.org/wiki/WebSocket) az ideális átviteli technológia, de más technológiákat (például [kiszolgáló által küldött eseményeket (SSE)](https://wikipedia.org/wiki/Server-sent_events) és hosszú lekérdezést) is használ a rendszer, ha egyéb lehetőségek nem érhetők el. A SignalR automatikusan észleli és inicializálja a megfelelő átvitelt, a kiszolgáló és az ügyfél által támogatott szolgáltatások alapján.

## <a name="developing-signalr-apps"></a>SignalR-alkalmazások fejlesztése

Jelenleg a SignalR két verziója használható a webalkalmazásokhoz: SignalR for ASP.NET, és ASP.NET Core SignalR, amely egyben az újabb verzió is. Az Azure SignalR szolgáltatás, vagy más néven *SignalR szolgáltatás* az ASP.NET Core SignalRre épülő, Azure által kezelt szolgáltatás. 

Az ASP.NET Core SignalR az előző verzió átdolgozása. Ezért az ASP.NET Core SignalR visszamenőlegesen nem kompatibilis a SignalR korábbi verzióival. Az API-k és a szolgáltatás működése eltérő. Az ASP.NET Core SignalR SDK .NET Standard, ezért továbbra is használható a .NET-keretrendszerrel. Azonban az új API-kat kell használnia a régiek helyett. Ha a SignalRt használja és szeretne ASP.NET Core SignalRre vagy Azure SignalR szolgáltatásra váltani, úgy kell módosítania a kódot, hogy kezelje az API-k közötti különbségeket.

Az Azure SignalR szolgáltatás használata esetén az ASP.NET Core SignalR kiszolgálóoldali összetevőjének üzemeltetése az Azure-ban történik. Mivel azonban a technológia az ASP.NET Core-ra épül, lehetőség van arra, hogy a tényleges webalkalmazást több különféle platformon (Windows, Linux és MacOS) futtassa, és az üzemeltetést az [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) vagy a [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index) szolgáltatással végezze. Saját folyamatok esetében saját üzemeltetést is használhat.

Ha azt szeretné, hogy az alkalmazás támogassa a legújabb funkciókat a webes ügyfelek valós idejű tartalmakkal való frissítéséhez, futtatható legyen a több platformon (Azure, Windows, Linux és MacOS), és üzemeltethető legyen különféle környezetekben, az Azure SignalR az ideális választás.


## <a name="why-not-deploy-signalr-myself"></a>Miért ne telepítsem saját kezűleg a SignalRt?

Továbbra is lehetséges üzembe helyezni egy ASP.NET Core SignalRt támogató Azure-webalkalmazást háttérösszetevőként a teljes alkalmazáshoz.

Az Azure SignalR egyik legnagyobb előnye az egyszerű használata. Az Azure SignalR szolgáltatás révén nem kell a teljesítménnyel, a méretezéssel és a rendelkezésre állással kapcsolatos problémákkal foglalkoznia. Ezeket a problémákat a rendszer kezeli Ön helyett a 99,9%-os szolgáltatásiszint-szerződésben foglaltak szerint.

A valós idejű tartalomfrissítésekhez az előnyben részesített technológia általában a WebSocket. Azonban a nagy számú állandó WebSocket-kapcsolatok terheléselosztása egyre nagyobb problémát jelent, ahogy a rendszer mérete nő. Gyakori megoldás a DNS-terheléselosztás, a hardveres terheléselosztás és a szoftveres terheléselosztás. Az Azure SignalR szolgáltatás megoldja ezt a problémát Ön helyett.

A másik ok, hogy nem kell ténylegesen üzemeltetnie egy webalkalmazást. Előfordulhat, hogy a webalkalmazás logikája [kiszolgáló nélküli számítást](https://azure.microsoft.com/overview/serverless-computing/) használ. Például előfordulhat, hogy a kód csak igény szerint lesz üzemeltetve és végrehajtva az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) eseményindítói segítségével. Ez a forgatókönyv bonyolult lehet, mivel a kód csak igény szerint fut és nem tart fenn hosszú kapcsolatot az ügyfelekkel. Az Azure SignalR szolgáltatás képes kezelni ezt a helyzetet, mivel a szolgáltatás már kezeli a kapcsolatokat.

## <a name="how-does-it-scale"></a>Hogyan skálázható?

A SignalR skálázásához gyakran az SQL Servert, az Azure Service Bust vagy a Redis Cache-t használják. Az Azure SignalR szolgáltatás kezeli a skálázási módszert Ön helyett. A teljesítmény és a költségek a módszertől függően változnak, és Önnek nem kell ezekkel a további szolgáltatásokkal foglalkoznia. Mindössze frissítenie kell a szolgáltatás egységeinek számát. Minden szolgáltatási egység legfeljebb 1000 ügyfélkapcsolatot támogat.

## <a name="next-steps"></a>További lépések
* [Rövid útmutató: Csevegőszoba létrehozása az Azure SignalRrel](signalr-quickstart-dotnet-core.md)  
  

