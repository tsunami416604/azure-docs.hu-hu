---
title: Egyéni irányítópultok létrehozása az Azure Application Insightsban | Microsoft Docs
description: Oktatóanyag egyéni KPI-irányítópultok Azure Application Insights használatával való létrehozásához.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: caf8b1899cad95ade6297e78e8f2cf35939ef189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661634"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Egyéni KPI irányítópultok létrehozása az Azure Application Insights használatával

Több irányítópultot is létrehozhat az Azure Portalon, amelyeknek a csempéi különböző erőforráscsoportokban és előfizetésekben található Azure-erőforrások adatait jelenítik meg.  Különböző Azure Application Insights-diagramok és -nézetek rögzítésével egyéni irányítópultokat hozhat létre, amelyek átfogó képet mutatnak az alkalmazás állapotáról és teljesítményéről. Ez az oktatóanyag bemutatja, hogyan lehet az Azure Application Insightsból származó többféle adatot és vizualizációt tartalmazó egyéni irányítópultokat létrehozni.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egyéni irányítópult létrehozása az Azure-ban
> * Csempe hozzáadása a csempekatalógusból
> * Standard Application Insights-metrikák hozzáadása az irányítópulthoz
> * Egyéni Application Insights-metrikadiagramok hozzáadása az irányítópulthoz
> * Naplók (Analytics) lekérdezés eredményeinek hozzáadása az irányítópulthoz



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../../azure-monitor/app/asp-net.md)-t.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása
Egyazon irányítópult több alkalmazáshoz, erőforráscsoporthoz és előfizetéshez tartozó erőforrásokat is tartalmazhat.  Az oktatóanyag első lépéseként hozzon létre egy új irányítópultot az alkalmazásához.  

1. Az irányítópult ablaktábláján válassza az **Új irányítópult**lehetőséget.

   ![Új irányítópult](media/tutorial-app-dashboards/1newdashboard.png)

1. Írja be az irányítópult nevét.
1. Tekintse át a **Csempekatalógust**, amelyben az irányítópultra rögzíthető számos különböző csempe található.  A csempék katalógusból való hozzáadása mellett közvetlenül az Application Insightsból rögzítheti a diagramokat és más nézeteket az irányítópulton.
1. Keresse meg a **Markdown** csempét, és húzza az irányítópultra.  Ez a csempe lehetővé teszi, hogy markdown formátumban formázott szöveget adjon hozzá, ami ideális leíró szöveg hozzáadásához az irányítópulthoz.
1. Adjon hozzá szöveget a csempe tulajdonságaiban, és méretezze át a csempét az irányítópult vásznán.
    
    ![Markdown-csempe szerkesztése](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kattintson a képernyő tetején a **Kész testreszabás gombra** a csempe testreszabási módjából való kilépéshez.

## <a name="add-health-overview"></a>Állapotáttekintés hozzáadása
A statikus szöveget tartalmazó irányítópult nem túl érdekes, ezért most adjon hozzá egy csempét az Application Insightsból az alkalmazással kapcsolatos információk megjelenítéséhez.  Az Application Insights-csempéket hozzáadhatja a csempekatalógusból, vagy közvetlenül az Application Insights-képernyőkről is rögzítheti őket.  Így a már ismert diagramokat és nézeteket konfigurálhatja, mielőtt rögzítené őket az irányítópulton.  Először adja hozzá az alkalmazás standard állapotáttekintését.  Ez nem igényel konfigurálást, és minimális testreszabási lehetőségeket biztosít az irányítópulton.


1. Válassza ki az **Application Insights-erőforrást** a kezdőképernyőn.
2. Az **Áttekintő** ablaktáblán ![kattintson a pinikon tűjének ikonjára,](media/tutorial-app-dashboards/pushpin.png) ha hozzá szeretné adni a csempét az utoljára megtekintett irányítópulthoz.  
 
3. A jobb felső sarokban megjelenik egy értesítés arról, hogy a csempe az irányítópulton lett rögzítve. Kattintson a **Rögzített irányítópultra** az értesítésben az irányítópultra való visszatéréshez vagy az irányítópult ablaktáblájának használatához.
4. Ez a csempe most hozzáadódik az irányítópulthoz. A Mozaik elhelyezésének módosításához válassza a **Szerkesztés** lehetőséget. Kattintson a helyére, és húzza a helyére, majd kattintson a **Kész testreszabás gombra.** Az irányítópulton ezzel már van egy olyan csempe, amely hasznos információkat tartalmaz.

    ![Áttekintő idővonal az irányítópulton](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Egyéni metrikadiagram hozzáadása
A **Metrika** panel segítségével az Application Insights által gyűjtött metrikák időbeli alakulását mutató diagramokat készíthet választható szűrőkkel és csoportosítással.  Ahogy az Application Insights minden más elemét, ezt a diagramot is felveheti az irányítópultra.  Ehhez előbb szükség van némi testreszabásra.

1. Válassza ki az **Application Insights-erőforrást** a kezdőképernyőn.
1. Válassza a **Metrika** lehetőséget.  
2. Már létrejött egy üres diagram, és most a rendszer kéri, hogy adjon hozzá egy metrikát.  Adjon hozzá egy metrikát a diagramhoz, valamint igény szerint szűrőt és csoportosítást is.  Az alábbi példában a kiszolgálókérelmek száma látható sikeresség szerint csoportosítva.  Ez a sikeres és sikertelen kérelmek akkumulált nézetét mutatja.

    ![Metrika hozzáadása](media/tutorial-app-dashboards/metrics.png)

4. Válassza **a Rögzítés az irányítópulthoz** lehetőséget a jobb oldalon. Ez hozzáadja a nézetet a legutóbb használt irányítópulthoz.

3.  A jobb felső sarokban megjelenik egy értesítés arról, hogy a csempe az irányítópulton lett rögzítve. Kattintson a **Rögzített irányítópultra** az értesítésben az irányítópulton való visszatéréshez vagy az irányítópult paneljének használatához.

4. Ez a csempe most hozzáadódik az irányítópulthoz. A Mozaik elhelyezésének módosításához válassza a **Szerkesztés** lehetőséget. Kattintson a helyére, és húzza a helyére, majd kattintson a **Kész testreszabás gombra.**

## <a name="add-logs-analytics-query"></a>Naplók (Analytics) hozzáadása lekérdezés
Az Azure Application Insights-naplók (Analytics) gazdag lekérdezési nyelvet biztosít, amely lehetővé teszi az Összes összegyűjtött Application Insights által gyűjtött adatok elemzését. A diagramokhoz és más nézetekhez hasonlóan a naplólekérdezés kimenetét is hozzáadhatja az irányítópulthoz.

Mivel az Azure Applications Insights Naplók (Analytics) egy külön szolgáltatás, meg kell osztania az irányítópultot, hogy egy naplólekérdezést tartalmazzon. Amikor megoszt egy Azure-irányítópultot, közzéteheti azt Azure-erőforrásként, amely elérhetővé teheti azt más felhasználók és erőforrások számára.  

1. Az irányítópult képernyőjének felső részén kattintson a **Megosztás** elemre.

    ![Irányítópult közzététele](media/tutorial-app-dashboards/8dashboard-share.png)

2. Hagyja változatlanul az **Irányítópult nevét**, és válassza ki az **Előfizetés nevét** az irányítópult megosztásához.  Kattintson a **Publish** (Közzététel) gombra.  Az irányítópult mostantól elérhető más szolgáltatások és előfizetések számára.  Meghatározhat adott felhasználókat, akiknek hozzáférést kíván biztosítani az irányítópulthoz.
1. Válassza ki az **Application Insights-erőforrást** a kezdőképernyőn.
2. Kattintson **a Naplók (Analytics)** a bal oldalon a figyelés alatt a Naplók (Analytics) portál megnyitásához.
3. Írja be a következő lekérdezést, amely a 10 leggyakrabban lekérdezett lapot és a kérelmek számát adja vissza:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. A lekérdezés eredményének ellenőrzéséhez kattintson a **Futtatás** gombra.
5. Kattintson a pin ikonra ![Gombostű ikon](media/tutorial-app-dashboards/pushpin.png) és válassza ki az irányítópult nevét. Ennek az esetben az előző lépésekkel ellentétben, ahol az utolsó irányítópultot használták, az az oka annak, hogy ez a beállítás egy irányítópultot választ, mivel a Naplók (Analytics) konzol külön szolgáltatás, és az összes elérhető megosztott irányítópult közül kell választania.

5. Mielőtt visszatérne az irányítópultra, adjon hozzá egy másik lekérdezést, de ezúttal jelenítse meg diagramként, hogy láthassa a naplók lekérdezésének megjelenítésének különböző módjait az irányítópulton. Indítsa el a következő lekérdezést, amely a 10 legtöbb kivétellel járó műveletet összesíti.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Válassza a **Diagram** lehetőséget, majd váltson a **Perec** típusra a kimenet megjelenítéséhez.

    ![Naplók (Analytics) diagram](media/tutorial-app-dashboards/11querychart.png)

6. Kattintson a pin ikonra ![Gombostű ikon](media/tutorial-app-dashboards/pushpin.png) a jobb felső sarokban a diagram ot az irányítópultra rögzítheti, és ezúttal válassza ki az irányítópultra való visszatéréshez kívánt hivatkozást.
4. A lekérdezések eredményei most megjelennek az irányítópulton a választott formátumban.  Kattintson és húzza az egyes eket a helyére, majd kattintson **a Kész testreszabás gombra.**
5. A ceruza ikon jának kijelölése ![Ceruza ikon](media/tutorial-app-dashboards/pencil.png) minden cím, hogy nekik egy leíró címet.

5. Válassza **a Megosztás** lehetőséget, ha újra közzé szeretné tenni a módosításokat az irányítópulton, amelyek mostantól számos diagramot és vizualizációt tartalmaznak az Application Insightsból.


## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az egyéni irányítópultok létrehozásával, tekintse át az Application Insights dokumentációját, amely egy esettanulmányt is tartalmaz.

> [!div class="nextstepaction"]
> [Részletes diagnosztika](../../azure-monitor/app/devops.md)
