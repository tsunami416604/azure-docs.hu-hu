---
title: "Az Azure Application Insights az ügyfeleknek megértéséhez |} Microsoft Docs"
description: "Az oktatóanyag az Azure Application Insights megértése, hogyan használják az ügyfelek a az alkalmazás használatával."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: db61c300ad82270e59d315fa3372d9e4390c7a21
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Azure Application Insights segítségével megérteni a felhasználók hogyan használják az alkalmazást

Azure Application Insights gyűjti a használati adatok, amelyekkel jobban megértheti, hogy a felhasználók hogyan használják az alkalmazással.  Ez az oktatóanyag végigvezeti Önt a különböző erőforrások elérhetők ezek az információk elemzése.  Megtudhatja, hogyan:

> [!div class="checklist"]
> * Az alkalmazáshoz hozzáférő felhasználók adatainak elemzése
> * Munkamenet-információk segítségével elemezheti az ügyfelek hogyan használják az alkalmazást
> * Adja meg, amelyek lehetővé teszik a kívánt felhasználói tevékenység a tényleges tevékenységével összehasonlítása tölcsérek 
> * Hozzon létre egy munkafüzetet vonják össze a képi megjelenítések és a lekérdezések egyetlen dokumentumba
> * Csoport hasonló felhasználók azokat együtt
> * Ismerje meg, hogy mely felhasználók vannak állítja azt vissza az alkalmazás
> * Ellenőrizze, hogy a felhasználók hogyan navigálnak az alkalmazás


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
- Töltse le és telepítse a [Visual Studio pillanatkép hibakereső](http://aka.ms/snapshotdebugger).
- Az Azure-bA egy .NET-alkalmazás központi telepítése és [engedélyezze az Application Insights SDK](app-insights-asp-net.md). 
- [Telemetriai adatokat küldhet az alkalmazásból](app-insights-usage-overview.md#send-telemetry-from-your-app) egyéni események/Lapmegtekintések hozzáadásához
- Küldési [felhasználói környezet](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) nyomon követhető a felhasználó funkciója adott idő alatt, és a használati szolgáltatások teljesen használják.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>A felhasználók adatainak beolvasása
A **felhasználók** lehetővé teszi a felhasználók az sokféleképpen fontos tudnivalók ismertetése. A Vezérlőpult használatával megérthetik, hogy olyan adatokat, mint ahol a felhasználók csatlakozik, az ügyfél részletes adatait, és az alkalmazás milyen területeken próbál hozzáférni. 

1. Válassza ki **Application Insights** és majd jelölje ki az előfizetését.
2. Válassza ki **felhasználók** a menüben.
3. Az alapértelmezett nézet megjeleníti az egyedi felhasználók száma, amelyek az alkalmazás az elmúlt 24 óra során legalább egyszer csatlakoztak.  Módosítsa az időszak, és állítsa be ezeket az információkat szűrése különböző egyéb feltételeket.

    ![Lekérdezés-szerkesztő](media\app-insights-tutorial-users\QueryBuilder.png)

6. Kattintson a **során** legördülő menüből, és módosítsa a időszak 7 nap.  Ez növeli a panelen levő különböző diagramok szereplő adatok.

    ![Időtartomány módosítása](media\app-insights-tutorial-users\TimeRange.png)

4. Kattintson a **szerint** legördülő meghibásodás hozzáadása egy felhasználói tulajdonságot a diagramhoz.  Válassza ki **ország vagy régió**.  A grafikon ugyanazokat az adatokat tartalmaz, de lehetővé teszi a felhasználók az egyes országok számát részletes információkat.

    ![Ország vagy régió](media\app-insights-tutorial-users\CountryorRegion.png)

5. A kurzor fölé különböző sávok a diagramon, és vegye figyelembe, hogy a számláló az egyes országok tükrözi csak a időszak, amely képviseli.
6. Tekintse meg a következő a **Insights** oszlop, amely a felhasználó adatai elemzést jobb szélén.  Ez közös tulajdonságok is a felhasználói adatok jelentős biztosít információkat, például az az időszak, és a rekordok egyedi munkamenetek száma 

    ![Elemzések oszlop](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Felhasználói munkamenetek elemzése
A **munkamenetek** panel hasonlít a **felhasználók** panel.  Ha **felhasználók** segít megérteni a felhasználók elérésekor az alkalmazás részleteinek **munkamenetek** segít megérteni a azoknak a felhasználóknak az alkalmazás használatáról.  

1. Válassza ki **munkamenetek** a menüben.
2. Tekintse meg a következő a diagramon, és vegye figyelembe, hogy rendelkezik-e ugyanazokkal a beállításokkal, hogy szűrje és az adatok és a a **felhasználók** panel.

    ![Munkamenetek lekérdezés-szerkesztő](media\app-insights-tutorial-users\SessionsBuilder.png)

3. A **ezeket a munkameneteket mintát** a jobb oldali ablaktábla listázza a munkamenetek, amelyek nagyszámú események tartalmazzák.  Ezek a érdekes munkamenetek elemzéséhez.

    ![Ezeket a munkameneteket minta](media\app-insights-tutorial-users\SessionsSample.png)

4. A munkamenetet megtekintéséhez kattintson a **munkamenet ütemterv**, amely minden műveletet jeleníti meg a munkamenetek.  Ez segít például a nagy mennyiségű kivételek munkamenetek azonosítása.

    ![Munkamenetek ütemterv](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Egy hasonló felhasználók csoportba
A **Kohorszok** felhasználók groupd a hasonló jellemzőkkel készlete.  Adatok szűrése cohorts használhatja más paneleken, hogy lehetővé teszi a felhasználók adott csoportja elemzése.  Például előfordulhat, hogy elemezni szeretné csak olyan felhasználók, akik befejezte a vásárlást.

1.  Válassza ki **Cohorts** a menüben.
2.  Kattintson a **új** egy új kohorszok létrehozásához.
3.  Válassza ki a **ki a használt** legördülő menüből, és adja meg a műveletet.  Csak azok a felhasználók ezt a műveletet a jelentés a időszak belül elvégző is fog szerepelni.

    ![Megadott műveleteket végrehajtó kohorszok](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Válassza ki **felhasználók** a menüben.
5.  Az a **megjelenítése** legördülő menüben válassza ki az imént kohorszok létrehozni.  Az adatok a diagram azoknak a felhasználóknak korlátozódik.

    ![A felhasználó-eszköz kohorszok](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Hasonlítsa össze a valósághoz kívánt tevékenység
Az előző paneleken összpontosított Mi az alkalmazás felhasználók volt, miközben **tölcsérek** mit felhasználók nem szeretne összpontosítani.  A tölcsér ismertetett lépések az alkalmazás és a felhasználók, akik lépései közötti áthelyezése százalékos jelöli.  Például létrehozhatja a tölcsér, hogy a felhasználók az alkalmazás kapcsolódni termék keresési százalékos arányát méri.  Láthatja majd százalékos aránya adja hozzá a termék egy kosár a felhasználók, és azok, akik egy megvásárolhatja hány százalékát.

1. Válassza ki **tölcsérek** a menü majd **új**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Írja be a **neve tölcsér**.
3. Hozzon létre egy tölcsér egyes lépéseihez szükséges művelet kiválasztásával legalább két lépést.  A műveletek listájának Application Insights által gyűjtött használati adatok össze.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Kattintson a **mentése** a tölcsér mentéséhez, és nézze meg, az eredményeket.  Az ablak jobb oldalán a tölcsér az első tevékenység előtt és után utolsó tevékenysége, amelyekkel jobban megértheti az adott feladatütemezési körül tendenciák felhasználói feltárásához leggyakoribb eseményeket jeleníti meg.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Ismerje meg, hogy mely ügyfelek visszaadása
**Megőrzési** segítségével megismerheti, hogy mely felhasználók vissza érkeznek az alkalmazáshoz.  

1. Válassza ki **megőrzési** a menüben.
2. Alapértelmezés szerint az elemzett adatok tartalmazzák a felhasználók, akik hajtotta végre semmilyen műveletet, és ezután visszaérkezik bármely művelet elvégzésére.  Ez a szűrő bármely belefoglalási, például kizárólag olyan felhasználók, akik adott vissza, miután befejezte a vásárlást módosítható.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. A visszatérő felhasználó, a feltételeknek eleget tevő grafikus is látható, és az űrlap különböző idő időtartamok tábla.  A tipikus mintája fokozatos csökkenését ad vissza a felhasználók adott idő alatt van.  A következő egy időszak hirtelen eldobási aggodalomra lehet. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Felhasználói navigációs elemzése
A **felhasználói folyamat** visualizes a felhasználók hogyan keresse meg a lapok és az alkalmazás szolgáltatásai között.  Ennek segítségével választ kaphat például adott felhasználók általában át egy adott oldal, hogyan azok általában kilép az alkalmazásból, és vannak-e rendszeresen ismétlődő semmilyen műveletet.

1.  Válassza ki **felhasználói adatfolyamok** a menüben.
2.  Kattintson a **új** hozzon létre egy új felhasználói folyamat, majd **szerkesztése** a részleteinek szerkesztéséhez.
3.  Növelje a **időtartomány** 7 napra, majd válassza ki az első esemény.  A folyamat esemény kezdődő felhasználói munkamenetek fogja követni.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  A felhasználói folyamat jelenik meg, és megtekintheti a különböző felhasználói elérési útját, és a munkamenet-adatokra.  Kék vonal jelzi a felhasználó az aktuális művelet után végrehajtott művelet.  A piros sor a felhasználói munkamenet végét jelöli.

    ![](media\app-insights-tutorial-users\flows.png)

5.  A folyamat esemény eltávolításához kattintson a **x** a művelet, és kattintson a sarkában **létrehozása Graph**.  A diagram újrarajzolódik eltávolítja az esemény minden példányát.  Kattintson a **szerkesztése** megtekintéséhez, hogy az esemény ezzel hozzáadja **események kizárt**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Használati adatok összesítése
**Munkafüzetek** adatmegjelenítésekkel, elemzési lekérdezések és szöveges interaktív dokumentumok egységgé kombinálják.  Gyakori használati adatok egy csoportba, összesítik az egy adott eseményből származó információkat vagy visszajelzést a csapat a az alkalmazás használati munkafüzetek segítségével.

1.  Válassza ki **munkafüzetek** a menüben.
2.  Kattintson a **új** új munkafüzet létrehozásához.
3.  A lekérdezés már biztosított összes használati adatok az elmúlt nap során, a sávdiagram jelennek meg, amely tartalmazza.  Is ezzel a lekérdezéssel, manuálisan szerkessze, vagy kattintson a **lekérdezések minta** számára a más hasznos lekérdezések.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Kattintson a **szerkesztésének befejezése után**.
5.  Kattintson a **szerkesztése** a felső ablaktáblán, a szöveg elején található a munkafüzet szerkesztéséhez.  Ez van formázva a markdown formátum használatával.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Kattintson a **felhasználók hozzáadása az** hozzáadása egy grafikonon felhasználó adataival.  A grafikon adatainak szerkesztése, ha szeretné, és kattintson a **szerkesztésének befejezése után** menti.


## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a felhasználók elemzése, hogy értékről a következő oktatóanyag áttekintésével megismerheti, hogyan lehet ezeket az információkat és más hasznos adatokat az alkalmazással kapcsolatos egyéni-irányítópultot létrehozni.

> [!div class="nextstepaction"]
> [Egyéni irányítópultok létrehozása](app-insights-tutorial-dashboards.md)
