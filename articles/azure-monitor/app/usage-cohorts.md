---
title: Az Azure Application Insights használati kohorszok |} A Microsoft Docs
description: Eltérő vagy felhasználók, munkamenetek, események vagy a gyakori, hogy műveleteket elemzése
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 810ccae08b540c417a0e7dfecb6ad5a8b110cd7b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54045880"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorszok

Egy kohorszt olyan felhasználók, munkamenetek, események vagy a gyakori, hogy műveleteket. Az Azure Application Insights analytics-lekérdezések kohorszok határozza meg. Azokban az esetekben, amelyekben egy adott elemzéséhez beállítása a felhasználók, vagy események ismételten kohorszok is nagyobb rugalmasságot nyújtanak pontosan az Önt érdeklő set Express.

![Kohorszok panel](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Alapszintű szűrők és kohorszok

Kohorszok szűrők hasonló módon használhatók. De kohorszok definíciók alkotják egyéni elemzési lekérdezések, hogy azok sokkal testreszabható és összetett. Ellentétben a szűrőket mentheti kohorszok, a csoport más tagjai felhasználhatja őket.

Megadhat egy kohorszt, felhasználók, akik az összes próbált meg egy új funkció az alkalmazásban. A kohorsz a következő mentheti az Application Insights-erőforrást. A jövőben elemezheti a mentett csoport egyes felhasználói könnyebbé vált.

> [!NOTE]
> Miután jönnek létre, a felhasználók, munkamenetek, események és a felhasználói folyamatok eszközökből kohorszok érhetők el.

## <a name="example-engaged-users"></a>Példa: Szerepet játszanak felhasználók

A csapata egy szerepet játszanak felhasználói bárki, aki használja az alkalmazás öt vagy több alkalommal az adott hónapban, határozza meg. Ebben a szakaszban, a következők kohorszának definiálása: ezek a felhasználók szerepet játszanak.

1. Nyissa meg a Kohorszok eszközzel.

2. Válassza ki a **Sablonkatalógus** fülre. Különböző kohorszok sablonok gyűjteményét találja.

3. Válassza ki **szerepet játszanak felhasználók – nap által használt**.

    Nincsenek a a kohorsz a következő három paramétert:
    * **Tevékenységek**, ha úgy dönt, mely eseményeket és oldalmegtekintéseket számít a "használati."
    * **Időszak**, egy hónapig definíciója.
    * **UsedAtLeastCustom**, szám, ahányszor felhasználók használatára van szükség valami időn belül számítanak bele foglalkozó.

4. Változás **UsedAtLeastCustom** való **5 + nap**, és hagyja **időszak** 28 napos alapértelmezett.

    ![Szerepet játszanak felhasználók](./media/usage-cohorts/003.png)

    Most a kohorsz a következő jelöli az összes felhasználóazonosító küldi el minden egyéni esemény vagy oldal nézet 5 nappal ezelőtti külön 28.

5. Kattintson a **Mentés** gombra.

   > [!TIP]
   > Adja meg a kohorsz nevét, például "Engaged felhasználók (5 vagy több nap)." Mentse a "Saját reports" vagy "Megosztott jelentések," attól függően, hogy mások, akik hozzáférhetnek az Application Insights-erőforráshoz a kohorsz a következő témakörben talál.

6. Válassza ki **vissza a katalógushoz**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Mire használható a kohorsz a következő használatával?

Nyissa meg a felhasználók eszköz. Az a **megjelenítése** listából válassza ki a létrehozott kohorsz **tartozó felhasználók**.

Mostantól a felhasználók eszköz szűrve van a felhasználói kohorsz a következő:

![Felhasználók panel szűri, hogy egy adott kohorsz](./media/usage-cohorts/004.png)

Néhány fontos dolog figyelje meg, hogy:

* A készlet normál szűrők használatával nem hozható létre. A dátum logika több speciális.
* A kohorsz a következő további a felhasználók eszköz a szokásos szűrők használatával is szűrheti. Ezért a kohorsz 28 napos windows rendszeren van definiálva, de továbbra is módosíthatja az időtartományt, a felhasználók eszköz 30, 60 vagy 90 napot.

Ezeket a szűrőket, amelyek nem lehetséges a Lekérdezésszerkesztő segítségével express kifinomultabb kérdések támogatja. Például _azokat, akik az elmúlt 28 nap foglalkoztak. Ezek a személyek milyen volt viselkednek az elmúlt 60 napban?_

## <a name="example-events-cohort"></a>Példa: Események kohorsz

Győződjön meg a kohorszok események is. Ebben a szakaszban, a következők kohorszának definiálása: az események és Lapmegtekintések. Ezután tekintse meg a más eszközök használatával. A kohorsz a következő határozhat meg a csapata a mérlegeli események egy meghatározott készletének _aktív használati_ vagy egy kapcsolódó bizonyos új szolgáltatása.

1. Nyissa meg a Kohorszok eszközzel.

2. Válassza ki a **Sablonkatalógus** fülre. A különböző kohorszok látni fogja a sablonok egy gyűjtemény.

3. Válassza ki **események választó**.

    ![Képernyőkép a események kiválasztása](./media/usage-cohorts/006.png)

4. Az a **tevékenységek** legördülő mezőben válassza ki a kívánt eseményeket, a kohorsz lehet.

5. A kohorsz mentése, és adjon meg egy nevet.

## <a name="example-active-users-where-you-modify-a-query"></a>Példa: Itt módosíthatja a lekérdezés aktív felhasználók

Az előző két kohorszok legördülő listák használatával lettek definiálva. De kohorszok is meghatározhat az elemzési lekérdezések teljes rugalmasság érdekében. Megtekintheti, hogy hozzon létre egy felhasználói kohorsz a következő, az Egyesült Királyság.

![Kohorszok eszközzel használatát ajánljuk figyelmébe az animált kép](./media/usage-cohorts/cohorts0001.gif)

1. Nyissa meg a Kohorszok eszközzel, válassza ki a **Sablonkatalógus** lapot, majd **üres felhasználók kohorsz**.

    ![Üres felhasználók kohorsz](./media/usage-cohorts/001.png)

    Nincsenek három szakaszra osztható:
    * A Markdown szöveg szakaszban, ahol meg kell adni a kohorsz részletesebben mások a csapata.

    * A Paraméterek szakaszban, ahol választja ki a saját paraméterek, például **tevékenységek** és egyéb legördülő listák az előző két példákban.

    * Lekérdezés szakaszban, ahol megadhatja a kohorsz analytics-lekérdezések használatával.

    A lekérdezés szakaszban Ön [analytics-lekérdezések írása](/azure/kusto/query). A lekérdezés kijelöli az egyes sorok, amelyek ismertetik a kohorsz szeretne meghatározni. A Kohorszok eszközzel implicit módon hozzáad egy "|} a lekérdezés összegzés szempontja a USER_ID paraméter értékét"záradékot. Ezek az adatok megtekintésekor alatt a lekérdezés egy táblázatban, így biztos lehet benne, a lekérdezési eredményeket adja vissza.

    > [!NOTE]
    > Ha nem látja a lekérdezést, és próbálja ki a szakaszt, hogy magasabb, és a lekérdezés felfedheti átméretezése. Az animált .gif, ez a szakasz elején átméretezési működését mutatja be.

2. Másolja és illessze be a következő szöveget a Lekérdezésszerkesztő:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Válassza ki **lekérdezés futtatása**. Ha nem látja a felhasználói azonosítók jelenik meg a táblázatban, váltson egy országot, ahol az alkalmazás egy felhasználó hozzá van.

4. Mentse, és nevezze el a kohorsz.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

_E egy bizonyos országból felhasználói kohorsz a következő definiált. E hasonlítsa össze a kohorsz a következő egyszerűen beállít egy szűrőt az adott ország, a felhasználók eszköz, ha különböző eredményeket látható. Hogy miért?_

Kohorszok és szűrők eltérőek. Tegyük fel, hogy a felhasználók az Egyesült Királyság (az előző példához hasonlóan meghatározott) az egyik példasablonunk rendelkezik, és összehasonlítja az eredményeket, ha a szűrőt, "ország vagy régió Nagy-Britannia =."

* A kohorsz a következő verziót minden eseményt a felhasználók, akik egy vagy több esemény az Egyesült Királyság, a jelenlegi időtartományban által küldött jeleníti meg. Ha akkor feloszthatja az ország vagy régió szerint, valószínűleg megjelenik számos országban és régióban.
* A szűrők verzió csak az egyesült királyságbeli események jeleníti meg. Azonban akkor feloszthatja az ország vagy régió szerint, ha csak az Egyesült Királyság látja.

## <a name="learn-more"></a>Részletek

* [Analytics lekérdezési nyelve](https://go.microsoft.com/fwlink/?linkid=856587)
* [Felhasználók, munkamenetek, események](usage-segmentation.md)
* [Felhasználói folyamatok](usage-flows.md)
* [Használat – áttekintés](usage-overview.md)