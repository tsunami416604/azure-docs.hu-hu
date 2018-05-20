---
title: Az Azure Application Insights használati cohorts |} Microsoft Docs
description: Más-más részhalmazához vagy felhasználók, munkamenetek, eseményeket és műveleteket, amelyek valamilyen közös elemzése
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: f8d566f552c86f749b914ffed70512209ad76ab7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights cohorts

Egy kohorszok olyan felhasználók, munkamenetek, eseményeket és műveleteket, amelyek valamilyen közös készlete. A Azure Application Insights részére a cohorts az elemzési lekérdezések alapján határozzák meg. Azokban az esetekben, melyekben egy adott elemzéséhez beállítása a felhasználók vagy események ismételten cohorts is rugalmasabban pontosan kíváncsiak vagyunk set Express.

![Cohorts ablaktábla](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Alapszintű szűrők és cohorts

Cohorts szűrők hasonló módon használt. De cohorts' definíciók beépített az egyéni elemzési lekérdezések úgy is sokkal alkalmazkodó és összetett. Szűrők, eltérően mentheti cohorts, más a csoport tagjai is felhasználhatja őket.

Megadhat egy kohorszok a felhasználók, akik az összes próbált meg az alkalmazás egy új funkciója. Az Application Insights-erőforrás a kohorszok mentheti. Akkor is könnyen mentett csoportját adott felhasználókat, a jövőben elemzéséhez.

> [!NOTE]
> Miután jönnek létre, a a felhasználók, a munkamenetek, az események és a felhasználó zajló kommunikációról eszközök cohorts érhetők el.

## <a name="example-engaged-users"></a>Példa: Részt vevő felhasználók

A csoport határozza meg egy minden felhasználó öt vagy több alkalommal az adott hónapban az alkalmazás minden felhasználójának. Ebben a szakaszban egy kohorszok minden felhasználó határozza meg.

1. Nyissa meg a Cohorts eszközt.

2. Válassza ki a **sablon gyűjtemény** fülre. Különböző cohorts sablonok gyűjteménye jelennek meg.

3. Válassza ki **minden--nappal használó felhasználók**.

    A kohorszok három paramétereinek van:
    * **Tevékenységek**, ha úgy dönt, mely eseményeket és az oldalmegtekintéseket számít a "használati."
    * **Időszak**, a hónap definíciója.
    * **UsedAtleastCustom**szám, ahányszor felhasználók kell használnia valami belül részt vevő számít.

4. Változás **UsedAtleastCustom** való **5 nap**, és hagyja **időszak** 28 napos alapértelmezett.

    ![Minden felhasználó](.\media\app-insights-usage-cohorts\003.png)

    Most a kohorszok jelenti. minden egyéni esemény vagy a lap nézet 5 küldi el az összes felhasználóazonosító külön nappal ezelőtti 28.

5. Kattintson a **Mentés** gombra.

   > [!TIP]
   >  Nevezze el a kohorszok, például "Engaged felhasználók (5 nap)." Mentse a "Saját jelentések" vagy "Jelentések, megosztás" attól függően, hogy más személyek, akiknek a kohorszok megjelenítéséhez az Application Insights-erőforráshoz való hozzáférés.

6. Válassza ki **vissza gyűjteményébe**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Mire szolgál a kohorszok használatával?

Nyissa meg a felhasználók eszközt. Az a **megjelenítése** legördülő listán válassza ki a létrehozott alatt kohorszok **tartozó felhasználók**.

A felhasználó-eszköz most már a felhasználók kohorszok szűrt:

![Felhasználók ablaktáblán egy adott kohorszok szűrve](.\media\app-insights-usage-cohorts\004.png)

Néhány fontos dolog, amit figyelje meg:
* Normál szűrők nem hozható létre ebben a készletben. A dátum logika további speciális.
* További szűrheti a kohorszok a normál szűrők segítségével a felhasználók eszközben. Így a kohorszok 28 napos windows van definiálva, de továbbra is módosíthatja a felhasználók eszközben 30, 60 vagy 90 napot az időtartományt.

Ezek a szűrők támogatja, amelyek nem express a lekérdezés-szerkesztő segítségével kifinomultabb kérdéseket. Példa: _személyek, akik az elmúlt 28 nap foglalkoztak. Hogyan indult azonos személyek szeretnék kezelni az elmúlt 60 napban?_

## <a name="example-events-cohort"></a>Példa: Események kohorszok

Az események cohorts is tehet. Ebben a szakaszban határozza meg azokat az eseményeket és az oldalmegtekintéseket egy kohorszok. Ezután láthatja, hogyan használhatja ezeket az eszközöket. A kohorszok definiálása az eseményeket, amelyek a csapat úgy ítéli meg, előfordulhat, hogy _aktív használati_ vagy egy bizonyos új szolgáltatáshoz kapcsolódó.

1. Nyissa meg a Cohorts eszközt.

2. Válassza ki a **sablon gyűjtemény** fülre. A különböző cohorts láthatja a sablonok gyűjteménye.

3. Válassza ki **események objektumválasztó**.

    ![Képernyőkép a események kiválasztása](.\media\app-insights-usage-cohorts\006.png)

4. Az a **tevékenységek** legördülő mezőben adja meg az eseményeket a kohorszok találhatók.

5. Mentse a kohorszok, és adjon neki egy nevet.

## <a name="example-active-users-where-you-modify-a-query"></a>Példa: Ha lekérdezés módosítása aktív felhasználók

Az előző két cohorts legördülő listák segítségével lettek megadva. De cohorts határozhat meg elemzési lekérdezések használatával teljes rugalmasságot biztosít. Megtekintéséhez, hogy a felhasználók egy kohorszok készíteni az Egyesült Királyságban.

![Animált kép útmutató alapján Cohorts eszköz használata](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Nyissa meg a Cohorts eszközt, jelölje be a **sablon gyűjtemény** lapot, és jelölje be **üres felhasználók kohorszok**.

    ![Üres felhasználók kohorszok](.\media\app-insights-usage-cohorts\001.png)

    Három szakaszok is vannak:
    * Markdown szöveges szakaszt, ahol ismerteti részletesen kohorszok mások a csoport.

    * Ahol például a saját paramétereivel, ellenőrizze a Paraméterek szakaszban **tevékenységek** és egyéb legördülő mezőben, az előző két példákban.

    * Lekérdezés szakasz, ahol megadhatja a kohorszok az analytics-lekérdezés segítségével.

    A lekérdezés területen, [írás analytics-lekérdezés](https://docs.loganalytics.io/index). A lekérdezés kijelöli az egyes sorok, amelyekre írják le a kohorszok kíván megadni. A Cohorts eszköz implicit módon hozzáad egy "|} a lekérdezés összesíteni user_Id"záradékkal. Ezeket az adatokat a lekérdezés egy tábla alatt látható az, hogy meg arról, hogy a lekérdezés eredményt adott vissza.

    > [!NOTE]
    > Ha nem látja a lekérdezést próbálja átméretezni a magasabb legyen, és a lekérdezés felfedheti szakaszt. Ez a szakasz elején animált .gif átméretezési viselkedését mutatja be.

2. Másolja és illessze be a lekérdezés-szerkesztő következő:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Válassza ki **lekérdezés**. Ha nem látja a táblázatban látható felhasználói azonosítók, váltson egy országban, ahol az alkalmazás rendelkezik felhasználók.

4. Mentse, és nevezze el a kohorszok.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

_A felhasználók egy bizonyos országból kohorszok meghatározta I. I hasonlítsa össze a kohorszok a a felhasználók eszközt, amely csak beállítását a szűrő az adott ország, a különböző eredmény jelenik meg. Hogy miért?_

Cohorts és a szűrők nem ugyanaz. Tegyük fel, hogy a felhasználók az Egyesült Királyságban (definiálva az előző példához hasonlóan) kohorszok rendelkezik, és összehasonlítja az eredményeket a szűrő beállítása az "ország vagy régió = Egyesült Királyságban."

* A kohorszok verzió összes esemény a felhasználók, akik az aktuális időtartományba esik az Egyesült Királyságban küldött egy vagy több esemény jeleníti meg. Ha ország vagy régió szerint felosztása, valószínűleg látni sok országokból és régiókból.
* A szűrők verziója csak az Egyesült Királyságban származó események jeleníti meg. De ha felosztása ország vagy régió szerint, csak az Egyesült Királyságban látható.

## <a name="learn-more"></a>Részletek
- [Analytics lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856587)
- [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
- [Felhasználói adatfolyamok](app-insights-usage-flows.md)
- [Használat – áttekintés](app-insights-usage-overview.md)