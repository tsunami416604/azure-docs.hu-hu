---
title: Vizsgálja meg és használati adatok megosztása a interaktív Azure Application Insights-munkafüzetek |} Microsoft docs
description: Webes alkalmazása felhasználóit demográfiai elemzése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin; daviste
ms.openlocfilehash: a871378b3e2cc0b34c925593c6f01952de3aa08e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Vizsgálja meg és használati adatok megosztása az Application Insightsban interaktív munkafüzetek

Munkafüzetek egyesítése [Azure Application Insights](app-insights-overview.md) adatmegjelenítésekkel, [elemzési lekérdezések](app-insights-analytics.md), és interaktív dokumentumok szövegdobozba. Munkafüzetekhez a ugyanahhoz az Azure-erőforráshoz hozzáféréssel rendelkező más csoport tagjai. Ez azt jelenti, hogy a lekérdezések és a munkafüzet létrehozásához használt funkciók érhetők el a más személy éri el a munkafüzetet, így könnyen vizsgálatát, kiterjesztése, és ellenőrizze a hibákat.

Munkafüzetek hasznosak a helyzetekben, például:

* Az alkalmazás használatát tervezi, amikor a fontos metrikák előzetesen nem tudja: számok felhasználók, adatmegőrzési arányt, átváltási, stb. Egyéb használati elemzőeszközök az Application Insightsban, eltérően munkafüzetek lehetővé teszik, hogy több típusú képi megjelenítések és elemzéseket, szabad formátumú feltárása az ilyen nagyszerű minősítené kombinálni.
* A csapattal elmagyarázza, hogyan működik-e egy újonnan kiadott funkció, megjelenítő felhasználó álló kulcs kapcsolati és más metrikákkal.
* Megosztják az eredményeket, egy A / B kísérletezhet az alkalmazás más a csoport tagjai. A kitűzött célokat a kísérleti fázisú funkciókat szövegre ismertetik, majd minden használati metrika és annak ellenőrzésére használ a kísérlet, és törölje a jelet hívás elemek számára, hogy volt-e mindegyik metrikát fölött vagy alatt-célzott Analytics lekérdezés megjelenítése.
* A jelentéskészítési kimaradás hatását a az alkalmazás adatokat, a szöveg magyarázat és az információt a következő lépései kimaradások elkerülése használati.

> [!NOTE]
> Az Application Insights-erőforrás Lapmegtekintések vagy munkafüzetek használandó egyéni események kell tartalmaznia. [Ismerje meg, hogyan állíthat be az alkalmazás automatikusan az Application Insights JavaScript SDK a lapmegtekintések gyűjtéséhez](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Szerkesztés, átrendezése, a Klónozás és a munkafüzet szakaszok törlése

A munkafüzet egy készült szakaszok: függetlenül szerkeszthető használati képi megjelenítéseket, diagramok, táblák, text vagy Analytics lekérdezési eredmények.

A munkafüzet szakasz tartalmának szerkesztéséhez kattintson a **szerkesztése** alatt és a munkafüzet szakasz jobb gomb.

![Application Insights munkafüzetek szakasz szerkesztési vezérlők](./media/app-insights-usage-workbooks/editing-controls.png)

1. Ha elkészült szakasz módosításához kattintson a **végzett szerkesztése** szakasz bal alsó sarkában található.

2. Szakasz duplikált létrehozásához kattintson a **ebben a szakaszban klónozni** ikonra. Ismétlődő szakaszok létrehozása egy nagy előző ismétlési elvesztése nélkül lekérdezés felépítésének módját.

3. Kattintson egy munkafüzet szakaszt áthelyezéséhez a **feljebb** vagy **lejjebb** ikonra.

4. A szakasz végleges eltávolításához kattintson a **eltávolítása** ikonra.

## <a name="adding-usage-data-visualization-sections"></a>Használati adatok képi megjelenítés szakaszok hozzáadása

Munkafüzetek beépített használati analytics képi megjelenítések négy típusú kínálnak. Minden ad választ az alkalmazás a használati közös kérdése. Vegye fel a táblázatokat és diagramokat, ezek a szakaszok eltérő, vegye fel az Analytics lekérdezési szakaszok (lásd alább).

A felhasználók hozzáadásához munkamenetek, az események vagy az adatmegőrzési szakasz a munkafüzetbe, használja a **felhasználó hozzáadása** vagy más megfelelő gombra a munkafüzet alján, vagy bármely szakasz alján.

![Felhasználók szakaszban munkafüzetekben](./media/app-insights-usage-workbooks/users-section.png)

**Felhasználók** szakaszok választ "hány felhasználó néhány tekint, vagy használja a webhely néhány funkciója?"

**Munkamenetek** szakaszok választ "hány munkamenetek volt igénybe néhány lap megtekintése vagy a webhely néhány funkció használatával?"

**Események** szakaszok választ "hány alkalommal nem felhasználók néhány a lapnak a megtekintésére vagy használja a webhely néhány szolgáltatást?"

A vezérlők és a képi megjelenítések egy készletet minden, a következő három szakasz típusú kínálja:

* [További tudnivalók a felhasználói munkamenetek és események szakaszok szerkesztése](app-insights-usage-segmentation.md)
* Váltás a fő diagram, hisztogram rácsok, automatikus insights és minta felhasználók képi megjelenítések használatával a **diagram megjelenítése**, **rácsvonalak megjelenítése**, **megjelenítése Insights**, és **Ezek próbafelhasználók** jelölőnégyzeteket minden szakasz elején.

![A munkafüzet megőrzési szakasz](./media/app-insights-usage-workbooks/retention-section.png)

**Megőrzési** szakaszok választ "Személyek néhány tekint, vagy a használt egyes szolgáltatások egy nap vagy hét, hány visszatért az ezt követő nap vagy hét?"

* [Tudjon meg többet az adatmegőrzési szakaszok szerkesztése](app-insights-usage-retention.md)
* A választható általános megőrzési diagram használatával váltása a **megjelenítése a teljes megőrzési diagram** jelölőnégyzetet a szakasz tetején.

## <a name="adding-application-insights-analytics-sections"></a>Application Insights Analytics szakaszok hozzáadása

![A munkafüzeteket Analytics szakasz](./media/app-insights-usage-workbooks/analytics-section.png)

Az Application Insights Analytics lekérdezésszakaszt adhat hozzá a munkafüzethez a **hozzáadása Analytics lekérdezési** gombra a munkafüzet alján, vagy bármely szakasz alján.

Vegyen fel tetszőleges lekérdezések keresztül az Application Insights adatainak munkafüzetek Analytics lekérdezés szakaszok segítségével. Ez azt jelenti, hogy Analytics lekérdezési szakaszok kell lennie a nyissa meg a a felhasználók, a munkamenetek, az események és a megőrzési, például a fent felsorolt négy eltérő a hellyel kapcsolatos kérdése megválaszolásához:

* Hány kivételek fejeződött a hely throw idő alatt, csökken a használat során?
* Mi volt a lapbetöltési idők néhány lap megtekintésének felhasználók terjesztése?
* Hány felhasználó láthatók a webhely néhány azon lapok készlete, de ez nem valamilyen egyéb lapok? Ez akkor lehet hasznos megértéséhez, ha a felhasználók, akik használni a webhely funkciók különböző részhalmazai fürttel rendelkezik (használja a `join` operátor ezzel a `kind=leftanti` Naplóelemzési lekérdezés nyelvű módosítóval).

Használja a [Naplóelemzési lekérdezése nyelvi referencia](https://docs.loganalytics.io/) kapcsolatos lekérdezések írásáról további.

## <a name="adding-text-and-markdown-sections"></a>Szöveg- és Markdown-szakaszok hozzáadása

Segítséget nyújt a fejlécére kattintva rendezhető, magyarázatot és magyarázatokkal hozzáadása a munkafüzeteket azokat a leírásokat kapcsolja táblázatokat és diagramokat olyan készlete. Szöveg részeiben munkafüzetek támogatása a [Markdown-szintaxis](https://daringfireball.net/projects/markdown/) formázás, például a fejlécére kattintva rendezhető, félkövér, dőlt és felsorolásokat szöveg.

A munkafüzet szöveges szakaszt hozzáadásához használja a **szöveget** gombra a munkafüzet alján, vagy bármely szakasz alján.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>És munkafüzetek megosztása a munkatársaival

Az Application Insights-erőforrást, vagy mentett munkafüzetek a **jelentések** privát, akkor vagy a következő szakasz a **megosztott jelentések** hozzáféréssel rendelkező összes felhasználó számára elérhető szakaszban a Application Insights-erőforrást. Az erőforrás a munkafüzetek megtekintéséhez kattintson a **nyitott** műveletsávon gombra.

A munkafüzetet, hogy e **jelentések**:

1. Kattintson a **nyitott** műveletsávon
2. Kattintson a "..." gomb melletti meg szeretné osztani a munkafüzet
3. Kattintson a **megosztott jelentések áthelyezése**.

A munkafüzet egy hivatkozás, vagy e-mailben megosztásához kattintson **megosztása** műveletsávon. Ne feledje, hogy a hivatkozás címzettjei kell-e az Azure portálon a munkafüzet az erőforráshoz való hozzáférés. Ahhoz, hogy a módosításokat, a címzettnek van szüksége, legalább az erőforrás-közreműködői jogokat.

PIN-kód egy Azure-irányítópultot munkafüzet mutató hivatkozást:

1. Kattintson a **nyitott** műveletsávon
2. Kattintson a "..." gombra a munkafüzetet, amelyet szeretne rögzíteni mellett
3. Kattintson a **rögzítés az irányítópulton**.

## <a name="next-steps"></a>További lépések

## <a name="next-steps"></a>További lépések
- Ahhoz, hogy a használati tapasztalatok, küldésének megkezdése [egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [lapmegtekintés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Ha egyéni események vagy Lapmegtekintések már küld, megismerkedhet a használati eszközök további, a szolgáltatás használatát a felhasználók.
    - [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](app-insights-usage-retention.md)
    - [Felhasználói folyamatok](app-insights-usage-flows.md)
    - [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)
    
