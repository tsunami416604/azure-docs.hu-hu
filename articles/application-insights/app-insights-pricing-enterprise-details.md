---
title: Örökölt nagyvállalati csomag díjszabásáról az Azure Application Insights |} A Microsoft Docs
description: Telemetria kötetek kezelése és figyelése az Application Insights költségeit.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: 820c1baaf920dc2ada2aba1a4a8cc3b11ab2db2d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726814"
---
# <a name="enterprise-plan-details"></a>Nagyvállalati csomag részletei

Az Azure Application Insights két díjcsomagok rendelkezik: alapszintű és vállalati. A [alapszintű](app-insights-pricing.md) tarifacsomagjának található alapértelmezett csomagjában. Tartalmazza az összes vállalati csomag szolgáltatásai, további költségek nélkül. A Basic csomag számlák elsősorban a, betöltött adatok mennyiségét. 

A vállalati csomag csomópontonkénti használati díj tartozik, és minden csomópont kap egy napi adatkeret. A vállalati díjszabási, díjkötelesek a csomagban foglalt adatkeret felett betöltött adatokért. Ha az Operations Management Suite használ, a vállalati csomagot kell választania. 

Jelenlegi díjak a pénznem és a régiót, lásd: [Application Insights díjszabásával](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> A 2018 április hogy [bevezetett](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) egy új díjszabási modellben az Azure monitoring. Ez a modell között a szolgáltatások teljes portfólióját fogad el egy egyszerű "használatalapú" modellt. Tudjon meg többet a [új díjszabási modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hogy a [ebben a modellben való áttérés a következmények felmérésében](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján és [hogyan választható, az új modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>A vállalati csomag működése

* Minden egyes csomópont, amely az alkalmazások telemetriai adatokat küld a nagyvállalati csomaghoz kell fizetnie.
 * A *csomópont* egy fizikai vagy virtuális gép vagy a platform--szolgáltatásként szerepkörpéldány, amelyen az alkalmazást.
 * Fejlesztői gépek, az ügyfelek böngészőin és a mobileszközök nem számítanak csomópontnak is.
 * Ha az alkalmazás által küldött telemetriai adatok, például egy webszolgáltatás és a egy háttérbeli feldolgozó több összetevőt az összetevőket külön-külön számoljuk.
 * [Élő metrikák Stream](app-insights-live-stream.md) adatok célokra díjszabás után nem kell fizetnie. Az előfizetéshez a költségek csomópontonként, nem az egyes alkalmazások vannak. Ha 12 a telemetriai adatokat küldő öt csomóponttal rendelkezik alkalmazások, a díj az öt csomópont van.
* Bár a havi díjak korlátozott kell fizetnie, csak az adott órán belül, ahol egy csomópont telemetriai adatokat küld egy alkalmazásból. A óradíjat számítunk fel a határolójeles havi díj 744 (31 napos hónap órák száma) elosztva.
* Egy kötet adatelosztás 200 MB / nap van megadva, az egyes csomópontok (az óránkénti részletességgel) észlelt. Nem használt adatok foglalási nem veszi át egy napot a következő.
 * Ha a vállalati díjszabási, az egyes előfizetésekhez beolvasása egy napi adatkeret meg, hogy telemetriát küldjön az Application Insights-erőforrások, az adott előfizetésben csomópontok száma alapján. Tehát ha öt csomópont által küldött adatokat minden nap, fog összevont kerete 1 GB-os alkalmazandó az összes Application Insights-erőforrások, az adott előfizetésben. Nem számít, hogy egyes csomópontok más csomópontoknál több adatot küldeni, mert a szolgáltatási keretbe foglalt adatmennyiség megoszlik az összes csomópont. Ha az adott napon, az Application Insights-erőforrások kapja meg több adatot tartalmaz a napi szintű adatelosztás ehhez az előfizetéshez, a kereten túli GB-onkénti díjak érvényesek. 
 * A napi adatkeretet számítjuk ki, hogy a nap (UTC használatával) órák száma, hogy egyes csomópontok 200 MB-TAL megszorozza 24 osztva telemetriát küld-e. Tehát ha négy csomóponton, a nap 24 órás 15 telemetriai adatokat küldő, a szolgáltatási keretbe foglalt adatmennyiség az adott napra lenne ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Az adatok keretet GB-onként 2.30-as USD díj ellenében a díjat, ha a csomópontok 1 GB adat küldése adott napon lenne 1,15 USD-t.
 * A vállalati csomag napi adatkeret ne oszthassák meg olyan alkalmazások, amelynek az alapszintű csomagot választotta. A fel nem használt juttatás nem vihetők el egymástól. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Példák a különálló csomópontok száma meghatározása

| Forgatókönyv                               | Teljes napi csomópontok száma |
|:---------------------------------------|:----------------:|
| 1 alkalmazás 3 Azure App Service-példányt és 1 virtuális kiszolgáló használatával | 4 |
| 2 virtuális gépen; 3 alkalmazásokról ezekhez az alkalmazásokhoz az Application Insights-erőforrások a következők ugyanahhoz az előfizetéshez, és a nagyvállalati csomaghoz | 2 | 
| 4 alkalmazások, amelyek Applications Insights-erőforrások ugyanabban az előfizetésben; vannak minden egyes 16 csúcsidőn 2 példány, és 8 csúcsidőben 4 példányok futó alkalmazás | 13.33 | 
| 1 feldolgozói szerepkör és 1 webes szerepkör, minden egyes 2 példánya fut a cloud services | 4 | 
| Az Azure Service Fabric-fürt 5 csomópontos 50 mikroszolgáltatások; fut Mindegyik mikroszolgáltatás 3 példánya fut | 5|

* A pontos csomópontok számában attól függ, mely az Application Insights SDK az alkalmazás használatával. 
  * Az SDK 2.2 és újabb verziók, az Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) és a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) jelentés minden egyes csomópontot gazda. Például a fizikai kiszolgáló és a Virtuálisgép-gazdagépek a számítógép nevét vagy a példány nevét, a cloud services.  Az egyetlen kivétel, csak használó alkalmazás a [.NET Core](https://dotnet.github.io/) és az Application Insights Core SDK-t. Ebben az esetben csak egy csomópont jelentett minden gazdagép esetén, mert az állomás neve nem érhető el. 
  * Az SDK korábbi verziói a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) viselkedik az SDK újabb verziók, de a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) jelentések csak egy csomópont, alkalmazás-gazdagépekkel számától függetlenül. 
  * Ha az alkalmazás az SDK-t beállítani **roleInstance** egyéni értékké, alapértelmezés szerint, hogy ugyanazt az értéket meghatározására szolgál csomópontok száma. 
  * Ha egy új SDK-verziót használja, hogy fut az ügyfélgépek és a mobileszközök, a csomópontok száma egy számot, amely nagyon nagy (miatt az ügyfélgépek és a mobileszközök nagy számú) előfordulhat, hogy vissza. 
