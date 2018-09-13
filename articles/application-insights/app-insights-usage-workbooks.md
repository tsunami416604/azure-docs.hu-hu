---
title: Vizsgálja meg és használati adatok megosztása az Azure Application Insights-interaktív munkafüzetek |} A Microsoft docs
description: A felhasználók a webalkalmazás demográfiai elemzése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/12/2017
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 016a26acc153fba1c38d926fd5389d02755c2ff5
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645584"
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Vizsgálja meg és használati adatok megosztása az Application Insights-interaktív munkafüzetek

Munkafüzetek egyesítése [Azure Application Insights](app-insights-overview.md) adatvizualizációkat, [elemzési lekérdezések](app-insights-analytics.md), és interaktív dokumentumok szöveget. Munkafüzetek ugyanazon Azure-erőforráshoz hozzáféréssel rendelkező más tagjai által szerkeszthető. Ez azt jelenti, hogy a lekérdezések és a egy munkafüzet létrehozásához használt funkciók érhetők el más személyek olvasása a munkafüzetet, így azok könnyen megismerését, bővítése és keresése a hibák.

Munkafüzetek például hasznosak:

* Felfedezése az alkalmazás használatát, ha előre nem ismeri az érdekes mérőszám vizsgálatára: számok felhasználók, arányt, átváltási árfolyamok, stb. Ellentétben más használatelemző eszközökkel az Application Insights munkafüzetek lehetővé teszik több bármilyen típusú vizualizációkat és elemzéseket, szabad formátumú feltárása az ilyen nagyszerű minősítené kombinálja.
* A csapata számára elmagyarázza, hogyan teljesít egy újonnan kiadott funkció, amely felhasználó által száma kulcs kapcsolati és más metrikákkal.
* Megosztják az eredményeket, egy A / B kísérletezhet az alkalmazásban, a csapat tagjaival. Ismertetik a kitűzött célokat a kísérlet szövegre, majd minden használati metrika- és elemzési lekérdezés értékeli ki a kísérletet, együtt egyértelmű hívás mintáit mindegyik metrikát volt-e felett vagy alatt-cél az, hogy megjelenítése.
* A leállások hatásának jelentéskészítés a használati adatok, szöveg magyarázata és ismerteti azokat a következő lépéseket a jövőben a leállások elkerülése érdekében az alkalmazás.

> [!NOTE]
> Az Application Insights-erőforrást kell tartalmaznia, Lapmegtekintések vagy egyéni események munkafüzetek használandó. [Ismerje meg, hogyan állíthat be az alkalmazás gyűjtése az Application Insights JavaScript SDK automatikusan a lapmegtekintések](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Szerkesztés, átrendezése, klónozás és a munkafüzet szakaszok törlése

A munkafüzet egy kezdeményezni szakaszok: függetlenül szerkeszthető használati Vizualizációk, diagramok, táblázatok, szöveges vagy elemzési lekérdezés eredményeit.

Egy munkafüzet szakasz tartalmának szerkesztéséhez kattintson a **szerkesztése** gombra, és jobb oldalán a munkafüzet szakaszt alább.

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/app-insights-usage-workbooks/editing-controls.png)

1. Ha végzett szerkeszt egy szakaszt, kattintson a **Szerkesztés kész** rész bal alsó sarokban található.

2. Duplicitní szakasz létrehozásához kattintson a **szakasz klónozása** ikonra. Ismétlődő szakaszok létrehozása remek lehetőség újrafuttathatja a lekérdezés előző ismétlések elvesztése nélkül.

3. A munkafüzet szakasz áthelyezéséhez kattintson a **feljebb** vagy **mozgatása lefelé** ikonra.

4. Szakasz végleges eltávolításához kattintson a **eltávolítása** ikonra.

## <a name="adding-usage-data-visualization-sections"></a>Használati adatok vizualizációs szakaszok hozzáadása

Munkafüzetek négy típusú beépített felhasználás elemzési vizualizációt is kínál. Minden ad választ az alkalmazás a használatával kapcsolatos gyakori kérdés. Adja hozzá a táblázatokban és diagramokon ezekben a szakaszokban eltérő, adja hozzá az elemzési lekérdezés szakaszok (lásd alább).

Szeretne hozzáadni a felhasználók, munkamenetek, események vagy megőrzési szakasz a munkafüzetet, használja a **felhasználó hozzáadása** vagy egyéb megfelelő gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

![A munkafüzetekben felhasználók szakaszban](./media/app-insights-usage-workbooks/users-section.png)

**Felhasználók** szakaszok megválaszolni a "hány felhasználó néhány lapon tekinthetők meg vagy használt néhány szolgáltatása a webhelyem?"

**Munkamenetek** szakaszok megválaszolni a "hány munkamenetek volt a felhasználók által néhány lap megtekintése vagy néhány funkcióval másolatot a webhelyemről?"

**Események** szakaszok megválaszolni a "hány alkalommal volt felhasználók bizonyos lap megtekintése vagy saját néhány funkcióját használja?"

A vezérlőelemek és a Vizualizációk azonos különböző egyes három szakasz kínálja:

* [További információ a felhasználók, munkamenetek és események szakaszok szerkesztése](app-insights-usage-segmentation.md)
* Váltsa át a fő diagram, hisztogram rácsok, automatikus insights és minta felhasználói Vizualizációk használata a **diagram megjelenítése**, **rács megjelenítése**, **megjelenítése Insights**, és **Ezek Mintafelhasználók** jelölőnégyzeteket minden szakasz elején.

![A munkafüzetekben megőrzési szakasz](./media/app-insights-usage-workbooks/retention-section.png)

**Adatmegőrzési** szakaszok választ ", akik néhány lapon tekinthetők meg és nem használt egyes szolgáltatások egy nap vagy hét, hogy hány visszatért az ezt követő nap vagy hét?"

* [Tudjon meg többet az adatmegőrzési szakaszok szerkesztése](app-insights-usage-retention.md)
* A választható teljes megőrzési diagram használatával váltógomb a **megjelenítése a teljes megőrzési diagram** jelölőnégyzetet a szakasz tetején.

## <a name="adding-application-insights-analytics-sections"></a>Hozzáadás, Application Insights-elemzési szakaszok

![A munkafüzetekben Analytics szakasz](./media/app-insights-usage-workbooks/analytics-section.png)

A munkafüzet egy Application Insights-elemzési lekérdezés szakaszban hozzáadásához használja a **hozzáadása elemzési lekérdezés** gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

Elemzési lekérdezés szakaszok segítségével tetszőleges lekérdezések hozzáadása az Application Insights-adatok munkafüzetek. Ez rugalmasságot azt jelenti, hogy Analytics lekérdezési szakaszokat kell lennie a go, a nem a felhasználók, munkamenetek, események és megőrzési, például a fent felsorolt négy a hellyel kapcsolatos bármilyen kérdés megválaszolásával:

* Hány kivételek fejeződött a hely throw idő alatt, csökken a használat során?
* Mi volt a lapbetöltési idők néhány lap megtekintésének felhasználók elosztását?
* Hány felhasználó helyen tekinthetők meg néhány azon lapok készlete, de nem más lapok készlete? Ez akkor lehet hasznos megértéséhez, ha a felhasználók, akik használni a webhely funkciók részhalmazokat fürttel rendelkezik (használja a `join` az operátor a `kind=leftanti` módosító Log Analytics lekérdezési nyelven).

Használja a [Log Analytics lekérdezési nyelv leírása](https://docs.loganalytics.io/) tudhat meg többet a lekérdezések írásának módját.

## <a name="adding-text-and-markdown-sections"></a>Szöveg- és Markdown-szakaszok hozzáadása

Fejlécek magyarázatok és mellékmondatokkal ad hozzá a munkafüzetek segít információkká táblákat és diagramokat egy narratíva. Munkafüzetek támogatási szöveg szakaszai a [Markdown-szintaxisa](https://daringfireball.net/projects/markdown/) szöveg formázását, például fejlécek félkövér, dőlt és listajeles listák készítéséhez.

A munkafüzet egy szöveges szakasz hozzáadásához használja a **szöveg hozzáadása** gomb alsó részén a munkafüzetet, vagy minden szakasz alján.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>És munkafüzetek megosztása munkatársaival

Application Insights-erőforrás, akár a mentett munkafüzetek a **jelentések mappa** magánjellegű, vagy a szakasz a **megosztott jelentések** hozzáférést mindenki számára elérhető, a szakasz a Application Insights-erőforrás. Az erőforrás minden a munkafüzetek megtekintéséhez kattintson a **nyílt** gombra a művelet sávon.

A munkafüzetet, amely a jelenleg **jelentések mappa**:

1. Kattintson a **nyílt** a művelet sávon
2. Kattintson a "..." gombot a megosztani kívánt munkafüzet
3. Kattintson a **helyezze át a megosztott jelentések**.

A munkafüzet egy hivatkozást, vagy e-mailen keresztül megosztásához kattintson **megosztása** a művelet sávon. Ne feledje, hogy a címzettek a hivatkozásra kell a munkafüzet megtekintéséhez az Azure Portalon az erőforráshoz való hozzáférés. Ahhoz, hogy a módosításokat, címzettek van szükség, legalább közreműködői engedélye az erőforrás.

A PIN-kódot egy munkafüzetet, egy Azure-irányítópultra mutató hivatkozást:

1. Kattintson a **nyílt** a művelet sávon
2. Kattintson a "..." gombot szeretne rögzíteni a munkafüzet
3. Kattintson a **rögzítés az irányítópulton**.

## <a name="next-steps"></a>További lépések

## <a name="next-steps"></a>További lépések
- Használati bővítsék küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [Lapmegtekintések](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha már küldhet egyéni eseményeket vagy lapmegtekintéseket, Fedezze fel az megtudhatja, hogy a felhasználók használni a szolgáltatást a Használatelemzési eszközben.
    - [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Adja hozzá a felhasználói környezet](app-insights-usage-send-user-context.md)
    
