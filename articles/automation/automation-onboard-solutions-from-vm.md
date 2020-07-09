---
title: Azure Automation Update Management engedélyezése Azure-beli virtuális gépről
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management egy Azure-beli virtuális gépről.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 481d59bd2a06103629dee88868d9e33de810fdab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204905"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Az Update Management engedélyezése Azure-beli virtuális gépről

Ez a cikk azt ismerteti, hogyan használható egy Azure-beli virtuális gép a [Update Management](automation-update-management.md) funkció más gépeken való engedélyezéséhez. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő virtuális gépet. 

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-the-feature-for-deployment"></a>A szolgáltatás engedélyezése a központi telepítéshez

1. A [Azure Portal](https://portal.azure.com)válassza a **virtuális gépek** lehetőséget, vagy keresse meg és válassza ki a **virtuális gépek** lehetőséget a kezdőlapon.

2. Válassza ki azt a virtuális gépet, amelynek engedélyezni szeretné a Update Management. A virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül. Ön

3. A virtuális gép lap **műveletek**területén válassza a **Update Management**lehetőséget.

4. Rendelkeznie kell `Microsoft.OperationalInsights/workspaces/read` engedéllyel annak megállapításához, hogy a virtuális gép engedélyezve van-e a munkaterületen. További információ a szükséges engedélyekről: a [gépek engedélyezéséhez szükséges engedélyek](automation-role-based-access-control.md#feature-setup-permissions). Ha többet szeretne megtudni arról, hogyan engedélyezheti egyszerre több gépet, tekintse meg az [Automation-fiók Update Managementának engedélyezése](automation-onboard-solutions-from-automation-account.md)című témakört.

5. Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** elemre Update Management engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet. 

    ![Az Update Management engedélyezése](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="next-steps"></a>További lépések

* Az Update Management virtuális gépekhez való használatához lásd: [Az Azure-beli virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md).
* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](troubleshoot/update-management.md).
* A Windows Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.
