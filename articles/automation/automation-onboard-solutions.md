---
title: Bevezetési Update, Change Tracking és Inventory Solutions Azure Automation
description: Tudnivalók az Update és a Change Tracking megoldás előkészítéséről az Azure Automationhöz.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457620"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Bevezetési Update, Change Tracking és Inventory Solutions Azure Automation

Ez az oktatóanyag bemutatja a virtuális gépek Update, Change Tracking és Inventory megoldásainak automatikus előkészítését az Azure Automationhöz:

> [!div class="checklist"]
> * Azure-beli virtuális gép előkészítése
> * Megoldások engedélyezése
> * Modulok telepítése és frissítése
> * Az előkészítési runbook importálása
> * A runbook indítása

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="onboard-an-azure-vm"></a>Azure-beli virtuális gép előkészítése

A gépek több módon is üzembe helyezhetők, a megoldás a [virtuális gépről](automation-onboard-solutions-from-vm.md)is felhasználható, így [több gép is tallózható](automation-onboard-solutions-from-browse.md) az [Automation-fiókból](automation-onboard-solutions-from-automation-account.md)vagy a runbook. Ez az oktatóanyag végigvezeti az Update Management runbook segítségével történő engedélyezésén. Nagy mennyiségű Azure-beli virtuális gép előkészítéséhez egy meglévő virtuális gépet kell előkészíteni a Change Tracking vagy az Update Management megoldással. Ebben a lépésben egy virtuális gépet fogunk előkészíteni az Update Management és a Change Tracking megoldással.

### <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

A Change Tracking és a leltári megoldások lehetővé teszik a virtuális gépek változásainak és [leltározásának](automation-vm-inventory.md) [nyomon követését](automation-vm-change-tracking.md) . Ebben a lépésben engedélyezi a megoldásokat egy virtuális gépen.

1. A Azure Portal válassza az **Automation-fiókok**lehetőséget, majd válassza ki az Automation-fiókját a listában.
1. Válassza a **leltár** elemet a **konfiguráció kezelése**területen.
1. Válasszon ki egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy újat. 
1. Kattintson az **Engedélyezés** gombra.

    ![Az Update megoldás előkészítése](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Az Update Management engedélyezése

Az Update Management megoldás segítségével kezelheti az Azure-beli Windows rendszerű virtuális gépek frissítéseit és javításait. Felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen. Ebben a lépésben engedélyezzük a megoldást a virtuális gépen.

1. Az Automation-fiókjában válassza a **Update Management** lehetőséget a **Update Management** szakaszban.
1. A kiválasztott log Analytics-munkaterület az előző lépésben használt munkaterület. Az Update Management megoldás előkészítéséhez kattintson az **Engedélyezés** lehetőségre. Az Update Management megoldás telepítése közben kék szalagcím látható. 

    ![Az Update megoldás előkészítése](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>A kezelni kívánt Azure-beli virtuális gép kiválasztása

Most, hogy engedélyezte a megoldásokat, hozzáadhat egy Azure-beli virtuális gépet a megoldásokhoz való előkészítésre.

1. Az Automation-fiókban válassza a **change Tracking** elemet a **konfiguráció kezelése**területen. 
2. A Change Tracking (változások nyomon követése) lapon kattintson az **Azure virtuális gépek hozzáadása** lehetőségre a virtuális gép hozzáadásához.

3. Válassza ki a virtuális gépet a listából, és kattintson az **Engedélyezés**gombra. Ez a művelet engedélyezi a virtuális gép Change Tracking és leltározási megoldásait.

   ![Az Update megoldás engedélyezése a virtuális gépen](media/automation-onboard-solutions/enable-change-tracking.png)

4. Ha a virtuális gép bevezetési értesítése befejeződik, válassza a **Update Management**alatt a **frissítés kezelése** lehetőséget.

5. Válassza az Azure-beli **virtuális gépek hozzáadása** lehetőséget a virtuális gép hozzáadásához.

6. Válassza ki a virtuális gépet a listából, majd válassza az **Engedélyezés** lehetőséget. Ez a művelet engedélyezi a Update Management megoldást a virtuális géphez.

   ![Az Update megoldás engedélyezése a virtuális gépen](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Ha nem várja meg a másik megoldás befejeződését, akkor a következő megoldás engedélyezésekor az üzenet jelenik meg:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modulok telepítése és frissítése

Frissíteni kell a legújabb Azure-modulokra, és importálnia kell az az [. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) modult a megoldás bevezetésének sikeres automatizálásához.

## <a name="update-azure-modules"></a>Az Azure-modulok frissítése

1. Az Automation-fiókban válassza a **megosztott erőforrások**alatt található **modulok** elemet. 
2. Az Azure-modulok a legújabb verzióra frissítéséhez válassza az **Azure-modulok frissítése** lehetőséget. 
3. Az **Igen** gombra kattintva frissítheti az összes meglévő Azure-modult a legújabb verzióra.

![A modulok](media/automation-onboard-solutions/update-modules.png) frissítése

### <a name="install-azoperationalinsights-module"></a>Telepítés az. OperationalInsights modul

1. Az Automation-fiókban válassza a **megosztott erőforrások**alatt található **modulok** elemet. 
2. Válassza a **Tallózás** katalógus lehetőséget a modul-gyűjtemény megnyitásához. 
3. Keresse meg az az. OperationalInsights, és importálja ezt a modult az Automation-fiókba.

![Az OperationalInsights modul importálása](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Az előkészítési runbook importálása

1. Az Automation-fiókban válassza a **runbookok** lehetőséget a **folyamat automatizálása**alatt.
1. Kattintson a **Böngészés a katalógusban** gombra.
1. Keressen a `update and change tracking` kifejezésre.
3. Válassza ki a runbook, és kattintson az **Importálás** elemre a forrás megtekintése lapon. 
4. A runbook az Automation-fiókba történő importálásához kattintson **az OK** gombra.

   ![Előkészítési runbook importálása](media/automation-onboard-solutions/import-from-gallery.png)

6. A Runbook lapon kattintson a **Szerkesztés**, majd a **Közzététel**elemre. 
7. A Runbook közzététele ablaktáblán kattintson az **Igen** gombra a Runbook közzétételéhez.

## <a name="start-the-runbook"></a>A runbook indítása

A runbook elindításához be kell állítani a Change Tracking vagy az Update Solutions szolgáltatást egy Azure-beli virtuális gépre. Szükség van egy meglévő virtuális gépre és egy erőforráscsoportra, amelynek paraméterei esetében már előkészítette a megoldást.

1. Nyissa meg az **enable-MultipleSolution** runbook.

   ![Többmegoldásos runbookok](media/automation-onboard-solutions/runbook-overview.png)

1. Kattintson az Indítás gombra, és adja meg a következő értékeket a paraméterek számára.

   * **VMNAME** – Hagyja üresen. Egy meglévő virtuális gép neve, amelyen az Update vagy Change tracking megoldást elő szeretné készíteni. Ha ezt az értéket üresen hagyja, a rendszer az erőforráscsoport minden virtuális gépén végrehajtja az előkészítést.
   * **VMRESOURCEGROUP** – Az előkészíteni kívánt virtuális gépeket tartalmazó erőforráscsoport neve.
   * **SUBSCRIPTIONID** – Hagyja üresen. Az előkészíteni kívánt új virtuális gép előfizetés-azonosítója. Ha üresen hagyja, a rendszer a munkaterület előfizetését használja. Ha másik előfizetés-azonosítót ad meg, akkor az Automation-fiókhoz tartozó futtató fiókot is meg kell adnia az előfizetés közreműködőjeként.
   * **ALREADYONBOARDEDVM** – Az Update vagy a Change Tracking megoldáshoz manuálisan előkészített virtuális gép neve.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – annak az erőforráscsoportnak a neve, amelyhez a virtuális gép tartozik.
   * **Megoldástípusa** – adja meg a **frissítéseket** vagy a **változáskövetési**.

   ![Az Enable-MultipleSolution runbook paraméterei](media/automation-onboard-solutions/runbook-parameters.png)

1. A runbookfeladat indításához kattintson az **OK** gombra.
1. A folyamat előrehaladását és az esetleges hibákat a runbookfeladat oldalán követheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Virtuális gép eltávolítása Update Managementról:

1. A Log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfiguráció `MicrosoftDefaultScopeConfig-Updates`mentett keresésével. A mentett keresések a munkaterület **általános** területén találhatók.
2. Távolítsa el a [Windows log Analytics Agent ügynököt](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy a Linux rendszerhez készült [log Analytics-ügynököt](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure-beli virtuális gép manuális előkészítése.
> * A szükséges Azure-modulok telepítése és frissítése.
> * Az Azure-beli virtuális gépeket előkészítő runbook importálása.
> * Az Azure-beli virtuális gépek automatikus előkészítését végző runbook elindítása.

Kövesse ezt a hivatkozást, ha többet szeretne megtudni a runbookok ütemezéséről.

> [!div class="nextstepaction"]
> [Runbookok ütemezése](automation-schedules.md).
