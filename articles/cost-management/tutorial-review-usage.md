---
title: "Tekintse át a használati és Azure költség felügyeleti költségek |} Microsoft Docs"
description: "Tekintse át a használati és költségek nyomon követhető a trendeket, hatékonyság hiánya észleli, és létre riasztásokat."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 36ebffb41211e443cc1619df46f50247945cc57c
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="review-usage-and-costs"></a>Felülvizsgálati használati és költségek

Azure költség-kezelést úgy Cloudyn használatát mutatja meg, és költségek, hogy a trendeket, nyomon követheti hatékonyság hiánya észleli, és létre riasztásokat. Minden használati és a költséghatékonyság adatok Cloudyn irányítópultok és jelentések jelennek meg. Ebben az oktatóanyagban szereplő példák ismerteti, ha a használati és költségek az irányítópultok és jelentések megtekintésével. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Használat követése és trendek költsége
> * Használati hatékonyság hiánya észlelése
> * Szokatlan költségeik vagy túlköltekezés figyelmeztetések létrehozása



## <a name="open-the-cloudyn-portal"></a>Nyissa meg a Cloudyn portált

Megtekintheti az összes használati és költségek az Cloudyn portálon. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon a https://app.cloudyn.com, és jelentkezzen be.

## <a name="track-usage-and-cost-trends"></a>Használat követése és trendek költsége

Használati és az idő alatt jelentések segítségével azonosíthatja a trendeket a költségek töltött tényleges pénz követi nyomon. A tényleges költség keresztül idő jelentéssel trendek megtekint indításához. Kattintson a portál felső jelentések menü **költség** > **költség Analysis** > **tényleges költség keresztül idő**. A jelentés első megnyitásakor nem csoportokat vagy a szűrők vonatkoznak rá.

Íme egy példa a jelentés:

![Példa jelentésre](./media/tutorial-review-usage/actual-cost01.png)

Ebben a jelentésben az összes költségkeretet az elmúlt 30 napban. Az Azure-szolgáltatásokhoz csak költségeik megtekintéséhez alkalmazni a csoportot, és az összes Azure-szolgáltatások majd szűréséhez. A következő kép bemutatja a szűrt szolgáltatások.

![szűrt szolgáltatások](./media/tutorial-review-usage/actual-cost02.png)

Az előző példában a kevesebb pénz telt indítása a 2017-08-31 mint előtt. Adott költségtrend továbbra is fennáll, a különböző szolgáltatások készül kilenc napig. Ezt követően további költségeik továbbra is mint korábban. Túl sok oszlop azonban egy nyilvánvaló trend lehet átalakítani. A jelentések nézetének módosítható egy vonal, vagy terület diagram más nézetekhez megjelenő adatok megjelenítéséhez. Az alábbi ábrán a trend egyértelműbben.

![a jelentés trend](./media/tutorial-review-usage/actual-cost03.png)

A példa jól látható, hogy Azure tárolási költség eldobott indítása a 2017-08-31 miközben szint más Azure-szolgáltatásokkal beszállítói költségeit maradt. Hogy Igen, a kiadások okáról? Ebben a példában néhány alkalmazott igényléséhez szabadságon volt, és nem használja a tároló szolgáltatást.

Útmutató videót nyomon követése a használati és a költséghatékonyság trendeket, lásd: [elemzése a felhőben, számlázási adatok és az Azure költség Management Cloudyn idő](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Használati hatékonyság hiánya észlelése

Optimalizáló jelentések hatékonyság javítása, használat optimalizálása, és menti a felhőben lévő erőforrások költött módokon azonosítása. Akkor hasznosak lehetnek szánt csökkentése érdekében az üresjárati vagy drága virtuális gépek költséghatékony méretezési ajánlásokkal.

Gyakori probléma, amely befolyásolja a szervezetek, amikor azok kezdetben erőforrások áthelyezése felhőben a virtualizálási stratégiát is. Azok a helyszíni-virtualizálási környezet a virtuális gépek létrehozásához használt hasonló megközelítést gyakran használnak. És azok azt feltételezik, hogy költségek csökkentése a helyszíni virtuális gépek áthelyezését a felhőbe, mint-van. Azonban ezt a megközelítést valószínűleg nem csökkentheti a költségeket.

A probléma oka, hogy a meglévő infrastruktúra már kifizetett. Felhasználók létrehozására és nagy virtuális gép fut, ha azok tetszését tartsa – inaktív vagy nem és kevés következménye. Nagy vagy tétlen virtuális gépek áthelyezését a felhőbe várhatóan *növelése* költségeket. Költség-foglalás erőforrások fontos, amikor a felhőszolgáltatók megállapodásokat ad meg. Véglegesítenie kell hogy használ-e az erőforrás teljes vagy nem kell fizetnie.

A költség hatályos méretezési javaslatok jelentés azonosítja a potenciális éves megtakarítások összehasonlítja a virtuális gép példány típus kapacitás el a korábbi CPU és memória-használati adatok.  

Kattintson a jelentések menü felső részén a portálon, **optimalizáló** > **árképzési optimalizálási** > **költség hatályos méretezési javaslatok**. Az Azure-bA csak az Azure virtuális gépek tekintse meg a szolgáltató szűréséhez. Íme egy példa lemezképet.

![Azure-beli virtuális gépek](./media/tutorial-review-usage/sizing01.png)

Ebben a példában $3,114 sikerült menteni a következő ajánlások módosítása a Virtuálisgép-példány típusokon. Kattintson a plusz jelre (+) **részletek** az első javasolt. Az alábbiakban az első ajánlás részleteit.

![Javaslat részletei](./media/tutorial-review-usage/sizing02.png)

Virtuális gép példány azonosítók megtekintése mellett a pluszjelre kattintva **lista a deduplikációra kijelölt**.

![A deduplikációra kijelölt listája](./media/tutorial-review-usage/sizing03.png)

Útmutató videót használati hatékonyság hiánya észleléséről, lásd: [Virtuálisgép-méret optimalizálása Azure költség felügyeleti által Cloudyn](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Hozzon létre riasztásokat szokatlan kiadások

Automatikusan a költségkeret rendellenességek észlelését és túlköltekezés kockázatok érdekelt felek is riasztást. Gyorsan és egyszerűen létrehozhat jelentést, hogy támogatási riasztások költségvetési alapján, és a küszöbértékek költség riasztásokról.

A költségkeret használatával bármilyen bármely jelentés riasztást hoz létre. Ebben a példában a tényleges költség keresztül idő jelentéssel értesítést küldenek, ha a teljes költségvetést Azure virtuális gép költségeik megújítására. Kattintson a portál felső jelentések menü **költség** > **költség Analysis** > **tényleges költség keresztül idő**. Állítsa be **csoportok** való **szolgáltatás** és **szűrőt a szolgáltatás a** való **Azure vagy Virtuálisgép**. A felső sarkában a jelentést, kattintson a **műveletek** , és válassza **ütemezni a jelentést**.

Használja a **ütemezés** lapon, a jelentés segítségével a gyakorisága, amelyet e-mailt küldhet magának. Csoportosítás és a szűrés meg használt címkéket, az e-mailben elküldött jelentésben szereplő. Kattintson a **küszöbérték** kiválasztása lapon, majd válassza ki **tényleges költség vs. Küszöbérték**. Ha a teljes költségvetést $ 500 000 értéket és lesz értesítési amikor költségek közelében felét, hozzon létre egy **vörös színnel** $250,000, és egy **sárga riasztás** : $240,000. Ezután válassza az egymást követő riasztások számát. Amikor megjelenik a megadott riasztások teljes számát, további rendszer nem küld figyelmeztetést. Az ütemezett jelentés mentése.

![Példa jelentésre](./media/tutorial-review-usage/schedule-alert01.png)

Választhatja azt is, a költség százalékát vs. Keret küszöbérték metrika riasztások létrehozásához. Adott metrika használatával használhat költségvetési százalékos pénznem értékek helyett.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Használat követése és trendek költsége
> * Használati hatékonyság hiánya észlelése
> * Szokatlan költségeik vagy túlköltekezés figyelmeztetések létrehozása


Előzetes adataihoz való hozzáférés szabályozásának ismertetése a következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Adatok elérés](tutorial-user-access.md)
