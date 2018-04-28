---
title: Adja hozzá az Azure virtuális gépek kiterjesztett metrikák |} Microsoft Docs
description: Ez a cikk segít engedélyezheti és konfigurálhatja az Azure virtuális gépek bővített diagnosztikai metrikákat.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4b00baba44a4724ce8f6a45a80692f7f566a35ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Az Azure virtuális gépek kiterjesztett metrikák hozzáadása

Költség felügyeleti részletes az erőforrásokra vonatkozó információk megjelenítése az Azure virtuális gépek Azure metrika adatait használja. Metrika értékét, más néven a teljesítményszámlálókat, a költség felügyeleti használja a jelentések készítéséhez. Azonban költség felügyeleti nem automatikusan minden Azure metrika adatokat gyűjt a Vendég virtuális gépek – engedélyeznie kell a metrika gyűjtemény. Ez a cikk segít engedélyezheti és konfigurálhatja a további diagnosztikához metrikák Azure virtuális gépeken.

Miután engedélyezte a metrika gyűjtemény, teheti:

- Tudja meg, amikor elérni próbált a virtuális gépek a memória, a lemez és a CPU-korlátok.
- Használati trendeket és a rendellenességek észlelését.
- Szabályozhatja költségeit méretezési használati megfelelően.
- Költség hatékony méretezésével optimalizálási javaslatok költség felügyeletből beolvasása.

Például érdemes figyelni a CPU és memória % Azure virtuális gépeken. Az Azure virtuális gép mérőszámok vannak rendelve _[állomás] százalékos CPU_ és _[Vendég] memóriaszázaléka_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Győződjön meg arról, hogy metrikák engedélyezve vannak-e a virtuális gépeken

1. Jelentkezzen be az Azure Portalra a http://portal.azure.com címen.
2. A **virtuális gépek**, jelöljön ki egy virtuális Gépet majd a **figyelés**, jelölje be **metrikák**. Elérhető listája látható.
3. Válassza ki az egyes metrikákat, és egy grafikonon jeleníti meg a számukra.  
    ![Példa a Metrika – állomás százalékos Processzor](./media/azure-vm-extended-metrics/metric01.png)

Az előző példában a szabványos metrikák korlátozott számú érhetők el a gazdagépeket, de memória metrikák nem. Memória metrikák kiterjesztett metrikák részét képezik. Ahhoz, hogy kiterjesztett metrikák néhány további lépést kell végrehajtani. Az alábbi információk végigvezeti Önt engedélyezésük.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Az Azure-portálon a kiterjesztett metrikák engedélyezése

Standard metrikák gazdagép számítógép metrikák. A _[állomás] százalékos CPU_ metrika csak egy példa. Vannak olyan Vendég virtuális gépek alapvető metrikákat, és azok még más néven bővített metrikák. Kiterjesztett mérőszámok közé _[Vendég] memóriaszázaléka_ és _[Vendég] memória_.

Kiterjesztett metrikák engedélyezése egy egyszerű. Az egyes virtuális gépek Vendég szint a figyelés bekapcsolható. Vendég szint figyelés engedélyezésekor a virtuális Gépet az Azure diagnostics-ügynök van telepítve. A következő érték azonos klasszikus és rendszeres virtuális gépek és a Windows és Linux virtuális gépek azonos.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Vendég szint meglévő virtuális gépeken figyelésének engedélyezése

1. A **virtuális gépek**, tekintse meg a virtuális gépek a listáját, és válassza ki a virtuális gépek.
2. A **figyelés**, jelölje be **metrikák**.
3. Kattintson a **diagnosztikai beállítások**.
4. A diagnosztikai beállítások lapján kattintson a **vendégszintű a figyelés bekapcsolható**. Linux virtuális gépek egy meglévő tárfiók szükséges. Ha a Windows virtuális gépek ne válasszon tárfiókot, majd egy létrejön.  
    ![Vendég szinten figyelésének engedélyezése](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Néhány perc elteltével az Azure diagnostics-ügynök telepítve van a virtuális gép. Frissítse az oldalt, és az elérhető mérőszámok listája frissült Vendég metrikákat.  
    ![Kiterjesztett metrikák](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Vendég szint az új virtuális gépeken futó figyelésének engedélyezése

Ha létrehozott egy új virtuális gépet, győződjön meg arról, hogy válassza **vendég operációs rendszer diagnosztika**. Linux virtuális gépek egy meglévő tárfiók szükséges. Ha a Windows virtuális gépek ne válasszon tárfiókot, majd egy létrejön.

![Vendég operációs rendszer diagnosztika engedélyezése](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Erőforrás-kezelő hitelesítő adatai

Kiterjesztett metrikák engedélyezése után győződjön meg arról, hogy költség felügyeleti hozzáférési joga van a [erőforrás-kezelő hitelesítő adatai](activate-subs-accounts.md). A hitelesítő adatokra szükség a felügyeleti költségek gyűjtése és teljesítményadatát megjeleníti a virtuális géphez. Azok a most is használja, költség optimalizálási javaslatok létrehozásához. Költség-felügyeleti követelményeket legalább három napon egy példányt határozzák meg, ha egy downsizing javaslat jelöltségét ellenőrző teljesítményadatait.

## <a name="enable-vm-metrics-with-a-script"></a>Egy parancsfájl VM mértékek engedélyezése

VM metrikák Azure PowerShell-parancsfájlok használatával engedélyezheti. Ha sok virtuális gép, engedélyezheti a metrikák a kívánt, egy parancsfájl segítségével automatizálható a folyamat. A Githubon, amelyek parancsfájlpéldákat [Azure engedélyezése diagnosztikai](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Az Azure teljesítménymutatók megtekintése

Az Azure-példányokon a Cloudyn portálon teljesítménymutatók megtekintéséhez navigáljon **eszközök** > **számítási** > **példány Explorer**. A Virtuálisgép-példányok listáját bontsa ki a példány, és ezután bontsa ki egy erőforrást, a részletek megtekintéséhez.

![Példány Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>További lépések

- Ha a fiók még nem engedélyezte a Azure Resource Manager API-hozzáférést, lépjen [aktiválása Azure-előfizetések és fiókok](activate-subs-accounts.md)
