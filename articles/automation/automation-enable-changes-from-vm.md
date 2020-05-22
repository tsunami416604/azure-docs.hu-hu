---
title: Azure Automation Change Tracking és leltár engedélyezése Azure-beli virtuális gépről
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Change Tracking és a leltár egy Azure-beli virtuális gépről.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4eccdef6bd3f2bfcd0eced8281f7b998536f22a9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749185"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Change Tracking és leltár engedélyezése Azure-beli virtuális gépről

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

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>A hatókör-konfiguráció keresése

A Change Tracking és a leltár a munkaterület hatókör-konfigurációját használja a szolgáltatás engedélyezéséhez szükséges számítógépek célzására. A hatókör-konfiguráció egy vagy több mentett keresés csoportja, amely a szolgáltatás hatókörének meghatározott számítógépekre való korlátozására szolgál. További információ: a [change Tracking és a leltár hatókör-konfigurációinak használata](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>További lépések

* [Change Tracking és leltár kezelése](change-tracking-file-contents.md)
* [A Change Tracking és a leltár hatókör-konfigurációinak használata](automation-scope-configurations-change-tracking.md)
* [Virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)
* [Munkaterület leválasztása az Automation-fiókból a Change Tracking és a leltárhoz](automation-unlink-workspace-change-tracking.md)
* [Virtuális gépek eltávolítása Change Tracking és leltárból](automation-remove-vms-from-change-tracking.md)
* [Azure-beli virtuális gépek változásainak hibáinak megoldása](automation-tutorial-troubleshoot-changes.md)
* [A Change Tracking és a leltárral kapcsolatos problémák elhárítása](troubleshoot/change-tracking.md)
