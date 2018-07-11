---
title: Oktatóanyag - A használat és a költségek áttekintése az Azure Cost Managementben | Microsoft Docs
description: Ebben az oktatóanyagban a használat és a költségek áttekintésével nyomon követheti a trendeket, észlelheti a hatékonysági hiányosságokat, és riasztásokat állíthat be.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 5505ec8dd25e5468fad81d4eb26980202425969a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "35628125"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Oktatóanyag: A használat és a költségek áttekintése

Az Azure Cost Managementben megtekintheti a használati mutatókat és a költségeket, így nyomon követheti a trendeket, észlelheti a hatékonysági hiányosságokat, valamint riasztásokat állíthat be. Minden használati és költségadat megjelenik a Cloudyn irányítópultjain és jelentéseiben. Az oktatóanyagban szereplő példák bemutatják, hogyan tekintheti át a használatot és a költségeket az irányítópultok és jelentések segítségével. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Használati és költségtrendek követése
> * A használat hatékonysági hiányosságainak észlelése
> * Szokatlan kiadásokra és túlköltekezésre figyelmeztető riasztások létrehozása
> * Adatok exportálása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy Azure-fiókkal.
- Rendelkeznie kell az Azure Cost Management próbaverziójával vagy fizetett előfizetésével.

## <a name="open-the-cloudyn-portal"></a>A Cloudyn portál megnyitása

A használati és költségadatokat a Cloudyn portálon tekintheti át. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.

## <a name="track-usage-and-cost-trends"></a>Használati és költségtrendek követése

A használattal és költségekkel kapcsolatos tényleges kiadásokat időalapú jelentésekkel követheti, és megfigyelheti a kirajzolódó tendenciákat. A tendenciák követéséhez használja a tényleges időalapú költségeket tartalmazó jelentést. A portál bal felső részén kattintson a **Costs**(Költségek) > **Cost Analysis**(Költségelemzés) > **Actual Cost Over Time** (Tényleges időalapú költségek) elemre. Amikor először megnyitja a jelentést, még nincsenek beállítva rajta csoportok vagy szűrők.

Egy példa a jelentésekre:

![példa jelentésre](./media/tutorial-review-usage/actual-cost01.png)

A jelentés az elmúlt 30 nap összes kiadását megjeleníti. Ha csak az Azure-szolgáltatásokhoz tartozó költségeket szeretné megtekinteni, alkalmazza a Service (Szolgáltatás) csoportot, és szűrjön rá az összes Azure-szolgáltatásra. A következő képen a szűrt szolgáltatások láthatók.

![szűrt szolgáltatások](./media/tutorial-review-usage/actual-cost02.png)

Az előző példában 2017. augusztus 31-től kezdve a korábbinál kevesebb kiadás volt tapasztalható. Ez a költségtrend nagyjából kilenc napon keresztül volt megfigyelhető a különböző szolgáltatásoknál. Ezt követően a költségek visszaálltak a korábbi szintre. Ha túl sok az oszlop, az megnehezítheti a trendek értelmezését. A jelentés nézetét módosíthatja vonal- vagy területdiagramra, hogy az adatok másképp jelenjenek meg. Az alábbi képen a trend sokkal jobban kirajzolódik.

![trend a jelentésben](./media/tutorial-review-usage/actual-cost03.png)

A példában jól látható, hogy az Azure Storage költségei 2017. augusztus 31-ével alacsonyabbak lettek, miközben a többi Azure-szolgáltatással kapcsolatos kiadások változatlanok maradtak. Mi okozta vajon a kiadások csökkenését? A példánkban az történt, hogy néhány alkalmazott szabadságon volt a kérdéses időszakban, és nem használta a Storage szolgáltatást.

A használati és költségtrendek követéséről szóló oktatóvideóért tekintse meg a [felhőszámlázási adatok időalapú elemzését az Azure Cost Managementben](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>A használat hatékonysági hiányosságainak észlelése

Az optimalizálással kapcsolatos jelentések segítenek növelni a hatékonyságot és optimalizálni a használatot, illetve azonosítani azokat a pontokat, ahol megtakarítások érhetők el a felhőalapú erőforrásokra fordított költségekben. Ezek a jelentések különösen nagy segítséget nyújthatnak azon költséghatékonysági méretezési javaslatok kidolgozásához, amelyek a tétlen vagy túl költséges virtuális gépek mennyiségének csökkentésére irányulnak.

Amikor a cégek átviszik az erőforrásaikat a felhőbe, gyakran jelent problémát a megfelelő virtualizálási stratégia kialakítása. Sokszor alkalmaznak hasonló megközelítést, mint a helyszíni virtualizálási környezethez létrehozott virtuális gépek esetében. Azt feltételezik, hogy ha a helyszíni virtuális gépeket módosítás nélkül helyezik át a felhőbe, a költségeik csökkennek majd. Ez a megközelítés azonban nagy valószínűséggel nem eredményezi a költségek csökkenését.

A probléma forrása, hogy a meglévő infrastruktúra már ki van fizetve. A felhasználók bármikor létrehozhatnak nagy méretű virtuális gépeket, és tetszés szerint működtethetik azokat – üresjáratban vagy sem, ennek nem sok jelentősége van. A nagy méretű vagy tétlen virtuális gépek felhőbe való áthelyezése azonban várhatóan a költségek *növekedését* fogja eredményezni. A felhőszolgáltatókkal kötött megállapodások során nagyon fontos szerepet játszik az erőforrások megfelelő költséglefoglalása. Akár teljes mértéken kihasználja őket, akár nem, a lefoglalt erőforrásokat ki kell fizetnie.

A költséghatékony méretezési javaslatokat tartalmazó jelentés a virtuálisgép-példánytípusok kapacitásának a processzor- és memóriahasználati előzményadatokkal való összevetésével azonosítja az éves szinten lehetséges megtakarításokat.  

A portál tetején lévő menüben kattintson az **Optimizer** (Optimalizáló) > **Sizing Optimization** (Méretezés optimalizálása) > **Cost Effective Sizing Recommendations** (Költséghatékony méretezési javaslatok) elemre. A szolgáltatónál szűrjön az Azure-ra, hogy csak az Azure-beli virtuális gépek adatai jelenjenek meg. Íme egy példa.

![Azure-beli virtuális gépek](./media/tutorial-review-usage/sizing01.png)

Példánkban 3114 dollár takarítható meg a virtuálisgép-példánytípusok módosítására vonatkozó javaslatok elfogadásával. Kattintson a plusz (+) jelre az első javaslat **Details** (Részletek) oszlopában. Megjelennek az első javaslat részletei.

![javaslat részletei](./media/tutorial-review-usage/sizing02.png)

A virtuálisgép-példányok azonosítóit a **List of Candidates** (Jelöltek listája) melletti plusz (+) jelre kattintva tekintheti meg.

![Jelöltek listája](./media/tutorial-review-usage/sizing03.png)

A használattal kapcsolatos hatékonysági hiányosságok felderítéséről szóló oktatóvideóért tekintse meg a [virtuális gépek méretének optimalizálását az Azure Cost Managementben](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Szokatlan kiadásokra figyelmeztető riasztások létrehozása

Az érintetteket automatikusan figyelmeztetheti a rendellenes kiadásokról és a túlköltekezési kockázatokról. A költségvetés és a költségek küszöbértékeit használó jelentések alapján gyorsan és egyszerűen hozhat létre riasztásokat.

Riasztásokat bármely kiadáshoz és bármely költségjelentés alapján létrehozhat. Példánkban a rendszer a tényleges időalapú költségeket tartalmazó jelentés alapján értesíti, amint az Azure-beli virtuális gépekkel kapcsolatos kiadások megközelítik a teljes költségkeretet. Az összes további lépés szükséges a riasztás létrehozásához. A portál tetején lévő menüben kattintson a **Costs**(Költségek) > **Cost Analysis**(Költségelemzés) > **Actual Cost Over Time** (Tényleges időalapú költségek) elemre. A **Groups** (Csoportok) alatt állítsa be a **Service** (Szolgáltatás), a **Filter on the service** (Szűrés a következő szolgáltatásra) alatt pedig az **Azure/VM** (Azure/virtuális gép) lehetőséget. A jelentés jobb felső sarkában kattintson az **Actions** (Műveletek) gombra, majd válassza a **Schedule report** (Jelentés ütemezése) lehetőséget.

A jelentés mentésére vagy ütemezésére szolgáló mező **Scheduling** (Ütemezés) lapján állítsa be a jelentés elküldését a saját e-mail-címére a kívánt gyakorisággal. Ügyeljen arra, hogy a **Send via email** (Küldés e-mailben) beállítás legyen kiválasztva. Az e-mailben küldött jelentés az összes használt címkét, csoportosítást és szűrőt tartalmazza majd. Kattintson a **Threshold** (Küszöbérték) lapra, és válassza az **Actual Cost vs. Threshold** (Tényleges költségek a küszöbértékhez képest) lehetőséget. Ha a teljes költségvetése 500 000 dollár, és szeretne értesítést kapni, amikor a költségek elérik ennek a felét, hozzon létre egy **vörös riasztást** 250 000 és egy **sárga riasztást** 240 000 dollárra. A megadott értékekben ne használjon vesszőt. Ezután válassza ki az egymást követő riasztások számát. Ha a riasztások száma eléri a megadott számot, a rendszer nem küld további riasztásokat. Mentse az ütemezett jelentést.

![példa jelentésre](./media/tutorial-review-usage/schedule-alert01.png)

Azt is megteheti, hogy a Cost Percentage vs. Budget (Költségszázalék a költségvetési küszöbértékhez képest) mutatót választja a riasztások alapjául. Ennek a mutatónak a használatával pontos összegek helyett a költségvetés százalékos arányában határozhatja meg a küszöböket.

## <a name="export-data"></a>Adatok exportálása

A jelentésekhez kapcsolódó riasztások létrehozásához hasonlóan a jelentésekből adatokat is exportálhat. Előfordulhat például, hogy a Cloudyn-fiókok listáját vagy más felhasználói adatokat szeretne exportálni. Egy jelentés exportálásához nyissa meg a jelentést, majd kattintson a jobb felső sarokban található **Actions** (Műveletek) gombra. Kiválaszthatja például az **Export all report data** (Az összes jelentésadat exportálása) elemet, így letöltheti és kinyomtathatja az információkat. Másik lehetőségként kiválaszthatja a **Schedule report** (Jelentés ütemezése) beállítást a jelentés e-mailben történő elküldésének ütemezéséhez.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Használati és költségtrendek követése
> * A használat hatékonysági hiányosságainak észlelése
> * Szokatlan kiadásokra és túlköltekezésre figyelmeztető riasztások létrehozása
> * Adatok exportálása


A következő oktatóanyag azt mutatja be, hogyan jelezheti előre a kiadásokat az előzményadatok alapján.

> [!div class="nextstepaction"]
> [Jövőbeli kiadások előrejelzése](tutorial-forecast-spending.md)
