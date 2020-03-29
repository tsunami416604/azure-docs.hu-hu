---
title: Feladat indítása a StorSimple Data Manager ben az Azure Automation használatával
description: Ismerje meg, hogyan használhatja az Azure Automationt a StorSimple Data Manager-feladatok indításához
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273988"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Feladat aktiválása az Azure Automation használatával

Ez a cikk bemutatja, hogyan használhatja az adatátalakítási szolgáltatás a StorSimple Data Manager szolgáltatás storSimple eszközadatok átalakításához. Az adatátalakítási feladatot kétféleképpen indíthatja el: 

 - A .NET SDK használata
 - Az Azure Automation runbook használata
 
Ez a cikk ismerteti, hogyan hozhat létre egy Azure Automation-runbookot, majd használja egy adatátalakítási feladat kezdeményezéséhez. Ha többet szeretne tudni arról, hogy miként kezdeményezhet adatátalakítást a .NET SDK-n keresztül, olvassa el [a .NET SDK használata adatátalakítási feladatok indításához.](storsimple-data-manager-dotnet-jobs.md)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

*   Az ügyfélszámítógépen telepített Azure PowerShell. [Töltse le az Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
*   Megfelelően konfigurált feladatdefiníció egy erőforráscsoporton belüli StorSimple Data Manager szolgáltatásban.
*   Töltse [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) le a fájlt a GitHub-tárházból. 
*   Töltse [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) le a parancsfájlt a GitHub-tárházból.

## <a name="step-by-step-procedure"></a>Lépésről lépésre

### <a name="set-up-the-automation-account"></a>Az Automation-fiók beállítása

1. Hozzon létre egy Azure Run As automation-fiókot az Azure Portalon. Ehhez nyissa meg **az Azure piacterét > mindent,** és keresse meg az **Automation**kifejezést. Válassza **az Automation-fiókok lehetőséget**.

    ![Futtatás automatizálási fiókként létrehozása](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Új automatizálási fiók hozzáadásához kattintson **a + Hozzáadás gombra.**

    ![Futtatás automatizálási fiókként létrehozása](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Az **Add Automation:**

   1. Adja meg az automatizálási fiók **nevét.**
   2. Válassza ki a StorSimple Data Manager szolgáltatáshoz kapcsolódó **előfizetést.**
   3. Hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévő erőforráscsoportból.
   4. Válasszon egy **helyet**.
   5. Hagyja bejelölve az alapértelmezett **Futtatás létrehozása fiókként** beállítást.
   6. Ha az irányítópulton gyors annektetét szeretne kapni, jelölje be a **Pin to dashboard**jelölőnégyzetet. Kattintson **a Létrehozás gombra.**

      ![Futtatás automatizálási fiókként létrehozása](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Az automatizálási fiók sikeres létrehozása után értesítést kap.
    
      ![Értesítés az automatizálási fiók telepítéséről](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      További információ: [Futtatás futtatása fiók](../automation/automation-create-runas-account.md)létrehozása.

3. Az újonnan létrehozott fiókban nyissa meg **a Megosztott erőforrások > modulok at,** és kattintson a + Add module ( Modul **hozzáadása**) elemre.

    ![1. importálási modul](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Tallózással `DataTransformationApp.zip` keresse meg a fájl helyét a helyi számítógépről, és jelölje ki és nyissa meg a modult. A modul importálásához kattintson az **OK** gombra.

    ![2. importálási modul](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Amikor az Azure Automation importál egy modult a fiókjába, kinyeri a modul metaadatait. Ez a művelet eltarthat néhány percig.

   ![4. importálási modul](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Értesítést kap a modul üzembe helyezéséről, és egy másik értesítést, amikor a folyamat befejeződött.  **A Modulok** állapota Elérhető re **változik.**

    ![5. importálási modul](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Automation-runbook importálása, közzététele és futtatása

Hajtsa végre az alábbi lépéseket a runbook importálásához, közzétételéhez és futtatásához a feladatdefiníció aktiválásához.

1. Az Azure Portalon nyissa meg az Automation-fiókját. Nyissa meg **a Folyamatautomatizálás > A Runbookok** at, és kattintson **a + Runbook hozzáadása gombra**.

    ![Runbook 1 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. A **Runbook hozzáadása csoportban**kattintson **a Meglévő runbook importálása**elemre.

3. Mutasson a `Trigger-DataTransformation-Job.ps1` **Runbook-fájl**Azure PowerShell-parancsfájljára. A runbook típusa automatikusan kiválasztásra kerül. Adja meg a runbook nevét és nem kötelező leírását. Kattintson **a Létrehozás gombra.**

    ![Runbook 2 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Az új runbook megjelenik az Automation-fiók runbookok listájában. Jelölje ki és kattintson erre a runbookra.

    ![Runbook 3 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Szerkesztsd a runbookot, és kattintson a **Teszt** ablaktáblára.

    ![4-es runbook hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Adja meg a paramétereket, például a StorSimple Data Manager szolgáltatás nevét, a társított erőforráscsoportot és a feladatdefiníció nevét. **Kezdd el** a tesztet. A jelentés akkor jön létre, amikor a futtatás befejeződött. További információért látogasson el a [runbook teszteléséhez.](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)

    ![Runbook 8 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Vizsgálja meg a runbook kimenetét a tesztablakban. Ha elégedett, zárja be az ablaktáblát. Kattintson **a Közzététel gombra,** és amikor megerősítést kér, erősítse meg és tegye közzé a runbookot.

    ![Runbook 6 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Lépjen vissza a **Runbookokhoz,** és válassza ki az újonnan létrehozott runbookot.

    ![Runbook 7 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Indítsa el** a runbookot. A **Runbook indítása mezőbe**írja be az összes paramétert. Kattintson **az OK gombra** az adatátalakítási feladat elküldéséhez és elindításához.

10. A feladat előrehaladásának figyeléséhez az Azure Portalon, nyissa meg a **Feladatok** a StorSimple Data Manager szolgáltatásban. Jelölje ki, és kattintson a feladatra a feladat részleteinek megtekintéséhez.

    ![Runbook 10 hozzáadása](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>További lépések

[Az adatok átalakításához használja a StorSimple Data Manager felhasználói felületét.](storsimple-data-manager-ui.md)
