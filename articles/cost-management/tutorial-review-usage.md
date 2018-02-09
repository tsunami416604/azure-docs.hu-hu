---
title: "A használat és a költségek áttekintése az Azure Cost Managementben | Microsoft Docs"
description: "A használat és a költségek áttekintésével nyomon követheti a trendeket, észlelheti a hatékonysági hiányosságokat, és riasztásokat állíthat be."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 72c5c9ff13577e7b4008c42133742d8de4904ae0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="review-usage-and-costs"></a>A használat és a költségek áttekintése

Az Azure Cost Management by Cloudynben megtekintheti a használati mutatókat és a költségeket, így nyomon követheti a trendeket, észlelheti a hatékonysági hiányosságokat, és riasztásokat állíthat be. Minden használati és költségadat megjelenik a Cloudyn irányítópultjain és jelentéseiben. Az oktatóanyagban szereplő példák bemutatják, hogyan tekintheti át a használatot és a költségeket az irányítópultok és jelentések segítségével. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Használati és költségtrendek követése
> * A használat hatékonysági hiányosságainak észlelése
> * Szokatlan kiadásokra és túlköltekezésre figyelmeztető riasztások létrehozása



## <a name="open-the-cloudyn-portal"></a>A Cloudyn portál megnyitása

A használati és költségadatokat a Cloudyn portálon tekintheti át. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com helyre, és jelentkezzen be.

## <a name="track-usage-and-cost-trends"></a>Használati és költségtrendek követése

A használattal és költségekkel kapcsolatos tényleges kiadásokat időalapú jelentésekkel követheti, és megfigyelheti a kirajzolódó tendenciákat. A tendenciák követéséhez használja a tényleges időalapú költségeket tartalmazó jelentést. A portál tetején a Jelentések menüben kattintson a **Cost**(Költségek) > **Cost Analysis**(Költségelemzés) > **Actual Cost Over Time** (Tényleges időalapú költségek) elemre. Amikor először megnyitja a jelentést, még nincsenek beállítva rajta csoportok vagy szűrők.

Egy példa a jelentésekre:

![példa jelentésre](./media/tutorial-review-usage/actual-cost01.png)

A jelentés az elmúlt 30 nap összes kiadását megjeleníti. Ha csak az Azure-szolgáltatásokhoz tartozó költségeket szeretné megtekinteni, alkalmazza a Service (Szolgáltatás) csoportot, és szűrjön rá az összes Azure-szolgáltatásra. A következő képen a szűrt szolgáltatások láthatók.

![szűrt szolgáltatások](./media/tutorial-review-usage/actual-cost02.png)

Az előző példában 2017. augusztus 31-től kezdve a korábbinál kevesebb kiadás volt tapasztalható. Ez a költségtrend nagyjából kilenc napon keresztül volt megfigyelhető a különböző szolgáltatásoknál. Ezt követően a költségek visszaálltak a korábbi szintre. Ha túl sok az oszlop, az megnehezítheti a trendek értelmezését. A jelentés nézetét módosíthatja vonal- vagy területdiagramra, hogy az adatok másképp jelenjenek meg. Az alábbi képen a trend sokkal jobban kirajzolódik.

![trend a jelentésben](./media/tutorial-review-usage/actual-cost03.png)

A példában jól látható, hogy az Azure Storage költségei 2017. augusztus 31-ével alacsonyabbak lettek, miközben a többi Azure-szolgáltatással kapcsolatos kiadások változatlanok maradtak. Mi okozta vajon a kiadások csökkenését? A példánkban az történt, hogy néhány alkalmazott szabadságon volt a kérdéses időszakban, és nem használta a Storage szolgáltatást.

A használati és költségtrendek követéséről szóló oktatóvideó: [Időalapú felhőszámlázási adatok elemzése az Azure Cost Management by Cloudynben](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>A használat hatékonysági hiányosságainak észlelése

Az optimalizálással kapcsolatos jelentések segítenek növelni a hatékonyságot és optimalizálni a használatot, illetve azonosítani azokat a pontokat, ahol megtakarítások érhetők el a felhőalapú erőforrásokra fordított költségekben. Ezek a jelentések különösen nagy segítséget nyújthatnak azon költséghatékonysági méretezési javaslatok kidolgozásához, amelyek a tétlen vagy túl költséges virtuális gépek mennyiségének csökkentésére irányulnak.

Amikor a cégek átviszik az erőforrásaikat a felhőbe, gyakran jelent problémát a megfelelő virtualizálási stratégia kialakítása. Sokszor alkalmaznak hasonló megközelítést, mint a helyszíni virtualizálási környezethez létrehozott virtuális gépek esetében. Azt feltételezik, hogy ha a helyszíni virtuális gépeket módosítás nélkül helyezik át a felhőbe, a költségeik csökkennek majd. Ez a megközelítés azonban nagy valószínűséggel nem eredményezi a költségek csökkenését.

A probléma forrása, hogy a meglévő infrastruktúra már ki van fizetve. A felhasználók bármikor létrehozhatnak nagy méretű virtuális gépeket, és tetszés szerint működtethetik azokat – üresjáratban vagy sem, ennek nem sok jelentősége van. A nagy méretű vagy tétlen virtuális gépek felhőbe való áthelyezése azonban várhatóan a költségek *növekedését* fogja eredményezni. A felhőszolgáltatókkal kötött megállapodások során nagyon fontos szerepet játszik az erőforrások megfelelő költséglefoglalása. Akár teljes mértéken kihasználja őket, akár nem, a lefoglalt erőforrásokat ki kell fizetnie.

A költséghatékony méretezési javaslatokat tartalmazó jelentés a virtuálisgép-példánytípusok kapacitásának a processzor- és memóriahasználati előzményadatokkal való összevetésével azonosítja az éves szinten lehetséges megtakarításokat.  

A portál tetején, a jelentések menüjében kattintson az **Optimizer** (Optimalizáló) > **Pricing Optimization** (Díjszabás optimalizálása) > **Cost Effective Sizing Recommendations** (Költséghatékony méretezési javaslatok) lehetőségre. A szolgáltatónál szűrjön az Azure-ra, hogy csak az Azure-beli virtuális gépek adatai jelenjenek meg. Íme egy példa.

![Azure-beli virtuális gépek](./media/tutorial-review-usage/sizing01.png)

Példánkban 3114 dollár takarítható meg a virtuálisgép-példánytípusok módosítására vonatkozó javaslatok elfogadásával. Kattintson a plusz (+) jelre az első javaslat **Details** (Részletek) oszlopában. Megjelennek az első javaslat részletei.

![javaslat részletei](./media/tutorial-review-usage/sizing02.png)

A virtuálisgép-példányok azonosítóit a **List of Candidates** (Jelöltek listája) melletti plusz (+) jelre kattintva tekintheti meg.

![Jelöltek listája](./media/tutorial-review-usage/sizing03.png)

A használattal kapcsolatos hatékonysági hiányosságok felderítéséről szóló oktatóvideó: [Virtuális gépek méretének optimalizálása az Azure Cost Management by Cloudynban](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Szokatlan kiadásokra figyelmeztető riasztások létrehozása

Az érintetteket automatikusan figyelmeztetheti a rendellenes kiadásokról és a túlköltekezési kockázatokról. A költségvetés és a költségek küszöbértékeit használó jelentések alapján gyorsan és egyszerűen hozhat létre riasztásokat.

Riasztásokat bármely kiadáshoz és bármely költségjelentés alapján létrehozhat. Példánkban a rendszer a tényleges időalapú költségeket tartalmazó jelentés alapján értesíti, amint az Azure-beli virtuális gépekkel kapcsolatos kiadások megközelítik a teljes költségkeretet. A portál tetején a Jelentések menüben kattintson a **Cost**(Költségek) > **Cost Analysis**(Költségelemzés) > **Actual Cost Over Time** (Tényleges időalapú költségek) elemre. A **Groups** (Csoportok) alatt állítsa be a **Service** (Szolgáltatás), a **Filter on the service** (Szűrés a következő szolgáltatásra) alatt pedig az **Azure/VM** (Azure/virtuális gép) lehetőséget. A jelentés jobb felső sarkában kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget.

A **Scheduling** (Ütemezés) lapon állítsa be a jelentés elküldését a saját e-mail-címére a kívánt gyakorisággal. Az e-mailben küldött jelentés az összes használt címkét, csoportosítást és szűrőt tartalmazza majd. Kattintson a **Threshold** (Küszöbérték) lapra, és válassza az **Actual Cost vs. Threshold** (Tényleges költségek a küszöbértékhez képest) lehetőséget. Ha a teljes költségvetése 500 000 dollár, és szeretne értesítést kapni, amikor a költségek elérik ennek a felét, hozzon létre egy **vörös riasztást** 250 000 és egy **sárga riasztást** 240 000 dollárra. Ezután válassza ki az egymást követő riasztások számát. Ha a riasztások száma eléri a megadott számot, a rendszer nem küld további riasztásokat. Mentse az ütemezett jelentést.

![példa jelentésre](./media/tutorial-review-usage/schedule-alert01.png)

Azt is megteheti, hogy a Cost Percentage vs. Budget (Költségszázalék a költségvetési küszöbértékhez képest) mutatót választja a riasztások alapjául. Ennek a mutatónak a használatával pontos összegek helyett a költségvetés százalékos arányában határozhatja meg a küszöböket.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Használati és költségtrendek követése
> * A használat hatékonysági hiányosságainak észlelése
> * Szokatlan kiadásokra és túlköltekezésre figyelmeztető riasztások létrehozása


Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan szabályozható az adatokhoz való hozzáférés.

> [!div class="nextstepaction"]
> [Az adatok hozzáférésének szabályozása](tutorial-user-access.md)
