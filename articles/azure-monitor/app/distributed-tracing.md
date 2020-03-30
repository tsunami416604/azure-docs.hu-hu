---
title: Elosztott nyomkövetés az Azure Application Insightsban | Microsoft dokumentumok
description: Tájékoztatást nyújt arról, hogy a Microsoft támogatja-e az OpenCensus projektben való partnerségünkön keresztül terjesztett nyomon követést
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c2f384370c3ceaf24164e4a27adc05b1a1e1ddf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294965"
---
# <a name="what-is-distributed-tracing"></a>Mi az elosztott nyomkövetés?

A modern felhő- és [mikroszolgáltatási](https://azure.com/microservices) architektúrák megjelenése egyszerű, egymástól függetlenül telepíthető szolgáltatásokat eredményezett, amelyek segíthetnek a költségek csökkentésében, miközben növelik a rendelkezésre állást és az átviteli szintet. De míg ezek a mozgások tették az egyes szolgáltatások könnyebben érthető, mint egész, ők már az általános rendszerek nehezebb értelme és hibakeresés.

A monolitikus architektúrákban megszoktuk a híváshalmokkal való hibakeresést. A híváshalmok ragyogó eszközök a végrehajtás folyamatának megjelenítéséhez (A method a B módszer, amely et C módszernek hívtak), valamint az egyes hívások részletei és paraméterei. Ez nagyszerű a monolitok vagy az egyetlen folyamaton futó szolgáltatások számára, de hogyan hibakeresést tehetünk le, ha a hívás egy folyamathatáron keresztül történik, nem csak a helyi veremre vonatkozó hivatkozás? 

Itt jön a helyébe az elosztott nyomkövetés.  

Az elosztott nyomkövetés a modern felhő- és mikroszolgáltatási architektúrák híváshalmainak felel meg, és egy egyszerű teljesítményprofil-kezelő vel van bedobva. Az Azure Monitorban két felhasználói élményt biztosítunk az elosztott nyomkövetési adatok fogyasztásához. Az első a [tranzakciódiagnosztikai](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) nézetünk, amely olyan, mint egy idődimenzióval rendelkező hívásverem. A tranzakciódiagnosztika nézet egyetlen tranzakció/kérelem láthatóságát biztosítja, és segítséget nyújt a megbízhatósági problémák és a teljesítményszűk keresztmetszetek kérésenkénti megtalálásához.

Az Azure Monitor egy [alkalmazástérkép-nézetet](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) is kínál, amely sok tranzakciót összesít, hogy a rendszerek interakciójának topológiai nézetét, valamint az átlagos teljesítmény- és hibaarányokat jelenítse meg. 

## <a name="how-to-enable-distributed-tracing"></a>Az elosztott nyomkövetés engedélyezése

Az alkalmazások ban lévő szolgáltatások között elosztott nyomkövetés engedélyezése olyan egyszerű, mint a megfelelő SDK vagy kódtár hozzáadása az egyes szolgáltatásokhoz, a szolgáltatás által megvalósított nyelv alapján.

## <a name="enabling-via-application-insights-sdks"></a>Engedélyezés az Application Insights SDK-kon keresztül

Az Application Insights SDK-k .NET, .NET Core, Java, Node.js és JavaScript minden támogatja az elosztott nyomkövetés natívan. Az egyes Application Insights SDK-k telepítésére és konfigurálására vonatkozó utasítások az alábbiakban érhetők el:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [Javascript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

A megfelelő Application Insights SDK telepítve és konfigurálva, nyomkövetési információkat automatikusan gyűjti a népszerű keretrendszerek, könyvtárak és technológiák SDK függőség automatikus gyűjtők. A támogatott technológiák teljes listája [a Függőség automatikus gyűjtési dokumentációjában](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)érhető el.

 Emellett bármely technológia manuálisan nyomon követhető a [TelemettryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) hívásával.

## <a name="enable-via-opencensus"></a>Engedélyezés opencensus-on keresztül

Az Application Insights SDK-k mellett az Application Insights is támogatja az [OpenCensus](https://opencensus.io/)szolgáltatáson keresztül elosztott nyomkövetést. OpenCensus egy nyílt forráskódú, szállító-független, egyetlen terjesztése a könyvtárak, hogy metrikák gyűjtése és elosztott nyomkövetés a szolgáltatások. Azt is lehetővé teszi, hogy a nyílt forráskódú közösség lehetővé tegye az elosztott nyomon követést olyan népszerű technológiákkal, mint a Redis, a Memcached vagy a MongoDB. [A Microsoft számos más felügyeleti és felhőbeli partnerrel együttműködik az OpenCensus szolgáltatásban.](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)

[Python](opencensus-python.md) 

Az OpenCensus webhely api-referenciadokumentációt tart fenn a [Python](https://opencensus.io/api/python/trace/usage.html) és a [Go](https://godoc.org/go.opencensus.io)számára, valamint különböző útmutatókat az OpenCensus használatára. 

## <a name="next-steps"></a>További lépések

* [OpenCensus Python használati útmutató](https://opencensus.io/api/python/trace/usage.html)
* [Alkalmazástérkép](./../../azure-monitor/app/app-map.md)
* [Végpontok között a teljesítmény figyelése](./../../azure-monitor/learn/tutorial-performance.md)
