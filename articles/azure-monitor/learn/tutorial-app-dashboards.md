---
title: Egyéni irányítópultok létrehozása az Azure Application Insightsban | Microsoft Docs
description: Oktatóanyag egyéni KPI-irányítópultok Azure Application Insights használatával való létrehozásához.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: a08e00b2debce970bdbd385b785806c8636e5946
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318419"
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

- Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../app/asp-net.md)-t.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása
Egyazon irányítópult több alkalmazáshoz, erőforráscsoporthoz és előfizetéshez tartozó erőforrásokat is tartalmazhat.  Az oktatóanyag első lépéseként hozzon létre egy új irányítópultot az alkalmazásához.  

1. Az irányítópult ablaktáblán válassza az **új irányítópult**lehetőséget.

   ![Új irányítópult](media/tutorial-app-dashboards/1newdashboard.png)

1. Írja be az irányítópult nevét.
1. Tekintse át a **Csempekatalógust**, amelyben az irányítópultra rögzíthető számos különböző csempe található.  A csempék katalógusból való hozzáadásán kívül a diagramokat és egyéb nézeteket is rögzíthet közvetlenül Application Insightsról az irányítópultra.
1. Keresse meg a **Markdown** csempét, és húzza az irányítópultra.  Ez a csempe lehetővé teszi a Markdown-ben formázott szöveg hozzáadását, amely ideális a leíró szöveg az irányítópulthoz való hozzáadásához.
1. Adjon hozzá szöveget a csempe tulajdonságaiban, és méretezze át a csempét az irányítópult vásznán.
    
    ![Markdown-csempe szerkesztése](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kattintson a Testreszabás **kész** elemre a képernyő tetején a csempe testreszabási módjának kilépéséhez.

## <a name="add-health-overview"></a>Állapotáttekintés hozzáadása
A statikus szöveget tartalmazó irányítópultok nem nagyon érdekesek, ezért az alkalmazással kapcsolatos információk megjelenítéséhez vegyen fel egy csempét a Application Insightsból.  Az Application Insights-csempéket hozzáadhatja a csempekatalógusból, vagy közvetlenül az Application Insights-képernyőkről is rögzítheti őket.  Így a már ismert diagramokat és nézeteket konfigurálhatja, mielőtt rögzítené őket az irányítópulton.  Először adja hozzá az alkalmazás standard állapotáttekintését.  Ez nem igényel konfigurálást, és minimális testreszabási lehetőségeket biztosít az irányítópulton.


1. Válassza ki az **Application Insights** erőforrást a kezdőképernyőn.
2. Az **Áttekintés** ablaktáblán kattintson a rögzítés ikonra ![ PIN-kód ikonra, ](media/tutorial-app-dashboards/pushpin.png) hogy hozzáadja a csempét az utoljára megtekintett irányítópulthoz.  
 
3. A jobb felső sarokban egy értesítés jelenik meg, amelyet a csempe rögzített az irányítópulton. Kattintson a **rögzítve az irányítópultra** az értesítésben, hogy visszatérjen az irányítópulthoz, vagy használja az irányítópult ablaktáblát.
4. Ezzel a csempével most már hozzá lesz adva az irányítópulthoz. A csempe elhelyezésének módosításához válassza a **Szerkesztés** lehetőséget. Kattintson és húzza rá a helyére, majd kattintson a **Testreszabás kész**lehetőségre. Az irányítópulton ezzel már van egy olyan csempe, amely hasznos információkat tartalmaz.

    ![Áttekintő idővonal az irányítópulton](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Egyéni metrikadiagram hozzáadása
A **Metrika** panel segítségével az Application Insights által gyűjtött metrikák időbeli alakulását mutató diagramokat készíthet választható szűrőkkel és csoportosítással.  Ahogy az Application Insights minden más elemét, ezt a diagramot is felveheti az irányítópultra.  Ehhez előbb szükség van némi testreszabásra.

1. Válassza ki a **Application Insights** erőforrást a kezdőképernyőn.
1. Válassza a **Metrikák** lehetőséget.  
2. Már létrejött egy üres diagram, és most a rendszer kéri, hogy adjon hozzá egy metrikát.  Adjon hozzá egy metrikát a diagramhoz, valamint igény szerint szűrőt és csoportosítást is.  Az alábbi példában a kiszolgálókérelmek száma látható sikeresség szerint csoportosítva.  Ez a sikeres és sikertelen kérelmek akkumulált nézetét mutatja.

    ![Metrika hozzáadása](media/tutorial-app-dashboards/metrics.png)

4. A jobb oldalon válassza a **rögzítés az irányítópulton** lehetőséget. Ez hozzáadja a nézetet a legutóbb használt irányítópulthoz.

3.  A jobb felső sarokban egy értesítés jelenik meg, amelyet a csempe rögzített az irányítópulton. Kattintson a **rögzítve az irányítópultra** az értesítésben, hogy visszatérjen az irányítópulthoz, vagy használja az irányítópult panelt.

4. Ezzel a csempével most már hozzá lesz adva az irányítópulthoz. A csempe elhelyezésének módosításához válassza a **Szerkesztés** lehetőséget. Kattintson és húzza rá a helyére, majd kattintson a **Testreszabás kész**lehetőségre.

## <a name="add-logs-analytics-query"></a>Naplók hozzáadása (Analytics) lekérdezés
Az Azure Application Insights-naplók (Analytics) sokoldalú lekérdezési nyelvet biztosítanak, amely lehetővé teszi az összes összegyűjtött adatok elemzését Application Insights. A diagramokhoz és egyéb nézetekhez hasonlóan a naplók kimenetét is hozzáadhatja az irányítópulthoz.

Mivel az Azure Applications-adatelemzési naplók (Analitika) egy különálló szolgáltatás, meg kell osztania az irányítópultot, hogy tartalmazza a naplók lekérdezését. Amikor megoszt egy Azure-irányítópultot, azt Azure-erőforrásként teszi közzé, amely más felhasználók és erőforrások számára is elérhetővé válik.  

1. Az irányítópult képernyőjének felső részén kattintson a **Megosztás** elemre.

    ![Irányítópult közzététele](media/tutorial-app-dashboards/8dashboard-share.png)

2. Hagyja változatlanul az **Irányítópult nevét**, és válassza ki az **Előfizetés nevét** az irányítópult megosztásához.  Kattintson a **Közzététel** gombra.  Az irányítópult mostantól elérhető más szolgáltatások és előfizetések számára.  Meghatározhat adott felhasználókat, akiknek hozzáférést kíván biztosítani az irányítópulthoz.
1. Válassza ki a **Application Insights** erőforrást a kezdőképernyőn.
2. Kattintson a **naplók (Analitika)** elemre a figyelés elemnél a naplók (Analytics) portál megnyitásához.
3. Írja be a következő lekérdezést, amely a 10 leggyakrabban lekérdezett lapot és a kérelmek számát adja vissza:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. A lekérdezés eredményeinek ellenőrzéséhez kattintson a **Futtatás** gombra.
5. Kattintson a rögzítés ikonra ![Gombostű ikon](media/tutorial-app-dashboards/pushpin.png) majd válassza ki az irányítópult nevét. Ennek a beállításnak az az oka, hogy egy irányítópultot olyan módon választ ki, amely a legutóbbi irányítópult használatának korábbi lépéseitől eltérően van, mivel a naplók (analitikai) konzol egy különálló szolgáltatás, és az összes elérhető megosztott irányítópult közül ki kell választania.

5. Mielőtt visszalépjen az irányítópultra, adjon hozzá egy másik lekérdezést, de ezúttal diagramként jeleníti meg, így láthatja, hogyan jelenítheti meg a naplók lekérdezéseit az irányítópulton. Indítsa el a következő lekérdezést, amely a 10 legtöbb kivétellel járó műveletet összesíti.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Válassza a **Diagram** lehetőséget, majd váltson a **Perec** típusra a kimenet megjelenítéséhez.

    ![Naplók (analitikai) diagram](media/tutorial-app-dashboards/11querychart.png)

6. Kattintson a rögzítés ikonra ![Gombostű ikon](media/tutorial-app-dashboards/pushpin.png) kattintson a jobb felső sarokban a diagram irányítópultra való rögzítéséhez, és ezúttal válassza a hivatkozást az irányítópultra való visszatéréshez.
4. A lekérdezések eredményei most megjelennek az irányítópulton a választott formátumban.  Kattintson és húzza az egyeseket a pozícióba, majd kattintson a **Testreszabás kész**lehetőségre.
5. Válassza a ceruza ikont ![Ceruza ikon](media/tutorial-app-dashboards/pencil.png) minden címben adjon meg egy leíró címet.

5. Válassza a **megosztás** lehetőséget, ha újra közzé szeretné tenni a módosításokat az irányítópulton, amely mostantól számos diagramot és vizualizációt tartalmaz a Application Insightsból.


## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett az egyéni irányítópultok létrehozásával, tekintse át az Application Insights dokumentációját, amely egy esettanulmányt is tartalmaz.

> [!div class="nextstepaction"]
> [Részletes diagnosztika](../app/devops.md)

