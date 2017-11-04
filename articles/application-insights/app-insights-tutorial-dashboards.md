---
title: "Hozzon létre egyéni irányítópultok Azure Application Insights |} Microsoft Docs"
description: "Az oktatóanyag segítséget nyújt egyéni KPI-irányítópultot létrehozni Azure Application insights szolgáltatással."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 0d2f98ca2fb39289b2916ddd24590924856507d6
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Egyéni KPI-irányítópultot létrehozni Azure Application insights szolgáltatással

Létrehozhat több Irányítópulton az Azure portálon, minden include csempék megjeleníteni az adatokat a több Azure-erőforrások másik erőforráscsoport-sablonok és előfizetések között.  Különböző diagramok és nézeteket, amely átfogó képet állapotát és az alkalmazás teljesítményének a egyéni irányítópultok létrehozásához Azure Application Insights is rögzíti.  Ez az oktatóanyag bemutatja, hogyan kell létrehozni egy egyéni irányítópultot, amely tartalmazza az adatokat és képi megjelenítéseket Azure Application Insights többféle típusú.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egyéni irányítópult létrehozása az Azure-ban
> * Vegye fel a csempe a csempe gyűjteményből
> * Standard metrikákat az Application Insights hozzáadása az irányítópult 
> * Egyéni metrika diagram Application Insights hozzáadása az irányítópulton
> * Adja hozzá az elemzési lekérdezések eredményeit az irányítópulton 



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Az Azure-bA egy .NET-alkalmazás központi telepítése és [engedélyezze az Application Insights SDK](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása
Egyazon irányítópultra több alkalmazás, erőforráscsoport-sablonok és előfizetések származó erőforrásokat is tartalmaznak.  Az oktatóanyag először hozzon létre egy új irányítópult az alkalmazáshoz.  

2.  A portál fő képernyőn válassza ki a **új irányítópult**.

    ![Új irányítópult](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Írja be az irányítópult nevét.
4. Tekintse meg a következő a **csempe gyűjtemény** számos különböző csempék, amelyeket az irányítópulton való rögzítéséhez.  A gyűjteményből csempék hozzáadásán rögzíthető diagramok és más nézetekhez, közvetlenül az Application Insights az irányítópulton.
5. Keresse meg a **Markdown** csempén, és húzza rá az irányítópult be.  Ez a csempe markdown, ami ideális leíró szöveg hozzáadása az irányítópult formázott szöveget hozzáadását teszi lehetővé.
6. Szöveg hozzáadása a csempe tulajdonságait, és méretezze át a irányítópult vásznon.
    
    ![Szerkessze a markdown csempe](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Kattintson a **végzett Testreszabás** az csempe testreszabási módjából való kilépéshez a képernyő tetején, majd **változtatásokat** menti a módosításokat.

    ![Markdown csempe az irányítópulton](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Adja hozzá a rendszerállapot – áttekintés
Egy irányítópultot, amelynek csak statikus szöveg nem nagyon fontos, most vegye fel a csempe az Application Insights az alkalmazással kapcsolatos információk megjelenítéséhez.  Az Application Insights csempéket is hozzáadhat a csempe gyűjteményből, vagy közvetlenül az Application Insights képernyők rögzítheti őket.  Ez lehetővé teszi, hogy konfigurálja a diagramok és a nézetek, amelynek már ismeri a őket az irányítópulton való rögzítés előtt.  Indítsa el a szabványos állapotának áttekintése az alkalmazáshoz való hozzáadásával.  Ez a konfiguráció nem igényel, és lehetővé teszi, hogy a minimális testreszabási az irányítópulton.


1. Válassza ki **Application Insights** az Azure menüjében, és válassza ki az alkalmazást.
2. Az a **áttekintése ütemterv**, válassza ki a helyi menüt, és kattintson a **rögzítés az irányítópulton**.  A csempe hozzáadása az utolsó irányítópultot, amely a megtekintett.  

    ![PIN-kód áttekintése ütemterv](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. Kattintson a képernyő tetején **irányítópult nézet** az irányítópulton való visszatéréshez.
4. Az Áttekintés ütemterv ezzel hozzáadja az irányítópulton való rögzítéséhez.  Kattintson és húzza azt egy helyen, majd **végzett Testreszabás** és **változtatásokat**.  Az irányítópult most már rendelkezik egy csempe, amely néhány hasznos információkat.

    ![Az Áttekintés ütemterv irányítópult](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Egyéni metrika diagram hozzáadása
A **metrikák** lehetővé teszi a diagramot az Application Insights által gyűjtött időbeli választható szűrők és csoportosítási metrikát.  Mint minden más, az Application Insightsban Ez a diagram az irányítópultot is hozzáadhat.  Ehhez szükség egy kis testreszabási kezdeni.

1. Válassza ki **Application Insights** az Azure menüjében, és válassza ki az alkalmazást.
1. Válassza ki **metrikák**.  
2. Egy üres diagram már létezik, és adja hozzá a metrika kéri.  Adja hozzá a metrikát a diagram, és opcionálisan adja hozzá a szűrő és csoportosítása.  Az alábbi példában látható sikeres szerint csoportosítva kiszolgálói kérelmek számát jelenti.  Ez biztosítja, hogy a sikeres és sikertelen kérelmek futó nézetét.

    ![A metrika hozzáadása](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Válassza ki a diagram és válassza ki a helyi menüt **rögzítés az irányítópulton**.  A nézet hozzáadása az utolsó irányítópultot, amellyel dolgozott.

    ![PIN-kód metrika diagram](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. Kattintson a képernyő tetején **irányítópult nézet** az irányítópulton való visszatéréshez.

4. Az Áttekintés ütemterv ezzel hozzáadja az irányítópulton való rögzítéséhez.  Kattintson és húzza azt egy helyen, majd **végzett Testreszabás** , majd **változtatásokat**. 

    ![a metrikák irányítópult](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Metrikaböngésző
**Metrikaböngésző** mely hasonló csatlakozva lehetővé teszi a jóval több testreszabási az irányítópulton való felvételekor.  Graph a metrikákat, amelyekkel az adott beállításokat szabályozó és követelményeitől függ.

1. Válassza ki **Application Insights** az Azure menüjében, és válassza ki az alkalmazást.
1. Válassza ki **Metrikaböngésző**. 
2. A diagram szerkesztése, és válassza ki egy vagy több metrikákat, és opcionálisan részletes konfigurációs kattintson ide.  A példa egy grafikonon nyomon követése lap átlagos válaszideje.
3. A felső Rajzszög ikonra kattintva vegye fel a diagram az irányítópulton, és húzza pozíciója.

    ![Metrikaböngésző](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. A Metrikaböngésző csempe lehetővé teszi, hogy további testreszabási az irányítópulton való hozzáadása után.  Kattintson jobb gombbal a csempe, és válasszon **cím szerkesztése** egyéni cím megadásához.  Lépjen tovább, és az egyéb testreszabásokat, ha azt szeretné.

    ![A metrikaböngésző irányítópult](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Most már rendelkezik a Metrikaböngésző diagram hozzáadva az irányítópulton való rögzítéséhez.

    ![A metrikaböngésző irányítópult](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Adja hozzá az Analytics-lekérdezés
Az Azure Application Insights Analytics biztosít egy részletes lekérdező nyelv, amely lehetővé teszi az adatok elemzése az Application Insights gyűjtött.  Hasonlóan a diagramok és más nézetekhez adja hozzá az Analytics-lekérdezés kimenetét az irányítópulton való rögzítéséhez.   

Mivel Azure alkalmazások Insights Analytics egy külön szolgáltatási, kell ahhoz, hogy a közé tartoznak az elemzési lekérdezések irányítópult megosztása.  Egy Azure-irányítópultot megosztásakor közzététel-erőforrásként egy Azure elérhetővé teheti azt más felhasználókat és erőforrásokat.  

1. Kattintson az irányítópultot tetején **megosztás**.

    ![Irányítópult közzététele](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Tartsa a **irányítópult neve** a azonos, és válassza ki a **előfizetés neve** az irányítópult megosztására.  Kattintson a **Publish** (Közzététel) gombra.  Az irányítópult mostantól elérhető egyéb szolgáltatásokkal és előfizetésekkel.  Opcionálisan meghatározhatja adott kell hozzáféréssel rendelkező felhasználók számára az irányítópulton.
1. Válassza ki **Application Insights** az Azure menüjében, és válassza ki az alkalmazást.
2. Kattintson a **Analytics** a Analytics portál megnyitása a képernyő tetején.

    ![Indítsa el elemzés](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Írja be a következő lekérdezést, ami a felső 10 leginkább kért lapok és a kérelmek számát adja vissza:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Kattintson a **Ugrás** érvényesítése a lekérdezés eredményeit.
5. A rögzítés ikonjára, és válassza ki az irányítópult nevét.  Az oka, hogy ezt a beállítást rendelkezik, akkor válasszon ki egy irányítópultot, az előző lépést, ha az utolsó irányítópulton használt eltérően nem, mert a Analytics konzol egy külön szolgáltatás, és válassza ki az összes elérhető megosztott irányítópultról kell.

    ![PIN-kód Analytics-lekérdezés](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Ahhoz, hogy lépjen vissza az irányítópulthoz, adja hozzá a egy másik lekérdezést, de ezúttal megjelenítheti Diagram, így a különböző módszereket megjelenítése az irányítópulton az Analytics-lekérdezés.  Indítsa el a következő lekérdezést, amely a legtöbb kivételével a felső 10 műveleteket foglalja össze.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Válassza ki **diagram** majd módosítsa a **perec** a kimeneti megjelenítéséhez.

    ![Diagramra](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. A rögzítés ikonjára kattintva kitűzheti a diagramot az Irányítópultjára, és ezúttal jelölje ki a hivatkozásra kattintva térjen vissza az irányítópulthoz.
4. A lekérdezések eredményeit most hozzáadódnak a formátuma, hogy a kijelölt irányítópulthoz.  Kattintson és húzza az egyes egy helyen, majd **szerkesztésének befejezése után**.
5. Kattintson a jobb gombbal a csempéket, és válassza ki **cím szerkesztése** érdekében, hogy azok egy leíró nevet.

    ![Az Analytics irányítópult](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Kattintson a **változtatásokat** véglegesíteni a módosításokat az irányítópulton való rögzítéséhez, hogy most már a különböző diagramok és az Application Insights képi megjelenítéseket tartalmaz.


## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte egyéni irányítópultok létrehozásához, rendelkezik a többi az Application Insights dokumentációját, beleértve az olyan esettanulmánynak tekinthető meg.

> [!div class="nextstepaction"]
> [A részletes diagnosztikai](app-insights-devops.md)