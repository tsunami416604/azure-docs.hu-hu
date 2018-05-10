---
title: Felhasználói munkamenet és esemény elemzése a Azure Application Insights |} Microsoft docs
description: Webes alkalmazása felhasználóit demográfiai elemzése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 3ac738d6b3c0f1f3579a9b644a03a01f1509173a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Az Application Insightsban felhasználók munkamenetek és események elemzés

Annak megállapítása, amikor személyek használja a webalkalmazás oldalak azok még legtöbb érdekli, ahol a felhasználók is található, és mit böngészők és az operációs rendszerek használnak. Üzleti és használati telemetriai elemezhet [Azure Application Insights](app-insights-overview.md).

![Application Insights felhasználók képernyőképe](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>Bevezetés

Ha még nem látja a felhasználók, munkamenetek vagy események paneleket az Application Insights portálon adatok [megtudhatja, hogyan lásson a használati eszközök](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A felhasználók, a munkamenetek és az események Szegmentálás eszköz

Három a használati paneleken eszközzel a azonos szeletelésére és feldarabolására használnak telemetriai adatokat a webes alkalmazás három szempontok alapján. Szűrés és a felosztás az adatokat, a különböző oldalakhoz és a szolgáltatások relatív használati észrevételeket fedik le.

* **Felhasználó-eszköz**: hányan használt, az alkalmazás és annak szolgáltatásait.  Felhasználók számlálási böngésző cookie-kban tárolt névtelen azonosítók használatával. A különböző böngészők vagy gépek használatával egy személy, egynél több felhasználó beleszámítanak.
* **Munkamenetek eszköz**: a felhasználói tevékenység hány munkamenetek kiterjed bizonyos lapok és az alkalmazás funkcióit. A munkamenet felhasználói tétlen fél óra múlva, vagy folyamatos használat 24 óra múlva számít.
* **Események eszköz**: milyen gyakran használt bizonyos lapok és az alkalmazás funkcióit. Egy nézet számít Ha egy böngészőben lap betöltésekor az alkalmazásból, feltéve hogy [tagolva azt](app-insights-javascript.md). 

    Egyéni esemény egy előfordulását valami történik az alkalmazás, gyakran egy felhasználói beavatkozást, például egy gombra kattintson vagy bizonyos feladatok végrehajtása a jelöli. Az alkalmazás kódja szúr be [egyéni események generálásához](app-insights-api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Az egyes felhasználók lekérdezése

Megismerkedhet a különböző felhasználói csoportokhoz a felhasználók eszköz tetején lekérdezési lehetőségekkel beállításával:

* Megjelenítése: Válassza ki a felhasználók egy kohorszok elemzéséhez.
* Használja ki: válassza ki az egyéni események és lapmegtekintés.
* Közben: Egy időtartományt válasszon.
* Által: Bucket az adatokat, vagy egy adott időn belül, vagy egy másik tulajdonságot, mint például a böngésző vagy a város módjának kiválasztása.
* Felosztott által: Egy tulajdonság amellyel vegyes vagy szegmens kívánt adatok kiválasztásához. 
* Szűrők felvétele: A lekérdezés egyes felhasználókhoz, munkamenetek vagy azok tulajdonságait, például a böngésző vagy a város alapján események korlátozza. 
 
## <a name="saving-and-sharing-reports"></a>És jelentések megosztása 
Mentheti felhasználók jelentéseket, vagy a saját csak akkor a jelentések szakaszban, vagy megosztott bárki más, a megosztott jelentések szakaszban az Application Insights-erőforráshoz való hozzáférés.

Ahhoz, hogy a felhasználók, munkamenetek vagy események jelentési; hivatkozás Kattintson a **megosztás** eszköztárán, majd másolja a hivatkozást.

Ahhoz, hogy a felhasználók, a munkamenetek és a események; az adatok másolatát Kattintson a **megosztás** eszköztárán, majd kattintson a **Word ikon** az adatokat egy Word-dokumentumot létrehozni. Vagy kattintson a **Word ikon** a fő diagram felett.

## <a name="meet-your-users"></a>Felel meg a felhasználók

A **felel meg a felhasználók** információval is az aktuális lekérdezés egyező körülbelül öt próbafelhasználók szakasz. Figyelembe véve, és az egyéni felhasználók számára, mellett összesítések, viselkedéseinek felfedezése ténylegesen használatának az alkalmazás áttekintést biztosít.

## <a name="next-steps"></a>További lépések

- Ahhoz, hogy a használati tapasztalatok, küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [lapmegtekintés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha egyéni események vagy Lapmegtekintések már küld, megismerkedhet a használati eszközök további, a szolgáltatás használatát a felhasználók.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Munkafüzetek](app-insights-usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)