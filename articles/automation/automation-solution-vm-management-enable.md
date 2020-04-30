---
title: Virtuális gépek indításának és leállításának engedélyezése Azure Automation munkaidőn kívüli megoldásban
description: Ez a cikk azt ismerteti, hogyan engedélyezhető az Azure-beli virtuális gépek Azure Automation indítása/leállítása virtuálisgép-megoldása.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096962"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Virtuális gépek megoldásának engedélyezése Azure Automation indítása/leállítása

A következő lépések végrehajtásával adja hozzá a **Start/Stop VMS During off-hours** megoldást egy új vagy meglévő Automation-fiókhoz és a csatolt log Analytics-munkaterülethez. A bevezetési folyamat befejezése után konfigurálja a változókat a megoldás testreszabásához.

>[!NOTE]
>A megoldás klasszikus virtuális gépekkel való használatához klasszikus futtató fiókra van szükség, amely alapértelmezés szerint nem jön létre. Klasszikus futtató fiók létrehozásával kapcsolatos utasításokért lásd: [klasszikus futtató fiók létrehozása](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Megoldás engedélyezése

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com).

2. Keresse meg és válassza ki az **Automation-fiókokat**.

3. Az Automation-fiókok lapon válassza ki az Automation-fiókját a listából.

4. Az Automation-fiókban válassza a **virtuális gép indítása/leállítása** **kapcsolódó erőforrások**lehetőséget. Innen kattintson **a további információ és a megoldás engedélyezése**lehetőségre. Ha már van üzembe helyezett indítási/leállítási virtuálisgép-megoldás, kiválaszthatja a **megoldás kezelése** elemre kattintva, és megkeresheti azt a listában.

   ![Engedélyezés az Automation-fiókból](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azt is megteheti, hogy az **erőforrás létrehozása**elemre kattintva a Azure Portal bárhonnan létrehozhatja. A piactér lapon írjon be egy kulcsszót (például **Indítás** vagy **Indítás/Leállítás**). Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Azt is megteheti, hogy egy vagy több kulcsszót ír be a megoldás teljes nevéből, majd lenyomja az ENTER billentyűt. A keresési eredmények közül válassza a **Start/Stop VMS During off-hours** lehetőséget.

5. A kiválasztott megoldás **Start/Stop VMS During off-hours** oldalán tekintse át az összegzési információkat, majd kattintson a **Létrehozás**gombra.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. Megjelenik a megoldás hozzáadása lap. A rendszer arra kéri, hogy konfigurálja a megoldást, mielőtt importálni tudja az Automation-előfizetésbe.

   ![Virtuálisgép-felügyelet – megoldás hozzáadása lap](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. A megoldás hozzáadása lapon válassza a **munkaterület**lehetőséget. Válasszon olyan Log Analytics munkaterületet, amely ugyanahhoz az Azure-előfizetéshez van csatolva, amelyhez az Automation-fiók tartozik. Ha nem rendelkezik munkaterülettel, válassza az **Új munkaterület létrehozása**lehetőséget. A Log Analytics munkaterület lapon hajtsa végre a következő lépéseket:

   - Adja meg az új Log Analytics munkaterület nevét, például **ContosoLAWorkspace**.
   - Válassza ki azt az **előfizetést** , amelyhez a legördülő listából választhatja ki a hivatkozást, ha az alapértelmezett beállítás nem megfelelő.
   - **Erőforráscsoport**esetén létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.
   - Válasszon egy **helyet**.
   - Válasszon **árképzési szintet**. Válassza a **GB-os (önálló)** lehetőséget. Azure Monitor naplók frissültek a [díjszabással](https://azure.microsoft.com/pricing/details/log-analytics/) , és a GB-onként az egyetlen lehetőség.

   > [!NOTE]
   > A megoldások engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához.
   >
   > A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

8. A szükséges információk megadása után a Log Analytics munkaterület lapon kattintson a **Létrehozás**gombra. A menü **értesítések** részén nyomon követheti a folyamat állapotát, amely visszaadja a megoldás hozzáadása lapra, ha elkészült.

9. A megoldás hozzáadása lapon válassza az **Automation-fiók**lehetőséget. Ha új Log Analytics munkaterületet hoz létre, létrehozhat egy új Automation-fiókot, amelyhez társítva van, vagy olyan meglévő Automation-fiókot választhat, amely még nincs Log Analytics-munkaterülethez csatolva. Válasszon ki egy meglévő Automation-fiókot, vagy kattintson az Automation-fiók **létrehozása**lehetőségre, majd az Automation-fiók hozzáadása lapon adja meg a következő információkat:
 
   - A **Név** mezőbe írja be az Automation-fiók nevét.

     A rendszer a kijelölt Log Analytics munkaterület alapján automatikusan kitölti az összes többi beállítást. Ezek a beállítások nem módosíthatók. A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók. Miután rákattintott az **OK gombra**, a rendszer érvényesíti a konfigurációs beállításokat, és létrehozza az Automation-fiókot. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

10. Végül a megoldás hozzáadása oldalon válassza a **Konfigurálás**lehetőséget. Megjelenik a paraméterek lap.

    ![Paraméterek lap a megoldáshoz](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   A rendszer a következőket kéri:
  
   - Határozza meg a **cél ResourceGroup nevét**. Ezek az értékek olyan erőforráscsoport-nevek, amelyek a megoldás által felügyelni kívánt virtuális gépeket tartalmazzák. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem megkülönböztetik a kis-és nagybetűket). Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni. Ezt az értéket a **External_Start_ResourceGroupNames** és a **External_Stop_ResourceGroupNames** változó tárolja.
  
   - Határozza meg a **virtuális gép kizárási listáját (karakterlánc)**. Ez az érték egy vagy több virtuális gép neve a cél erőforráscsoporthoz. Több nevet is megadhat, és vesszővel elválaszthatja őket (az értékek nem megkülönböztetik a kis-és nagybetűket). Helyettesítő karakter használata támogatott. Ezt az értéket a **External_ExcludeVMNames** változó tárolja.
  
   - Válasszon ki egy **ütemtervet**. Adja meg az ütemterv dátumát és időpontját. A rendszer a kiválasztott időponttól kezdve ismétlődő napi ütemtervet hoz létre. Egy másik régió kiválasztása nem érhető el. Ha a megoldás konfigurálása után szeretné beállítani az ütemtervet a megadott időzónára, tekintse meg [az indítási és leállítási ütemterv módosítása](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)című témakört.
  
   - Ha **e-mail-értesítéseket** szeretne kapni egy műveleti csoportból, fogadja el az alapértelmezett értéket **, és adjon** meg egy érvényes e-mail-címet. Ha a **nem** lehetőséget választja, de az e-mail-értesítések fogadását követően később dönt, akkor az érvényes e-mail-címekkel létrehozott [műveleti csoportot](../azure-monitor/platform/action-groups.md) a vesszővel elválasztva frissítheti. A következő riasztási szabályokat is engedélyeznie kell:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > A **célként megadott ResourceGroup-nevek** alapértelmezett értéke a **&ast;**. Ez egy előfizetésben lévő összes virtuális gépet célozza meg. Ha nem szeretné, hogy a megoldás az előfizetésben lévő összes virtuális gépet megcélozza, ezt az értéket frissíteni kell az erőforráscsoport-nevek listájára az ütemtervek engedélyezése előtt.

11. Miután konfigurálta a megoldáshoz szükséges kezdeti beállításokat, kattintson az **OK** gombra a paraméterek lap bezárásához, majd válassza a **Létrehozás**lehetőséget. 

Az összes beállítás érvényesítése után a megoldás üzembe lesz helyezve az előfizetésében. Ez a folyamat több másodpercig is eltarthat, és a menü **értesítések** részén nyomon követheti a folyamat állapotát.

> [!NOTE]
> Ha Azure Cloud Solution Provider (Azure CSP) előfizetéssel rendelkezik, az üzembe helyezés befejezése után az Automation-fiókjában lépjen a **változók** elemre a **megosztott erőforrások** területen, és állítsa a [External_EnableClassicVMs](automation-solution-vm-management.md#variables) változót **hamis**értékre. Ezzel leállítja a megoldást a klasszikus virtuálisgép-erőforrások keresésére.

## <a name="next-steps"></a>További lépések

Most, hogy engedélyezte a megoldást [, beállíthatja](automation-solution-vm-management-config.md) , hogy támogassa a virtuális gép felügyeleti követelményeit.