---
title: A távoli felügyeleti megoldás - Azure |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan azureiotsuite.com a távoli felügyeleti megoldásgyorsító kiépítéséhez.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 30d0b0b0250563cf09a50e8165ad9fb1047fd570
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>A távoli felügyeleti megoldásgyorsító központi telepítése

Ez az oktatóanyag bemutatja, hogyan a távoli felügyeleti megoldásgyorsító kiépítéséhez. A megoldás a azureiotsuite.com telepít. A megoldás további információt a beállítás további részletekért lásd a parancssori felület használatával is telepítheti [központi telepítése a parancssorból egy megoldásgyorsító](iot-accelerators-remote-monitoring-deploy-cli.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító központi telepítése
> * Jelentkezzen be a megoldásgyorsító

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>A megoldásgyorsító központi telepítése

A megoldásgyorsító központi telepítése az Azure-előfizetéshez, előtt ki kell választania néhány konfigurálási beállításhoz:

1. Jelentkezzen be [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) Azure-fiók hitelesítő adataival.

1. Kattintson a **próbálja most** a a **távoli megfigyelési** csempére.

    ![Válassza ki a távoli figyelése](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. A a **távoli figyelő létrehozása megoldás** lapján adjon meg egy **megoldás neve** a távoli felügyeleti megoldásgyorsító számára.

1. Válassza ki a **alapvető** vagy **szabványos** központi telepítés. Megtudhatja, hogyan működik, vagy hogy bemutatója futtatni, válassza a központi telepítése a **alapvető** költségek minimalizálása érdekében a beállítást.

1. Válasszon **Java** vagy **.NET** nyelve. A mikroszolgáltatások állnak rendelkezésre, vagy a Java, vagy a .NET hitelesítés megvalósításához.

1. Tekintse át a **megoldás részletei** panel a konfigurációs beállításokkal kapcsolatos további információk.

1. Válassza ki a megoldás kiépítéséhez használni kívánt **Előfizetést** és **Régiót**.

1. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tarthat futtatásához:

    ![Távoli figyelés megoldás részletei](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

A hibaelhárítási információkat, lásd: [Mi a teendő, ha a központi telepítés nem sikerül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) a GitHub-tárházban.

## <a name="sign-in-to-the-solution-accelerator"></a>Jelentkezzen be a megoldásgyorsító

Ha a kiépítési folyamat befejeződött, bejelentkezhet a távoli felügyeleti megoldásgyorsító való.

1. Az a **kiépített megoldások** lapon, válassza ki az új távoli figyelési megoldás:

    ![Új megoldás kiválasztása](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. A távoli felügyeleti megoldás a panelen megjelenő információk is megtekinthetők. Válasszon **megoldás irányítópultja** kapcsolódni a távoli felügyeleti megoldás.

    > [!NOTE]
    > Törölheti a távoli felügyeleti megoldás a panelről, amikor elkészült, vele.

    ![Megoldás panel](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. A távoli felügyeleti megoldás irányítópultja jeleníti meg a böngészőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító központi telepítése
> * Jelentkezzen be a megoldásgyorsító

A távoli felügyeleti megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](./../iot-suite/iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->