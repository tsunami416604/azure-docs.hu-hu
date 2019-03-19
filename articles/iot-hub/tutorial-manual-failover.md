---
title: Azure IoT hub manuális feladatátvétele | Microsoft Docs
description: Azure IoT hub manuális feladatátvételének bemutatása
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: ff8ef576a0dae0f03cc0c80f83ce0c24cf3945a2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093735"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Oktatóanyag: Manuális feladatátvétel végrehajtása egy IoT hub (nyilvános előzetes verzió)

A manuális feladatátvétel az IoT Hub szolgáltatás egyik funkciója, amelynek segítségével elvégezhető egy hub műveleteinek [feladatátvétele](https://en.wikipedia.org/wiki/Failover) az elsődleges régióból a megfelelő, földrajzilag párosított másodlagos Azure-régióba. A manuális feladatátvétel regionális szintű katasztrófák vagy hosszabb időtartamú szolgáltatáskimaradás alkalmával hajtható végre. Emellett végrehajthat tervezett feladatátvételt is a vészhelyreállítási képességek tesztelésére, bár ehhez inkább egy tesztelési célú, és nem egy éles környezetben futó IoT hub használatát javasoljuk. A manuális feladatátvételi funkció további költségek nélkül érhető el ügyfeleink számára.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * IoT hub létrehozása az Azure Portal használatával. 
> * Feladatátvétel végrehajtása. 
> * A másodlagos helyen futó hub megtekintése.
> * Feladat-visszavétel végrehajtása az IoT hub műveleteinek visszahelyezésére az elsődleges helyre. 
> * A központ a megfelelő helyen való megfelelő működésének ellenőrzése.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Kattintson a **+ Erőforrás létrehozása** elemre, és válassza az **Eszközök internetes hálózata**, majd az **IoT Hub** elemet.

   ![IoT hub létrehozását ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-01.png)

3. Válassza az **Alapvető beállítások** lapot. Töltse ki az alábbi mezőket.

    **Előfizetés**: Válassza ki a használni kívánt Azure-előfizetést.

    **Erőforráscsoport**: Kattintson az **Új létrehozása** gombra, majd adja a **ManlFailRG** nevet az erőforráscsoportnak.

    **Régió**: Válasszon ki egy közeli régiót, amely része az előzetes verziónak. Ebben az oktatóanyagban a következőt használjuk: `westus2`. A feladatátvétel csak földrajzilag párosított Azure-régiók között hajtható végre. A westus2 régióval földrajzilag párosított régió a WestCentralUS régió.
    
   > [!NOTE]
   > Manuális feladatátvétel jelenleg nyilvános előzetes verzióban érhető el, és el *nem* a következő Azure-régióban érhető el: USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Észak-Európa, Nyugat-Európa, Dél-Brazília és USA déli középső RÉGIÓJA.

   **IoT hub neve**: Adja meg az IoT hub nevét. A hub nevének globálisan egyedinek kell lennie. 

   ![IoT hub létrehozásához az Alapvető beállítások panelt ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Kattintson az **Áttekintés + létrehozás** elemre. (Az alapértelmezett méret- és léptékbeállításokat használja.) 

4. Tekintse át az információkat, majd kattintson a **Létrehozás** elemre az IoT hub létrehozásához. 

   ![IoT hub létrehozásának utolsó lépését ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Manuális feladatátvétel végrehajtása

Vegye figyelembe, hogy naponta legfeljebb két feladatátvétel és két feladat-visszavétel hajtható végre minden egyes IoT hub esetében.

1. Kattintson az **Erőforráscsoportok** elemre, majd válassza ki a **ManlFailRG** erőforráscsoportot. Kattintson a hubra az erőforrások listájában. 

2. Az IoT Hub panel **Rugalmasság** területén kattintson a **Manuális feladatátvétel (előzetes verzió)** lehetőségre. Vegye figyelembe, hogy ha a hub nem egy érvényes régióban lett telepítve, a manuális feladatátvétel lehetőség le lesz tiltva.

   ![IoT Hub-tulajdonságok panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-01.png)

3. A Manuális feladatátvétel panelen láthatja az **IoT hub elsődleges helye** és az **IoT hub másodlagos helye** elemeket. Az elsődleges hely kezdetben az IoT hub létrehozásakor megadott hellyel egyezik, és mindig azt a helyet jelzi, ahol a hub épp aktív. A másodlagos hely az elsődleges hellyel párosított standard [földrajzilag párosított Azure-régió](../best-practices-availability-paired-regions.md). A helyek értékei nem módosíthatók. Ebben az oktatóanyagban az elsődleges régió a `westus2`, a másodlagos régió pedig a `WestCentralUS`.

   ![A Manuális feladatátvétel panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-02.png)

3. A Manuális feladatátvétel panel tetején kattintson a **Feladatátvétel inicializálása** lehetőségre. Megjelenik a **Manuális feladatátvétel megerősítése** panel. Írja be az IoT hub nevét, megerősítve, hogy ezen szeretne feladatátvételt végrehajtani. Ezután a feladatátvétel indításához kattintson az **OK** gombra.

   A manuális feladatátvétel végrehajtásához szükséges idő a hubhoz regisztrált eszközök számával arányos. Például 100 000 eszköz esetén a feladatátvétel 15 percig tarthat, ötmillió eszköz esetén azonban egy órát vagy többet is igénybe vehet.

4. A **Manuális feladatátvétel megerősítése** panelen írja be az IoT hub nevét, megerősítve, hogy ezen szeretne feladatátvételt végrehajtani. Ezután a feladatátvétel indításához kattintson az OK gombra. 

   ![A Manuális feladatátvétel panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Amíg a manuális feladatátvételi folyamat fut, egy szalagcím jelenik meg a Manuális feladatátvétel panelen, amely tájékoztatja, hogy manuális feladatátvétel van folyamatban. 

   ![Folyamatban lévő manuális feladatátvétel képernyőképe](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Ha bezárja, majd az Erőforráscsoport panelen rákattintva újból megnyitja az IoT Hub panelt, egy szalagcím jelenik meg, amely tájékoztatja, hogy a hub nem aktív. 

   ![Inaktív IoT hub képernyőképe](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Miután a folyamat befejeződött, az elsődleges és másodlagos régiók felcserélődnek a Manuális feladatátvétel oldalon, és a hub újra aktív lesz. Ebben a példában az elsődleges régió most a `WestCentralUS`, a másodlagos régió pedig a `westus2`. 

   ![Befejeződött feladatátvétel képernyőképe](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása 

A manuális feladatátvétel végrehajtását követően a hub feladatait visszahelyezheti az eredeti elsődleges régióba – ezt feladat-visszavételnek nevezzük. Ha épp most hajtotta végre a feladatátvételt, körülbelül egy órát várnia kell, mielőtt kezdeményezhetné a feladat-visszavételt. Ha ennél hamarabb próbál feladat-visszavételt végrehajtani, egy hibaüzenet jelenik meg.

A feladat-visszavétel a manuális feladatátvételhez hasonlóan történik. A lépések a következők: 

1. Feladat-visszavétel végrehajtásához lépjen vissza az IoT hubhoz tartozó IoT Hub panelre.

2. Az IoT Hub panel **Rugalmasság** területén kattintson a **Manuális feladatátvétel (előzetes verzió)** lehetőségre. 

3. A Manuális feladatátvétel panel tetején kattintson a **Feladatátvétel inicializálása** lehetőségre. Megjelenik a **Manuális feladatátvétel megerősítése** panel. 

4. A **Manuális feladatátvétel megerősítése** panelen írja be az IoT hub nevét, megerősítve, hogy ezen szeretne feladat-visszavételt végrehajtani. Ezután a feladat-visszavétel indításához kattintson az OK gombra. 

   ![Manuális feladat-visszavételi kérelem képernyőképe](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   A szalagok jelennek meg a végezze el egy feladatátvételt a szakaszban leírtak szerint. A feladat-visszavétel befejeztével ismét a `westus2` jelenik meg elsődleges, a `WestCentralUS` pedig másodlagos helyként az eredeti beállításnak megfelelően.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Az oktatóanyaghoz létrehozott erőforrások eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben ez eltávolítja az IoT hubot és magát az erőforráscsoportot is. 

1. Kattintson az **Erőforráscsoportok** lehetőségre. 

2. Keresse meg és válassza ki a **ManlFailRG** erőforráscsoportot. Kattintson rá a megnyitásához. 

3. Kattintson az **Erőforráscsoport törlése** lehetőségre. Amikor a rendszer kéri, írja be az erőforráscsoport nevét, és kattintson a **Törlés** elemre a megerősítéshez. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt ismertette, hogyan konfigurálhat és hajthat végre manuális feladatátvételt, és hogyan kérhet feladat-visszavételt a következő feladatok végrehajtásával:

> [!div class="checklist"]
> * IoT hub létrehozása az Azure Portal használatával. 
> * Feladatátvétel végrehajtása. 
> * A másodlagos helyen futó hub megtekintése.
> * Feladat-visszavétel végrehajtása az IoT hub műveleteinek visszahelyezésére az elsődleges helyre. 
> * A központ a megfelelő helyen való megfelelő működésének ellenőrzése.

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [IoT-eszköz állapotának kezelése](tutorial-device-twins.md)
