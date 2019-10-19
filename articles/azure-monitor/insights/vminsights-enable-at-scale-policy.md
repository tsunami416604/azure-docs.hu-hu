---
title: Azure Monitor for VMs engedélyezése a Azure Policy használatával | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Monitor for VMs több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Policy használatával.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 068d320c85a1e10027c92b92c1b3c205a999bed7
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553829"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Azure Monitor for VMs (előzetes verzió) engedélyezése Azure Policy használatával

Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs (előzetes verzió) Azure-beli virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz Azure Policy használatával. A folyamat végén sikeresen konfigurálta a Log Analytics és a függőségi ügynökök engedélyezését, és azonosíthatja a nem megfelelő virtuális gépeket.

Az összes Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport felderítéséhez, kezeléséhez és Azure Monitor for VMs engedélyezéséhez Azure Policy vagy Azure PowerShell is használhat. Azure Policy az ajánlott módszer, mert a szabályzat-definíciók segítségével hatékonyan szabályozhatja az előfizetéseket, így biztosítva az újonnan kiosztott virtuális gépek egységes megfelelőségét és automatikus engedélyezését. Ezek a szabályzat-definíciók:

* Telepítse a Log Analytics-ügynököt és a függőségi ügynököt.
* Jelentés a megfelelőségi eredményekről.
* Szervizelés a nem megfelelő virtuális gépek esetében.

Ha szeretné elvégezni ezeket a feladatokat Azure PowerShell vagy egy Azure Resource Manager sablonnal, tekintse meg a [Azure monitor for VMS (előzetes verzió) engedélyezése Azure PowerShell vagy Azure Resource Manager sablonok használatával](vminsights-enable-at-scale-powershell.md)című témakört.

## <a name="manage-policy-coverage-feature-overview"></a>A szabályzatok lefedettségi funkcióinak áttekintése

Eredetileg a Azure Monitor for VMs szabályzat-definícióinak kezeléséhez és üzembe helyezéséhez Azure Policy élményt kizárólag Azure Policy. A szabályzatok kezelése szolgáltatással egyszerűbbé és egyszerűbben deríthető fel, kezelhető és engedélyezhető az **Azure monitor for VMS engedélyezése** kezdeményezés, amely magában foglalja a korábban említett szabályzat-definíciókat is. Ezt az új funkciót a Azure Monitor for VMs első **lépések** lapján érheti el. Válassza a **házirend-lefedettség kezelése** lehetőséget a **Azure monitor for VMS házirend-lefedettség** lap megnyitásához.

![Azure Monitor virtuális gépekről – első lépések lap](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Itt megtekintheti és kezelheti a felügyeleti csoportokon és előfizetéseken keresztüli kezdeményezés hatókörét. Megtudhatja, hány virtuális gép létezik az egyes felügyeleti csoportokban és előfizetésekben, valamint a megfelelőségi állapotukban.

![Azure Monitor for VMs házirend kezelése lap](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Ezek az információk hasznosak lehetnek a Azure Monitor for VMs irányítási forgatókönyvének megtervezéséhez és végrehajtásához egy központi helyről. Míg a Azure Policy megfelelőségi nézetet biztosít, ha egy házirend vagy kezdeményezés egy hatókörhöz van rendelve, ezzel az új oldallal felfedezheti, hogy a házirend vagy kezdeményezés hol van rendelve, és hogyan rendelhető hozzá a helyhez. Minden olyan művelet, mint például a hozzárendelés, a megtekintés és a Szerkesztés, Azure Policy közvetlenül az átirányítás. A **Azure monitor for VMS Policy lefedettségi** oldal egy kibővített és integrált felhasználói felület, amely csak a kezdeményezés **engedélyezése Azure monitor for VMS**.

Ezen a lapon konfigurálhatja a Log Analytics munkaterületet Azure Monitor for VMs számára is, amely:

- Telepíti a Service Map megoldást.
- A teljesítmény-diagramok, a munkafüzetek, valamint az egyéni naplók és riasztások által használt operációsrendszer-teljesítményszámlálók használatát teszi lehetővé.

![Munkaterületek Azure Monitor for VMs konfigurálása](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ez a lehetőség nem kapcsolódik semmilyen házirend-művelethez. Elérhetővé teszi a Azure Monitor for VMs engedélyezéséhez szükséges [Előfeltételek](vminsights-enable-overview.md) teljesítését.  

### <a name="what-information-is-available-on-this-page"></a>Milyen információk érhetők el ezen a lapon?

Az alábbi táblázat a szabályzat-lefedettségi lapon bemutatott információk részletezését és értelmezését ismerteti.

| Függvény | Leírás | 
|----------|-------------| 
| **Hatókör** | Felügyeleti csoport és előfizetések, amelyekkel a felügyeleti csoport hierarchiáján keresztül lehatolhat, vagy örökölt a hozzáférés.|
| **Szerepkör** | A hatókörhöz tartozó szerepkör, amely lehet olvasó, tulajdonos vagy közreműködő. Bizonyos esetekben előfordulhat, hogy üresen jelenik meg, amely azt jelzi, hogy az előfizetés hozzáférhet az előfizetéshez, de nem ahhoz a felügyeleti csoporthoz, amelyhez tartozik. A más oszlopokban lévő információk a szerepkörtől függően változnak. A szerepkör kulcsfontosságú szerepet játszik a megjelenő adatok és a szabályzatok vagy kezdeményezések (tulajdonos), a Szerkesztés vagy a megfelelőség megtekintésével kapcsolatos műveletek elvégzésében. |
| **Összes virtuális gép** | A hatókörben lévő virtuális gépek száma. Felügyeleti csoport esetén az előfizetés vagy a gyermek felügyeleti csoportba ágyazott virtuális gépek összessége. |
| **Hozzárendelések lefedettsége** | A házirend vagy kezdeményezés által érintett virtuális gépek százalékos aránya. |
| **Hozzárendelés állapota** | A házirend vagy kezdeményezés-hozzárendelés állapotára vonatkozó információk. |
| **Megfelelő virtuális gépek** | A házirend vagy kezdeményezés keretében megfelelő virtuális gépek száma. A kezdeményezés **engedélyezése Azure monitor for VMS**esetében ez az a virtuális gépek száma, amelyek log Analytics ügynökkel és függőségi ügynökkel rendelkeznek. Bizonyos esetekben előfordulhat, hogy a hozzárendelés, a virtuális gépek és a nem megfelelő engedélyek miatt üresen jelenik meg. Az információkat a **megfelelőségi állapot**alatt kell megadnia. |
| **Megfelelőség** | A teljes megfelelőségi szám a különböző erőforrások összegével megosztható különálló erőforrások összege. |
| **Megfelelőségi állapot** | A házirend-vagy kezdeményezés-hozzárendelés megfelelőségi állapotával kapcsolatos információk.|

A házirend vagy kezdeményezés hozzárendelésekor a hozzárendelésben kiválasztott hatókör lehet a felsorolt hatókör vagy annak egy részhalmaza. Előfordulhat például, hogy létrehozott egy előfizetéshez tartozó hozzárendelést (házirend hatóköre), és nem felügyeleti csoportot (lefedettségi hatókör). Ebben az esetben a **hozzárendelési lefedettség** értéke azt jelzi, hogy a házirend vagy a kezdeményezés hatókörében lévő virtuális gépek a lefedettségi hatókörben lévő virtuális gépekkel vannak osztva. Egy másik esetben előfordulhat, hogy kizárta néhány virtuális gépet, erőforráscsoportot vagy előfizetést a házirend hatókörből. Ha az érték üres, akkor azt jelzi, hogy a házirend vagy a kezdeményezés nem létezik, vagy Önnek nincs megfelelő engedélye. Az információk a **hozzárendelés állapota**alatt vannak megadva.

## <a name="enable-by-using-azure-policy"></a>Engedélyezés Azure Policy használatával

A Azure Monitor for VMs engedélyezése a bérlő Azure Policy használatával:

- A kezdeményezés társítása hatókörhöz: felügyeleti csoport, előfizetés vagy erőforráscsoport.
- Tekintse át és javítsa ki a megfelelőségi eredményeket.

A Azure Policy hozzárendelésével kapcsolatos további információkért tekintse meg a [Azure Policy áttekintése](../../governance/policy/overview.md#policy-assignment) című témakört, és a folytatás előtt tekintse át a [felügyeleti csoportok áttekintését](../../governance/management-groups/overview.md) .

### <a name="policies-for-azure-vms"></a>Azure-beli virtuális gépek házirendjei

Az Azure-beli virtuális gépek szabályzat-definíciói az alábbi táblázatban láthatók.

|Név |Leírás |Type (Típus) |
|-----|------------|-----|
|\[Preview \]: Azure Monitor for VMs engedélyezése |A megadott hatókörben lévő virtuális gépek Azure Monitor engedélyezése (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi át. |Kezdeményezés |
|\[Preview \]: a függőségi ügynök telepítésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva |A virtuális gépeket nem megfelelőként jelenti, ha a virtuálisgép-rendszerkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: naplózás Log Analytics ügynök üzembe helyezése – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva |A virtuális gépeket nem megfelelőként jelenti, ha a virtuálisgép-rendszerkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: függőségi ügynök telepítése Linux rendszerű virtuális gépekhez |Telepítse a függőségi ügynököt Linux rendszerű virtuális gépekre, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: a Windows rendszerű virtuális gépek függőségi ügynökének telepítése |Windows rendszerű virtuális gépek esetén telepítse a függőségi ügynököt, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken |Telepítse Log Analytics-ügynököt Linux rendszerű virtuális gépekre, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: Log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken |Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure-beli virtuálisgép-méretezési csoportokra vonatkozó szabályzatok

Az Azure virtuálisgép-méretezési csoport szabályzat-definíciói az alábbi táblázatban láthatók.

|Név |Leírás |Type (Típus) |
|-----|------------|-----|
|\[Preview \]: a Azure Monitor engedélyezése a virtuálisgép-méretezési csoportokhoz |Engedélyezze Azure Monitor a virtuálisgép-méretezési csoportokhoz a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport). A Log Analytics munkaterületet paraméterként veszi át. Megjegyzés: Ha a méretezési csoport frissítési szabályzata kézi értékre van állítva, alkalmazza a bővítményt a készletben lévő összes virtuális gépre a frissítés meghívásával. A CLI-ben ez az az vmss Update-instances. |Kezdeményezés |
|\[Preview \]: a függőségi ügynök üzembe helyezése a virtuálisgép-méretezési csoportokban – a virtuális gép rendszerképe (OS) nincs listázva |A virtuálisgép-méretezési csoport nem megfelelőként való jelentése, ha a virtuális gép rendszerképe (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: naplózás Log Analytics ügynök üzembe helyezése virtuálisgép-méretezési csoportokban – a virtuális gép rendszerképe (operációs rendszer) fel van listázva |A virtuálisgép-méretezési csoport nem megfelelőként való jelentése, ha a virtuális gép rendszerképe (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: a függőségi ügynök telepítése Linux rendszerű virtuálisgép-méretezési csoportokhoz |A függőségi ügynök telepítése Linux rendszerű virtuálisgép-méretezési csoportokhoz, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: a Windows rendszerű virtuálisgép-méretezési csoportokhoz tartozó függőségi ügynök telepítése |A függőségi ügynök telepítése Windows rendszerű virtuálisgép-méretezési csoportokhoz, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: a Linux rendszerű virtuálisgép-méretezési csoportokra vonatkozó Log Analytics-ügynök üzembe helyezése |A Linux rendszerű virtuálisgép-méretezési csoportokra vonatkozó Log Analytics-ügynök üzembe helyezése, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Preview \]: Log Analytics ügynök üzembe helyezése Windows rendszerű virtuálisgép-méretezési csoportokhoz |Telepítse a Windows rendszerű virtuálisgép-méretezési csoportokhoz Log Analytics Agent ügynököt, ha a virtuális gép lemezképe (OS) definiálva van a listában, és az ügynök nincs telepítve. |Szabályzat |

Az önálló szabályzatot (a kezdeményezés nem tartalmazza) a következő témakörben találja:

|Név |Leírás |Type (Típus) |
|-----|------------|-----|
|\[Preview \]: a virtuális gép naplózása Log Analytics munkaterületen – a jelentés eltérése |A virtuális gépeket nem megfelelőként jelentse be, ha nem jelentkeznek be a házirend vagy kezdeményezés-hozzárendelésben megadott Log Analytics munkaterületre. |Szabályzat |

### <a name="assign-the-azure-monitor-initiative"></a>Az Azure Monitor kezdeményezés kiosztása

Ha a házirend-hozzárendelést a **Azure monitor for VMS házirend-lefedettségi** lapon szeretné létrehozni, kövesse az alábbi lépéseket. A lépések végrehajtásának megismeréséhez tekintse meg a [szabályzat-hozzárendelés létrehozása a Azure Portalből](../../governance/policy/assign-policy-portal.md)című témakört.

A házirend vagy kezdeményezés hozzárendelésekor a hozzárendelésben kiválasztott hatókör lehet az itt felsorolt hatókör vagy annak egy részhalmaza. Előfordulhat például, hogy létrehozott egy hozzárendelést az előfizetéshez (házirend hatóköre), és nem a felügyeleti csoportot (lefedettségi hatókör). Ebben az esetben a lefedettségi arány azt jelzi, hogy a házirend vagy a kezdeményezés hatókörében lévő virtuális gépek a lefedettségi hatókörben lévő virtuális gépekkel vannak elosztva. Egy másik esetben előfordulhat, hogy kizárta néhány virtuális gépet vagy erőforráscsoportot, vagy előfizetést a szabályzat hatókörében. Ha üres, akkor azt jelzi, hogy a házirend vagy a kezdeményezés nem létezik, vagy Önnek nincs engedélye. Az információk a **hozzárendelés állapota**alatt vannak megadva.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A Azure Portal válassza a **figyelő**elemet. 

3. Az **eredmények szakaszban válassza** az **Virtual Machines (előzetes verzió)** lehetőséget.
 
4. Válassza az **első lépések** fület. A lapon válassza a **házirend-lefedettség kezelése**lehetőséget.

5. Válasszon ki egy felügyeleti csoportot vagy egy előfizetést a táblából. Válassza a **hatókör** elemet a három pontra (...) kattintva. A példában a hatókör korlátozza a házirend-hozzárendelést a virtuális gépek egy csoportjának kényszerítésére.

6. A **Azure Policy-hozzárendelés** lapon a rendszer előre kitölti a kezdeményezés **engedélyezése Azure monitor for VMS**. 
    A **hozzárendelés neve** mezőt a rendszer automatikusan kitölti a kezdeményezés nevével, de módosíthatja. Hozzáadhat egy opcionális leírást is. A **hozzárendelt** mező automatikusan kitöltődik a bejelentkezett felhasználó alapján. Ez az érték nem kötelező.

7. Választható Ha el szeretne távolítani egy vagy több erőforrást a hatókörből, válassza a **kizárások**lehetőséget.

8. A támogatott régió **log Analytics munkaterület** legördülő listájában válasszon ki egy munkaterületet.

   > [!NOTE]
   > Ha a munkaterület a hozzárendelés hatókörén kívül esik, adja meg *log Analytics közreműködői* engedélyeit a szabályzat-HOZZÁRENDELÉS elsődleges azonosítójához. Ha ezt nem teszi meg, előfordulhat, hogy a központi telepítési hiba például a `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` a hozzáférés megadására, [a felügyelt identitás manuális konfigurálását](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)ismertető témakörben.
   > 
   >  A **felügyelt identitás** jelölőnégyzet be van jelölve, mert a hozzárendelt kezdeményezés tartalmaz egy házirendet a *deployIfNotExists* hatásával.
    
9. Az **identitás helye** legördülő listában válassza ki a megfelelő régiót.

10. Válassza a **Hozzárendelés** elemet.

A hozzárendelés létrehozása után a **Azure monitor for VMS házirend-lefedettségi** oldal frissíti a **hozzárendelési lefedettséget**, a **hozzárendelési állapotot**, a **megfelelő virtuális gépeket**és a **megfelelőségi állapotot** , hogy tükrözze a módosításokat. 

A következő mátrix a kezdeményezés minden lehetséges megfelelőségi állapotát leképezi.  

| Megfelelőségi állapot | Leírás | 
|------------------|-------------|
| **Megfelelő** | A hatókörben lévő összes virtuális gép rendelkezik a rájuk telepített Log Analytics és függőségi ügynökökkel.|
| **Nem megfelelő** | A hatókörben lévő összes virtuális gép nem rendelkezik központilag telepített Log Analytics és függőségi ügynökökkel, ezért szervizelésre lehet szükség.|
| **Nincs elindítva** | Új hozzárendelés lett hozzáadva. |
| **Lock** | Nincs megfelelő jogosultsága a felügyeleti csoportnak. <sup>1</sup> | 
| **Üres** | Nincs hozzárendelve szabályzat. | 

<sup>1</sup> ha nem rendelkezik hozzáféréssel a felügyeleti csoporthoz, kérje meg a tulajdonost, hogy adjon hozzáférést. Vagy megtekintheti a megfelelőséget, és kezelheti a hozzárendeléseket a alárendelt felügyeleti csoportokon vagy előfizetéseken keresztül. 

Az alábbi táblázat a kezdeményezés minden lehetséges hozzárendelési állapotát leképezi.

| Hozzárendelés állapota | Leírás | 
|------------------|-------------|
| **Sikeres** | A hatókörben lévő összes virtuális gép rendelkezik a rájuk telepített Log Analytics és függőségi ügynökökkel.|
| **Figyelmeztetés** | Az előfizetés nem felügyeleti csoportban van.|
| **Nincs elindítva** | Új hozzárendelés lett hozzáadva. |
| **Lock** | Nincs megfelelő jogosultsága a felügyeleti csoportnak. <sup>1</sup> | 
| **Üres** | Nem találhatók virtuális gépek, vagy nincs hozzárendelve szabályzat. | 
| **Művelet** | Rendeljen hozzá egy szabályzatot, vagy szerkesszen egy hozzárendelést. | 

<sup>1</sup> ha nem rendelkezik hozzáféréssel a felügyeleti csoporthoz, kérje meg a tulajdonost, hogy adjon hozzáférést. Vagy megtekintheti a megfelelőséget, és kezelheti a hozzárendeléseket a alárendelt felügyeleti csoportokon vagy előfizetéseken keresztül.

## <a name="review-and-remediate-the-compliance-results"></a>A megfelelőségi eredmények áttekintése és szervizelése

Az alábbi példa egy Azure-beli virtuális gép, de a virtuálisgép-méretezési csoportokra is vonatkozik. A megfelelőségi eredmények áttekintését a nem [megfelelőségi eredmények azonosítása](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)című témakörben tekintheti meg. A **Azure monitor for VMS házirend-lefedettség** lapon válasszon ki egy felügyeleti csoportot vagy egy előfizetést a táblából. A három pont (...) gombra kattintva válassza a **megfelelőség megtekintése** lehetőséget.   

![Azure-beli virtuális gépek szabályzatának megfelelősége](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

A kezdeményezésben foglalt szabályzatok eredményei alapján a virtuális gépek a következő helyzetekben nem megfelelőként lesznek jelezve:

* Log Analytics ügynök vagy függőségi ügynök nincs telepítve.  
    Ez a forgatókönyv a meglévő virtuális gépekkel való hatókörre jellemző. Ennek enyhítéséhez telepítse a szükséges ügynököket a nem megfelelő szabályzatra vonatkozó [szervizelési feladatok létrehozásával](../../governance/policy/how-to/remediate-resources.md) .  
    - \[Preview \]: függőségi ügynök telepítése Linux rendszerű virtuális gépekhez
    - \[Preview \]: a Windows rendszerű virtuális gépek függőségi ügynökének telepítése
    - \[Preview \]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
    - \[Preview \]: Log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken

* A virtuálisgép-rendszerkép (operációs rendszer) nincs azonosítva a házirend-definícióban.  
    A központi telepítési házirend feltételei csak a jól ismert Azure VM-rendszerképekből üzembe helyezett virtuális gépekre vonatkoznak. A dokumentációban megtekintheti, hogy támogatott-e a virtuális gép operációs rendszere. Ha ez nem támogatott, duplikálja a központi telepítési szabályzatot, és frissítse vagy módosítsa, hogy a rendszerkép megfelelő legyen.  
    - \[Preview \]: a függőségi ügynök telepítésének naplózása – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva
    - \[Preview \]: naplózás Log Analytics ügynök üzembe helyezése – a virtuálisgép-rendszerkép (operációs rendszer) nincs listázva

* A virtuális gépek nem jelentkeznek be a megadott Log Analytics munkaterületre.  
    Lehetséges, hogy a kezdeményezési hatókörben lévő egyes virtuális gépek bejelentkeznek a házirend-hozzárendelésben megadott Log Analytics-munkaterületre. Ez a szabályzat egy olyan eszköz, amely azonosítja, hogy mely virtuális gépek jelentik a nem megfelelő munkaterületeket.  
    - \[Preview \]: a virtuális gép naplózása Log Analytics munkaterületen – a jelentés eltérése

## <a name="edit-an-initiative-assignment"></a>Kezdeményezési hozzárendelés szerkesztése

A felügyeleti csoportokhoz vagy előfizetésekhez való hozzárendelést követően bármikor módosíthatja a következő tulajdonságokat:

- Hozzárendelés neve
- Leírás
- Hozzárendelte
- Log Analytics munkaterület
- Kivételek

## <a name="next-steps"></a>Következő lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el. 

- A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md). 

- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:. 
