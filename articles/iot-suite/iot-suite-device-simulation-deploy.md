---
title: Az eszköz szimuláció megoldás - Azure üzembe helyezése |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan azureiotsuite.com az eszköz szimuláció megoldást kiépítéséhez.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 52890c51b06777a2d74fcf143bee4e5e8919ba75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Az Azure IoT eszköz szimuláció megoldás üzembe helyezéséhez

Ez az oktatóanyag bemutatja, hogyan eszköz szimuláció megoldás telepítéséhez. A megoldás a azureiotsuite.com telepít.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz szimuláció megoldás konfigurálása
> * Az eszköz szimuláció megoldás üzembe helyezéséhez
> * Jelentkezzen be az eszköz szimuláció megoldás

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

Csak a megoldás telepítheti az Azure-előfizetéshez, ki kell választania néhány konfigurálási beállításhoz:

1. Jelentkezzen be [azureiotsuite.com](https://www.azureiotsuite.com) az Azure használatával fiók hitelesítő adatait, és kattintson a **+** egy új megoldás létrehozásához:

    ![Új megoldás létrehozása](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. Kattintson a **válasszon** a a **eszköz szimuláció** csempén:

    ![Válassza ki az eszköz szimulálása](media/iot-suite-device-simulation-deploy/select.png)

1. Az a **létrehozása eszköz szimuláció megoldás** lapján adjon meg egy **megoldás neve** eszköz szimuláció megoldást.

1. Válassza ki a megoldás kiépítéséhez használni kívánt **Előfizetést** és **Régiót**.

1. Adja meg, ha egy új IoT Hub az eszköz szimuláció megoldás üzembe helyezéséhez. Ha bejelöli a jelölőnégyzetet, egy új IoT Hub központilag telepítik az előfizetéshez. Választás, függetlenül mindig mutathat a szimuláció IoT-központot.

1. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tarthat futtatásához:

    ![Eszköz szimuláció megoldás részletei](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Jelentkezzen be a megoldás

Ha a kiépítési folyamat befejeződött, bejelentkezhet a eszköz szimuláció megoldásához.

1. A a **kiépített megoldások** kattintson **indítása** alatt az új eszköz szimuláció megoldás:

    ![Új megoldás kiválasztása](media/iot-suite-device-simulation-deploy/newsolution.png)

1. Az eszköz szimuláció-megoldás a panelen megjelenő információk is megtekinthetők. Válasszon **megoldás irányítópultja** csatlakozni az eszköz szimuláció megoldás.

    > [!NOTE]
    > Törölheti az eszköz szimuláció megoldás a panelről, amikor elkészült, vele.

    ![Megoldás panel](media/iot-suite-device-simulation-deploy/properties.png)

1. Az eszköz szimuláció megoldás irányítópulton jeleníti meg a böngészőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A megoldás konfigurálása
> * A megoldás üzembe helyezése
> * Jelentkezzen be a megoldás

Az eszköz szimuláció megoldás telepített, akkor a következő lépéssel fogja [Fedezze fel az eszköz szimuláció-megoldás funkcióinak](./iot-suite-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
