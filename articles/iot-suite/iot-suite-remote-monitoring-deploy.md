---
title: "A távoli felügyeleti megoldás - Azure |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan azureiotsuite.com a távoli figyelési előkonfigurált megoldást kiépítéséhez."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/09/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 159397ba4fc93628acbf2bb53edf5eb88fd0cac7
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>A távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez

Ez az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált távoli figyelő megoldást. A megoldás a azureiotsuite.com telepít. A megoldás további információt a beállítás további részletekért lásd a parancssori felület használatával is telepítheti [központi telepítése a parancssorból előkonfigurált megoldás](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az előkonfigurált megoldás konfigurálása
> * Az előkonfigurált megoldás üzembe helyezéséhez
> * Jelentkezzen be az előkonfigurált megoldás

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Az előkonfigurált megoldás üzembe helyezéséhez

Mielőtt központilag telepíteni az előkonfigurált megoldás az Azure-előfizetéshez, ki kell választania néhány konfigurálási beállításhoz:

1. Jelentkezzen be [azureiotsuite.com](https://www.azureiotsuite.com) az Azure használatával fiók hitelesítő adatait, és kattintson a  **+**  egy új megoldás létrehozásához:

    ![Új megoldás létrehozása](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. Kattintson a **kiválasztása** a a **távoli figyelési preview** csempére.

    ![Válassza ki a távoli figyelése](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. A a **távoli figyelő létrehozása megoldás** lapján adjon meg egy **megoldásnév** a megoldás a távoli figyelésének előre konfigurált.

1. Válassza ki a **alapvető** vagy **szabványos** központi telepítés. Megtudhatja, hogyan működik, vagy hogy bemutatója futtatni, válassza a központi telepítése a **alapvető** költségek minimalizálása érdekében a beállítást.

1. Válasszon **Java** vagy **.NET** nyelve. A mikroszolgáltatások állnak rendelkezésre, vagy a Java, vagy a .NET hitelesítés megvalósításához.

1. Tekintse át a **megoldás részletei** panel a konfigurációs beállításokkal kapcsolatos további információk.

1. Válassza ki a megoldás kiépítéséhez használni kívánt **Előfizetést** és **Régiót**.

1. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tarthat futtatásához:

    ![Távoli felügyeleti megoldás részletei](media/iot-suite-remote-monitoring-deploy/createform.png)

A hibaelhárítási információkat, lásd: [Mi a teendő, ha a központi telepítés nem sikerül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) a GitHub-tárházban.

## <a name="sign-in-to-the-preconfigured-solution"></a>Jelentkezzen be az előkonfigurált megoldás

Ha a kiépítési folyamat befejeződött, bejelentkezhet a távoli felügyeleti előre konfigurált megoldásához.

1. Az a **kiépített megoldások** lapon, válassza ki az új távoli figyelési megoldás:

    ![Új megoldás kiválasztása](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. A távoli felügyeleti megoldás a panelen megjelenő információk is megtekinthetők. Válasszon **megoldás irányítópultja** kapcsolódni a távoli felügyeleti megoldás.

    > [!NOTE]
    > Törölheti a távoli felügyeleti megoldás a panelről, amikor elkészült, vele.

    ![Megoldás panel](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. A távoli felügyeleti megoldás irányítópultja jeleníti meg a böngészőben.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az előkonfigurált megoldás konfigurálása
> * Az előkonfigurált megoldás üzembe helyezéséhez
> * Jelentkezzen be az előkonfigurált megoldás

A távoli felügyeleti megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->