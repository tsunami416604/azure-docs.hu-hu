---
title: Az Update és a Change Tracking megoldás előkészítése az Azure Automationhöz
description: Tudnivalók az Update és a Change Tracking megoldás előkészítéséről az Azure Automationhöz.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: d247369647106cf1671a8770a6dce21f1a34a4b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082194"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Az Update és a Change Tracking megoldás előkészítése az Azure Automationhöz

Ez az oktatóanyag bemutatja a virtuális gépek Update, Change Tracking és Inventory megoldásainak automatikus előkészítését az Azure Automationhöz:

> [!div class="checklist"]
> * Azure-beli virtuális gép előkészítése
> * Megoldások engedélyezése
> * Modulok telepítése és frissítése
> * Az előkészítési runbook importálása
> * A runbook indítása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="onboard-an-azure-vm"></a>Azure-beli virtuális gép előkészítése

A gépek előkészítésének több módja is van. A megoldást [egy virtuális gépről](automation-onboard-solutions-from-vm.md), [az Automation-fiókjában](automation-onboard-solutions-from-automation-account.md) lévő [több számítógép tallózásával](automation-onboard-solutions-from-browse.md) vagy egy runbook segítségével készítheti elő. Ez az oktatóanyag végigvezeti az Update Management runbook segítségével történő engedélyezésén. Nagy mennyiségű Azure-beli virtuális gép előkészítéséhez egy meglévő virtuális gépet kell előkészíteni a Change Tracking vagy az Update Management megoldással. Ebben a lépésben egy virtuális gépet fogunk előkészíteni az Update Management és a Change Tracking megoldással.

### <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

A Change Tracking és az Inventory megoldással [változáskövetési](automation-vm-change-tracking.md) és [leltárkészítési](automation-vm-inventory.md) képességeket biztosíthat a virtuális gépek számára. Ebben a lépésben engedélyezzük a megoldást egy virtuális gépen.

1. A bal oldali menüben válassza az **Automation-fiókok** lehetőséget, majd válassza ki az Automation-fiókot a listából.
1. A **KONFIGURÁCIÓKEZELÉS** területen válassza az **Inventory** megoldást.
1. Válasszon egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy újat. Kattintson az **Engedélyezés** gombra.

![Az Update megoldás előkészítése](media/automation-onboard-solutions/inventory-onboard.png)

Ha a Change tracking és az Inventory megoldás előkészítése befejeződött az értesítés szerint, a **KONFIGURÁCIÓKEZELÉS** területen kattintson az **Update Management** lehetőségre.

### <a name="enable-update-management"></a>Az Update Management engedélyezése

Az Update Management megoldás segítségével kezelheti az Azure-beli Windows rendszerű virtuális gépek frissítéseit és javításait. Felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen. Ebben a lépésben engedélyezzük a megoldást a virtuális gépen.

1. Az Automation-fiók **FRISSÍTÉSKEZELÉS** területén válassza az **Update Management** lehetőséget.
1. A kiválasztott Log Analytics-munkaterület megegyezik az előző lépésben használt munkaterülettel. Az Update Management megoldás előkészítéséhez kattintson az **Engedélyezés** lehetőségre.

![Az Update megoldás előkészítése](media/automation-onboard-solutions/update-onboard.png)

Az Update Management megoldás telepítése közben kék szalagcím látható. A megoldás engedélyezését követően a **KONFIGURÁCIÓKEZELÉS** területen válassza a **Change Tracking** lehetőséget a továbblépéshez.

### <a name="select-azure-vm-to-be-managed"></a>A kezelni kívánt Azure-beli virtuális gép kiválasztása

Most, hogy engedélyezte a megoldásokat, hozzáadhat egy Azure-beli virtuális gépet a megoldásokhoz való előkészítésre.

1. A virtuális gép hozzáadásához az Automation-fiók **Change tracking** lapján kattintson az **+ Azure-beli virtuális gép hozzáadása** lehetőségre.

1. Válassza ki a virtuális gépet a listából, majd válassza az **Engedélyezés** lehetőséget. Ezzel a művelettel engedélyezi a Change Tracking és az Inventory megoldást a virtuális gépen.

   ![Az Update megoldás engedélyezése a virtuális gépen](media/automation-onboard-solutions/enable-change-tracking.png)

1. Ha a virtuális gép előkészítése befejeződött az értesítés szerint, az Automation-fiók **FRISSÍTÉSKEZELÉS** lapján kattintson az **Update Management** lehetőségre.

1. A virtuális gép hozzáadásához kattintson az **+ Azure-beli virtuális gép hozzáadása** lehetőségre.

1. Válassza ki a virtuális gépet a listából, majd válassza az **Engedélyezés** lehetőséget. Ezzel a művelettel engedélyezi az Update Management megoldást a virtuális gépen.

   ![Az Update megoldás engedélyezése a virtuális gépen](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Ha nem várja meg a másik megoldás hajtsa végre a következő megoldás engedélyezésekor a következő üzenetet kapja: *Ez vagy egy másik virtuális gépet folyamatban van egy másik megoldás telepítése. Amikor a telepítés befejeződik, az Engedélyezés gomb újra aktívvá válik, és kérheti a megoldás telepítését ezen a virtuális gépen.*

## <a name="install-and-update-modules"></a>Modulok telepítése és frissítése

A megoldás-előkészítés sikeres automatizálásához a legújabb verzióra kell frissítenie az Azure-modulokat, és importálnia kell az `AzureRM.OperationalInsights` modult.

## <a name="update-azure-modules"></a>Az Azure-modulok frissítése

Az Automation-fiók **MEGOSZTOTT ERŐFORRÁSOK** területén válassza a **Modulok** lehetőséget. Az Azure-modulok a legújabb verzióra frissítéséhez válassza az **Azure-modulok frissítése** lehetőséget. A meglévő Azure-modulok a legújabb verzióra frissítéséhez válassza az **Igen** lehetőséget.

![Modulok frissítése](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Az AzureRM.OperationalInsights modul telepítése

A **Modulok** lapon a modulkatalógus megnyitásához kattintson a **Böngészés a katalógusban** lehetőségre. Keresse meg az AzureRM.OperationalInsights modult, majd importálja az Automation-fiókba.

![Az OperationalInsights modul importálása](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Az előkészítési runbook importálása

1. Az Automation-fiók **FOLYAMATAUTOMATIZÁLÁS** területén válassza a **Runbookok** lehetőséget.
1. Kattintson a **Böngészés a katalógusban** gombra.
1. Keressen az **update and change tracking** kifejezésre, kattintson a runbookra, majd a **Forrás megtekintése** lapon válassza az **Importálás** lehetőséget. Kattintson az **OK** gombra a runbook az Automation-fiókba importálásához.

   ![Előkészítési runbook importálása](media/automation-onboard-solutions/import-from-gallery.png)

1. A **Runbook** lapon kattintson a **Szerkesztés**, majd a **Közzététel** lehetőségre. A **Runbook közzététele** párbeszédpanelen kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="start-the-runbook"></a>A runbook indítása

A runbook indításához elő kellett készítenie a change tracking vagy az update megoldást egy Azure-beli virtuális gépen. Szükség van egy meglévő virtuális gépre és egy erőforráscsoportra, amelynek paraméterei esetében már előkészítette a megoldást.

1. Nyissa meg az Enable-MultipleSolution runbookot.

   ![Többmegoldásos runbookok](media/automation-onboard-solutions/runbook-overview.png)

1. Kattintson az Indítás gombra, és adja meg a következő értékeket a paraméterek számára.

   * **VMNAME** – Hagyja üresen. Egy meglévő virtuális gép neve, amelyen az Update vagy Change tracking megoldást elő szeretné készíteni. Ha ezt az értéket üresen hagyja, a rendszer az erőforráscsoport minden virtuális gépén végrehajtja az előkészítést.
   * **VMRESOURCEGROUP** – Az előkészíteni kívánt virtuális gépeket tartalmazó erőforráscsoport neve.
   * **SUBSCRIPTIONID** – Hagyja üresen. Az előkészíteni kívánt új virtuális gép előfizetés-azonosítója. Ha üresen hagyja, a rendszer a munkaterület előfizetését használja. Ha másik előfizetés-azonosítót ad meg, akkor az Automation-fiókhoz tartozó futtató fiókot is meg kell adnia az előfizetés közreműködőjeként.
   * **ALREADYONBOARDEDVM** – Az Update vagy a Change Tracking megoldáshoz manuálisan előkészített virtuális gép neve.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – A virtuális gépet tartalmazó erőforráscsoport neve.
   * **SOLUTIONTYPE** – Adja meg az **Updates** vagy a **Change Tracking** kifejezést

   ![Az Enable-MultipleSolution runbook paraméterei](media/automation-onboard-solutions/runbook-parameters.png)

1. A runbookfeladat indításához kattintson az **OK** gombra.
1. A folyamat előrehaladását és az esetleges hibákat a runbookfeladat oldalán követheti.

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
