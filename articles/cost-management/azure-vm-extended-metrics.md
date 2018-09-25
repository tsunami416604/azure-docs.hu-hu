---
title: Azure-beli virtuális gépek kiterjesztett metrikák hozzáadása |} A Microsoft Docs
description: Ez a cikk segít engedélyezze és konfigurálja a bővített diagnosztikai metrikák az Azure virtuális gépeknél.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9c09f473f5d8b399c4fb1b1ad449c783c797f4dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968509"
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
> Kiterjesztett metrikaadatok gyűjtemény csak az Azure vendégszintű figyelést is támogatja. A Cloudyn, ezért nem kompatibilis az a Log Analytics Virtuálisgép-bővítményt.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Győződjön meg arról, hogy a metrikák engedélyezve vannak a virtuális gépeken

1. Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.
2. A **virtuális gépek**, válasszon ki egy virtuális Gépet, majd a **figyelés**válassza **metrikák**. Elérhető mérőszámok listája látható.
3. Bizonyos metrikák, és a egy graph adatait jeleníti meg őket.  
    ![A példában a metrika – gazdagép százalékos Processzorhasználat](./media/azure-vm-extended-metrics/metric01.png)

Az előző példában korlátozott számú standard metrikák érhetők el a gazdagépeket, de memória metrikák nem. Memória metrikák kiterjesztett metrikák részét képezik. Kiterjesztett metrikák engedélyezése néhány további lépést kell végrehajtania. A következő információkat végigvezeti őket engedélyezése.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Az Azure Portalon kiterjesztett metrikák engedélyezése

Standard mérőszámok gazdagépmetrikák számítógépen. A _[gazdagép] százalékos Processzorhasználat_ metrika egy példa. Is vannak a Vendég virtuális gépek alapvető metrikákat, és azok használ kiterjesztett metrikák is nevezik. Kiterjesztett metrikák közé _[Vendég] memóriahasználat (%)_ és _[Vendég] rendelkezésre álló memória_.

Kiterjesztett metrikák engedélyezése rendkívül egyszerű. Minden virtuális gép vendégszintű figyelés engedélyezése. Ha engedélyezi a vendégszintű figyelést, az Azure diagnostics-ügynök telepítve van a virtuális gépen. Az alábbi eljárás megegyezik a klasszikus és a normál virtuális gépek és a Windows és Linux rendszerű virtuális gépek azonos.

Ne feledje, hogy a tárfiók Azure és a Linux vendégszintű monitorozás szükséges. Ha engedélyezi a vendégszintű figyelést, ha nem adja meg egy meglévő tárfiókot, majd egy jön létre az Ön számára.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>A meglévő virtuális gépeken vendégszintű figyelés engedélyezése

1. A **virtuális gépek**, a virtuális gépek listájának megtekintése, és válassza ki a virtuális Gépet.
2. A **figyelés**válassza **metrikák**.
3. Kattintson a **diagnosztikai beállítások**.
4. A diagnosztikai beállítások oldalon kattintson a **vendégszintű figyelés engedélyezése**.  
    ![Vendég szinten figyelés engedélyezése](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Néhány perc elteltével az Azure diagnostics-ügynök telepítve van a virtuális gépen. Frissítse az oldalt, és a Vendég mérőszámok rendelkezésre álló metrikák listája frissül.  
    ![Kiterjesztett metrikák](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Vendégszintű figyelés új virtuális gépeken

Amikor új virtuális gépeket hoz létre, győződjön meg arról, hogy kiválasztotta **vendég operációs rendszer diagnosztikája**.

![A vendég operációs rendszer diagnosztika engedélyezése](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Erőforrás-kezelő hitelesítő adatai

Miután engedélyezte a kiterjesztett metrikák, győződjön meg arról, hogy Cloudyn hozzáfér a [erőforrás-kezelő hitelesítő adatai](activate-subs-accounts.md). A hitelesítő adatok szükségesek a Cloudyn segítségével begyűjtheti és megjelenítheti a teljesítményadatokat a virtuális gépek számára. Is használhatók, a költségek optimalizálása javaslatok létrehozása. A Cloudyn kell meghatározni, hogy egy adott downsizing javaslat jelölt-példányból származó teljesítményadatok legalább három nap.

## <a name="enable-vm-metrics-with-a-script"></a>A parancsfájl a VM-metrikák engedélyezése

Engedélyezheti a VM-metrikák az Azure PowerShell-parancsfájlokkal. Ha azt szeretné, hogy engedélyezze a mérőszámok a több virtuális gépet, a parancsfájl segítségével automatizálja a folyamatot. Példa a Githubon érhetők el: [Azure engedélyezése diagnosztikai](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Az Azure teljesítménymetrikák megjelenítése

A Cloudyn portálon az Azure-példányokon futó teljesítmény-mérőszámok megtekintéséhez lépjen **eszközök** > **számítási** > **példány Explorer**. A Virtuálisgép-példányok listáját bontsa ki a példány, és ezután bontsa ki az erőforrást, a részletek megtekintéséhez.

![Példány Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>További lépések

- Ha az Azure Resource Manager API-hozzáférés még nem engedélyezte a fiókokhoz, folytassa [Activate Azure-előfizetések és fiókok](activate-subs-accounts.md).
