---
title: Adatok gyűjtése egy Azure virtuális gépről az Azure Monitor segítségével | Microsoft dokumentumok
description: Itt megtudhatja, hogyan engedélyezheti a Log Analytics-ügynök virtuálisgép-bővítményét, és ezzel az adatok összegyűjtését az Azure-beli virtuális gépekről a Log Analytics segítségével.
ms.subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 08/19/2019
ms.custom: mvc, sec-javascript-october2019
ms.openlocfilehash: 2e63b2ca0ee7246009068e6a2875795a14aeddb3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240337"
---
# <a name="collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Adatok gyűjtése egy Azure virtuális gépről az Azure Monitor segítségével

[Az Azure Monitor](../overview.md) adatokat gyűjthet közvetlenül az Azure virtuális gépek egy Log Analytics munkaterület részletes elemzés és korreláció. A Windows [és](../../virtual-machines/extensions/oms-windows.md) [Linux](../../virtual-machines/extensions/oms-linux.md) loganalytics virtuálisgép-bővítmény telepítése lehetővé teszi, hogy az Azure Monitor adatokat gyűjtsön az Azure virtuális gépekről. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja és gyűjthet adatokat az Azure Linux vagy a Windows virtuális gépek ről a virtuálisgép-bővítmény használatával néhány egyszerű lépéssel.  
 
A rövid útmutató feltételezi, hogy rendelkezik egy meglévő Azure-beli virtuális géppel. Ha nem, akkor virtuális gépekre vonatkozó rövid útmutatók alapján létrehozhat egy [Windows virtuális gépet](../../virtual-machines/windows/quick-create-portal.md) vagy egy [Linux virtuális gépet](../../virtual-machines/linux/quick-create-cli.md).

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a . 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**

    ![Azure portál](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Válassza **a Létrehozás**lehetőséget, majd adja meg a következő elemek hez szükséges beállításokat:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*.  
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.
  
        ![Log Analytics-erőforrás panel létrehozása](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Miután megadta a szükséges információkat a **Log Analytics munkaterület** ido pane, válassza az OK **gombot.**  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="enable-the-log-analytics-vm-extension"></a>A Log Analytics virtuálisgép-bővítményének engedélyezése

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Az Azure-ban már üzembe helyezett Windows és Linux rendszerű virtuális gépekhez a Log Analytics-ügynököt a virtuálisgép-bővítménnyel kell telepíteni. A bővítmény használata leegyszerűsíti a telepítés folyamatát és automatikusan konfigurálja az ügynököt, hogy elküldje az adatokat a megadott Log Analytics-munkaterületre. Az ügynök is automatikusan frissül, ha egy újabb verzió továbbfejlesztett, biztosítva, hogy a legújabb funkciók és javítások. A folytatás előtt ellenőrizze, hogy a virtuális gép fut-e, különben a folyamat nem lesz sikeresen befejeződött.  

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen. 

1. Az Azure Portalon válassza a Bal felső sarokban található **Összes szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**

2. A Log Analytics-munkaterületek listájában válassza ki a korábban létrehozott *DefaultLAWorkspace* elemet.

3. A bal oldali menü Munkaterület-adatforrások csoportban válassza a **Virtuális gépek**lehetőséget.  

4. Válassza ki a **Virtuális gépek** listájából azt a gépet, amelyre telepíteni szeretné az ügynököt. Figyelje meg, hogy a virtuális gép **Log Analytics-kapcsolat állapota** értékénél **Nincs kapcsolat** látható.

5. A virtuális gép részletei között válassza a **Csatlakozás** lehetőséget. A rendszer automatikusan telepíti és konfigurálja az ügynököt a Log Analytics-munkaterületen. Ez a folyamat néhány percet vesz igénybe, amely idő alatt az **Állapot** a **Csatlakozás állapotot**mutatja.

6. Az ügynök telepítése után a **Log Analytics-kapcsolat állapota** frissül, és **Ez a munkaterület** lesz.

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése

Az Azure Monitor gyűjthet eseményeket a Windows eseménynaplók vagy a Linux Syslog és teljesítményszámlálók, amelyek a hosszabb távú elemzés és jelentéskészítés, és tegyen lépéseket, ha egy adott feltétel észlelése. A következő lépésekkel konfigurálhatja az események gyűjtését a Windows vagy Linux rendszernaplókból, illetve (kezdetnek) egyes gyakran használt teljesítményszámlálókból.  

### <a name="data-collection-from-windows-vm"></a>Adatgyűjtés Windows rendszerű virtuális gépekről

1. Válassza ki a **Speciális beállítások** elemet.

    ![A Log Analytics speciális beállításai](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

2. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.

3. Eseménynaplókat a nevük begépelésével adhat hozzá.  Írja be **a Rendszer** **+** parancsot, majd válassza ki a pluszjelet .

4. A táblázatban jelölje be a **Hiba** és **Figyelmeztetés** súlyossági szintet.

5. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

6. Válassza a **Windowsos teljesítményadatok** lehetőséget a teljesítményszámláló-adatok egy windowsos gépen történő gyűjtésének engedélyezéséhez.

7. Amikor először konfigurálja egy új Log Analytics-munkaterület Windows-teljesítményszámlálóit, akkor gyorsan létrehozhat több gyakran használt számlálót. Ezek mindegyike mellett egy jelölőnégyzet található.

    ![Alapértelmezett Windows-teljesítményszámlálók kiválasztva](media/quick-collect-azurevm/windows-perfcounters-default.png)

    Válassza **a Kijelölt teljesítményszámlálók hozzáadása**lehetőséget.  A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.
  
8. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

### <a name="data-collection-from-linux-vm"></a>Adatgyűjtés Linux rendszerű virtuális gépekről

1. Válassza a **Syslog** elemet.  

2. Eseménynaplókat a nevük begépelésével adhat hozzá.  Írja be a **Syslog parancsot,** majd válassza ki a pluszjelet **+**.  

3. A táblázatban törölje a jelet az **Információ**, **Értesítés** és **Hibakeresés lehetőségéből.** 

4. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

5. Válassza a **Linuxos teljesítményadatok** elemet a teljesítményszámláló-adatok gyűjtésének Linux rendszerű gépen történő engedélyezéséhez. 

6. Amikor először konfigurálja egy új Log Analytics-munkaterület Linux-teljesítményszámlálóit, akkor több gyakran használt számlálót is gyorsan létrehozhat. Ezek mindegyike mellett egy jelölőnégyzet található.

    ![Alapértelmezett Windows-teljesítményszámlálók kiválasztva](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Válassza **az Alkalmaz alatt a konfigurációt a gépeimre,** majd a Kiválasztott **teljesítményszámlálók hozzáadása**lehetőséget.  A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.  

7. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

Most, hogy engedélyezte az adatgyűjtést, futtasson le egy egyszerű naplóbeli keresést, hogy megtekinthessen néhány, a cél virtuális gépekről származó adatot.  

1. A kijelölt munkaterületen a bal oldali ablaktáblában válassza a **Naplók**lehetőséget.

2. A Naplók lekérdezés lapon `Perf` írja be a lekérdezésszerkesztőt, és válassza a **Futtatás lehetőséget.**

    ![Példa Log Analytics-naplókeresési lekérdezésre](./media/quick-collect-windows-computer/log-analytics-portal-queryexample.png) 

    Az alábbi képen lévő lekérdezés például 10 000 teljesítményrekordot adott vissza. Ön ennél sokkal kevesebb találatot fog kapni.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Log Analytics-munkaterületet. Ehhez válassza ki a korábban létrehozott Log Analytics-munkaterületet, és az erőforráslapon válassza a **Törlés**lehetőséget.


![Log Analytics-erőforrás törlése](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>További lépések

Most, hogy már gyűjti a működési és teljesítményadatokat a Windows vagy Linux rendszerű virtuális gépeiről, könnyen nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, és reagálhat a kapott eredményekre.  

Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../../azure-monitor/learn/tutorial-viewdata.md)
