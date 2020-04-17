---
title: Azure rövid útmutató – Azure Automation-runbook létrehozása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Automation-runbookot
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 250f51c9f028dd55d8327259e35b82b0c392c1f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536998"
---
# <a name="create-an-azure-automation-runbook"></a>Azure Automation-runbook létrehozása

Az Azure Automation-runbookok az Azure-on keresztül hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Automation-runbookok létrehozásához. Ez a rövid útmutató az Automation PowerShell-runbookok létrehozásán, szerkesztésén, tesztelésén és közzétételén vezeti át.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az https://portal.azure.comAzure-ba a segítségével.

## <a name="create-the-runbook"></a>A runbook létrehozása

Először hozzunk létre egy runbookot. Az ebben a rövid útmutatóban létrehozott minta runbook alapértelmezett kimenete: `Hello World`.

1. Nyissa meg az Automation-fiókját.

1. Kattintson a Folyamatautomatizálás csoport **Runbookok** **gombparancsára.** Megjelenik a runbookok listája.

1. Kattintson a **runbook létrehozása** elemre a lista tetején.

1. Írja `Hello-World` be a runbook nevét a **Név** mezőbe, és válassza a **PowerShell** lehetőséget a **Runbook-típus** mezőhöz. 

   ![Adja meg az Automation-runbook információit az oldalon](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Kattintson **a Létrehozás gombra.** Létrejön a runbook, és megnyílik a PowerShell-runbook szerkesztése oldal.

    ![PowerShell-parancsprogram készítése a runbookszerkesztőben](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Írja be vagy másolja és illessze be a következő kódot a szerkesztés panelre. Létrehoz egy választható bemeneti `Name` `World`paramétert, amelynek alapértelmezett értéke a , és egy ezt a bemeneti értéket használó karakterláncot ad ki:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. A **Mentés gombra** kattintva mentse a runbook piszkozatpéldányát.

    ![PowerShell-parancsprogram készítése a runbookszerkesztőben](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>A runbook tesztelése

A runbook létrehozása után tesztelnie kell a runbookot annak ellenőrzéséhez, hogy működik-e.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

1. Írjon be egy értéket a **Név** mezőbe, és kattintson az **Indítás** gombra. Elindul a tesztfeladat, és megjelenik a feladat állapota és kimenete.

    ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Zárja be a Teszt ablaktáblát a jobb felső sarokban lévő **X-re** kattintva. A megjelenő felugró ablakban válassza az **OK** lehetőséget.

1. A PowerShell-runbook szerkesztése oldalon kattintson a **Közzététel** lehetőségre, hogy a runbookot a fiókban lévő runbook hivatalos verziójaként tegye közzé.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>A runbook futtatása

A runbook közzététele után megjelenik az áttekintő oldal.

1. A runbook áttekintése oldalon kattintson az **Indítás** gombra a runbook Runbook indítása konfigurációs oldalának megnyitásához.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Hagyja üresen a **Név** mezőt, hogy az alapértelmezett értéket használja, és kattintson az **OK** gombra. A runbook-feladat elküldésre kerül, és megjelenik a Feladat lap.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Ha a feladat `Running` `Completed`állapota vagy, kattintson a **Kimenet** gombra a Kimenet ablaktábla megnyitásához és a runbook kimenetének megtekintéséhez.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott, szerkesztett, tesztelt és közzétett egy runbookot, és elindított egy runbookfeladatot. Az Automation-runbookokkal kapcsolatos további információkért folytassa az Automationben létrehozható és használható különböző runbooktípusokkal foglalkozó cikkel.

> [!div class="nextstepaction"]
> [Automation útmutató – Runbooktípusok](./automation-runbook-types.md)
