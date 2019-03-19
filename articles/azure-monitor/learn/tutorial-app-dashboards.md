---
title: Egyéni irányítópultok létrehozása az Azure Application Insightsban | Microsoft Docs
description: Oktatóanyag egyéni KPI-irányítópultok Azure Application Insights használatával való létrehozásához.
keywords: ''
services: application-insights
author: lgayhardt
ms.author: lagayhar
ms.date: 01/11/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 3abe0511200bf5828b485b15a4b8a512731c4ffa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082126"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Egyéni KPI irányítópultok létrehozása az Azure Application Insights használatával

Több irányítópultot is létrehozhat az Azure Portalon, amelyeknek a csempéi különböző erőforráscsoportokban és előfizetésekben található Azure-erőforrások adatait jelenítik meg.  Különböző Azure Application Insights-diagramok és -nézetek rögzítésével egyéni irányítópultokat hozhat létre, amelyek átfogó képet mutatnak az alkalmazás állapotáról és teljesítményéről.  Ez az oktatóanyag bemutatja, hogyan lehet az Azure Application Insightsból származó többféle adatot és vizualizációt tartalmazó egyéni irányítópultokat létrehozni.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egyéni irányítópult létrehozása az Azure-ban
> * Csempe hozzáadása a csempekatalógusból
> * Standard Application Insights-metrikák hozzáadása az irányítópulthoz 
> * Egyéni Application Insights-metrikadiagramok hozzáadása az irányítópulthoz
> * Analytics-lekérdezések eredményeinek hozzáadása az irányítópulthoz 



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../../azure-monitor/app/asp-net.md)-t. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása
Egyazon irányítópult több alkalmazáshoz, erőforráscsoporthoz és előfizetéshez tartozó erőforrásokat is tartalmazhat.  Az oktatóanyag első lépéseként hozzon létre egy új irányítópultot az alkalmazásához.  

1. Irányítópult panelen válassza ki a **új irányítópult**.

   ![Új irányítópult](media/tutorial-app-dashboards/1newdashboard.png)

1. Írja be az irányítópult nevét.
1. Tekintse át a **Csempekatalógust**, amelyben az irányítópultra rögzíthető számos különböző csempe található.  Amellett, hogy a katalógusból is hozzáadhat csempéket, közvetlenül az Application Insightsból is rögzíthet diagramokat és egyéb nézeteket az irányítópultra.
1. Keresse meg a **Markdown** csempét, és húzza az irányítópultra.  Ezzel a csempével Markdown-formátumú szövegeket adhat az irányítópulthoz, ami ideális a leíró szövegekhez.
1. Adjon hozzá szöveget a csempe tulajdonságaiban, és méretezze át a csempét az irányítópult vásznán.
    
    ![Markdown-csempe szerkesztése](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kattintson a **testreszabás kész** csempe testreszabási módból való kilépéshez a képernyő tetején.

## <a name="add-health-overview"></a>Állapotáttekintés hozzáadása
A csak statikus szövegeket tartalmazó irányítópult nem valami érdekes, ezért most adjunk hozzá egy Application Insights-csempét az alkalmazással kapcsolatos információk megjelenítésére.  Az Application Insights-csempéket hozzáadhatja a csempekatalógusból, vagy közvetlenül az Application Insights-képernyőkről is rögzítheti őket.  Így a már ismert diagramokat és nézeteket konfigurálhatja, mielőtt rögzítené őket az irányítópulton.  Először adja hozzá az alkalmazás standard állapotáttekintését.  Ez nem igényel konfigurálást, és minimális testreszabási lehetőségeket biztosít az irányítópulton.


1. Válassza ki a **Application Insights** erőforrás a kezdőképernyőn.
2. Az a **áttekintése** ablaktáblán kattintson a rajzszög ikonra a csempe hozzáadása a legutóbb használt megtekintett irányítópulthoz.  

    ![Áttekintő idővonal rögzítése](media/tutorial-app-dashboards/3overview.png)
 
3. A jobb felső sarokban található egy értesítés jelenik meg, hogy a csempét az irányítópulton rögzítve lett-e. Kattintson a **rögzítve az irányítópulton** lépjen vissza az irányítópultra, vagy az irányítópult ablaktábláját használja az értesítésben.
4. A csempére most már hozzá van adva az irányítópulthoz. Válassza ki **szerkesztése** a jelentésvásznon való elhelyezését a csempe módosításához. Kattintson és az informatikai húzza a helyére, majd **testreszabás kész**. Az irányítópulton ezzel már van egy olyan csempe, amely hasznos információkat tartalmaz.

    ![Áttekintő idővonal az irányítópulton](media/tutorial-app-dashboards/4dashboard-edit.png)



## <a name="add-custom-metric-chart"></a>Egyéni metrikadiagram hozzáadása
A **Metrika** panel segítségével az Application Insights által gyűjtött metrikák időbeli alakulását mutató diagramokat készíthet választható szűrőkkel és csoportosítással.  Ahogy az Application Insights minden más elemét, ezt a diagramot is felveheti az irányítópultra.  Ehhez előbb szükség van némi testreszabásra.

1. Válassza ki a **Application Insights** a kezdőképernyő-erőforrást.
1. Válassza a **Metrika** lehetőséget.  
2. Már létrejött egy üres diagram, és most a rendszer kéri, hogy adjon hozzá egy metrikát.  Adjon hozzá egy metrikát a diagramhoz, valamint igény szerint szűrőt és csoportosítást is.  Az alábbi példában a kiszolgálókérelmek száma látható sikeresség szerint csoportosítva.  Ez a sikeres és sikertelen kérelmek akkumulált nézetét mutatja.

    ![Metrika hozzáadása](media/tutorial-app-dashboards/5sumserverrequests.png)

4. Válassza ki **rögzítés az irányítópulton** a jobb oldalon. Ez hozzáadja a nézetet a legutóbb használt irányítópulthoz.

    ![Metrikadiagram rögzítése](media/tutorial-app-dashboards/6sumserverrequests-pin.png)

3.  A jobb felső sarokban található egy értesítés jelenik meg, hogy a csempét az irányítópulton rögzítve lett-e. Kattintson a **rögzítve az irányítópulton** lépjen vissza az irányítópultra, vagy az irányítópult panelen az értesítésben.

4. A csempére most már hozzá van adva az irányítópulthoz. Válassza ki **szerkesztése** a jelentésvásznon való elhelyezését a csempe módosításához. Kattintson és az informatikai húzza a helyére, majd **testreszabás kész**.

    ![Metrika az irányítópulton](media/tutorial-app-dashboards/7dashboard-edit2.png)

## <a name="add-analytics-query"></a>Analytics-lekérdezés hozzáadása
Az Azure Application Insights Analytics egy részletes lekérdezési nyelvet biztosít, amellyel az Application Insights által gyűjtött minden adatot elemezhet.  A diagramokhoz és egyéb nézetekhez hasonlóan az Analytics-lekérdezések kimenetei is felvehetők az irányítópultra.   

Mivel az Azure Applications Insights Analytics egy külön szolgáltatás, meg kell osztania az irányítópultot, ha Analytics-lekérdezéseket is meg kíván jeleníteni rajta. Amikor megoszt egy Azure-irányítópultot, Azure-erőforrásként teszi közzé, így más felhasználók és erőforrások számára is hozzáférhetnek.  

1. Az irányítópult képernyőjének felső részén kattintson a **Megosztás** elemre.

    ![Irányítópult közzététele](media/tutorial-app-dashboards/8dashboard-share.png)

2. Hagyja változatlanul az **Irányítópult nevét**, és válassza ki az **Előfizetés nevét** az irányítópult megosztásához.  Kattintson a **Publish** (Közzététel) gombra.  Az irányítópult mostantól elérhető más szolgáltatások és előfizetések számára.  Meghatározhat adott felhasználókat, akiknek hozzáférést kíván biztosítani az irányítópulthoz.
1. Válassza ki a **Application Insights** a kezdőképernyő-erőforrást.
2. Kattintson az **Analytics** elemre a képernyő tetején az Analytics-portál megnyitásához.

    ![Az Analytics indítása](media/tutorial-app-dashboards/9analytics.png)

3. Írja be a következő lekérdezést, amely a 10 leggyakrabban lekérdezett lapot és a kérelmek számát adja vissza:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Kattintson a **futtatása** érvényesítése a lekérdezés eredményeit.
5. Kattintson a rajzszög ikonra, és válassza ki az irányítópult nevét. Az előző lépésekkel ellentétben, amelyek mind az utolsó irányítópultot használták, ebben a beállításban ki kell választania az irányítópultot, mivel az Analytics-konzol egy külön szolgáltatás, és az összes elérhető megosztott irányítópult közül kell választania.

    ![Analytics-lekérdezés rögzítése](media/tutorial-app-dashboards/10query.png)

5. Mielőtt visszalépne az irányítópultra, adjon hozzá egy másik lekérdezést, ezúttal azonban diagramként jelenítse meg, hogy láthassa az Analytics-lekérdezések irányítópulton való megjelenítésének különböző módszereit.  Indítsa el a következő lekérdezést, amely a 10 legtöbb kivétellel járó műveletet összesíti.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Válassza a **Diagram** lehetőséget, majd váltson a **Perec** típusra a kimenet megjelenítéséhez.

    ![Analytics-diagram](media/tutorial-app-dashboards/11querychart.png)

6. A rajzszög ikonra kattintva rögzítse a diagramot az irányítópultra, majd ezúttal a hivatkozásra kattintva lépjen vissza az irányítópultra.
4. A lekérdezések eredményei most megjelennek az irányítópulton a választott formátumban.  Kattintson és húzzon minden egy helyen, majd **testreszabás kész**.
5. Kattintson a ceruza ikonra kattintva adjon mindegyiknek egy leíró címet minden egyes címére.

    ![Analytics-adatok az irányítópulton](media/tutorial-app-dashboards/12edit-title.png)

5. Válassza ki **megosztás** való ismételt közzétételéhez az irányítópulton, amely mostantól tartalmazza a különböző diagramok és Vizualizációk az Application Insights szolgáltatásból a módosításokat.


## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az egyéni irányítópultok létrehozásával, tekintse át az Application Insights dokumentációját, amely egy esettanulmányt is tartalmaz.

> [!div class="nextstepaction"]
> [Részletes diagnosztika](../../azure-monitor/app/devops.md)
