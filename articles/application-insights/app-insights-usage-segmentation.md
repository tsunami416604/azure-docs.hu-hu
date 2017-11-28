---
title: "Felhasználói munkamenet és esemény elemzése a Azure Application Insights |} Microsoft docs"
description: "Webes alkalmazása felhasználóit demográfiai elemzése."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 04efb82addd0f307c68c73e28e46b602e5bc194a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Az Application Insightsban felhasználók munkamenetek és események elemzés

Ismerje meg a webalkalmazás személyek használatakor, azok még legtöbb érdekli, ahol a felhasználók találhatók oldalak, milyen böngészők és operációs rendszerek használnak. Üzleti és használati telemetriai elemezhet [Azure Application Insights](app-insights-overview.md).

## <a name="get-started"></a>Bevezetés

Ha még nem látja a felhasználók, munkamenetek vagy események paneleket az Application Insights portálon adatok [megtudhatja, hogyan lásson a használati eszközök](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A felhasználók, a munkamenetek és az események Szegmentálás eszköz

Három a használati paneleken eszközzel a azonos szeletelésére és feldarabolására használnak telemetriai adatokat a webes alkalmazás három szempontok alapján. Szűrés és a felosztás az adatokat, a különböző oldalakhoz és a szolgáltatások relatív használati észrevételeket fedik le.

* **Felhasználó-eszköz**: hányan használt, az alkalmazás és annak szolgáltatásait.  Felhasználók számlálási böngésző cookie-kban tárolt névtelen azonosítók használatával. A különböző böngészők vagy gépek használatával egy személy, egynél több felhasználó beleszámítanak.
* **Munkamenetek eszköz**: a felhasználói tevékenység hány munkamenetek kiterjed bizonyos lapok és az alkalmazás funkcióit. A munkamenet tétlenség fél óra múlva, vagy használja a folyamatos 24 órás követően számít.
* **Események eszköz**: milyen gyakran használt bizonyos lapok és az alkalmazás funkcióit. Egy nézet számít Ha egy böngészőben lap betöltésekor az alkalmazásból, feltéve hogy [tagolva azt](app-insights-javascript.md). 

    Egyéni esemény egy előfordulását valami történik az alkalmazás, gyakran egy felhasználói beavatkozást, például egy gombra kattintson vagy bizonyos feladatok végrehajtása a jelöli. Az alkalmazás kódja szúr be [egyéni események generálásához](app-insights-api-custom-events-metrics.md#trackevent).

![Használati eszköz](./media/app-insights-usage-segmentation/users.png)

## <a name="querying-for-certain-users"></a>Az egyes felhasználók lekérdezése 

Megismerkedhet a különböző felhasználói csoportokhoz a felhasználók eszköz tetején lekérdezési lehetőségekkel beállításával: 

* Használja ki: válassza ki az egyéni események és lapmegtekintés. 
* Közben: Egy időtartományt válasszon. 
* Által: Bucket az adatokat, vagy egy adott időn belül, vagy egy másik tulajdonságot, mint például a böngésző vagy a város módjának kiválasztása. 
* Felosztott által: Egy tulajdonság amellyel vegyes vagy szegmens kívánt adatok kiválasztásához. 
* Szűrők felvétele: A lekérdezés egyes felhasználókhoz, munkamenetek vagy azok tulajdonságait, például a böngésző vagy a város alapján események korlátozza. 
 
## <a name="saving-and-sharing-reports"></a>És jelentések megosztása 
Mentheti felhasználók jelentéseket, vagy a saját csak akkor a jelentések szakaszban, vagy megosztott bárki más, a megosztott jelentések szakaszban az Application Insights-erőforráshoz való hozzáférés.  
 
A jelentés mentése vagy a tulajdonságainak szerkesztése során válassza a "Jelenlegi relatív időtartomány" való mentse a jelentést a rendszer folyamatosan frissített adatokat, ha visszalép néhány rögzített időn.  
 
Válassza ki a "Jelenlegi abszolút időtartomány" jelentés mentése a rögzített adatok vannak beállítva. Ne feledje, hogy az Application Insightsban csak tárolja, ha egy jelentés ezzel egy abszolút időtartomány óta eltelt legfeljebb 90 nappal 90 napra vonatkozó, a jelentés üresen jelenik meg. 
 
## <a name="example-instances"></a>Példa példányok

A példa példányok szakasz néhány olyan egyéni felhasználók számára, munkamenetek vagy eseményeket, amelyek megfelel-e az aktuális lekérdezés információkat jeleníti meg. Figyelembe véve, és az egyéni felhasználók számára, mellett összesítések, viselkedéseinek felfedezése ténylegesen használatának az alkalmazás áttekintést biztosít. 
 
## <a name="insights"></a>Insights 

Az elemzések oldalsávon jeleníti meg, amelyek közös tulajdonságokkal felhasználók nagy fürtjein. Ezeken a fürtökön felfedhetők meglepő trendeket használatának az alkalmazást. Ha például 40 %-ra az összes, az alkalmazás használatát egy szolgáltatás segítségével személyek származik.  


## <a name="next-steps"></a>Következő lépések
- Ahhoz, hogy a használati tapasztalatok, küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [lapmegtekintés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha egyéni események vagy Lapmegtekintések már küld, megismerkedhet a használati eszközök további, a szolgáltatás használatát a felhasználók.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Munkafüzetek](app-insights-usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)

