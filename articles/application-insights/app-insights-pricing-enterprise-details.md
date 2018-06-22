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
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: 65307eab0bf1b5f502f11c14c369826cd12e0966
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309833"
---
# <a name="enterprise-plan-details"></a>Nagyvállalati csomag részletei

Az Azure Application Insights van két árképzési tervek: Basic és Enterprise. A [alapvető](app-insights-pricing.md) terv árképzési az alapértelmezett terv. Ez magában foglalja az összes vállalati terv funkciót, minden további költség nélkül. Az alapszintű csomag váltók elsősorban okozhatnak az adatokat a köteten. 

A vállalati tervben szereplő csomópontonként járnak, és minden csomópont kap egy napi adatok támogatás. A vállalat díjszabási csomaggal van szó, a fent a belefoglalt támogatás okozhatnak adatokat. Ha az Operations Management Suite használ, a vállalati terv kell kiválasztani. 

Lásd: az a régió és aktuális árak [Application Insights árképzési](http://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> A április 2018 azt [bevezetett](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) Azure figyelési új árképzési modellt. Ez a modell egy egyszerű "használatalapú" modell a teljes kaphat a szolgáltatások figyelése fogad el. További információ a [új árképzési modellt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hogy a [ebben a modellben való áthelyezése hatásának értékelése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján és [hogyan programba az új modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>A vállalati terv működése

* Az egyes csomópontok által küldött telemetriai alkalmazások a vállalati tervben kell fizetnie.
 * A *csomópont* egy fizikai vagy virtuális gép vagy egy platform,--szolgáltatás szerepkör-példányhoz, amelyen az alkalmazást.
 * Fejlesztési gépek, az ügyfélböngészők és a mobileszközök nem számítanak csomópontokként megjelölve.
 * Ha az alkalmazás által küldött telemetriai adatok, például egy webszolgáltatás-bővítmény és a háttér-feldolgozók több összetevőt az összetevőket külön bájtjai számítanak.
 * [Élő Stream metrikák](app-insights-live-stream.md) adatok díjszabási célra nem számítanak. Az előfizetés a költségek csomópontonként, nem alkalmazásonkénti vannak. Ha rendelkezik, amely 12 vonatkozó telemetriai adatokat küldhet az öt csomóponttal alkalmazások, az elsők között csak az öt csomóponttal.
* Bár a havi díjak korlátozott akkor van szó, csak a minden órában, amelyben egy csomópont telemetriai adatokat küld az alkalmazásokból. Az óránkénti kell fizetni osztva a 744 (a 31 napos hónap órák száma) idézőjelekbe foglalt havi kell fizetni.
* Az egyes csomópontok (az óránkénti részletességű) észlelt a adatok kötet foglalási, 200 MB naponkénti kap. Nem használt adatok foglalási nem átkerülnek egy nap után a Tovább gombra.
 * Ha árképzés terv a vállalat úgy dönt, minden egyes előfizetés lekérdezi a napi támogatás telemetriai adatokat küldhet az Application Insights-erőforrások, az adott előfizetés csomópontok száma alapján. Tehát ha által küldött adatokat minden nap az öt csomóponttal rendelkezik, akkor kell egy készletezett támogatás az adott előfizetés összes Application Insights-erőforrások alkalmazott 1 GB. Nem számít, ha az egyes csomópontok más csomópontok-nál több adatot küldjön, mivel a befoglalt adatok meg vannak osztva az összes csomópont között. Ha egy adott napon az Application Insights-erőforrások jelenik meg több adat ehhez az előfizetéshez a napi adatok-foglalás szerepel, a / GB-os keretét adatok díjak vonatkoznak. 
 * A napi adatok támogatás számítása a napra (UTC) órák száma, hogy minden csomópont küld-e a telemetriai adatok és a 200 MB 24 osztva. Igen, ha négy csomópont telemetriai 15 nap 24 óra során küldött, az adott napon adatát lenne ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Adatok túlhasználati 2.30-as USD / GB ár az elsők között lenne 1,15 USD Ha a csomópontok küldött 1 GB adatot adott napon.
 * A vállalati terv napi támogatás az alkalmazásokkal, amelynek választotta az alapszintű csomag nincs megosztva. Használaton kívüli támogatás nem átvitt napi. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Példák különböző csomópont számának meghatározása

| Forgatókönyv                               | Teljes napi száma |
|:---------------------------------------|:----------------:|
| 1 alkalmazás 3 Azure App Service-példány és az 1 virtuális kiszolgáló használatával | 4 |
| 3, 2 virtuális gép; futó alkalmazások az Application Insights-erőforrások, az alkalmazás: ugyanazt az előfizetést, és a vállalati tervben | 2 | 
| 4 alkalmazások, amelyek alkalmazások Insights-erőforrások vannak ugyanahhoz az előfizetéshez; minden alkalmazást 16 csúcsidőn 2 példányok és 4 példányok 8 csúcsidőben | 13.33 | 
| Az 1 feldolgozói szerepkör és 1 webes szerepkör, minden futó 2 példányait cloud services csomag | 4 | 
| Azure Service Fabric-fürt 5-csomópont futtató 50 mikroszolgáltatások; minden egyes mikroszolgáltatási 3 példánya fut | 5|

* A pontos csomópont számbavételi függ, mely Application Insights SDK az alkalmazás használatával. 
  * Az SDK 2.2 és újabb verziók, mind az Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) és a [webes SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) minden alkalmazásgazda csomópontként jelentést. Többek között a számítógép fizikai kiszolgáló és a Virtuálisgép-gazdák vagy a példány nevét felhőszolgáltatásai számára.  Az egyetlen kivétel ez alól csak használó alkalmazások a [.NET Core](https://dotnet.github.io/) és az Application Insights Core SDK. Ebben az esetben csak egy csomópont esetén jelentést kap minden gazdagép, mert az állomás neve nem érhető el. 
  * Az SDK korábbi verzióiban a [webes SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) SDK újabb verziók, viselkedik, de a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) csak egy csomópont függetlenül alkalmazásgazdája számát jelenti. 
  * Ha az alkalmazás az SDK-t beállítani **roleInstance** egyéni értékre, alapértelmezés szerint ugyanezt az értéket azt határozza meg, csomópontok száma. 
  * Egy új SDK-verzió az ügyfélgépek és a mobileszközök futtatott alkalmazáshoz használata, a csomópontok száma egy számot, amely túl nagy (az ügyfél gépek vagy a mobil eszközök nagy számú) miatt előfordulhat, hogy vissza. 
