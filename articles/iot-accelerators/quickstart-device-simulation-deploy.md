---
title: Eszközszimulációs megoldás üzembe helyezése és futtatása az Azure-ban | Microsoft Docs
description: Ebben a rövid útmutatóban üzembe helyezheti az Azure IoT-eszközszimulációt, és futtathat egy szimulációt
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/28/2018
ms.author: troyhop
ms.openlocfilehash: a109f3536ea8709313de3d1d6d17ce69c5652289
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753933"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>Rövid útmutató: IoT-eszközszimuláció üzembe helyezése és futtatása az Azure-ban

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe az Azure IoT-eszközszimulációt IoT-megoldásának teszteléséhez. A megoldásgyorsító telepítését követően első lépésként futtathat egy mintaszimulációt.

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-device-simulation"></a>Eszközszimuláció üzembe helyezése

Amikor üzembe helyezi az eszközszimulációt az Azure-előfizetéséhez, néhány konfigurációs beállítást meg kell adnia.

Jelentkezzen be az [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) webhelyen az Azure-fiók hitelesítő adataival.

Kattintson az **Eszközszimuláció** csempére:

![Válassza az Eszközszimuláció lehetőséget](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Kattintson a **Kipróbálom most** elemre az Eszközszimuláció leírását tartalmazó lapon:

![Kattintás a Kipróbálom most elemre](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

Az **Eszközszimulációs megoldás létrehozása** oldalon adjon meg egy egyedi **Megoldásnevet**.

Válassza ki a megoldásgyorsító üzembe helyezéséhez használni kívánt **Előfizetést** és **Régiót**. Általában az Önhöz legközelebbi régiót érdemes választani. Mindehhez [globális rendszergazdának vagy felhasználónak](iot-accelerators-permissions.md) kell lennie az előfizetésben.

Az Eszközszimulációs megoldással együtt használandó IoT Hub üzembe helyezéséhez jelölje be a jelölőnégyzetet. Később bármikor módosíthatja a szimulációja által használt IoT Hubot.

Kattintson a **Megoldás létrehozása** gombra a megoldás kiépítésének elkezdéséhez. A folyamat legalább öt percig tart:

![Az eszközszimulációs megoldás adatai](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Bejelentkezés a megoldásba

Ha a kiépítési folyamat befejeződött, bejelentkezhet az Eszközszimulációs példányba az **Indítás** gombra kattintva:

![Eszközszimuláció megnyitása](./media/quickstart-device-simulation-deploy/choosenew.png)

Kattintson az **Elfogadás** gombra az engedélykérések elfogadásához. Ekkor a böngészőben megjelenik az Eszközszimulációs megoldás irányítópultja.

Az első megnyitáskor az Eszközszimuláció irányítópultján lát egy **Első lépések** című útmutatót. Kattintson az első csempére egy mintaszimuláció megnyitásához. Ha bezárja az **Első lépések** című útmutatót, megnyithatja az **Egyszerű mintaszimulációt** az irányítópulton a hozzá tartozó csempére kattintva:

![A megoldás irányítópultja](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>Mintaszimuláció

A mintaszimulációt nem lehet szerkeszteni. A szimuláció az alábbi beállításokkal van konfigurálva:

| Beállítás             | Érték                       |
| ------------------- | --------------------------- |
| Cél IoT Hub      | Használja az előzetesen kiépített IoT Hub-ot |
| Eszközmodell        | Teherautó                       |
| Eszközök száma   | 10                          |
| Telemetria gyakorisága | 10 másodperc                  |
| Szimuláció időtartama | Határozatlan ideig fut            |

![Szimuláció konfigurálása](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>A szimuláció futtatása

Kattintson a **Szimuláció megkezdése** gombra. A szimuláció a konfiguráció szerint határozatlan ideig fog futni. A **Szimuláció leállítása** gombra kattintva bármikor leállíthatja a szimulációt. A szimuláció az éppen aktuális futtatás statisztikáit jeleníti meg.

![Szimuláció futtatása](./media/quickstart-device-simulation-deploy/runningsimulation.png)

Egyszerre csak egy szimulációt futtathat az Eszközszimulációs példányban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne ismerkedni az eszközzel, hagyja üzembe helyezve az Eszközszimulációt.

Ha már nincs szüksége az Eszközszimulációra, törölje a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról. Ehhez kattintson a hozzá tartozó csempére, majd kattintson a **Megoldás törlése** gombra:

![Megoldás törlése](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezte az Eszközszimulációt, és futtatott egy IoT-eszközszimulációt.

> [!div class="nextstepaction"]
> [Egy vagy több eszköztípussal rendelkező szimuláció létrehozása](iot-accelerators-device-simulation-create-simulation.md)