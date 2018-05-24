---
title: Azure rövid útmutató – Azure Automation-runbook létrehozása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Automation-runbookot
services: automation
author: csand-msft
ms.author: csand
ms.date: 12/14/2017
ms.topic: quickstart
ms.service: automation
ms.component: process-automation
ms.custom: mvc
ms.openlocfilehash: 4aafff81957943fc19f0f6d2fce8a41f7be58d16
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="create-an-azure-automation-runbook"></a>Azure Automation-runbook létrehozása

Az Azure Automation-runbookok az Azure-on keresztül hozhatók létre. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Automation-runbookok létrehozásához. Ez a rövid útmutató az Automation PowerShell-runbookok létrehozásán, szerkesztésén, tesztelésén és közzétételén vezeti át.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="create-runbook"></a>Runbook létrehozása

Először hozzunk létre egy runbookot. Az ebben a rövid útmutatóban létrehozott minta runbook alapértelmezett kimenete: `Hello World`.

1. Nyissa meg az Automation-fiókját.

1. Kattintson a **FOLYAMATOK AUTOMATIZÁLÁSA** területen lévő **Runbookok** lehetőségre. Megjelenik a runbookok listája.

1. Kattintson a lista tetején található **Runbook hozzáadása** gombra. A **Runbook hozzáadása** oldalon válassza a **Gyors létrehozás** lehetőséget.

1. Adja a runbooknak a „Hello-World” **nevet**, és válassza a **PowerShell** lehetőséget a **runbook típusaként**. Kattintson a **Create** (Létrehozás) gombra.

   ![Adja meg az Automation-runbook információit az oldalon](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Létrejön a runbook, és megnyílik a **PowerShell-runbook szerkesztése** oldal.

    ![PowerShell-parancsprogram készítése a runbookszerkesztőben](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Írja be vagy másolja és illessze be a következő kódot a szerkesztés panelre. Ez a „Name” nevű opcionális bemeneti paramétert hozza létre a „World” alapértelmezett értékkel, és ezt a bemeneti értéket használó karakterláncot eredményezi:
   
   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Kattintson a **Mentés** gombra a runbook vázlatának mentéséhez.

    ![PowerShell-parancsprogram készítése a runbookszerkesztőben](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>A runbook tesztelése

A runbook létrehozása után tesztelje, hogy működik-e a runbook.

1. Kattintson a **Teszt panelre** a **Teszt** oldal megnyitásához.

1. Írjon be egy értéket a **Név** mezőbe, és kattintson az **Indítás** gombra. Elindul a tesztfeladat, és megjelenik a feladat állapota és kimenete.

    ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. A jobb felső sarokban látható **X** gombra kattintva zárja be a **Teszt** oldalt. A megjelenő felugró ablakban válassza az **OK** lehetőséget.

1. A **PowerShell-runbook szerkesztése** oldalon kattintson a **Közzététel** lehetőségre, hogy a runbookot a fiókban lévő runbook hivatalos verziójaként tegye közzé.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>A runbook futtatása

A runbook közzététele után megjelenik az áttekintő oldal.

1. A runbook áttekintése oldalon kattintson az **Indítás** gombra a runbook **Runbook indítása** konfigurációs oldalának megnyitásához.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Hagyja üresen a **Név** mezőt, hogy az alapértelmezett értéket használja, és kattintson az **OK** gombra. Elküldi a runbookfeladatot, és megjelenik a feladat oldala.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. Amikor a **Feladat állapota** **Fut** vagy **Befejezve**, kattintson a **Kimenet** lehetőségre a **Kimenet** panel megnyitásához és a runbook kimenetének megtekintéséhez.

   ![Runbook tesztfeladat](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a runbookot. Ehhez válassza ki a runbook listából a runbookot, és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott, szerkesztett, tesztelt és közzétett egy runbookot, és elindított egy runbookfeladatot. Az Automation-runbookokkal kapcsolatos további információkért folytassa az Automationben létrehozható és használható különböző runbooktípusokkal foglalkozó cikkel.

> [!div class="nextstepaction"]
> [Automation útmutató – Runbooktípusok](./automation-runbook-types.md)
