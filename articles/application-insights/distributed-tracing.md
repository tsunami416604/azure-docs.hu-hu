---
title: Az Azure Application Insights nyomkövetésére elosztott |} A Microsoft Docs
description: A Microsoft támogatásával kapcsolatos információkat biztosít a helyi továbbító és a partneri kapcsolat a OpenCensus projekt az elosztott nyomkövetést
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 47ea4fadba50fdbbd6d83531bd4eb40fc581d2ad
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950199"
---
# <a name="what-is-distributed-tracing"></a>Mi az az elosztott nyomkövetési?

Modern felhő- és mikroszolgáltatás-architektúrák megjelenésével növekedését egyszerű, függetlenül üzembe helyezhető szolgáltatások, amelyek segítségével csökkentheti a költségeket a rendelkezésre állási és az átviteli sebesség növelése mellett. Azonban amíg ezek áthelyezések száma – az egyes szolgáltatások végzett megérteni a teljes, igazi érdeme, átfogó rendszerek nehezebb kapcsolatos okból és a hibakereséshez.

A monolitikus architektúrák közben használtunk a hívási hibakereséséről. Hívási azok ragyogó eszközök megjelenítéséhez, a folyamat-végrehajtás (módszer A hívott metódus B, c. módszer neve), valamint a részletek és a paraméterek egyes ezeket a hívásokat. Ez a nagyszerű kódtömbök vagy egyetlen folyamat futó szolgáltatások esetén, de hogyan tegye azt debug mikor a hívás nem egy folyamat határán, nem csak a helyi veremben hivatkozást? 

Ez hol elosztott nyomkövetést érhető el.  

Elosztott nyomkövetést megegyezik a modern felhő- és mikroszolgáltatás-architektúrák, a hívás halmok egy egyszerűsített teljesítmény-Profilkészítő lépett fel igény szerinti hozzáadásával. Az Azure monitorban a két megközelítés elosztott nyomkövetési adatok felhasználásához biztosítunk. Az első az [tranzakció diagnosztikája](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) hasonlóan a hívási veremben egy time dimenzió az új funkció a nézetben. A tranzakció diagnosztikája nézet egyetlen tranzakció/kérelemként rálátást biztosít, és hasznos megbízhatósággal kapcsolatos problémákat és a egy kérelem történik a teljesítmény szűk a kiváltó okok kimutatásához.

Az Azure Monitor is biztosít egy [alkalmazástérkép](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) nézetet, amelyben a rendszerek együttműködését, és Mik azok az átlagos teljesítmény és a hibák gyakoriságát topológiai nézetével megjeleníthető sok tranzakció összesíti. 

## <a name="how-to-enable-distributed-tracing"></a>Elosztott nyomkövetést engedélyezése

Elosztott nyomkövetést engedélyezése egy alkalmazás szolgáltatásban szúrni a megfelelő SDK-t vagy a könyvtár összes szolgáltatás alapján a nyelv, a szolgáltatás implementálva lett.

## <a name="enabling-via-application-insights-sdks"></a>Application Insights SDK-k használatával engedélyezése

Az Application Insights SDK-k a .NET, .NET Core, Java, Node.js és JavaScript összes támogatja a natív módon az elosztott nyomkövetési. Útmutatás telepítéséhez, és minden Application Insights SDK konfigurálása alatt érhetők el:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

A megfelelő Application Insights SDK telepítése és konfigurálása, a nyomkövetés automatikusan gyűjt adatokat a népszerű keretrendszereket, könyvtárak és technológiák SDK függőségi auto-gyűjtők által. A támogatott technológiák teljes listája megtalálható [a függőségi az automatikus gyűjtés dokumentáció](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Emellett minden olyan technológia nyomon követhetők manuálisan hívásával [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) a a [TeleletryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Keresztül OpenCensus engedélyezése

Az Application Insights SDK-k mellett az Application Insights is támogatja keresztül elosztott nyomkövetést [OpenCensus](https://opencensus.io/). OpenCensus egy nyílt forráskódú, gyártófüggetlen, egyetlen terjesztési kódtárak metrikák gyűjtési és elosztott nyomkövetést a szolgáltatások biztosítása érdekében. Emellett lehetővé teszi a nyílt forráskódú fejlesztői Közösség elosztott nyomkövetést, Redis, a Memcached, vagy a MongoDB népszerű technológiáival engedélyezéséhez. [A Microsoft együttműködnek a OpenCensus számos más figyelési és a felhő partnerek](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Elosztott nyomkövetést képességek hozzáadása alkalmazáshoz való hozzárendelésként az OpenCensus, először [telepítése és konfigurálása az Application Insights helyi továbbító](./opencensus-local-forwarder.md). Itt konfigurálhatja a OpenCensus elosztott nyomkövetési adatokat a helyi továbbító keresztül irányíthatja. Mindkét [Python](./opencensus-python.md) és [Go](./opencensus-go.md) támogatottak.

A OpenCensus webhely fenntartja API referenciadokumentációt tartalmaz a [Python](https://opencensus.io/api/python/trace/usage.html) és [Go](https://godoc.org/go.opencensus.io), különféle különböző útmutatók a OpenCensus valamint. 

## <a name="next-steps"></a>További lépések

* [OpenCensus Python – használati útmutató](https://opencensus.io/api/python/trace/usage.html)
* [Alkalmazástérkép](./app-insights-app-map.md)
* [Teljes körű alkalmazásteljesítmény-figyelés](./app-insights-tutorial-performance.md)