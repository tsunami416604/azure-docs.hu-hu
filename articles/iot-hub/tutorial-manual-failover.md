---
title: Azure IoT hub manuális feladatátvétele | Microsoft Docs
description: Ismerje meg, hogyan hajthat végre manuális feladatátvételt az IoT hub egy másik régióba, és ellenőrizze, hogy működik, majd küldje vissza az eredeti régióba, és ellenőrizze újra.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 86b39beb2958194f7c86409c5d78992616234b05
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769905"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Oktatóanyag: Manuális feladatátvétel végrehajtása egy IoT-központszámára

A manuális feladatátvétel az IoT Hub szolgáltatás egyik funkciója, amelynek segítségével elvégezhető egy hub műveleteinek [feladatátvétele](https://en.wikipedia.org/wiki/Failover) az elsődleges régióból a megfelelő, földrajzilag párosított másodlagos Azure-régióba. A manuális feladatátvétel regionális szintű katasztrófák vagy hosszabb időtartamú szolgáltatáskimaradás alkalmával hajtható végre. Emellett végrehajthat tervezett feladatátvételt is a vészhelyreállítási képességek tesztelésére, bár ehhez inkább egy tesztelési célú, és nem egy éles környezetben futó IoT hub használatát javasoljuk. A manuális feladatátvételi funkció további költségek nélkül érhető el ügyfeleink számára.

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * IoT hub létrehozása az Azure Portal használatával. 
> * Feladatátvétel végrehajtása. 
> * A másodlagos helyen futó hub megtekintése.
> * Feladat-visszavétel végrehajtása az IoT hub műveleteinek visszahelyezésére az elsődleges helyre. 
> * A központ a megfelelő helyen való megfelelő működésének ellenőrzése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. Az oktatóanyagban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 

2. Kattintson a **+ Erőforrás létrehozása** elemre, és válassza az **Eszközök internetes hálózata**, majd az **IoT Hub** elemet.

   ![IoT hub létrehozását ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-01.png)

3. Válassza az **Alapok** lapot.

    **Előfizetés**: Válassza ki a használni kívánt Azure-előfizetést.

    **Erőforráscsoport**: Kattintson az **Új létrehozása** gombra, majd adja a **ManlFailRG** nevet az erőforráscsoportnak.

    **Régió**: válasszon egy önhöz közeli régiót. Ebben az oktatóanyagban a következőt használjuk: `West US 2`. A feladatátvétel csak földrajzilag párosított Azure-régiók között hajtható végre. Az USA nyugati régiójával párosított régió a WestCentralUS.
    
   **IoT hub neve**: Adja meg az IoT hub nevét. A hub nevének globálisan egyedinek kell lennie. 

   ![IoT hub létrehozásához az Alapvető beállítások panelt ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Kattintson az **Áttekintés + létrehozás** elemre. (Az alapértelmezett méret- és léptékbeállításokat használja.) 

4. Tekintse át az információkat, majd kattintson a **Létrehozás** elemre az IoT hub létrehozásához. 

   ![IoT hub létrehozásának utolsó lépését ábrázoló képernyőkép](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Manuális feladatátvétel végrehajtása

Vegye figyelembe, hogy naponta legfeljebb két feladatátvétel és két feladat-visszavétel hajtható végre minden egyes IoT hub esetében.

1. Kattintson az **Erőforráscsoportok** elemre, majd válassza ki a **ManlFailRG** erőforráscsoportot. Kattintson a hubra az erőforrások listájában. 

1. Az IoT-központ ablaktáblájának **Beállítások** területén kattintson a **Feladatátvétel**gombra.

   ![IoT Hub-tulajdonságok panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-01.png)

1. A Kézi feladatátvétel ablaktáblán az **Aktuális hely** és a **Feladatátvételi hely**látható. Az aktuális hely mindig azt a helyet jelzi, ahol a hub jelenleg aktív. A feladatátvételi hely a szabványos [Azure geo-párosított régió,](../best-practices-availability-paired-regions.md) amely párosítva van az aktuális helyen. A helyek értékei nem módosíthatók. Ebben az oktatóanyagban `West US 2` az aktuális hely, `West Central US`a feladatátvételi hely pedig .

   ![A Manuális feladatátvétel panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-02.png)

1. A Kézi feladatátvétel idomtábla tetején kattintson a **Feladatátvétel indítása gombra.** 

1. A megerősítés i. központ a megerősítést kérő ablaktáblán adja meg az IoT hub nevét, és győződjön meg arról, hogy az a feladatátvételt szeretné.In the confirmation pane, fill in the name of your IoT hub to confirm it's the one you you want to failover. Ezután a feladatátvétel elindításához kattintson a **Feladatátvétel gombra.**

   A manuális feladatátvétel végrehajtásához szükséges idő a hubhoz regisztrált eszközök számával arányos. Például 100 000 eszköz esetén a feladatátvétel 15 percig tarthat, ötmillió eszköz esetén azonban egy órát vagy többet is igénybe vehet.

   ![A Manuális feladatátvétel panel képernyőképe](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   A manuális feladatátvételi folyamat futása közben egy szalagcím jelenik meg, amely azt mondja, hogy a manuális feladatátvétel folyamatban van. 

   ![Folyamatban lévő manuális feladatátvétel képernyőképe](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Ha bezárja az IoT Hub ablaktáblát, és újra megnyitja az Erőforráscsoport ablaktáblán, megjelenik egy szalagcím, amely jelzi, hogy a központ egy manuális feladatátvétel közepén van. 

   ![Az IoT Hub feladatátvételét bemutató képernyőkép](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Miután elkészült, a kézi feladatátvételi lap aktuális és feladatátvételi területei tükröznek, és a hub ismét aktív. Ebben a példában az `WestCentralUS` aktuális hely most van, és a feladatátvételi hely most. `West US 2` 

   ![Befejeződött feladatátvétel képernyőképe](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Az áttekintő lapon egy szalagcím is látható, amely jelzi, `West Central US`hogy a feladatátvétel befejeződött, és az IoT Hub fut.

   ![A feladatátvételt bemutató képernyőkép befejeződött az áttekintő lapon](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása 

A manuális feladatátvétel végrehajtását követően a hub feladatait visszahelyezheti az eredeti elsődleges régióba – ezt feladat-visszavételnek nevezzük. Ha épp most hajtotta végre a feladatátvételt, körülbelül egy órát várnia kell, mielőtt kezdeményezhetné a feladat-visszavételt. Ha ennél hamarabb próbál feladat-visszavételt végrehajtani, egy hibaüzenet jelenik meg.

A feladat-visszavétel a manuális feladatátvételhez hasonlóan történik. A lépések a következők: 

1. Feladat-visszavétel végrehajtásához lépjen vissza az IoT hubhoz tartozó IoT Hub panelre.

2. Az IoT-központ ablaktáblájának **Beállítások** területén kattintson a **Feladatátvétel**gombra. 

3. A Kézi feladatátvétel idomtábla tetején kattintson a **Feladatátvétel indítása gombra.** 

4. A megerősítés i. ablaktáblán töltse ki az IoT hub nevét, és győződjön meg arról, hogy az szeretné visszasegíteni. Ezután a feladat-visszavétel indításához kattintson az OK gombra. 

   ![Manuális feladat-visszavételi kérelem képernyőképe](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Itt is a feladatátvétel végrehajtását ismertető szakaszban ismertetett szalagcímek jelennek meg. Miután a feladat-visszavétel `West US 2` befejeződött, ismét `West Central US` megjelenik az aktuális hely, és mint a feladatátvételi hely, az eredetileg beállított.

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
