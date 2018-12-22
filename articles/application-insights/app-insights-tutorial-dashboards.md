---
title: Egyéni irányítópultok létrehozása az Azure Application Insightsban | Microsoft Docs
description: Oktatóanyag egyéni KPI-irányítópultok Azure Application Insights használatával való létrehozásához.
keywords: ''
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: a67f8c0f6be1c388c4138ee0269d7ad8b76d8c4c
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744331"
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

- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../azure-monitor/app/asp-net.md)-t. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása
Egyazon irányítópult több alkalmazáshoz, erőforráscsoporthoz és előfizetéshez tartozó erőforrásokat is tartalmazhat.  Az oktatóanyag első lépéseként hozzon létre egy új irányítópultot az alkalmazásához.  

2.  A portál fő képernyőjén válassza az **Új irányítópult** lehetőséget.

    ![Új irányítópult](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Írja be az irányítópult nevét.
4. Tekintse át a **Csempekatalógust**, amelyben az irányítópultra rögzíthető számos különböző csempe található.  Amellett, hogy a katalógusból is hozzáadhat csempéket, közvetlenül az Application Insightsból is rögzíthet diagramokat és egyéb nézeteket az irányítópultra.
5. Keresse meg a **Markdown** csempét, és húzza az irányítópultra.  Ezzel a csempével Markdown-formátumú szövegeket adhat az irányítópulthoz, ami ideális a leíró szövegekhez.
6. Adjon hozzá szöveget a csempe tulajdonságaiban, és méretezze át a csempét az irányítópult vásznán.
    
    ![Markdown-csempe szerkesztése](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. A képernyő tetején a **Testreszabás kész** elemre kattintva lépjen ki a testreszabási módból, majd a **Módosítások közzététele** gombra kattintva mentse a módosításokat.

    ![Markdown csempe az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Állapotáttekintés hozzáadása
A csak statikus szövegeket tartalmazó irányítópult nem valami érdekes, ezért most adjunk hozzá egy Application Insights-csempét az alkalmazással kapcsolatos információk megjelenítésére.  Az Application Insights-csempéket hozzáadhatja a csempekatalógusból, vagy közvetlenül az Application Insights-képernyőkről is rögzítheti őket.  Így a már ismert diagramokat és nézeteket konfigurálhatja, mielőtt rögzítené őket az irányítópulton.  Először adja hozzá az alkalmazás standard állapotáttekintését.  Ez nem igényel konfigurálást, és minimális testreszabási lehetőségeket biztosít az irányítópulton.


1. Válassza az **Application Insights** elemet az Azure menüjében, majd válassza ki az alkalmazást.
2. Az **Áttekintő idővonalon** válassza a helyi menüt, és kattintson a **Rögzítés az irányítópulton** elemre.  Ez hozzáadja a csempét az utolsó megtekintett irányítópulthoz.  

    ![Áttekintő idővonal rögzítése](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. A képernyő tetején kattintson **Az irányítópult megtekintése** elemre az irányítópultra való visszatéréshez.
4. Az Áttekintő idővonal most már hozzá van adva az irányítópulthoz.  Kattintson rá, húzza a helyére, majd kattintson a **Testreszabás kész** és a **Módosítások közzététele** elemre.  Az irányítópulton ezzel már van egy olyan csempe, amely hasznos információkat tartalmaz.

    ![Áttekintő idővonal az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Egyéni metrikadiagram hozzáadása
A **Metrika** panel segítségével az Application Insights által gyűjtött metrikák időbeli alakulását mutató diagramokat készíthet választható szűrőkkel és csoportosítással.  Ahogy az Application Insights minden más elemét, ezt a diagramot is felveheti az irányítópultra.  Ehhez előbb szükség van némi testreszabásra.

1. Válassza az **Application Insights** elemet az Azure menüjében, majd válassza ki az alkalmazást.
1. Válassza a **Metrika** lehetőséget.  
2. Már létrejött egy üres diagram, és most a rendszer kéri, hogy adjon hozzá egy metrikát.  Adjon hozzá egy metrikát a diagramhoz, valamint igény szerint szűrőt és csoportosítást is.  Az alábbi példában a kiszolgálókérelmek száma látható sikeresség szerint csoportosítva.  Ez a sikeres és sikertelen kérelmek akkumulált nézetét mutatja.

    ![Metrika hozzáadása](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Nyissa meg a diagram helyi menüjét, és kattintson a **Rögzítés az irányítópulton** elemre.  Ez hozzáadja a nézetet a legutóbb használt irányítópulthoz.

    ![Metrikadiagram rögzítése](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. A képernyő tetején kattintson **Az irányítópult megtekintése** elemre az irányítópultra való visszatéréshez.

4. Az Idővonal metrikadiagram most már hozzá van adva az irányítópulthoz. Kattintson rá, húzza a helyére, és kattintson a **Testreszabás kész**, majd a **Módosítások közzététele** elemre. 

    ![Metrika az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Metrikaböngésző
A **Metrikaböngésző** a Metrika panelhez hasonlóan működik, azonban jóval több testreszabási lehetőséget kínál az irányítópultra felvéve.  Az Ön igényeitől függ, hogy melyiket használja a metrikák megjelenítéséhez.

1. Válassza az **Application Insights** elemet az Azure menüjében, majd válassza ki az alkalmazást.
1. Válassza a **Metrikaböngésző** lehetőséget. 
2. Kattintson a diagramra a szerkesztéséhez, és válasszon ki egy vagy több metrikát, valamint igény szerint egy részletes konfigurációt.  A példában egy vonaldiagram látható, amely a lapok átlagos válaszidejét mutatja.
3. A jobb felső sarokban a rögzítés ikonra kattintva adja hozzá a diagramot az irányítópulthoz, majd húzza a helyére.

    ![Metrikaböngésző](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. A Metrikaböngésző csempe további testreszabási lehetőségeket biztosít, miután hozzáadta az irányítópulthoz.  Kattintson a jobb gombbal a csempére, és válassza a **Cím szerkesztése** lehetőséget egy egyéni cím megadásához.  Igény szerint hajtson végre egyéb módosításokat.

    ![Metrikaböngésző az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. A Metrikaböngésző diagram hozzá lett adva az irányítópulthoz.

    ![Metrikaböngésző az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Analytics-lekérdezés hozzáadása
Az Azure Application Insights Analytics egy részletes lekérdezési nyelvet biztosít, amellyel az Application Insights által gyűjtött minden adatot elemezhet.  A diagramokhoz és egyéb nézetekhez hasonlóan az Analytics-lekérdezések kimenetei is felvehetők az irányítópultra.   

Mivel az Azure Applications Insights Analytics egy külön szolgáltatás, meg kell osztania az irányítópultot, ha Analytics-lekérdezéseket is meg kíván jeleníteni rajta.  Amikor megoszt egy Azure-irányítópultot, Azure-erőforrásként teszi közzé, így más felhasználók és erőforrások számára is hozzáférhetnek.  

1. Az irányítópult képernyőjének felső részén kattintson a **Megosztás** elemre.

    ![Irányítópult közzététele](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Hagyja változatlanul az **Irányítópult nevét**, és válassza ki az **Előfizetés nevét** az irányítópult megosztásához.  Kattintson a **Publish** (Közzététel) gombra.  Az irányítópult mostantól elérhető más szolgáltatások és előfizetések számára.  Meghatározhat adott felhasználókat, akiknek hozzáférést kíván biztosítani az irányítópulthoz.
1. Válassza az **Application Insights** elemet az Azure menüjében, majd válassza ki az alkalmazást.
2. Kattintson az **Analytics** elemre a képernyő tetején az Analytics-portál megnyitásához.

    ![Az Analytics indítása](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Írja be a következő lekérdezést, amely a 10 leggyakrabban lekérdezett lapot és a kérelmek számát adja vissza:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Kattintson az **Indítás** elemre a lekérdezés eredményeinek ellenőrzéséhez.
5. Kattintson a rajzszög ikonra, és válassza ki az irányítópult nevét.  Az előző lépésekkel ellentétben, amelyek mind az utolsó irányítópultot használták, ebben a beállításban ki kell választania az irányítópultot, mivel az Analytics-konzol egy külön szolgáltatás, és az összes elérhető megosztott irányítópult közül kell választania.

    ![Analytics-lekérdezés rögzítése](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Mielőtt visszalépne az irányítópultra, adjon hozzá egy másik lekérdezést, ezúttal azonban diagramként jelenítse meg, hogy láthassa az Analytics-lekérdezések irányítópulton való megjelenítésének különböző módszereit.  Indítsa el a következő lekérdezést, amely a 10 legtöbb kivétellel járó műveletet összesíti.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Válassza a **Diagram** lehetőséget, majd váltson a **Perec** típusra a kimenet megjelenítéséhez.

    ![Analytics-diagram](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. A rajzszög ikonra kattintva rögzítse a diagramot az irányítópultra, majd ezúttal a hivatkozásra kattintva lépjen vissza az irányítópultra.
4. A lekérdezések eredményei most megjelennek az irányítópulton a választott formátumban.  Kattintson az egyes diagramokra, és húzza őket a helyükre, majd kattintson a **Szerkesztés kész** elemre.
5. Kattintson a jobb gombbal az egyes csempékre, és a **Cím szerkesztése** elemre kattintva adjon mindegyiknek egy leíró nevet.

    ![Analytics-adatok az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. A **Módosítások közzététele** elemre kattintva véglegesítheti a módosításokat az irányítópulton, amely most már különböző Application Insights-diagramokat és -vizualizációkat tartalmaz.


## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az egyéni irányítópultok létrehozásával, tekintse át az Application Insights dokumentációját, amely egy esettanulmányt is tartalmaz.

> [!div class="nextstepaction"]
> [Részletes diagnosztika](app-insights-devops.md)
