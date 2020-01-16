---
title: Bővített mérőszámok hozzáadása az Azure Virtual Machines szolgáltatáshoz | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és konfigurálhatja az Azure-beli virtuális gépek bővített diagnosztikai metrikáit.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
ms.custom: seodec18
ms.openlocfilehash: bd9089b868284902cdc33d87972d573dfbf0097b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989552"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Bővített mérőszámok hozzáadása az Azure Virtual Machines szolgáltatáshoz

A Cloudyn az Azure-beli virtuális gépekről származó Azure metrikus adatokat használ az erőforrásaival kapcsolatos részletes információk megjelenítéséhez. A Cloudyn a metrikus adatokat, más néven teljesítményszámlálókat is használja a jelentések létrehozásához. A Cloudyn azonban nem gyűjt automatikusan adatokat a vendég virtuális gépekről származó összes Azure metrikus adatról, engedélyeznie kell a metrikus gyűjteményt. Ez a cikk segítséget nyújt az Azure-beli virtuális gépek további diagnosztikai metrikáinak engedélyezéséhez és konfigurálásához.

A metrikák gyűjtésének engedélyezése után a következőket teheti:

- Tudnia kell, hogy mikor éri el a virtuális gépek a memóriáját, a lemezét és a CPU-korlátot.
- A használat trendjeinek és rendellenességének észlelése.
- A költségeket a használat alapján méretezéssel szabályozhatja.
- Költséghatékony méretezési optimalizálási javaslatokat kaphat a Cloudyn.

Előfordulhat például, hogy figyelni kívánja az Azure-beli virtuális gépek PROCESSZORának%-át és memóriáját. Az Azure-beli virtuális gépek mérőszámai a processzor és a \Memory _százalékos arányát_ _\% előjegyzett bájtok használatban_.

> [!NOTE]
> A kiterjesztett metrikai adatok gyűjtése csak az Azure-beli vendég szintű figyeléssel támogatott. A Cloudyn nem kompatibilis a [log Analytics ügynökkel](../../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Annak megállapítása, hogy engedélyezve vannak-e a kiterjesztett metrikák

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. A **virtuális gépek**területen válasszon ki egy virtuális gépet, majd a **figyelés**területen válassza a **metrikák**lehetőséget. Megjelenik az elérhető metrikák listája.
3. Jelöljön ki néhány mérőszámot, és egy gráf megjeleníti az adatokat.  
    ![Példa metrika – gazda százalékos PROCESSZORa](./media/azure-vm-extended-metrics/metric01.png)

Az előző példában a gazdagépek számára korlátozott számú szabványos metrika található, a memória metrikái azonban nem. A memória metrikái a kiterjesztett mérőszámok részét képezik. Ebben az esetben a kiterjesztett metrikák nincsenek engedélyezve a virtuális gépen. A kiterjesztett mérőszámok engedélyezéséhez néhány további lépést is végre kell hajtania. Az alábbi információk végigvezetik a szolgáltatás engedélyezésén.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Kiterjesztett metrikák engedélyezése a Azure Portalban

A standard metrikák a gazdaszámítógépek metrikái. A _százalékos CPU_ -metrika egy példa. A vendég virtuális gépekhez alapszintű mérőszámok is tartoznak, és ezeket kiterjesztett mérőszámoknak is nevezik. Többek között a kiterjesztett mérőszámok például a _\Memory\% a használatban lévő bájtok és a_ _\Memory\Available bájtok_.

A kiterjesztett mérőszámok engedélyezése egyszerű. Minden virtuális gép esetében engedélyezze a vendég szintű figyelést. Ha engedélyezi a vendég szintű figyelést, a rendszer telepíti az Azure Diagnostics-ügynököt a virtuális gépre. Alapértelmezés szerint a rendszer a kiterjesztett metrikák alapszintű készletét adja hozzá. A klasszikus és a normál virtuális gépek esetében a következő folyamat ugyanaz, mint a Windows és a Linux rendszerű virtuális gépek esetében.

Ne feledje, hogy mind az Azure-, mind a Linux-alapú vendég szintű figyeléshez szükség van egy Storage-fiókra. Ha engedélyezi a vendég szintű figyelést, ha nem választ egy meglévő Storage-fiókot, a rendszer létrehoz egyet.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>A vendég szintű figyelés engedélyezése a meglévő virtuális gépeken

1. A **Virtual Machinesban**tekintse meg a virtuális gépek listáját, majd válasszon ki egy virtuális gépet.
2. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.
3. A diagnosztikai beállítások lapon kattintson a **vendég szintű figyelés engedélyezése**lehetőségre.  
    ![a vendég szintű figyelés engedélyezése az Áttekintés oldalon](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Néhány perc elteltével az Azure Diagnostics-ügynök telepítve van a virtuális gépre. A metrikák alapszintű készlete lesz hozzáadva. Frissítse az oldalt. A hozzáadott teljesítményszámlálók az Áttekintés lapon jelennek meg.
5. A Figyelés területen válassza a **metrikák**lehetőséget.
6. A metrikák diagram metrikus **névtér**területén válassza a **vendég (klasszikus)** lehetőséget.
7. A metrikák listájában megtekintheti a vendég virtuális gép összes rendelkezésre álló teljesítményszámlálói értékét.  
    ![Példa kiterjesztett mérőszámok listája](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>A vendég szintű figyelés engedélyezése az új virtuális gépeken

Új virtuális gépek létrehozásakor a felügyelet **lapon válassza az** **operációs rendszer vendég diagnosztika**elemét.

![Vendég operációs rendszer diagnosztika beállítása a következőre](./media/azure-vm-extended-metrics/new-enable-diag.png)

További információ az Azure-beli virtuális gépek bővített metrikáinak engedélyezéséről: [Az Azure Linux-ügynök és az](../../virtual-machines/extensions/agent-linux.md) [Azure Virtual Machine Agent áttekintése](../../virtual-machines/extensions/agent-windows.md)és használata.

## <a name="resource-manager-credentials"></a>Resource Manager hitelesítő adatai

A kiterjesztett mérőszámok engedélyezése után ellenőrizze, hogy a Cloudyn hozzáfér-e a [Resource Manager hitelesítő adataihoz](../../cost-management/activate-subs-accounts.md). A hitelesítő adatai szükségesek ahhoz, hogy a Cloudyn adatokat gyűjtsön és jelenítsen meg a virtuális gépek számára. A költségeket optimalizálási javaslatok létrehozásához is használják. A Cloudyn legalább három napi teljesítményadatokat igényel egy példányból annak megállapítása érdekében, hogy az egy leépítéses javaslatra van-e kiépítve.

## <a name="enable-vm-metrics-with-a-script"></a>VIRTUÁLIS gépek metrikáinak engedélyezése parancsfájlokkal

Azure PowerShell parancsfájlokkal engedélyezheti a VM-metrikákat. Ha sok virtuális gépen szeretné engedélyezni a metrikák használatát, használhat parancsfájlt a folyamat automatizálására. Példa parancsfájlok a GitHubon az [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics)szolgáltatásban.

## <a name="view-azure-performance-metrics"></a>Azure teljesítmény-metrikák megtekintése

Ha szeretné megtekinteni az Azure-példányok teljesítmény-mérőszámait a Cloudyn-portálon, navigáljon az **eszközök** > **számítási** > **példány-kezelőbe**. A virtuálisgép-példányok listájában bontson ki egy példányt, majd bontson ki egy erőforrást a részletek megtekintéséhez.

![Példa a példány-kezelőben megjelenő információkra](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Következő lépések

- Ha még nem engedélyezte a fiókok Azure Resource Manager API-hozzáférését, folytassa az [Azure-előfizetések és-fiókok aktiválásával](../../cost-management/activate-subs-accounts.md).
