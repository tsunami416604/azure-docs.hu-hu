---
title: "1Az adathasználat elemzése a Log Analyticsben | Microsoft Docs"
description: "A Log Analytics Használat lapján megtekintheti az OMS szolgáltatásnak elküldött adatok mennyiségét."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/11/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4c1d0319e768eec69718d8b49db30069ec1331d9


---
# <a name="analyze-data-usage-in-log-analytics"></a>Az adathasználat elemzése a Log Analyticsben
Az Operations Management Suite (OMS) Log Analytics összetevője összegyűjti az adatokat, és rendszeresen elküldi őket az OMS szolgáltatásnak.  A **Használat** lapon megtekinthető, hogy mennyi adat érkezik az OMS szolgáltatáshoz. Az is látható a **Használat** lapon, hogy a megoldások mennyi adatot küldenek, és a kiszolgálók milyen gyakran küldenek adatokat.

> [!NOTE]
> Ha az [OMS webhelyen](http://www.microsoft.com/oms) létrehozott ingyenes fiókot használ, naponta legfeljebb 500 MB adatot küldhet az OMS szolgáltatásnak. Ha eléri a napi korlátot, az adatelemzés leáll, és másnap folytatódik. Előfordulhat továbbá, hogy újra kell küldenie azokat az adatokat, amelyeket az OMS nem fogadott vagy nem dolgozott fel.
> 
> 

Az eddigi használatot megtekintheti az OMS **Áttekintés** irányítópultjának **Használat** csempéjén.

![A Használat csempe](./media/log-analytics-usage/usage-tile.png)

Ha túllépte vagy megközelítette a napi használati korlátot, valamelyik megoldás eltávolításával csökkentheti az OMS szolgáltatásnak küldött adatmennyiséget. A megoldások eltávolításáról az [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a Megoldástárból) című cikk nyújt tájékoztatást.

![A Használat irányítópult](./media/log-analytics-usage/usage-dashboard.png)

A **Használat** lapon az alábbi információk találhatók meg:

* Napi átlagos használat
* Az egyes megoldások adathasználata az elmúlt 30 napban
* A környezetbe tartozó kiszolgálók által az OMS szolgáltatásnak elküldött adatok mennyisége az elmúlt 30 napban
* Az adatforgalmi csomag tarifacsomagja és becsült költsége
* A szolgáltatói szerződésre (SLA-ra) vonatkozó információk, azt is beleértve, hogy mennyi ideig tart az OMS-nek az adatok feldolgozása

## <a name="to-work-with-usage-data"></a>A használati adatok használata
1. Az **Áttekintés** lapon kattintson a **Használat** csempére.
2. A **Használat** lapon tekintse meg az Önt legjobban érdeklő használati kategóriákat.
3. Ha valamelyik megoldása túl sokat fogyaszt a napi feltöltési kvótából, akkor esetleg eltávolíthatja azt a megoldást.

## <a name="to-view-your-estimated-cost-and-billing-information"></a>A becsült költség- és számlázási adatok megtekintése
1. Az **Áttekintés** lapon kattintson a **Használat** csempére.
2. Kattintson a **Használat** lap **Használat** területén, a **Becsült költség** felirat mellett látható sávnyílra (**>**).
3. A kibontott **Az Ön adatforgalmi díjcsomagja** területen láthatja a becsült havi költséget.  
    ![Az Ön adatforgalmi díjcsomagja](./media/log-analytics-usage/usage-data-plan.png)
4. Ha látni szeretné a számlázási adatokat, jelenítse meg az előfizetés adatait a **Számla megtekintése** elemre kattintva.
   * Az előfizetések lapján kattintson az előfizetésre. Ennek hatására megjelennek a részletes adatok, valamint a használat tételes listája.  
       ![előfizetést](./media/log-analytics-usage/usage-sub01.png)
   * Az előfizetés Összegzés lapján különféle műveletek állnak rendelkezésére, amelyekkel kezelheti az előfizetést, és további részleteket jeleníthet meg róla.  
       ![Az előfizetés részletei](./media/log-analytics-usage/usage-sub02.png)

## <a name="to-view-data-batches-for-your-sla"></a>Az SLA adatkötegeinek megtekintése
1. Az **Áttekintés** lapon kattintson a **Használat** csempére.
2. A **Szolgáltatásiszint-szerződés** területen kattintson az **SLA részleteinek letöltése** elemre.
3. Letöltődik egy Excel XLSX-fájl, amelynek tartalmát áttekintheti.  
    ![SLA részletei](./media/log-analytics-usage/usage-sla-details.png)

## <a name="next-steps"></a>Következő lépések
* Részletesen megismerheti a megoldások által gyűjtött adatokat a [Log searches in Log Analytics](log-analytics-log-searches.md) (Naplóbeli keresés a Log Analyticsben) című cikkből.




<!--HONumber=Nov16_HO2-->


