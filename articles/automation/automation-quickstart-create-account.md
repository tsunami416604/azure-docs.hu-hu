---
title: Azure rövid útmutató– Azure Automation-fiók létrehozása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre Azure Automation-fiókot, és hogyan futtathat runbookot
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536981"
---
# <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

Azure Automation-fiókot az Azure-on keresztül hozhat létre az Azure Portalon, egy böngészőalapú felhasználói felületen, amely számos erőforráshoz biztosít hozzáférést. Egy Automation-fiók kezelheti az erőforrásokat az összes régióban és egy adott bérlő előfizetései között. 

Ez a rövid útmutató ismerteti, hogy hozzon létre egy Automation-fiókot, és futtasson egy runbookot a fiókban. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[Jelentkezzen be az Azure-ba.](https://portal.azure.com)

## <a name="create-automation-account"></a>Automation-fiók létrehozása

1. Válasszon nevet az Azure-fiókjához. Az automatizálási fióknevek régiónként és erőforráscsoportonként egyediek. Előfordulhat, hogy a törölt Automation-fiókok nevei nem érhetők el azonnal.

    > [!NOTE]
    > A fiók név nem módosítható, miután beírta a felhasználói felületre. 

2. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

3. Válassza **az Informatikai & felügyeleti eszközök**, majd az **Automatizálás**lehetőséget.

4. Adja meg a számlaadatokat, beleértve a kijelölt fióknevet is. Az **Azure-beli futtató fiók létrehozása** területen válassza az **Igen** lehetőséget az Azure-beli hitelesítést leegyszerűsítő összetevők automatikus engedélyezéséhez. Ha az információ elkészült, kattintson a **Létrehozás** gombra az Automation-fiók központi telepítésének elindításához.

    ![Adja meg az Automation-fiók információit az oldalon](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Az Automation-fiók üzembe helyezéséhez használható helyek frissített listáját a [Régiónként elérhető Termékek (Termékek régiónként) (Termékek régiónként elérhető)](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)témakörben tájékszerezheti.

5. A telepítés befejezése után kattintson a **Minden szolgáltatás gombra.**

6. Válassza **az Automatizálási fiókok lehetőséget,** majd válassza ki a létrehozott Automation-fiókot.

    ![Az Automation-fiókok áttekintése](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook futtatása

Futtassa az oktatóanyag egyik runbookját.

1. Kattintson a Folyamatautomatizálás csoport **Runbookok** **gombparancsára.** Megjelenik a runbookok listája. Alapértelmezés szerint több oktatóanyag runbookok engedélyezve vannak a fiókban.

    ![Automation-fiók runbookok listája](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Válassza ki az **AzureAutomationTutorialScript** runbookot. Ezzel megnyílik a runbook áttekintési oldala.

    ![A runbook áttekintése](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Kattintson az **Indítás** gombra, és a Runbook indítása oldalon kattintson az **OK** gombra a runbook elindításához.

    ![Runbookfeladat oldal](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Miután a `Running`feladat állapota lesz, kattintson a **Kimenet** vagy a **Minden napló** a runbook-feladat kimenetének megtekintéséhez. A jelen oktatóanyag runbookjának kimenete az Azure-erőforrások listája.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Automation-fiókot, elindított egy runbook feladatot, és megtekintette a feladat részleteit. Ha bővebb információra van szüksége az Azure Automationnel kapcsolatban, folytassa az első runbook létrehozásával foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Automation rövid útmutató – Runbook létrehozása](./automation-quickstart-create-runbook.md)

