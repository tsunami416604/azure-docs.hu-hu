---
title: A hagyományos vállalati Azure Application Insights terv díjszabása |} Microsoft Docs
description: Telemetria kötetek kezelése, és figyelje az Application Insightsban költségeket.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Vállalati csomag részletei

Az Application Insights két árképzési tervek rendelkezik. A séma elnevezése [alapvető](app-insights-pricing.md), amely tartalmazza az összes funkciót a vállalati terv nem hozzáadása költség és váltók elsősorban az adatok okozhatnak a köteten. Ha az Operations Management Suite használ, úgy kell dönthet, a vállalati terv, amely egy csomópontonként díjat számítanak naponta adatok támogatás együtt, és majd díjat számítanak a belefoglalt támogatás fent okozhatnak adatok.

Tekintse meg a [árképzést ismertető oldalra Application Insights](http://azure.microsoft.com/pricing/details/application-insights/) aktuális árak a pénznem és régióban.

## <a name="heres-how-the-enterprise-plan-works"></a>Ez a vállalati terv működése

* A vállalati tervben alkalmazások telemetriai adatokat küldő csomópontonként kell fizetnie.
 * A *csomópont* egy fizikai vagy virtuális gépet, vagy a Platform,--szolgáltatás szerepkör-példány, az alkalmazás.
 * Fejlesztési gépek, az ügyfélböngészők és a mobileszközök nem számítanak csomópontok.
 * Ha az alkalmazás még több összetevők által küldött telemetriai adatok, például egy webszolgáltatás-bővítmény és a háttér-feldolgozók ezek nem számítanak bele.
 * [Élő Stream metrikák](app-insights-live-stream.md) adatok nem számítanak purposes.* díjszabási egy előfizetésből, a díjak nem alkalmazásonkénti csomópontonként. Ha az öt csomóponttal 12 telemetriai adat küldése az öt csomóponttal alkalmazásokat, majd az elsők között van.
* Bár a havi díjak korlátozott akkor van szó, csak a minden órában, amelyben egy csomópont telemetriai adatokat küld az alkalmazásokból. Az óránkénti kell fizetni az ajánlatban szereplő havi kell fizetni az / 744 (a 31 napos hónap órák száma).
* Az egyes csomópontok (az óránkénti részletességű) észlelt kap egy adatok kötet foglalási, 200 MB / nap. Nem használt adatok foglalási nem átkerül egy nap után a Tovább gombra.
 * Ha a vállalati árképzési beállítást választja, minden egyes előfizetés lekérdezi a napi támogatás a telemetriai adatok küldése az Application Insights-erőforrások, az adott előfizetés csomópontok száma alapján. Ezért ha 5 csomópontok adatküldés minden nap, hogy egy készletezett támogatás az adott előfizetés Application Insights-erőforrások alkalmazott 1 GB. Nem számít, ha az egyes csomópontok más csomópontok-nál több adatot küld, mivel a befoglalt adatok meg vannak osztva az összes csomópont között. Ha egy adott napon az Application Insights-erőforrások jelenik meg több adat ehhez az előfizetéshez a napi adatok-foglalás szerepel, a / GB-os keretét adatok díjak vonatkoznak. 
 * A napi adatok támogatás számítása a napra (UTC) órák száma, hogy minden csomópont küld telemetriai 24 alkalommal 200 MB hányadosa. Így ha 4 csomópontok 15 nap 24 óra során telemetriai adatok küldését, az adott napon adatát lenne ((4 x 15) / 24) x 200 MB = 500 MB. Adatok túlhasználati 2.30-as USD / GB ár az elsők között lenne 1,15 USD Ha a csomópontok küldött 1 GB adatot adott napon.
 * A terv napi támogatás nincsenek megosztva, amelynek választotta, az alapszintű beállítás és a nem használt engedélyezett alkalmazások vállalati nem átkerülnek a napi. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Néhány példa meghatározása során különböző csomópontok száma

| Forgatókönyv                               | Teljes napi száma |
|:---------------------------------------|:----------------:|
| 1 alkalmazás használ 3 Azure App Service-példány és az 1 virtuális kiszolgáló | 4 |
| 3, 2 virtuális gép, és az Application Insights-erőforrások ezeket az alkalmazásokat a futó alkalmazások: ugyanazt az előfizetést, és a vállalati terv | 2 | 
| 4 alkalmazások, amelyek alkalmazások Insights-erőforrások vannak ugyanahhoz az előfizetéshez. Minden alkalmazás fut, 16 csúcsidőn 2 példányok és 8 csúcsidőben 4 példányok. | 13.33 | 
| Az 1 feldolgozói szerepkör és 1 webes szerepkör, minden futó 2 példányait cloud services csomag | 4 | 
| 5-csomópont Service Fabric-fürt minden egyes micro-szolgáltatást, futó 3 példányait futtató 50 micro-szolgáltatások, | 5|

* A pontos leltár viselkedés csomópont függ, amelyen az Application Insights SDK az alkalmazás használatával. 
  * SDK verzióiban 2.2-es és újabb verziók esetében, mind az Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) vagy [webes SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fogja jelentés minden egyes alkalmazás-csomópontként, például a fizikai kiszolgáló és a Virtuálisgép-gazdák számítógépnév vagy a példány neve felhőszolgáltatások esetén.  Az egyetlen kivétel csak alkalmazások használatával [.NET Core](https://dotnet.github.io/) és az Application Insights Core SDK, amelyben eset csak egy csomópont lesz jelentve állomások, mert az állomás neve nem érhető el. 
  * Az SDK korábbi verzióiban a [webes SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fog viselkedni, ahogy az SDK újabb verziók, azonban a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) csak egy csomópont függetlenül a tényleges alkalmazásgazdája számát jelenti. 
  * Ha az alkalmazás az SDK segítségével egyéni értékre állították roleInstance, alapértelmezés szerint ugyanezt az értéket használandó csomópontok számának meghatározása. 
  * Ügyfélgépek vagy mobil eszközökhöz futó alkalmazáshoz egy új SDK verzióját használja, akkor lehetséges, hogy a csomópontok száma előfordulhat, hogy vissza egy szám, amely túl nagy (az ügyfél gépek vagy a mobil eszközök nagy száma). 
