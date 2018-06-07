---
title: A távoli figyelésére szolgáló megoldás - Azure telepítése |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan a távoli megfigyelési megoldásgyorsító azureiotsuite.com való kiépítéséhez.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 42f6afcd3cb6880ba6c9cdd2a51e2a3e9ff2c2d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626853"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>A távoli megfigyelési megoldásgyorsító központi telepítése

Ez az oktatóanyag bemutatja, hogyan a távoli megfigyelési megoldásgyorsító kiépítéséhez. A megoldás a azureiotsuite.com telepít. A megoldás további információt a beállítás további részletekért lásd a parancssori felület használatával is telepítheti [központi telepítése a parancssorból egy megoldásgyorsító](iot-accelerators-remote-monitoring-deploy-cli.md).

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

1. A a **távoli figyelő létrehozása megoldás** lapján adjon meg egy **megoldás neve** a távoli megfigyelési megoldásgyorsító számára.

1. Válassza ki a **alapvető** vagy **szabványos** központi telepítés. Megtudhatja, hogyan működik, vagy hogy bemutatója futtatni, válassza a központi telepítése a **alapvető** költségek minimalizálása érdekében a beállítást.

1. Válasszon **Java** vagy **.NET** nyelve. A mikroszolgáltatások állnak rendelkezésre, vagy a Java, vagy a .NET hitelesítés megvalósításához.

1. Tekintse át a **megoldás részletei** panel a konfigurációs beállításokkal kapcsolatos további információk.

1. Válassza ki a megoldás kiépítéséhez használni kívánt **Előfizetést** és **Régiót**.

1. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tarthat futtatásához:

    ![Távoli figyelés megoldás részletei](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

A hibaelhárítási információkat, lásd: [Mi a teendő, ha a központi telepítés nem sikerül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) a GitHub-tárházban.

## <a name="sign-in-to-the-solution-accelerator"></a>Jelentkezzen be a megoldásgyorsító

Ha a kiépítési folyamat befejeződött, bejelentkezhet a távoli megfigyelési megoldásgyorsító számára.

1. Az a **kiépített megoldások** lapon, válassza ki az új távoli figyelésére szolgáló megoldás:

    ![Új megoldás kiválasztása](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. A távoli figyelésére szolgáló megoldás a panelen megjelenő kapcsolatos információk is megtekinthetők. Válasszon **megoldás irányítópultja** kapcsolódni a távoli figyelésére szolgáló megoldás.

    > [!NOTE]
    > Törölheti a távoli figyelésére szolgáló megoldás a panelről, amikor elkészült, vele.

    ![Megoldás panel](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. A távoli figyelési megoldást irányítópult jeleníti meg a böngészőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító központi telepítése
> * Jelentkezzen be a megoldásgyorsító

A távoli figyelésére szolgáló megoldás telepített, akkor a következő lépéssel fogja [megismerkedhet a megoldás irányítópultja képességeit](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->