---
title: Azure rövid útmutató – Azure Automation-runbook létrehozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Automation runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a784ddbc2ab1298bec3e2dc21469785163d7a228
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711992"
---
# <a name="create-an-azure-automation-runbook"></a>Azure Automation-runbook létrehozása

Az Azure Automation-runbookok az Azure-on keresztül hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Automation-runbookok létrehozásához. Ez a rövid útmutató az Automation PowerShell-runbookok létrehozásán, szerkesztésén, tesztelésén és közzétételén vezeti át.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="create-the-runbook"></a>A runbook létrehozása

Először hozzunk létre egy runbookot. Az ebben a rövid útmutatóban létrehozott minta runbook alapértelmezett kimenete: `Hello World`.

1. Nyissa meg az Automation-fiókját.

1. Kattintson a **runbookok** elemre a **folyamat automatizálása**alatt. Megjelenik a runbookok listája.

1. Kattintson a **Runbook létrehozása** elemre a lista tetején.

1. Adja meg a `Hello-World` runbook nevét a **név** mezőben, majd válassza a **PowerShell** lehetőséget a **runbook típusa** mezőben. 

   ![Adja meg az Automation-runbook információit az oldalon](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kattintson a **Létrehozás**gombra. Létrejön a runbook, és megnyílik a PowerShell-runbook szerkesztése oldal.

    ![PowerShell-parancsprogram készítése a runbookszerkesztőben](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Írja be vagy másolja és illessze be a következő kódot a szerkesztés panelre. Egy opcionális `Name` , alapértelmezett értékkel rendelkező bemeneti paramétert hoz létre `World` , amely a következő bemeneti értéket használó karakterláncot jeleníti meg:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kattintson a **Save (Mentés** ) gombra a runbook piszkozatának mentéséhez.

    ![PowerShell-parancsprogram készítése a runbookszerkesztőben](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>A runbook tesztelése

A runbook létrehozása után tesztelje a runbook, és ellenőrizze, hogy működik-e.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

1. Írjon be egy értéket a **Név** mezőbe, és kattintson az **Indítás** gombra. Elindul a tesztfeladat, és megjelenik a feladat állapota és kimenete.

    ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. A jobb felső sarokban található **X** gombra kattintva zárjuk be a teszt panelt. A megjelenő felugró ablakban válassza az **OK** lehetőséget.

1. A PowerShell-runbook szerkesztése oldalon kattintson a **Közzététel** lehetőségre, hogy a runbookot a fiókban lévő runbook hivatalos verziójaként tegye közzé.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>A runbook futtatása

A runbook közzététele után megjelenik az áttekintő oldal.

1. A runbook áttekintése oldalon kattintson az **Indítás** gombra a runbook Runbook indítása konfigurációs oldalának megnyitásához.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Hagyja üresen a **Név** mezőt, hogy az alapértelmezett értéket használja, és kattintson az **OK** gombra. A rendszer elküldte a runbook-feladatot, és megjelenik a feladatok lap.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Ha a feladatok állapota `Running` vagy `Completed` , kattintson a **kimenet** gombra a kimenet ablaktábla megnyitásához, és tekintse meg a runbook kimenetét.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, szerkesztett, tesztelt és közzétett egy runbookot, és elindított egy runbookfeladatot. Az Automation-runbookokkal kapcsolatos további információkért folytassa az Automationben létrehozható és használható különböző runbooktípusokkal foglalkozó cikkel.

> [!div class="nextstepaction"]
> [Automation útmutató – Runbooktípusok](./automation-runbook-types.md)
