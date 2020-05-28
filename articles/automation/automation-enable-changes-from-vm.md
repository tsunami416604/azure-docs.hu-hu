---
title: Azure Automation Change Tracking és leltár engedélyezése Azure-beli virtuális gépről
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Change Tracking és a leltár egy Azure-beli virtuális gépről.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 7e87d5b3a4488dac05758e160b09f8fa8b393d09
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117951"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>A Change Tracking és az Inventory engedélyezése Azure-beli virtuális gépről

Ez a cikk azt ismerteti, hogyan használható egy Azure-beli virtuális gép a [change Tracking és a leltár](change-tracking.md) funkció más gépeken való engedélyezéséhez. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet. 

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) a gépek kezeléséhez.
* Egy [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

1. A [Azure Portal](https://portal.azure.com)válassza a **virtuális gépek** lehetőséget, vagy keresse meg és válassza ki a **virtuális gépek** lehetőséget a kezdőlapon.

2. Válassza ki azt a virtuális gépet, amelynek engedélyezni szeretné a Change Tracking és a leltárt. A virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül.

3. A virtuális gép lapon válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció**felügyelete alatt.

4. Rendelkeznie kell `Microsoft.OperationalInsights/workspaces/read` engedéllyel annak megállapításához, hogy a virtuális gép engedélyezve van-e a munkaterületen. További információ a szükséges engedélyekről: [szolgáltatások telepítési engedélyei](automation-role-based-access-control.md#feature-setup-permissions). Ha többet szeretne megtudni arról, hogyan engedélyezheti egyszerre több gépet, tekintse meg az [Automation-fiók Change Tracking és leltározásának engedélyezése](automation-enable-changes-from-auto-acct.md)című témakört.

5. Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** lehetőségre a virtuális gép Change Trackingának és leltározásának engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet. 

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>A központi telepítés hatókörének korlátozása

A Change Tracking és a leltár a munkaterületen belüli hatókör-konfigurációval célozza meg a számítógépeket, hogy fogadják a módosításokat. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>További lépések

* A szolgáltatással kapcsolatos további részletekért tekintse meg a [change Tracking és a leltár kezelésével](change-tracking-file-contents.md)foglalkozó témakört.
* A hatókör-konfigurációkkal kapcsolatos további információkért lásd: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](automation-scope-configurations-change-tracking.md).
* Ha szeretné megtudni, hogyan használhatja a szolgáltatást a környezetében telepített szoftverek azonosítására, tekintse meg a [virtuális gépeken telepített szoftverek felderítését](automation-tutorial-installed-software.md)ismertető témakört.
* Ha nem szeretné integrálni az Automation-fiókját egy Log Analytics munkaterülettel a szolgáltatás engedélyezésekor, olvassa el a [munkaterület összekapcsolása az Automation-fiókból](automation-unlink-workspace-change-tracking.md)című témakört.
* Amikor befejezte a virtuális gépek változásainak telepítését, eltávolíthatja őket a [virtuális gépek eltávolítása Change Tracking és leltárból](automation-remove-vms-from-change-tracking.md)című témakörben leírtak szerint.
* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](troubleshoot/change-tracking.md)című témakört.
