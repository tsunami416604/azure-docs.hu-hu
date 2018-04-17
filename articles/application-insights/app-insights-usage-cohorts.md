---
title: Az Azure Application Insights használati Cohorts |} Microsoft docs
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
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights Cohorts

Egy kohorszok olyan felhasználók, munkamenetek, eseményeket és műveleteket, amelyek valamilyen közös készlete. Az Azure Application Insights cohorts az elemzési lekérdezések határozzák meg. Ha veszi észre magát a felhasználók adott csoportja elemzése vagy események ismételten cohorts tudhatja meg pontosan a készlet Express még nagyobb rugalmasságot kíváncsiak vagyunk.

![Cohorts ablaktábla](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Alapszintű szűrők és cohorts

Cohorts hasonló módon szűrőként használatosak, amíg az, hogy egy kohorszok definition össze egyéni elemzési lekérdezések lehetővé teszik sokkal alkalmazkodó és összetett. Szűrők, eltérően mentheti cohorts, más a csoport tagjai is felhasználhatja őket.

Megadhat egy kohorszok, amely az összes próbált meg az alkalmazás egy új funkciója. Ez az Application Insights-erőforrás mentése kohorszok azt lehetővé teszi az adott csoport a felhasználók a jövőben egy kattintással elemzése.

> [!NOTE]
> Létrehozása után a felhasználók, a munkamenetek, az események és a felhasználó zajló kommunikációról eszközeinek cohorts érhetők el.

## <a name="example-engaged-users"></a>Példa: részt vevő felhasználók

A csoport határozza meg egy minden felhasználó öt vagy több alkalommal az adott hónapban az alkalmazás minden felhasználójának. Is határozza meg a minden felhasználó kohorszok.

1. Nyissa meg a **Cohorts** eszköz.

2. Kattintson a **sablon gyűjtemény** fülre. Itt megtalálja a sablonok gyűjteménye a különböző cohorts.

3. Válasszon **részt vevő felhasználók** – nap által használt ".

    A kohorszok három paramétereinek van:
      * **Tevékenységek** , amely segítségével kiválaszthatja, hogy mely események és az oldalmegtekintéseket kell a "használati" számít.
      * **Időszak** havonta definíciója.
      * **UsedAtleastCustom** hányszor segítségével valamilyen belül minden felhasználóként száma van szükségük.

4. Változás **UsedAtleastCustom** "5 + days", és hagyja **időszak** 28 nap az alapértelmezett értékre.

    ![Kép](.\media\app-insights-usage-cohorts\003.png)

    A kohorszok most az összes felhasználóazonosító távozó bármilyen egyéni esemény vagy a lap láthassák, az elmúlt 28 napban öt külön napokon jelöli.

5. Kattintson a **Save** (Mentés) gombra.

   > [!TIP]
   >  Nevezze el a kohorszok, például "Engaged felhasználók (5 nap)", és mentse a "Saját jelentések" vagy "Megosztás jelentések" függően Ha azt szeretné, hogy mások az portot Insights erőforráshoz való hozzáférés a kohorszok megjelenítéséhez.

6. Kattintson a **vissza gyűjteményébe**.

### <a name="what-can-you-do-with-this-cohort"></a>Mire szolgál a kohorszok rendelkező?

Nyissa meg a **felhasználók** eszköz > a a **megjelenítése** legördülő > válassza a kohorszok szerinti létrehozott **tartozó felhasználók...**

A felhasználó-eszköz most már a felhasználók kohorszok szűrt:

![Felhasználók ablaktáblán egy adott kohorszok szűrve](.\media\app-insights-usage-cohorts\004.png)

Néhány fontos dolog, amit figyelje meg:
   * Ez olyan szokásos szűrők nem hozott létre. A dátum logika további speciális.
   * Ez a szokásos szűrők segítségével a felhasználók eszközben kohorszok további végezhet. Ezért a kohorszok 28 napos windows van definiálva, miközben továbbra is módosíthatja az időtartományt, a felhasználók eszközben 30, 60 vagy 90 napot. 

Ez lehetővé teszi például kifinomultabb kérdései: _számára, akiknek az elmúlt 28 nap foglalkoztak, hogyan volt azonos személyek szeretnék kezelni az elmúlt 60 napban?_ , ellenkező esetben nem lehet a lekérdezés-szerkesztő segítségével express.

## <a name="example-events-cohort"></a>Példa: események kohorszok

Az események cohorts is tehet. Most adja meg azokat az eseményeket és az oldalmegtekintéseket egy kohorszok, majd tekintse meg, hogyan használhatja ezeket az eszközöket. Ez akkor lehet hasznos lehet definiálni az eseményeket, amelyek úgy ítéli meg, a csapat _aktív használati_, vagy egy bizonyos új szolgáltatáshoz kapcsolódó események a kulcstulajdonságokat határozza meg.

1. Nyissa meg a **Cohorts** eszköz.

2. Kattintson a **sablon gyűjtemény** fülre. Itt megtalálja a sablonok gyűjteménye a különböző cohorts.

3. Válasszon **események objektumválasztó**.

    ![Képernyőkép az események kiválasztása](.\media\app-insights-usage-cohorts\006.png)

4. Az a **tevékenységek** legördülő menüben válassza ki az eseményeket szeretné megadni a kohorszok

5. Mentse a kohorszok, és adjon neki egy nevet.

## <a name="example-active-users-where-you-modify-query"></a>Példa: Ha módosítsa a lekérdezést aktív felhasználók

Az előző két cohorts meghatározott legördülő lista használatával. De azt is megadhatja, cohorts elemzési lekérdezések teljes rugalmasságot biztosít. Nézzük meg, hogyan hozzon létre egy kohorszok a felhasználók az Egyesült Királyságban.

![Animált kép útmutató alapján Cohorts eszköz használata](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Nyissa meg a **Cohorts** eszköz > kattintson **sablon gyűjtemény** lapon > Válasszon **üres felhasználók kohorszok**.

    ![Üres felhasználók Kohorszok](.\media\app-insights-usage-cohorts\001.png)

    Három szakaszok is vannak:
       * Ha is ismerteti részletesen kohorszok mások a csoport Markdown szöveges szakaszt.

       * A Paraméterek szakaszban használhatja például a saját paramétereivel, hogy a **tevékenységek** és egyéb legördülő listák megnyílásának az előző két példákban.

       * Az elemzés lekérdezéssel kohorszok meghatározásához használt lekérdezés szakasz.

    A lekérdezés területen, [írás Analytics-lekérdezés](https://docs.loganalytics.io/index) , amely kijelöli az egyes sorok, amelyekre írják le a kohorszok kíván megadni. A Cohorts eszköz implicit módon hozzáad egy "|} a lekérdezés összesíteni user_Id"záradékkal. Ez látható az alábbi táblázatban a lekérdezés, hogy meg arról, hogy a lekérdezés eredményt adott vissza.

    > [!NOTE]
    > Ha nem látja a lekérdezést próbálja átméretezni a magasabb legyen, és a lekérdezés felfedheti szakaszt. Ez a szakasz elején animált .gif átméretezési viselkedését mutatja be.

2. Másolás-beillesztés a Lekérdezésszerkesztő az alábbiakat:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Kattintson a **lekérdezés**. A táblázatban látható felhasználói azonosítók kell megjelennie. Ha nem, akkor módosítsa a országban, ahol az alkalmazás rendelkezik felhasználók.

4. Mentse, és nevezze el a kohorszok.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

_A felhasználók egy bizonyos országból kohorszok meghatározta I. Ez a szűrő csak a felhasználó-eszköz az adott ország beállítása a felhasználók eszköz kohorszok I összehasonlítására, a különböző eredmények jelenik meg. Hogy miért?_

Cohorts és a szűrők nem ugyanaz. Tegyük fel, hogy a felhasználók az Egyesült Királyságban (például a fenti példa meghatározott) kohorszok rendelkezik, és összehasonlítja az eredményeket a szűrő beállítása az "ország vagy régió = Egyesült Királyságban."

* A kohorszok verzió a felhasználóktól legalább egy eseményt az aktuális időtartományba esik az Egyesült Királyságban a küldő összes események jelennek meg. Ország vagy régió szerint ossza fel, ha sok országokból és régiókból valószínűleg láthatja.
* A szűrők verziója csak az Egyesült Királyságban származó események jelennek meg. Mivel az ország vagy régió szerint ossza fel, ha csak az Egyesült Királyságban láthatja.

## <a name="learn-more"></a>Részletek
- [Analytics lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856587)
- [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
- [Felhasználói folyamatok](app-insights-usage-flows.md)
- [Használat – áttekintés](app-insights-usage-overview.md)