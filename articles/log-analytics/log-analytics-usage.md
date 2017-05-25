---
title: "1Az adathasználat elemzése a Log Analyticsben | Microsoft Docs"
description: "A Log Analytics használati irányítópultjával megtekintheti, hogy mennyi adatot küld a rendszer az OMS szolgáltatásnak."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 7e3d4b83fefdc70f292cf85b682cf8ed756bf4c5
ms.openlocfilehash: e7f04df679604f274c8ad9bf4daddc63c8b5418a
ms.contentlocale: hu-hu
ms.lasthandoff: 02/22/2017


---
# <a name="analyze-data-usage-in-log-analytics"></a>Az adathasználat elemzése a Log Analyticsben
A Log Analytics adatokat gyűjt, és rendszeres időközönként elküldi azokat az OMS szolgáltatásnak.  A **Log Analytics-használat** irányítópulton megtekintheti, hogy mennyi adatot küld a rendszer az OMS szolgáltatásnak. Az irányítópult azt is megmutatja, hogy mennyi adatot küldenek a megoldások, és hogy milyen gyakran küldenek adatokat a kiszolgálók.

> [!NOTE]
> Ha ingyenes fiókkal rendelkezik, naponta legfeljebb 500 MB adatot küldhet el az OMS szolgáltatásnak. Ha eléri a napi korlátot, az adatelemzés leáll, és a következő nap elején folytatódik. Ebben az esetben minden olyan adatot újra kell küldeni, amelyet az OMS nem fogadott el, vagy nem dolgozott fel.

Ha túllépte, vagy hamarosan eléri a napi használati korlátot, szükség szerint eltávolíthat egy megoldást, hogy csökkentse az OMS szolgáltatásnak elküldendő adatok mennyiségét. A megoldások eltávolításáról az [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a Megoldástárból) című cikk nyújt tájékoztatást.

![A Használat irányítópult](./media/log-analytics-usage/usage-dashboard01.png)

A **Log Analytics-használat** irányítópult az alábbi információkat jeleníti meg:

- Adatmennyiség
    - Adatmennyiség az idő függvényében (az aktuális időtartomány alapján)
    - Adatmennyiség megoldásonként
    - Adatok, amelyek nincsenek számítógéphez rendelve
- Számítógépek
    - Adatokat küldő számítógépek
    - Számítógépek, amelyek nem küldtek adatokat az elmúlt 24 órában
- Ajánlatok
    - Insight- és Analytics-csomópontok
    - Automation and Control-csomópontok
    - Biztonsági csomópontok
- Teljesítmény
    - Az adatok gyűjtésével és indexelésével eltelt idő
- Lekérdezések listája

## <a name="understanding-nodes-for-oms-offers"></a>OMS-ajánlatok csomópontjainak ismertetése

Amennyiben a *csomópontonkénti (OMS)* tarifacsomagban van, akkor a díjszabás az engedélyezett csomópontok és megoldások számán alapul. A használat adatait megjelenítő irányítópult *Ajánlatok* szakaszában tekintheti meg, hogy az egyes ajánlatok csomópontjaiból mennyi van használatban.

![A Használat irányítópult](./media/log-analytics-usage/log-analytics-usage-offerings.png)

## <a name="to-work-with-usage-data"></a>A használati adatok használata
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com) Azure-előfizetésével.
2. A **központi** menüben kattintson a **További szolgáltatások** elemre, majd az erőforrások listájába írja be a következőt: **Log Analytics**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Log Analytics** elemre.  
    ![Azure-központ](./media/log-analytics-usage/hub.png)
3. A **Log Analytics** irányítópultja megjeleníti a munkaterületek listáját. Jelöljön ki egy munkaterületet.
4. A *munkaterületek* irányítópultján kattintson a **Log Analytics-használat** elemre.
5. A **Log Analytics-használat** irányítópulton kattintson az **Idő: Elmúlt 24 óra** elemre az időintervallum módosításához.  
    ![időintervallum](./media/log-analytics-usage/time.png)
6. Tekintse meg a kívánt területeket megjelenítő használatikategória-paneleket. Válasszon ki egy panelt, majd kattintson az egyik elemére további részletek megtekintéséhez a [naplókeresésben](log-analytics-log-searches.md).  
    ![példa adathasználati panelre](./media/log-analytics-usage/blade.png)
7. A Naplók keresése irányítópulton tekintse meg a keresés eredményeit.  
    ![példa használati panelre a naplókeresésben](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Következő lépések
* A szolgáltatások és megoldások által összegyűjtött, és az OMS-nek elküldött részletes információkért tekintse meg [a Log Analyticsben végzett naplókereséssel kapcsolatos](log-analytics-log-searches.md) cikket.

