---
title: Azure Application Insights használati kohorszok | Microsoft dokumentumok
description: Különböző készletek vagy felhasználók, munkamenetek, események vagy műveletek elemzése, amelyek bensőséges dolgokkal rendelkeznek
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0c552e8f3e732c49da02b2f5704fb9cf312fb3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671086"
---
# <a name="application-insights-cohorts"></a>Alkalmazáselemzési kohorszok

A kohorsz olyan felhasználók, munkamenetek, események vagy műveletek készlete, amelyekben van valami közös. Az Azure Application Insightsban a kohorszokat egy elemzési lekérdezés határozza meg. Azokban az esetekben, amikor a felhasználók vagy események egy adott csoportját ismételten elemezni kell, a kohorszok nagyobb rugalmasságot biztosítanak ahhoz, hogy pontosan azt a készletet fejezhessék ki, amely érdekli.

![Kohorszok ablaktábla](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorszok kontra alapvető szűrők

A kohorszokat a szűrőkhöz hasonló módon használják. A kohorszdefiníciók azonban egyéni elemzési lekérdezésekből épülnek fel, így sokkal alkalmazkodóbbak és összetettebbek. A szűrőkkel ellentétben a kohorszokat mentheti, hogy a csapat többi tagja újra felhasználhassa őket.

Megadhat egy kohorszt azon felhasználókszámára, akik mind kipróbáltak egy új funkciót az alkalmazásban. Ezt a kohorszt mentheti az Application Insights-erőforrásban. A jövőben könnyen elemezhető az adott felhasználók mentett csoportja.

> [!NOTE]
> Létrehozása után a kohorszok elérhetők a Felhasználók, munkamenetek, események és felhasználói folyamatok eszközökből.

## <a name="example-engaged-users"></a>Példa: Elkötelezett felhasználók

A csapat úgy definiálja az elkötelezett felhasználót, mint bárkit, aki egy adott hónapban ötször vagy többször használja az alkalmazást. Ebben a szakaszban meghatározza az eljegyzett felhasználók kohorszát.

1. Nyissa meg a Kohorszok eszközt.

2. Válassza a **Sablongyűjtemény** lapot. A különböző kohorszok sablonjainak gyűjteménye látható.

3. Válassza **az Elkötelezett felhasználók lehetőséget - a Használt napok szerint.**

    Ennek a kohorsznak három paramétere van:
    * **Tevékenységek**, ahol kiválaszthatja, hogy mely események és oldalmegtekintések számítanak "használatnak".
    * **Időszak**, a hónap meghatározása.
    * **UsedAtLeastCustom**, azon alkalmak száma, amikor a felhasználóknak használniuk kell valamit egy időszakon belül, hogy részt vegyenek.

4. Módosítsa **a UsedAtLeastCustom** értéket **5+ napra,** és hagyja az **Időszakot** az alapértelmezett 28 napon belül.

    ![Elkötelezett felhasználók](./media/usage-cohorts/003.png)

    Ez a kohorsz most az összes egyéni eseménnyel vagy oldalmegtekintéssel küldött felhasználói azonosítót jelöli az elmúlt 28 nap 5 különböző napján.

5. Kattintson a **Mentés** gombra.

   > [!TIP]
   > Adjon nevet a kohorsznak, például "Elkötelezett felhasználók (5+ Nap)." Mentse azt a "Saját jelentések" vagy a "Megosztott jelentések" című helyre, attól függően, hogy azt szeretné, hogy az Application Insights-erőforráshoz hozzáféréssel rendelkező más személyek is lássák ezt a kohorszt.

6. Válassza **a Vissza a gyűjteménybe**lehetőséget.

### <a name="what-can-you-do-by-using-this-cohort"></a>Mit lehet tenni ezzel a kohorszhasználatával?

Nyissa meg a Felhasználók eszközt. A **Megjelenítés** legördülő lista válassza ki a létrehozott kohorszt a **Felhasználók csoportban.**

Most a Felhasználók eszköz a felhasználók kohorszára van szűrve:

![Adott kohorszra szűrt felhasználók ablaktábla](./media/usage-cohorts/004.png)

Néhány fontos dolog, hogy észre:

* Ezt a készletet nem hozhatja létre normál szűrőkkel. A dátumlogika fejlettebb.
* Ezt a kohorszt tovább szűrheti a Felhasználók eszköz normál szűrőivel. Így bár a kohorsz 28 napos ablakokon van definiálva, a Felhasználók eszköz időtartományát továbbra is 30, 60 vagy 90 napra állíthatja be.

Ezek a szűrők olyan kifinomultabb kérdéseket támogatnak, amelyeket a lekérdezésszerkesztőn keresztül nem lehet kifejezni. Erre példa azok _az emberek, akik az elmúlt 28 napban részt vettek. Hogy viselkedtek ugyanazok az emberek az elmúlt 60 napban?_

## <a name="example-events-cohort"></a>Példa: Események kohorsz

Azt is, hogy kohorszok események. Ebben a szakaszban adhatja meg az események és az oldalmegtekintések kohorszát. Ezután láthatja, hogyan kell használni őket a többi eszközből. Ez a kohorsz olyan eseményeket határozhat meg, amelyeket a csapat _aktív használatnak_ tekint, vagy egy bizonyos új funkcióhoz kapcsolódó készletet.

1. Nyissa meg a Kohorszok eszközt.

2. Válassza a **Sablongyűjtemény** lapot. A különböző kohorszok sablonjainak gyűjteménye jelenik meg.

3. Válassza **az Eseményválasztó lehetőséget**.

    ![Képernyőkép az eseményválasztóról](./media/usage-cohorts/006.png)

4. A **Tevékenységek** legördülő lista válassza ki a kohorszban lenni kívánt eseményeket.

5. Mentsd el a kohorszot, és adj neki nevet.

## <a name="example-active-users-where-you-modify-a-query"></a>Példa: Aktív felhasználók, ahol módosítja a lekérdezést

Az előző két kohorszt legördülő mezők használatával határozták meg. De a kohorszokat is meghatározhatja elemzési lekérdezések használatával a teljes rugalmasság érdekében. Hogy megnézhesse, hogyan, hozzon létre egy kohorszt a felhasználók az Egyesült Királyságból.

![Animált kép séta használata Kohorsz eszköz](./media/usage-cohorts/cohorts0001.gif)

1. Nyissa meg a Kohorszok eszközt, válassza a **Sablongyűjtemény** lapot, és válassza **az Üres felhasználók kohorsz lehetőséget**.

    ![Üres felhasználók kohorsz](./media/usage-cohorts/001.png)

    Három részből áll:
   * Markdown-szövegrész, ahol részletesebben leírhatja a kohorszot a csapat többi tagja számára.

   * A paraméterek szakasz, ahol a saját paramétereket, mint **a tevékenységek** és egyéb legördülő mezők az előző két példa.

   * Egy lekérdezési szakasz, ahol a kohorszegy egy elemzési lekérdezés használatával definiálható.

     A lekérdezésszakaszban [elemzési lekérdezést ír.](/azure/kusto/query) A lekérdezés kiválasztja a definiálni kívánt kohorszot leíró sorok bizonyos készletét. A Kohorsz eszköz ezután implicit módon hozzáad egy "| a lekérdezés user_Id" záradékkal összegezve. Ezek az adatok a lekérdezés alatt jelennek meg egy táblában, így meggyőződhet arról, hogy a lekérdezés eredményeket ad vissza.

     > [!NOTE]
     > Ha nem látja a lekérdezést, próbálja meg átméretezése a szakaszt, hogy magasabb, és felfedi a lekérdezést. A szakasz elején látható animált .gif az átméretezési viselkedést mutatja be.

2. Másolja és illessze be a következő szöveget a lekérdezésszerkesztőbe:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Válassza a **Lekérdezés futtatása** lehetőséget. Ha nem látja a felhasználói azonosítók jelennek meg a táblázatban, módosítsa az országot/régiót, ahol az alkalmazás felhasználók.

4. Mentse és nevezze el a kohorszot.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

_Egy bizonyos országból/régióból származó felhasználók ból álló kohorszot határoztam meg. Amikor összehasonlítom ezt a kohorsza a Felhasználók eszköz csak beállít egy szűrőt, hogy az ország / régió, látom a különböző eredményeket. miért?_

A kohorszok és a szűrők különbözőek. Tegyük fel, hogy van egy kohorsz a felhasználók az Egyesült Királyságban (meghatározott, mint az előző példa), és összehasonlítja az eredményeket a szűrő beállítása "Ország vagy régió = Egyesült Királyság."

* A kohorsz verzió az okat a felhasználóktól származó összes eseményt megjeleníti, akik egy vagy több eseményt küldtek az Egyesült Királyságból az aktuális időtartományban. Ha ország vagy régió szerint oszlik, valószínűleg sok országot és régiót lát.
* A szűrők verziója csak az Egyesült Királyságból származó eseményeket jeleníti meg. De ha ország vagy régió szerint oszlasz meg, akkor csak az Egyesült Királyságot látod.

## <a name="learn-more"></a>Részletek

* [Elemzés lekérdezési nyelve](https://go.microsoft.com/fwlink/?linkid=856587)
* [Felhasználók, munkamenetek, események](usage-segmentation.md)
* [Felhasználói folyamatok](usage-flows.md)
* [Használat – áttekintés](usage-overview.md)
