---
title: Azure-erőforrás figyelése az Azure Monitorsegítségével
description: Ismerje meg, hogyan gyűjthet és elemezhet adatokat egy Azure-erőforráshoz az Azure Monitorban.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79503656"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Rövid útmutató: Egy Azure-beli virtuális gép figyelése az Azure Monitorsegítségével
[Az Azure Monitor](../overview.md) akkor kezdi meg az azure-beli virtuális gépekről történő adatgyűjtést, amikor azok létrejönnek. Ebben a rövid útmutatóban rövid útmutatót hajt hat az Azure virtuális gépekhez automatikusan gyűjtött adatokra, és az Azure Portalon való megtekintésre. Ezután engedélyezi [az Azure Monitor virtuális gépek a](../insights/vminsights-overview.md) virtuális gép, amely lehetővé teszi, hogy a virtuális gép ügynökök adatok gyűjtése és elemzése a vendég operációs rendszer, beleértve a folyamatokat és azok függőségeit.

A rövid útmutató feltételezi, hogy rendelkezik egy meglévő Azure-beli virtuális géppel. Ha nem, létrehozhat egy [Windows virtuális gép,](../../virtual-machines/windows/quick-create-portal.md) vagy hozzon létre egy [Linux virtuális gép](../../virtual-machines/linux/quick-create-cli.md) a virtuális gép rövid útmutatók at.

Az Azure-erőforrásokból gyűjtött figyelési adatok részletesebb leírását az [Azure virtuális gépek figyelése az Azure Monitor segítségével című témakörben tartalmazza.](../insights/monitor-vm-azure.md)


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Töltse ki az Azure-erőforrás gyorsútmutatójának befejezése.
Az [Azure Monitor segítségével teljes azure-erőforrás figyelése](quick-monitor-azure-resource.md) az áttekintő lap, a tevékenységnapló és a virtuális gép metrikák megtekintéséhez az előfizetésben. Az Azure virtuális gépek ugyanazokat a figyelési adatokat gyűjtik, mint bármely más Azure-erőforrás, de ez csak a gazdagép virtuális gép. A rövid útmutató többi része a vendég operációs rendszer és a munkaterhelések figyelésére összpontosít.


## <a name="enable-azure-monitor-for-vms"></a>Az Azure-figyelő engedélyezése virtuális gépekhez
Míg a metrikák és a tevékenységnaplók a gazdagép virtuális gép, szüksége van egy ügynök és néhány konfiguráció tanai a vendég operációs rendszer és a számítási feladatok figyelési adatok gyűjtéséhez és elemzéséhez. Az Azure Monitor virtuális gépekhez telepíti ezeket az ügynököket, és további hatékony funkciókat biztosít a virtuális gépek figyeléséhez.

1. Nyissa meg a virtuális gép menüjét.
2. Vagy kattintson az **Áttekintés** lap Csempéjének Ugrás az **elemzési adataihoz** elemre, vagy kattintson a **Figyelés** menü **Elemzési** adatai ra.

    ![Áttekintő lap](media/quick-monitor-azure-vm/overview-insights.png)

3. Ha a virtuális gépek Azure Monitorszolgáltatása még nincs engedélyezve a virtuális gépen, kattintson az **Engedélyezés gombra.** 

    ![Elemzési adatok engedélyezése](media/quick-monitor-azure-vm/enable-insights.png)

4. Ha a virtuális gép még nincs csatolva a Log Analytics-munkaterülethez, a rendszer kéri, hogy válasszon ki egy meglévő munkaterületet, vagy hozzon létre egy újat. Válassza ki az alapértelmezett, amely egy munkaterület egyedi névvel ugyanabban a régióban, mint a virtuális gép.

    ![Munkaterület kiválasztása](media/quick-monitor-azure-vm/select-workspace.png)

5. A bevezetés néhány percet vesz igénybe, mivel a bővítmények engedélyezve vannak, és az ügynökök telepítve vannak a virtuális gépen. Amikor elkészült, egy üzenet jelenik meg, hogy az elemzési adatok sikeresen üzembe lettek helyezve. Kattintson **az Azure Monitor** a virtuális gépek Azure Monitor megnyitásához.

    ![Az Azure-figyelő megnyitása](media/quick-monitor-azure-vm/azure-monitor.png)

6. A virtuális gép az előfizetésben lévő, a bedeszkázott bármely más virtuális gépekkel együtt jelenik meg. Válassza a **Nem figyelt** lapot, ha meg szeretné tekinteni az előfizetésben nem beépített virtuális gépeket.

    ![Bevezetés](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Munkaterület konfigurálása
Amikor új Log Analytics-munkaterületet hoz létre, azt úgy kell konfigurálni, hogy naplót gyűjtsön. Ezt a konfigurációt csak egyszer kell végrehajtani, mivel a konfigurációt minden olyan virtuális gépnek elküldi, amely csatlakozik hozzá.

1. Válassza **a Munkaterület konfigurációját,** majd válassza ki a munkaterületet.

2. Válassza a **Speciális beállítások lehetőséget**

    ![A Log Analytics speciális beállításai](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Adatgyűjtés Windows rendszerű virtuális gépekről


2. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.

3. Eseménynapló hozzáadása a napló nevének beírásával.  Írja be **a Rendszer** **+** parancsot, majd válassza ki a pluszjelet .

4. A táblázatban jelölje be a **Hiba** és **Figyelmeztetés** súlyossági szintet.

5. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

### <a name="data-collection-from-linux-vm"></a>Adatgyűjtés Linux rendszerű virtuális gépekről

1. Válassza a **Syslog** elemet.  

2. Eseménynapló hozzáadása a napló nevének beírásával.  Írja be a **Syslog parancsot,** majd válassza ki a pluszjelet **+**.  

3. A táblázatban törölje a jelet az **Információ**, **Értesítés** és **Hibakeresés lehetőségéből.** 

4. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

7. Kattintson a virtuális gépre, majd válassza a **Teljesítmény** lapot. Ez a virtuális gép vendég operációs rendszeréből gyűjtött teljesítményszámlálók kiválasztott csoportját jeleníti meg. Görgessen le a további számlálók megtekintéséhez, és mozgassa az egeret egy diagram fölé, hogy különböző időpontokban megtekinthesse az átlagot és a százalékos arányokat.

    ![Teljesítmény](media/quick-monitor-azure-vm/performance.png)

9. Válassza a **Térkép** lehetőséget a leképezési szolgáltatás megnyitásához, amely a virtuális gépen futó folyamatokat és azok függőségeit mutatja. Válassza **a Tulajdonságok** lehetőséget a tulajdonságablak megnyitásához, ha még nincs megnyitva.

    ![Térkép](media/quick-monitor-azure-vm/map.png)

11. Bontsa ki a virtuális gép folyamatait. Válassza ki az egyik folyamatot a részletek megtekintéséhez és függőségeinek kiemeléséhez.

    ![Folyamatok](media/quick-monitor-azure-vm/processes.png)

12. Jelölje ki újra a virtuális gépet, majd válassza **az Események naplózása**lehetőséget. 

    ![Események naplózása](media/quick-monitor-azure-vm/log-events.png)

13. A virtuális gép Log Analytics-munkaterületében tárolt táblák listája látható. Ez a lista attól függően, hogy Windows vagy Linux virtuális gépet használ, eltérő lesz. Kattintson az **Esemény** táblára. Ez magában foglalja a Windows eseménynaplójából származó összes eseményt. A Log Analytics egy egyszerű lekérdezéssel nyílik meg az eseménynapló-bejegyzések lekéréséhez.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban engedélyezte az Azure Monitor virtuális gépek egy virtuális gép, és konfigurálta a Log Analytics munkaterületet, hogy a vendég operációs rendszer események gyűjtése. Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../../azure-monitor/learn/tutorial-viewdata.md)
