---
title: Az Azure Monitor engedélyezése a virtuális gépek az Azure policyval |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan engedélyezi az Azure Monitor-beli virtuális gépek több Azure-beli virtuális gépek vagy a virtuális gép méretezési csoportok az Azure Policy használatával.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524145"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Az Azure Monitor engedélyezése a virtuális gépek (előzetes verzió) az Azure policyval

Ez a cikk ismerteti az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek Azure-beli virtuális gépek vagy a virtuális gép méretezési csoportok az Azure Policy használatával. Ez a folyamat végén lesz sikeresen konfigurálva a Log Analytics és a függőségi ügynökök engedélyezése, és azonosítja a virtuális gépek, amelyek nem felelnek.

Fedezze fel, kezelése és engedélyezése az Azure Monitor az összes az Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoportok virtuális gépek számára, használhatja, vagy az Azure Policy, vagy az Azure PowerShell-lel. Az Azure Policy az ajánlott módszer azért, mert a szabályzatdefiníciók hatékonyan szabályozza a konzisztens a megfelelőség biztosítása az előfizetések kezelheti és automatikus engedélyezése az újonnan üzembe helyezett virtuális gépek. A szabályzatdefiníciók:

* A Log Analytics-ügynököket és a függőségi ügynök üzembe helyezése.
* Megfelelési eredmények jelentést.
* Javítsa a nem kompatibilis virtuális gépek.

Ha Ön erre az Azure PowerShell vagy az Azure Resource Manager-sablonnal érdekli, lásd: [engedélyezése az Azure PowerShell vagy a Resource Manager-sablon használatával](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Kezelheti a szabályzati lefedettség funkcióinak áttekintése

Az Azure Policyvel kezelésére és üzembe helyezése a virtuális gépek az Azure Monitor szabályzatdefiníciók tapasztalatok eredetileg kizárólag az Azure Policy hajtottak végre. Az a **kezelése szabályzati lefedettség** funkció lehetővé teszi, hogy egyszerűbb és könnyebben megtalálhatóvá, a kezelése és a méretezett engedélyezése a **engedélyezése az Azure Monitor-beli virtuális gépek** kezdeményezés, amely tartalmazza a szabályzatdefiníciók a korábban említett. Ez az új funkció eléréséhez a **Ismerkedés** kiválasztásával az Azure Monitor-beli virtuális gépek lapján **kezelése szabályzati lefedettség**. A virtuális gépek szabályzati lefedettség lap nyílik meg. 

![Az Azure Monitor a virtuális gépek első lépések lap](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Itt, is ellenőrizze és lefedettségét a kezdeményezés kezelése a felügyeleti csoportok és az előfizetések között, valamint megismerheti, hogy hány virtuális gépet minden egyes felügyeleti csoportok és az előfizetések és megfelelőségi állapotuk szerepel.   

![A virtuális gépek kezelése szabályzat lapján az Azure Monitor](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Ez az információ hasznos tervezheti meg és hajtsa végre a cégirányítási forgatókönyv egyetlen központi helyről-beli virtuális gépek az Azure Monitor. Azure Policy lehetővé teszi a megfelelőségi szabályzatot vagy kezdeményezést egy hatókörhöz rendelt, amíg az új lap, is Fedezze fel, a házirend kezdeményezés nincs hozzárendelve, és rendelje hozzá a helyben. Minden művelet (hozzárendelése, megtekintése, szerkesztése) közvetlenül az Azure Policy átirányítása. A virtuális gépek szabályzati lefedettség oldal az Azure Monitor egy kibontott és integrált környezetet, csak a kezdeményezés **engedélyezése az Azure Monitor-beli virtuális gépek**. 

Ezen a lapon beállíthatja úgy is a Log Analytics-munkaterület az Azure Monitor-beli virtuális gépek, amely végrehajtja a következőket:

- A Szolgáltatástérkép telepítése és az Infrastructure Insights megoldás telepítése
- Lehetővé teszi az operációs rendszer teljesítményszámlálók a teljesítménydiagramok, munkafüzetek, és az egyéni napló lekérdezések és a riasztások használják.

![A virtuális gépek az Azure Monitor munkaterület konfigurálása](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ez a beállítás házirend műveletek nem kapcsolódik, és készen áll a kielégítéséhez egyszerű módot biztosítanak a [Előfeltételek](vminsights-enable-overview.md) szükséges virtuális gépek az Azure Monitor engedélyezéséhez szükséges.  

### <a name="what-information-is-available-on-this-page"></a>Milyen információ érhető el ezen az oldalon?
A következő táblázat nyújt információkat a milyen információk jelennek meg a szabályzat lefedettség lap, és hogyan kell értelmezni.

| Függvény | Leírás | 
|----------|-------------| 
| **Hatókör** | A felügyeleti csoport és az előfizetések közül vagy az örökölt abban, hogy a felügyeleti részletezési biztonságicsoport-hierarchiával való hozzáférést.|
| **Szerepkör** | A következő hatókörre, a szerepkör lehet olvasó tulajdonos vagy közreműködő. Bizonyos esetekben, előfordulhat, hogy üresen jelenik meg, annak jelzésére, hogy előfordulhat, hogy hozzáférést előfizetéshez, de nem a felügyeleti csoporthoz tartozik. Információk az egyéb oszlopok milyen látható adatok és -kezdeményezéshez és házirend (tulajdonos) hozzárendelése, szerkeszti őket, vagy tekintse meg a megfelelőség szempontjából elvégezhető műveleteket meghatározásakor kulcsot, mert a szerepkör függően változhat. |
| **Virtuális gépek összesen** | Virtuális gépek száma, hogy a hatókör alatt. Egy felügyeleti csoporthoz tartozó, egy összeg, az előfizetéseket és/vagy az alárendelt felügyeleti csoportot a beágyazott virtuális gépek. |
| **Hozzárendelés lefedettség** | %-Os virtuális gépek kezdeményezés/szabályzat alá esnek. |
| **Hozzárendelés állapota** | Ez az oszlop alatt talál információkat állapotát a szabályzat / kezdeményezési hozzárendelés. |
| **Kompatibilis virtuális gépek** | A szabályzat/kezdeményezés megfelelő virtuális gépek száma. A kezdeményezés **engedélyezése az Azure Monitor-beli virtuális gépek** a Log Analytics-ügynököket és a függőségi ügynök rendelkező virtuális gépek száma. Bizonyos esetekben, előfordulhat, hogy üresen jelenik meg, vagy nincs hozzárendelés, vagy nincsenek olyan virtuális gépek, vagy nincs megfelelő jogosultsága miatt. Információ a megfelelőségi állapotot. |
| **Megfelelőség** | Az általános megfelelőségi érték egyezik meg a különböző erőforrások, amelyek megfelelő osztva az összes különböző erőforrás összege. |
| **Megfelelőségi állapot** | A szabályzat megfelelőségi állapotát az információk / kezdeményezési hozzárendelés.|

A kezdeményezés szabályzat hozzárendelése után a kiválasztott a hozzárendelési hatókör a felsorolt hatókörben vagy egy része lehet. Például előfordulhat, hogy létrehozott egy hozzárendelés előfizetést (szabályzat hatóköre) és nem a felügyeleti csoport (lefedettség hatókör). Ebben az esetben értékét **hozzárendelés lefedettség** utalna házirend (vagy a kezdeményezés) virtuális gépek hatókör elosztja a virtuális gépek lefedettség hatókörében. Egy másik esetben előfordulhat, hogy ki lett zárva néhány virtuális gépen, erőforráscsoport vagy előfizetés a szabályzat hatóköre. Ha az érték üres, azt jelzi, hogy a szabályzat kezdeményezés nem létezik vagy nincs engedélye (információkat hozzárendelés állapota).

## <a name="enable-using-azure-policy"></a>Engedélyezés az Azure Policyval

Az Azure Monitor engedélyezése a virtuális gépek a bérlő Azure Policy használatával:

- A kezdeményezés hozzárendelése egy hatókör: felügyeleti csoport, előfizetéshez vagy erőforráscsoporthoz
- Tekintse át és szervizelése megfelelőségi eredményeit

Azure Policy hozzárendelésével kapcsolatos további információkért lásd: [Azure Policy – áttekintés](../../governance/policy/overview.md#policy-assignment) , és tekintse át a [felügyeleti csoportok áttekintése](../../governance/management-groups/index.md) a folytatás előtt.

### <a name="policies-for-azure-vms"></a>Az Azure-beli virtuális gépek házirendek

A szabályzatdefiníciók egy Azure virtuális gép az alábbi táblázatban láthatók:

|Name (Név) |Leírás |Típus |
|-----|------------|-----|
|[Előzetes verzió]: Az Azure Monitor-beli virtuális gépek engedélyezése |Az Azure Monitor engedélyezése a virtuális gépek (VM) a megadott hatókör (felügyeleti csoport, előfizetéshez vagy erőforráscsoportot). Log Analytics-munkaterületet használja paraméterként. |Kezdeményezés |
|[Előzetes verzió]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása |Jelentések virtuális gépek nem megfelelő, ha a virtuális gép rendszerkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Log Analytics-ügynök telepítése – virtuális gép rendszerkép (OS) listán nem szereplő naplózása |Jelentések virtuális gépek nem megfelelő, ha a virtuális gép rendszerkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Függőségi ügynök Linux rendszerű virtuális gépek üzembe helyezése |Függőségi ügynök telepítése Linux rendszerű virtuális gépekhez a virtuális gép (OS) rendszerkép definiálása a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Függőségi ügynök Windows-beli virtuális gépek üzembe helyezése |Ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve, telepítse a függőségi ügynököt Windows virtuális. |Házirend |
|[Előzetes verzió]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése |Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépekhez a virtuális gép (OS) rendszerkép definiálása a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése |Log Analytics ügynök Windows virtuális gépek üzembe helyezése, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Házirend |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Házirendek az Azure-beli virtuálisgép-méretezési csoportokban

A szabályzatdefiníciók számára egy Azure-beli virtuálisgép-méretezési csoportot az alábbi táblázatban láthatók:

|Name (Név) |Leírás |Típus |
|-----|------------|-----|
|[Előzetes verzió]: A Virtuálisgép-méretezési csoportok (VMSS) az Azure Monitor engedélyezése |Az Azure Monitor engedélyezése a virtuális gép méretezési csoportok a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoportot). Log Analytics-munkaterület szükséges paraméterként. Megjegyzés: a méretezési csoport upgradePolicy Manual értékre van állítva, ha szeretné a bővítmény alkalmazása a készletben lévő összes virtuális gép frissítése meghívásával rajtuk. CLI-ben ez lenne az vmss update-instances. |Kezdeményezés |
|[Előzetes verzió]: Függőségi ügynök telepítése a VMSS – virtuális gép rendszerkép (OS) listán nem szereplő naplózása |Jelentések virtuálisgép-méretezési csoport beállítása nem megfelelő, ha a virtuális gép rendszerkép (OS) nincs definiálva a listáról, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Log Analytics-ügynök telepítése a VMSS – virtuális gép rendszerkép (OS) listán nem szereplő naplózása |Jelentések virtuálisgép-méretezési csoport beállítása nem megfelelő, ha a virtuális gép rendszerkép (OS) nincs definiálva a listáról, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Függőségi ügynök Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése |Függőségi ügynök telepítése Linux virtuálisgép-méretezési csoport állítja be, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Függőségi ügynök a Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése |Telepítse a függőségi ügynök a Windows virtuálisgép-méretezési csoport állítja be, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése |Log Analytics-ügynök telepítése Linux virtuálisgép-méretezési csoport állítja be, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Házirend |
|[Előzetes verzió]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése |Helyezze üzembe a Log Analytics az ügynök a Windows virtuálisgép-méretezési csoport állítja be, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Házirend |

(Nem tartalmazza a kezdeményezés-) önálló házirend az alábbiakban ismertetjük:

|Name (Név) |Leírás |Típus |
|-----|------------|-----|
|[Előzetes verzió]: Naplózási Log Analytics-munkaterület VM - jelentés eltérés |Virtuális gépek jelentést nem megfelelő, ha azok nem naplózását a házirend/kezdeményezés-hozzárendelést a megadott Log Analytics-munkaterületen. |Szabályzat |

### <a name="assign-the-azure-monitor-initiative"></a>Az Azure Monitor kezdeményezés hozzárendelése
A virtuális gépek szabályzati lefedettség oldal az Azure Monitor a szabályzat-hozzárendelés létrehozásához hajtsa végre az alábbi lépéseket. Megtudhatja, hogyan hajtsa végre ezeket a lépéseket, tekintse meg [szabályzat-hozzárendelés létrehozása az Azure Portalról](../../governance/policy/assign-policy-portal.md).

A kezdeményezés szabályzat hozzárendelése után a kiválasztott a hozzárendelési hatókör lehet az itt felsorolt hatókör és a egy részét. Például előfordulhat, hogy hozott létre (a szabályzat hatóköre) előfizetés-hozzárendelés és nem a felügyeleti csoport (lefedettség hatókör), amelyben a megkülönbözteti a kis lefedettség % azt jelentené házirend (vagy a kezdeményezés) virtuális gépek hatókör elosztja a virtuális gépek lefedettség hatókörében. Egyes esetekben előfordulhat, hogy ki lett zárva néhány virtuális gépeket vagy RGs vagy előfizetést a szabályzat hatóköre.  Abban az esetben, ha üres, akkor a rendszer azt jelzi, hogy a szabályzat mindkét / kezdeményezés nem létezik, vagy nem rendelkezik engedélyekkel (hozzárendelés állapotának megadott információ)  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure Portalon válassza ki a **figyelő**. 

3. Válasszon **(előzetes verzió) virtuális gépek** a a **Insights** szakaszban.
 
4. Válassza ki a **Ismerkedés** lapra, és válassza ki a lapon **kezelése szabályzati lefedettség**.

5. Válassza ki egy felügyeleti csoport vagy az előfizetés a táblázatból, és válassza ki **hatókör** a három pontra (...) kattintva.      Ebben a példában egy hatókör korlátozza a szabályzat-hozzárendelést a csoportosított kényszerítésre kijelölt virtuális gépek.

6. Az a **Azure szabályzat-hozzárendelés** oldal, előre fel van töltve a kezdeményezés **engedélyezése az Azure Monitor-beli virtuális gépek**. 
    A **hozzárendelés neve** mezőt automatikusan kitölti a kezdeményezési nevű, de ezt módosíthatja. Az opcionális leírást is hozzáadhat. A **által hozzárendelt** mezőt automatikusan kitölti éppen bejelentkezett alapján, és ezt az értéket nem kötelező.

7. (Nem kötelező) A hatókör egy vagy több erőforrás eltávolításához jelölje ki **kizárások**.

8. Az a **Log Analytics-munkaterület** legördülő listában a támogatott régió, válassza ki egy munkaterületet.

   > [!NOTE]
   > Ha a munkaterület a hozzárendelés hatókörén kívül esik, biztosítson *Log Analytics-közreműködő* engedélyeket a szabályzat-hozzárendelés egyszerű azonosító. Ha nem ezt teszi, mint például láthatja az üzembe helyezési hibák: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Tekintse át a hozzáférést, [hogyan konfigurálhatja manuálisan a felügyelt identitás](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  A **felügyelt identitás** jelölőnégyzet be van jelölve, a kezdeményezés érvényessége a felhasználóhoz egy házirend tartalmazza a *deployIfNotExists* érvénybe.
    
9. Az a **identitás kezelése hely** legördülő listára, válassza ki a megfelelő régiót.

10. Válassza a **Hozzárendelés** elemet.

Miután létrehozta a hozzárendelését, a virtuális gépek szabályzati lefedettség oldal az Azure Monitor frissíti, hozzárendelés lefedettség, hozzárendelés állapota, megfelelőségi virtuális gépek és megfelelőségi állapota az elvégzett módosításoknak megfelelően. 

A következő mátrix leképezi a kezdeményezés minden lehetséges megfelelőségi állapota.  

| Megfelelőségi állapot | Leírás | 
|------------------|-------------|
| **Megfelelő** | A hatókörben lévő összes virtuális gép rendelkezik a Log Analytics és a függőségi ügynököt rájuk.|
| **Nem megfelelő** | A hatókör nem minden virtuális gép rendelkezik a Log Analytics és a függőségi ügynökök ellenőrzésére és szervizelési lehet szükség.|
| **Nem indult el** | Új hozzárendelés lett hozzáadva. |
| **zárolás** | Nem rendelkezik megfelelő jogosultságokkal a felügyeleti csoporthoz. <sup>1</sup> | 
| **Üres** | Nincs szabályzat hozzárendelve. | 

<sup>1</sup> Ha nincs hozzáférése a felügyeleti csoporthoz, kérje meg a hozzáférést biztosíthat, vagy megtekintheti a megfelelőségi és-hozzárendelések kezeléséhez az alárendelt felügyeleti csoportok vagy előfizetések tulajdonosa. 

Az alábbi táblázatban minden lehetséges hozzárendelés állapota a kezdeményezés képezi le.

| Hozzárendelés állapota | Leírás | 
|------------------|-------------|
| **Sikeres** | A hatókörben lévő összes virtuális gép rendelkezik a Log Analytics és a függőségi ügynököt rájuk.|
| **Figyelmeztetés** | Az előfizetéshez nem tartozik egy felügyeleti csoporthoz.|
| **Nem indult el** | Új hozzárendelés lett hozzáadva. |
| **zárolás** | Nem rendelkezik megfelelő jogosultságokkal a felügyeleti csoporthoz. <sup>1</sup> | 
| **Üres** | Nincs virtuális gép létezik, vagy a szabályzat nincs hozzárendelve. | 
| **Művelet** | Szabályzat hozzárendelése és Edit-hozzárendelés | 

<sup>1</sup> Ha nincs hozzáférése a felügyeleti csoporthoz, kérje meg a hozzáférést biztosíthat, vagy megtekintheti a megfelelőségi és-hozzárendelések kezeléséhez az alárendelt felügyeleti csoportok vagy előfizetések tulajdonosa. 

## <a name="review-and-remediate-the-compliance-results"></a>Tekintse át és szervizelése megfelelőségi eredményeit

Az alábbi példa egy Azure virtuális gép van, de a virtual machine scale sets is vonatkozik. Megismerheti a megfelelőségi eredmények áttekintéséhez olvassa el [azonosíthatja a meg nem felelés eredmények](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). A virtuális gépek szabályzati lefedettség oldal az Azure monitor, egy felügyeleti csoport vagy az előfizetés a táblázatból válassza ki és **nézet megfelelőségi** a három pontra (...) kattintva.   

![Szabályzatoknak való megfelelés, Azure-beli virtuális gépek](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

A kezdeményezés található házirendek eredményei alapján a virtuális gépek jelentett nem megfelelő a következő esetekben:

* A log Analytics vagy a függőségi ügynök nincs telepítve.  
    Ebben a forgatókönyvben jellemző, a meglévő virtuális gépek hatókör. Csökkentése érdekében, hogy, üzembe helyezése a szükséges ügynökök által [szervizelési feladatokat hozhat létre](../../governance/policy/how-to/remediate-resources.md) lévő nem megfelelő házirendhez.  
    - [Előzetes verzió]: Deploy Dependency Agent for Linux VMs
    - [Előzetes verzió]: Deploy Dependency Agent for Windows VMs
    - [Előzetes verzió]: Deploy Log Analytics Agent for Linux VMs
    - [Előzetes verzió]: Deploy Log Analytics Agent for Windows VMs

* VM-lemezkép (OS) nem azonosítja a szabályzat-definícióban.  
    A feltételek a központi telepítésre vonatkozó házirendet, például csak jól ismert Azure Virtuálisgép-rendszerképekből telepített virtuális gépekkel. Ellenőrizze a dokumentációban, megtekintéséhez, hogy támogatott-e a virtuális gép operációs rendszerének. Nem támogatott, ha a központi telepítésre vonatkozó házirendet és a frissítés ismétlődő, vagy módosítsa azt, hogy a rendszerkép megfelelő legyen.  
    - [Előzetes verzió]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
    - [Előzetes verzió]: Log Analytics-ügynök telepítése – virtuális gép rendszerkép (OS) listán nem szereplő naplózása

* Virtuális gépek nem jelentkezik be a megadott Log Analytics-munkaterületet.  
    Akkor lehet, hogy néhány virtuális gépet a kezdeményezési hatókörében jelentkeznek be a másik a szabályzat-hozzárendelés megadott Log Analytics-munkaterületet. Ez a szabályzat egy olyan eszköz azonosításához, amely a virtuális gépek egy nem megfelelő munkaterületnek jelentenek.  
    - [Előzetes verzió]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Kezdeményezés-hozzárendelés szerkesztése

Kezdeményezés hozzárendelése a felügyeleti csoportba vagy előfizetésbe után bármikor módosítsa a következő tulajdonságait módosíthatja:

- Hozzárendelés neve
- Leírás
- Hozzárendelte
- Log Analytics-munkaterület
- Kivételek

## <a name="next-steps"></a>További lépések

Most, hogy a virtuális gépek számára engedélyezve van a figyelés és az Azure Monitor-beli virtuális gépek elemzési érhető el ezt az információt. Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). Azonosíthatja a szűk keresztmetszeteket és a virtuális gépek teljesítményét a teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).