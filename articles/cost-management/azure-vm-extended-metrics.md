---
title: Azure-beli virtuális gépek kiterjesztett metrikák hozzáadása |} A Microsoft Docs
description: Ez a cikk segít engedélyezze és konfigurálja a bővített diagnosztikai metrikák az Azure virtuális gépeknél.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: cost-management
ms.custom: seodec18
ms.openlocfilehash: dfe957ae67a8e154a62e443d2cc1769531dab77b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544784"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Azure-beli virtuális gépek kiterjesztett metrikák hozzáadása

A Cloudyn az Azure-beli virtuális gépekről származó Azure metrikaadatok részletes saját erőforrásaival kapcsolatos információk megjelenítéséhez használ. Metrikaadatok teljesítményszámlálók, más néven Cloudyn használják jelentések készítéséhez. Azonban a Cloudyn nem automatikusan gyűjtse össze az összes Azure metrikaadatok Vendég virtuális gépek – engedélyeznie kell a metrika gyűjtemény. Ez a cikk segít engedélyezze és konfigurálja a további diagnosztikai metrikák az Azure virtuális gépeknél.

Miután engedélyezte a metrika gyűjtemény, akkor a következőket teheti:

- Tudnia kell, ha a virtuális gépek vannak éri el a memória, lemez és a CPU-korlátok.
- Használati trendek és rendellenességek észlelése.
- Méretezési használat alapján szabályozhatja a költségeket.
- Költség hatékony méretezése a Cloudyn optimalizálási javaslatokat kaphat.

Például előfordulhat, hogy szeretné figyelni a CPU-memória %-át az Azure-beli virtuális. Az Azure-beli Virtuálisgép-metrikák megfelelnek _[gazdagép] százalékos Processzorhasználat_ és _[Vendég] memóriahasználat (%)_.

> [!NOTE]
> Kiterjesztett metrikaadatok gyűjtemény csak az Azure vendégszintű figyelést is támogatja. A Cloudyn, ezért nem kompatibilis az Azure Monitor naplók Virtuálisgép-bővítményt.

## <a name="determine-whether-extended-metrics-are-enabled"></a>Határozza meg, hogy engedélyezi-e kiterjesztett metrikák

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. A **virtuális gépek**, válasszon ki egy virtuális Gépet, majd a **figyelés**válassza **metrikák**. Elérhető mérőszámok listája látható.
3. Bizonyos metrikák, és a egy graph adatait jeleníti meg őket.  
    ![A példában a metrika – gazdagép százalékos Processzorhasználat](./media/azure-vm-extended-metrics/metric01.png)

Az előző példában korlátozott számú standard metrikák érhetők el a gazdagépeket, de memória metrikák nem. Memória metrikák kiterjesztett metrikák részét képezik. Kiterjesztett metrikák ebben az esetben nincs engedélyezve a virtuális gép számára. Kiterjesztett metrikák engedélyezése néhány további lépést kell végrehajtania. A következő információkat végigvezeti őket engedélyezése.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Az Azure Portalon kiterjesztett metrikák engedélyezése

Standard mérőszámok gazdagépmetrikák számítógépen. A _[gazdagép] százalékos Processzorhasználat_ metrika egy példa. Is vannak a Vendég virtuális gépek alapvető metrikákat, és azok használ kiterjesztett metrikák is nevezik. Kiterjesztett metrikák közé _[Vendég] memóriahasználat (%)_ és _[Vendég] rendelkezésre álló memória_.

Kiterjesztett metrikák engedélyezése rendkívül egyszerű. Minden virtuális gép vendégszintű figyelés engedélyezése. Ha engedélyezi a vendégszintű figyelést, az Azure diagnostics-ügynök telepítve van a virtuális gépen. Alapértelmezés szerint egy alapvető házirendcsoport kiterjesztett metrikák kerülnek. Az alábbi eljárás megegyezik a klasszikus és a normál virtuális gépek és a Windows és Linux rendszerű virtuális gépek azonos.

Ne feledje, hogy a tárfiók Azure és a Linux vendégszintű monitorozás szükséges. Ha engedélyezi a vendégszintű figyelést, ha nem adja meg egy meglévő tárfiókot, majd egy jön létre az Ön számára.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>A meglévő virtuális gépeken vendégszintű figyelés engedélyezése

1. A **virtuális gépek**, a virtuális gépek listájának megtekintése, és válassza ki a virtuális Gépet.
2. A **figyelés**válassza **diagnosztikai beállítások**.
3. A diagnosztikai beállítások oldalon kattintson a **vendégszintű figyelés engedélyezése**.  
    ![Vendég szinten figyelés az Áttekintés lap engedélyezése](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Néhány perc elteltével az Azure diagnostics-ügynök telepítve van a virtuális gépen. Egy alapvető házirendcsoport metrikák kerülnek. Frissítse az oldalt. A hozzáadott teljesítményszámlálók az Áttekintés lapon jelennek meg.
5. Válassza ki a figyelés területen **metrikák**.
6. A metrikák diagram alatt **metrika Namespace**válassza **Vendég (klasszikus)**.
7. A metrika listában megtekintheti az összes rendelkezésre álló teljesítményszámlálók vonatkozóan a Vendég virtuális Gépen.  
    ![kiterjesztett példa mérőszámok listája](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Vendégszintű figyelés új virtuális gépeken

Amikor hoz létre új virtuális gépeket, a kezelés lapon, válassza ki a **a** a **operációs rendszer Vendég diagnosztikai**.

![a vendég operációs rendszer diagnosztika beállítása](./media/azure-vm-extended-metrics/new-enable-diag.png)

Kiterjesztett metrikák az Azure-beli virtuális gépek engedélyezésével kapcsolatos további információkért lásd: [megértését és az Azure Linux-ügynök használatával](../virtual-machines/extensions/agent-linux.md) és [Azure-beli virtuálisgép-ügynök – áttekintés](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Erőforrás-kezelő hitelesítő adatai

Miután engedélyezte a kiterjesztett metrikák, győződjön meg arról, hogy Cloudyn hozzáfér a [erőforrás-kezelő hitelesítő adatai](activate-subs-accounts.md). A hitelesítő adatok szükségesek a Cloudyn segítségével begyűjtheti és megjelenítheti a teljesítményadatokat a virtuális gépek számára. Is használhatók, a költségek optimalizálása javaslatok létrehozása. A Cloudyn kell meghatározni, hogy egy adott downsizing javaslat jelölt-példányból származó teljesítményadatok legalább három nap.

## <a name="enable-vm-metrics-with-a-script"></a>A parancsfájl a VM-metrikák engedélyezése

Engedélyezheti a VM-metrikák az Azure PowerShell-parancsfájlokkal. Ha azt szeretné, hogy engedélyezze a mérőszámok a több virtuális gépet, a parancsfájl segítségével automatizálja a folyamatot. Példa a Githubon érhetők el: [Azure engedélyezése diagnosztikai](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Az Azure teljesítménymetrikák megjelenítése

A Cloudyn portálon az Azure-példányokon futó teljesítmény-mérőszámok megtekintéséhez lépjen **eszközök** > **számítási** > **példány Explorer**. A Virtuálisgép-példányok listáját bontsa ki a példány, és ezután bontsa ki az erőforrást, a részletek megtekintéséhez.

![Példány Explorer látható példa információk](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>További lépések

- Ha az Azure Resource Manager API-hozzáférés még nem engedélyezte a fiókokhoz, folytassa [Activate Azure-előfizetések és fiókok](activate-subs-accounts.md).
