---
title: Azure-beli virtuális gépek kiterjesztett metrikák hozzáadása |} A Microsoft Docs
description: Ez a cikk segít engedélyezze és konfigurálja a bővített diagnosztikai metrikák az Azure virtuális gépeknél.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
ms.custom: seodec18
ms.openlocfilehash: ebbdd89d3ef41d4fb40197cbd83038b5cbc02073
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230147"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Azure-beli virtuális gépek kiterjesztett metrikák hozzáadása

A Cloudyn az Azure-beli virtuális gépekről származó Azure metrikaadatok részletes saját erőforrásaival kapcsolatos információk megjelenítéséhez használ. Metrikaadatok teljesítményszámlálók, más néven Cloudyn használják jelentések készítéséhez. Azonban a Cloudyn nem automatikusan gyűjtse össze az összes Azure metrikaadatok Vendég virtuális gépek – engedélyeznie kell a metrika gyűjtemény. Ez a cikk segít engedélyezze és konfigurálja a további diagnosztikai metrikák az Azure virtuális gépeknél.

Miután engedélyezte a metrika gyűjtemény, akkor a következőket teheti:

- Tudnia kell, ha a virtuális gépek vannak éri el a memória, lemez és a CPU-korlátok.
- Használati trendek és rendellenességek észlelése.
- Méretezési használat alapján szabályozhatja a költségeket.
- Költség hatékony méretezése a Cloudyn optimalizálási javaslatokat kaphat.

Például előfordulhat, hogy szeretné figyelni a CPU-memória %-át az Azure-beli virtuális. Az Azure-beli virtuális gépek mérőszámai a processzor és a \Memory _százalékos arányát_ _\% előjegyzett bájtok használatban_.

> [!NOTE]
> Kiterjesztett metrikaadatok gyűjtemény csak az Azure vendégszintű figyelést is támogatja. A Cloudyn nem kompatibilis a [log Analytics ügynökkel](../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Határozza meg, hogy engedélyezi-e kiterjesztett metrikák

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. A **virtuális gépek**területen válasszon ki egy virtuális gépet, majd a **figyelés**területen válassza a **metrikák**lehetőséget. Elérhető mérőszámok listája látható.
3. Bizonyos metrikák, és a egy graph adatait jeleníti meg őket.  
    ![A példában a metrika – gazdagép százalékos Processzorhasználat](./media/azure-vm-extended-metrics/metric01.png)

Az előző példában korlátozott számú standard metrikák érhetők el a gazdagépeket, de memória metrikák nem. Memória metrikák kiterjesztett metrikák részét képezik. Kiterjesztett metrikák ebben az esetben nincs engedélyezve a virtuális gép számára. Kiterjesztett metrikák engedélyezése néhány további lépést kell végrehajtania. A következő információkat végigvezeti őket engedélyezése.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Az Azure Portalon kiterjesztett metrikák engedélyezése

Standard mérőszámok gazdagépmetrikák számítógépen. A _százalékos CPU_ -metrika egy példa. Is vannak a Vendég virtuális gépek alapvető metrikákat, és azok használ kiterjesztett metrikák is nevezik. Többek között a kiterjesztett mérőszámok például a _\Memory\% a használatban lévő bájtok és a_ _\Memory\Available bájtok_.

Kiterjesztett metrikák engedélyezése rendkívül egyszerű. Minden virtuális gép vendégszintű figyelés engedélyezése. Ha engedélyezi a vendégszintű figyelést, az Azure diagnostics-ügynök telepítve van a virtuális gépen. Alapértelmezés szerint egy alapvető házirendcsoport kiterjesztett metrikák kerülnek. Az alábbi eljárás megegyezik a klasszikus és a normál virtuális gépek és a Windows és Linux rendszerű virtuális gépek azonos.

Ne feledje, hogy a tárfiók Azure és a Linux vendégszintű monitorozás szükséges. Ha engedélyezi a vendégszintű figyelést, ha nem adja meg egy meglévő tárfiókot, majd egy jön létre az Ön számára.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>A meglévő virtuális gépeken vendégszintű figyelés engedélyezése

1. A **Virtual Machinesban**tekintse meg a virtuális gépek listáját, majd válasszon ki egy virtuális gépet.
2. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.
3. A diagnosztikai beállítások lapon kattintson a **vendég szintű figyelés engedélyezése**lehetőségre.  
    ![a vendég szintű figyelés engedélyezése az Áttekintés oldalon](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Néhány perc elteltével az Azure diagnostics-ügynök telepítve van a virtuális gépen. Egy alapvető házirendcsoport metrikák kerülnek. Frissítse az oldalt. A hozzáadott teljesítményszámlálók az Áttekintés lapon jelennek meg.
5. A Figyelés területen válassza a **metrikák**lehetőséget.
6. A metrikák diagram metrikus **névtér**területén válassza a **vendég (klasszikus)** lehetőséget.
7. A metrika listában megtekintheti az összes rendelkezésre álló teljesítményszámlálók vonatkozóan a Vendég virtuális Gépen.  
    ![kiterjesztett példa mérőszámok listája](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Vendégszintű figyelés új virtuális gépeken

Új virtuális gépek létrehozásakor a felügyelet **lapon válassza az** **operációs rendszer vendég diagnosztika**elemét.

![a vendég operációs rendszer diagnosztika beállítása](./media/azure-vm-extended-metrics/new-enable-diag.png)

További információ az Azure-beli virtuális gépek bővített metrikáinak engedélyezéséről: [Az Azure Linux-ügynök és az](../virtual-machines/extensions/agent-linux.md) [Azure Virtual Machine Agent áttekintése](../virtual-machines/extensions/agent-windows.md)és használata.

## <a name="resource-manager-credentials"></a>Erőforrás-kezelő hitelesítő adatai

A kiterjesztett mérőszámok engedélyezése után ellenőrizze, hogy a Cloudyn hozzáfér-e a [Resource Manager hitelesítő adataihoz](activate-subs-accounts.md). A hitelesítő adatok szükségesek a Cloudyn segítségével begyűjtheti és megjelenítheti a teljesítményadatokat a virtuális gépek számára. Is használhatók, a költségek optimalizálása javaslatok létrehozása. A Cloudyn kell meghatározni, hogy egy adott downsizing javaslat jelölt-példányból származó teljesítményadatok legalább három nap.

## <a name="enable-vm-metrics-with-a-script"></a>A parancsfájl a VM-metrikák engedélyezése

Engedélyezheti a VM-metrikák az Azure PowerShell-parancsfájlokkal. Ha azt szeretné, hogy engedélyezze a mérőszámok a több virtuális gépet, a parancsfájl segítségével automatizálja a folyamatot. Példa parancsfájlok a GitHubon az [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics)szolgáltatásban.

## <a name="view-azure-performance-metrics"></a>Az Azure teljesítménymetrikák megjelenítése

Ha szeretné megtekinteni az Azure-példányok teljesítmény-mérőszámait a Cloudyn-portálon, navigáljon az **eszközök** > **számítási** > **példány-kezelőbe**. A Virtuálisgép-példányok listáját bontsa ki a példány, és ezután bontsa ki az erőforrást, a részletek megtekintéséhez.

![Példány Explorer látható példa információk](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>További lépések

- Ha még nem engedélyezte a fiókok Azure Resource Manager API-hozzáférését, folytassa az [Azure-előfizetések és-fiókok aktiválásával](activate-subs-accounts.md).
