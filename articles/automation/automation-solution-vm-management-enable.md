---
title: Virtuális gépek indítási/leállítási engedélyezése munkaidőn kívüli megoldás közben
description: Ez a cikk ismerteti, hogyan engedélyezheti az Azure Automation Start/Stop virtuálisgép-megoldást az Azure virtuális gépekhez.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 7b619d3c9b4b334e637d6a1c456256cb33ad5134
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261380"
---
# <a name="enable-azure-startstop-vms-solution"></a>Az Azure Start/Stop virtuális gépek megoldásának engedélyezése

Hajtsa végre az alábbi lépéseket a virtuális gépek kezdési/leállítási/-leállításához munkaidőn kívüli megoldás ban egy új vagy meglévő Automation-fiókhoz és a csatolt Log Analytics-munkaterülethez. A bevezetési folyamat befejezése után konfigurálja a változókat a megoldás testreszabásához.

>[!NOTE]
>A megoldás klasszikus virtuális gépekkel való használatához klasszikus futtatási fiókra van szükség, amely alapértelmezés szerint nem jön létre. A klasszikus futtatási mint fiók létrehozásáról a [Klasszikus futtatás létrehozása mint fiók](automation-create-standalone-account.md#create-a-classic-run-as-account)című témakörben talál.
>

## <a name="enable-solution"></a>Megoldás engedélyezése

1. Jelentkezzen be az Azure [Portalra.](https://portal.azure.com)

2. Keresse meg és válassza az **Automation-fiókok lehetőséget.**

3. Az **Automation-fiókok** lapon válassza ki az Automation-fiókot a listából.

4. Az Automation-fiókban válassza a **Virtuális gép indítása/leállítása** lehetőséget a **Kapcsolódó erőforrások csoportban.** Itt kattintson a További információ a **megoldásról és engedélyezése**elemre. Ha már rendelkezik üzembe helyezett virtuálisgép-indítási/leállítási megoldással, kiválaszthatja azt **a Megoldás kezelése** gombra kattintva, és megtalálhatja a listában.

   ![Engedélyezés automatizálási fiókból](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Az Azure Portalon bárhonnan létrehozhatja, ha az **Erőforrás létrehozása**elemre kattint. A Piactér lapon írjon be egy kulcsszót, például **a Start** vagy **a Start/Stop kulcsszót.** Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Másik lehetőségként beírhat egy vagy több kulcsszót a megoldás teljes nevéből, majd nyomja le az Enter billentyűt. Válassza **a virtuális gépek indítása/leállítása munkaidőn kívül** lehetőséget a keresési eredmények között.

5. A kiválasztott megoldás **kezdési/leállítási/leállítási/leállítási** oldalán tekintse át az összesítő információkat, majd kattintson a **Létrehozás gombra.**

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. Megjelenik **a Megoldás hozzáadása** lap. A rendszer kéri, hogy konfigurálja a megoldást, mielőtt importálhatja azt az Automation-előfizetésbe.

   ![Virtuálisgép-kezelés megoldás hozzáadása lapja](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. A **Megoldás hozzáadása** lapon válassza a **Munkaterület**lehetőséget. Válasszon ki egy Log Analytics-munkaterületet, amely ugyanahhoz az Azure-előfizetéshez kapcsolódik, amelyben az Automation-fiók található. Ha nem rendelkezik munkaterülettel, válassza **az Új munkaterület létrehozása**lehetőséget. A **Log Analytics munkaterület-lapon** hajtsa végre az alábbi lépéseket:

   - Adja meg az új **Log Analytics-munkaterület**nevét, például a "ContosoLAWorkspace".Specify a name for the new Log Analytics workspace , such as "ContosoLAWorkspace".
   - Válassza ki **azt az előfizetést,** amelyre hivatkozni szeretne a legördülő listából való kijelöléssel, ha a kiválasztott alapértelmezett beállítás nem megfelelő.
   - Az **Erőforráscsoport**csoport ban létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.
   - Válasszon egy **helyet**.
   - Válasszon egy **tarifacsomagot**. Válassza a **Gb-onként (önálló)** lehetőséget. Az Azure Monitor-naplók frissített [díjszabási](https://azure.microsoft.com/pricing/details/log-analytics/) és a GB-onkénti szint az egyetlen lehetőség.

   > [!NOTE]
   > A megoldások engedélyezésekor csak bizonyos régiók esetén lehet összekapcsolni egy Log Analytics-munkaterületet és egy Automation-fiókot.
   >
   > A támogatott leképezési párok listáját az [Automation-fiók és a Log Analytics munkaterület területleképezése című témakörben tájékírja.](how-to/region-mappings.md)

8. Miután megadta a szükséges információkat a **Log Analytics munkaterület** imát, kattintson a **Létrehozás gombra.** A folyamat előrehaladását az **Értesítések** menüben követheti nyomon, amely akkor tér vissza a **Megoldás hozzáadása** lapra, amikor elkészült.

9. A **Megoldás hozzáadása** lapon válassza az **Automation-fiók lehetőséget.** Ha új Log Analytics-munkaterületet hoz létre, létrehozhat egy új Automation-fiókot, amelyet hozzá kell társviszonyba venni, vagy kiválaszthat egy meglévő Automation-fiókot, amely még nincs csatolva a Log Analytics-munkaterülethez. Jelöljön ki egy meglévő Automation-fiókot, vagy kattintson **az Automatizálási fiók létrehozása gombra,** és az **Automatizálási fiók hozzáadása** lapon adja meg a következő információkat:
 
   - A **Név** mezőbe írja be az Automation-fiók nevét.

     Az összes többi beállítás automatikusan kitöltődik a kiválasztott Log Analytics munkaterület alapján. Ezek a beállítások nem módosíthatók. A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók. Miután az **OK**gombra kattintott, a konfigurációs beállítások érvényesítésre kerülnek, és létrejön nek az Automation-fiók. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

10. Végül a **Megoldás hozzáadása** lapon válassza a **Konfiguráció**lehetőséget. Megjelenik **a Paraméterek** lap.

    ![A megoldás paraméterei lap](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Itt a rendszer a következő utasításokat kéri:
  
   - Adja meg a **célerőforráscsoport nevét**. Ezek az értékek olyan erőforráscsoportnevek, amelyek a megoldás által kezelendő virtuális gépeket tartalmaznak. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem különböztetik meg a kis- és nagybetűket). Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni. Ez az érték a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** változókban tárolódik.
  
   - Adja meg a **virtuális gép kizárási listáját (karakterlánc)**. Ez az érték a célerőforrás-csoport egy vagy több virtuális gépének a neve. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem különböztetik meg a kis- és nagybetűket). Helyettesítő karakter használata támogatott. Ez az érték a **External_ExcludeVMNames** változóban tárolódik.
  
   - Jelöljön ki egy **Ütemezést**. Válassza ki az ütemezés dátumát és időpontját. A program a kiválasztott idővel kezdődően ismétlődő napi ütemezést hoz létre. Egy másik régió kiválasztása nem érhető el. Ha a megoldás konfigurálása után az adott időzónára szeretné beállítani az ütemezést, olvassa [el az Indítási és leállítási ütemezés módosítása című témakört.](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)
  
   - Ha **e-mailes értesítéseket** szeretne kapni egy műveletcsoporttól, fogadja el az **Igen** alapértelmezett értéket, és adjon meg egy érvényes e-mail címet. Ha a **Nem** lehetőséget választja, de egy későbbi időpontban úgy dönt, hogy e-mailértesítést szeretne kapni, frissítheti az érvényes e-mail címekkel létrehozott [műveletcsoportot,](../azure-monitor/platform/action-groups.md) vesszővel elválasztva. A következő riasztási szabályokat is engedélyeznie kell:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > A **célerőforráscsoport nevek** alapértelmezett **&ast;** értéke a . Ez az előfizetésben szereplő összes virtuális gépet célozza meg. Ha nem szeretné, hogy a megoldás az előfizetésben lévő összes virtuális gépet célozza meg, ezt az értéket frissíteni kell az erőforráscsoportok nevének listájára az ütemezések engedélyezése előtt.

11. Miután konfigurálta a megoldáshoz szükséges kezdeti beállításokat, kattintson az **OK** gombra a **Paraméterek** lap bezárásához, és válassza a **Létrehozás lehetőséget.** 

Az összes beállítás érvényesítése után a megoldás telepítve van az előfizetésben. Ez a folyamat több másodpercig is eltarthat, és nyomon követheti a folyamat előrehaladását az **Értesítések** menüből.

> [!NOTE]
> Ha rendelkezik egy Azure Cloud Solution Provider (Azure CSP) előfizetéssel, a telepítés befejezése után az Automation-fiókban lépjen **a Változók** a **Megosztott erőforrások** csoportban, és állítsa a [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) **változót False**értékre. Ez megakadályozza, hogy a megoldás klasszikus virtuálisgép-erőforrásokat keres.

## <a name="next-steps"></a>További lépések

Most, hogy a megoldás engedélyezve van, [konfigurálhatja,](automation-solution-vm-management-config.md) hogy támogassa a virtuális gép felügyeleti követelményeit.