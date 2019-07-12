---
title: Az Azure Monitor engedélyezése a virtuális gépek az Azure Policy használatával |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan engedélyezi az Azure Monitor-beli virtuális gépek több Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoportok Azure Policy használatával.
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
ms.openlocfilehash: cf06004c70609dbea59a47b207e3568299260a82
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594451"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Azure Monitor engedélyezése a virtuális gépek (előzetes verzió) az Azure Policy használatával

Ez a cikk ismerteti az Azure Monitor engedélyezése (előzetes verzió) virtuális gépek Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoportok Azure Policy használatával. Ez a folyamat végén lesz sikeresen konfigurálva a Log Analytics és a függőségi ügynökök engedélyezése és nem megfelelő virtuális gépeket azonosított.

Fedezze fel, kezelése és engedélyezése az Azure Monitor az összes az Azure-beli virtuális gépek vagy virtuálisgép-méretezési csoportok virtuális gépek számára, használhatja, vagy az Azure Policy, vagy az Azure PowerShell-lel. Az Azure Policy a lehetőség ajánlott, mert kezelheti hatékonyan szabályozásához az előfizetések konzisztens a megfelelőség biztosítása a szabályzatdefiníciók és automatikus engedélyezése az újonnan üzembe helyezett virtuális gépek. A szabályzatdefiníciók:

* A Log Analytics-ügynököket és a függőségi ügynök üzembe helyezése.
* Megfelelési eredmények jelentést.
* Javítsa a nem kompatibilis virtuális gépek.

Ha érdekli az Azure PowerShell vagy az Azure Resource Manager-sablon feladatok végrehajtásának, [engedélyezése az Azure Monitor-beli virtuális gépek (előzetes verzió) az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Kezelheti a szabályzati lefedettség funkcióinak áttekintése

Az Azure Policyvel kezelésére és üzembe helyezése a virtuális gépek az Azure Monitor szabályzatdefiníciók tapasztalatok eredetileg, kizárólag az Azure Policy azért volt szükség. A szabályzati lefedettség kezelése funkciónak köszönhetően egyszerűbb és könnyebben megtalálhatóvá, a kezelése és a méretezett engedélyezése a **engedélyezése az Azure Monitor-beli virtuális gépek** kezdeményezés, amely tartalmazza a korábban említett szabályzatdefiníciók. Ez az új funkció eléréséhez a **Ismerkedés** Azure Monitor-beli virtuális gépek lapján. Válassza ki **kezelése szabályzati lefedettség** megnyitásához a **szabályzati lefedettség virtuális gépek az Azure Monitor** lapot.

![Az Azure Monitor a virtuális gépek első lépések lap](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Itt ellenőrizze, és a kezdeményezés lefedettség kezelése a felügyeleti csoportok és az előfizetések között. Megismerheti, hogy hány virtuális gépet minden egyes felügyeleti csoportok és az előfizetések és megfelelőségi állapotuk szerepel.

![A virtuális gépek kezelése szabályzat lapján az Azure Monitor](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Ez az információ hasznos megtervezése, és hajtsa végre a cégirányítási forgatókönyv egyetlen központi helyről-beli virtuális gépek az Azure Monitor segítséget. Azure Policy megfelelőségi nézetet nyújt, ha egy szabályzatot vagy kezdeményezést egy hatókörhöz van rendelve, amíg az új lap, is Fedezze fel, a szabályzatot vagy kezdeményezést nincs hozzárendelve, és rendelje hozzá a helyen. Minden művelet, például hozzárendelése, megtekintése és szerkesztése közvetlenül az Azure Policy átirányítási. A **szabályzati lefedettség virtuális gépek az Azure Monitor** lap egy kibontott és integrált környezetet, csak a kezdeményezés **engedélyezése az Azure Monitor-beli virtuális gépek**.

Ezen az oldalon is konfigurálható a Log Analytics-munkaterületet az Azure Monitor-beli virtuális gépek, amelyek:

- Telepíti a Service Map telepítése és az Infrastructure Insights megoldásokat.
- Lehetővé teszi az operációs rendszer teljesítményszámlálók a teljesítménydiagramok, munkafüzetek, és az egyéni napló lekérdezések és a riasztások használják.

![A virtuális gépek az Azure Monitor munkaterület konfigurálása](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ez a beállítás nem kapcsolódik a házirend műveletek. Elérhető kielégítéséhez egyszerű módot biztosítanak a [Előfeltételek](vminsights-enable-overview.md) szükséges virtuális gépek az Azure Monitor engedélyezéséhez szükséges.  

### <a name="what-information-is-available-on-this-page"></a>Milyen információ érhető el ezen az oldalon?
Az alábbi táblázat bontásban megjelenő információk a szabályzat lefedettség és hogyan kell értelmezni.

| Függvény | Leírás | 
|----------|-------------| 
| **Hatókör** | A felügyeleti csoport és az előfizetések közül vagy az örökölt a hozzáférést a felügyeleti csoport hierarchián keresztül részletezés segítségével.|
| **Szerepkör** | A szerepkör a következő hatókörre, amely lehet olvasó, a tulajdonos vagy közreműködő. Néhány esetben úgy tűnhet, üres jelzi, hogy előfordulhat, hogy hozzáférést az előfizetéshez, de nem a felügyeleti csoporthoz tartozik. Információk az egyéb oszlopok szerepkörétől függően változik. A szerepkör az kulcs milyen adatokat láthatja, és a házirendek vagy a kezdeményezések (tulajdonos) hozzárendelése, szerkeszti őket, vagy tekintse meg a megfelelőség szempontjából elvégezhető műveleteket meghatározásakor. |
| **Virtuális gépek összesen** | Virtuális gépek száma, hogy a hatókör alatt. Egy felügyeleti csoporthoz tartozó, egy összeg az előfizetések vagy az alárendelt felügyeleti csoportot a beágyazott virtuális gépek. |
| **Hozzárendelés lefedettség** | %-Os virtuális gépek, a szabályzatot vagy kezdeményezést alá esnek. |
| **Hozzárendelés állapota** | Információk a házirendet és a kezdeményezés-hozzárendelés állapota. |
| **Kompatibilis virtuális gépek** | A szabályzatot vagy kezdeményezést a megfelelő virtuális gépek száma. A kezdeményezés **engedélyezése az Azure Monitor-beli virtuális gépek**, mind a Log Analytics-ügynököket, és a függőségi ügynök rendelkező virtuális gépek száma. Néhány esetben úgy tűnhet, üres miatt nincs hozzárendelés, nincsenek olyan virtuális gépek vagy nincs megfelelő jogosultsága. Információ alapján **megfelelőségi állapot**. |
| **Megfelelőség** | Az általános megfelelőségi érték egyezik meg a különböző erőforrások, amelyek megfelelő osztva az összes különböző erőforrás összege. |
| **Megfelelőségi állapot** | A megfelelőségi állapot a szabályzatot vagy kezdeményezést-hozzárendelésre vonatkozó információk.|

A szabályzat vagy a kezdeményezés hozzárendelésekor a kiválasztott a hozzárendelési hatókör lehet a felsorolt hatókörben vagy egy részét. Például előfordulhat, hogy létrehozott egy hozzárendelés előfizetést (szabályzat hatóköre) és nem a felügyeleti csoport (lefedettség hatókör). Ebben az esetben értékét **hozzárendelés lefedettség** azt jelzi, hogy a virtuális gépek, a házirend vagy a kezdeményezési hatókör elosztja a virtuális gépek lefedettség hatókörében. Egy másik esetben előfordulhat, hogy ki lett zárva néhány virtuális gépen, erőforráscsoport vagy előfizetés a szabályzat hatóköre. Ha az érték üres, az azt jelzi, hogy a szabályzatot vagy kezdeményezést nem létezik, vagy nem rendelkezik engedéllyel. Információ alapján **hozzárendelés állapota**.

## <a name="enable-by-using-azure-policy"></a>Az Azure Policy segítségével

Az Azure Monitor engedélyezése a virtuális gépek a bérlő Azure Policy használatával:

- A kezdeményezés hozzárendelése egy hatókör: felügyeleti csoport, előfizetéshez vagy erőforráscsoporthoz.
- Tekintse át és szervizelése megfelelőségi eredményeket.

Azure Policy hozzárendelésével kapcsolatos további információkért lásd: [Azure Policy – áttekintés](../../governance/policy/overview.md#policy-assignment) , és tekintse át a [felügyeleti csoportok áttekintése](../../governance/management-groups/index.md) a folytatás előtt.

### <a name="policies-for-azure-vms"></a>Az Azure-beli virtuális gépek házirendek

A szabályzatdefiníciók egy Azure virtuális gép az alábbi táblázatban láthatók.

|Name (Név) |Leírás |Type |
|-----|------------|-----|
|\[Előzetes verzió\]: Az Azure Monitor-beli virtuális gépek engedélyezése |Az Azure Monitor engedélyezése a virtuális gépek (felügyeleti csoport, előfizetéshez vagy erőforráscsoporthoz) a megadott hatókörben. Log Analytics-munkaterületet használja paraméterként. |Kezdeményezés |
|\[Előzetes verzió\]: Függőségi ügynök telepítése – Virtuálisgép-rendszerképet (OS) listán nem szereplő naplózása |Jelentések virtuális gépek akkor nem megfelelő, ha a Virtuálisgép-lemezkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Előzetes verzió\]: A Log Analytics-ügynök telepítése – Virtuálisgép-rendszerképet (OS) listán nem szereplő naplózása |Jelentések virtuális gépek akkor nem megfelelő, ha a Virtuálisgép-lemezkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Előzetes verzió\]: Függőségi ügynök Linux rendszerű virtuális gépek üzembe helyezése |Ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve, telepítse a függőségi ügynök Linux rendszerű virtuális gépekhez. |Szabályzat |
|\[Előzetes verzió\]: Függőségi ügynök Windows-beli virtuális gépek üzembe helyezése |Függőségi ügynök Windows-beli virtuális gépek üzembe helyezése, ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Előzetes verzió\]: Linux rendszerű virtuális gépekhez a Log Analytics-ügynök telepítése |Ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve, üzembe helyezése Linux rendszerű virtuális gépekhez a Log Analytics-ügynököket. |Szabályzat |
|\[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése |Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése, ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Szabályzat |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Házirendek az Azure-beli virtuálisgép-méretezési csoportokban

A szabályzatdefiníciók számára egy Azure-beli virtuálisgép-méretezési csoportot az alábbi táblázatban láthatók.

|Name (Név) |Leírás |Type |
|-----|------------|-----|
|\[Előzetes verzió\]: Virtuálisgép-méretezési csoportokhoz az Azure Monitor engedélyezése |Az Azure Monitor engedélyezése a virtuális gép méretezési csoportjai (felügyeleti csoport, előfizetéshez vagy erőforráscsoporthoz) a megadott hatókörben. Log Analytics-munkaterületet használja paraméterként. Megjegyzés: Ha a méretezési csoport házirend értéke Manual, alkalmazni a bővítményt a készletben lévő összes virtuális gép frissítés meghívásával rajtuk. A parancssori felületen Ez az az vmss update-instances. |Kezdeményezés |
|\[Előzetes verzió\]: Függőségi ügynök telepítése a virtuális gép méretezési csoportjai – Virtuálisgép-rendszerképet (OS) listán nem szereplő naplózása |A jelentés a virtuálisgép-méretezési csoportba, nem megfelelő, ha a Virtuálisgép-lemezkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Előzetes verzió\]: A Log Analytics ügynök központi telepítése virtuálisgép-méretezési csoportok – a Virtuálisgép-rendszerképet (OS) listán nem szereplő naplózása |A jelentés a virtuálisgép-méretezési csoportba, nem megfelelő, ha a Virtuálisgép-lemezkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Előzetes verzió\]: Függőségi ügynök Linux virtuális gép méretezési csoportok üzembe helyezése |Telepítse a függőségi ügynök Linux virtuálisgép-méretezési csoport állítja be, ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Szabályzat |
|\[Előzetes verzió\]: Függőségi ügynök a Windows virtuális gép méretezési csoportok üzembe helyezése |Windows virtuálisgép-méretezési csoport állítja be, ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve a függőségi ügynök telepítése. |Szabályzat |
|\[Előzetes verzió\]: Log Analytics-ügynököket a Linux rendszerű virtuálisgép-méretezési csoportok üzembe helyezése |Linuxos virtuálisgép-méretezési csoport állítja be, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve a Log Analytics-ügynök telepítése. |Szabályzat |
|\[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gép méretezési csoportok üzembe helyezése |Windows virtuálisgép-méretezési csoport állítja be, ha a Virtuálisgép-lemezkép (OS) van definiálva, a listában, és az ügynök nincs telepítve a Log Analytics-ügynök telepítése. |Szabályzat |

(Nem tartalmazza a kezdeményezés-) önálló házirend az alábbiakban ismertetjük:

|Name (Név) |Leírás |Type |
|-----|------------|-----|
|\[Előzetes verzió\]: Virtuális gép – jelentés eltérés naplózása a Log Analytics-munkaterület |A jelentés azokról a nem megfelelő, ha azok nem jelentkezik a Log Analytics-munkaterületet a szabályzatot vagy kezdeményezést-hozzárendelésben megadott virtuális gépek. |Szabályzat |

### <a name="assign-the-azure-monitor-initiative"></a>Az Azure Monitor kezdeményezés hozzárendelése
A szabályzat-hozzárendelés létrehozása a **szabályzati lefedettség virtuális gépek az Azure Monitor** lapon, kövesse az alábbi lépéseket. Megtudhatja, hogyan hajtsa végre ezeket a lépéseket, tekintse meg [szabályzat-hozzárendelés létrehozása az Azure Portalról](../../governance/policy/assign-policy-portal.md).

A szabályzat vagy a kezdeményezés hozzárendelésekor a kiválasztott a hozzárendelési hatókör lehet az itt felsorolt hatókör és a egy részét. Például előfordulhat, hogy létrehozott egy hozzárendelést a az előfizetés (szabályzat hatóköre), és nem a felügyeleti csoport (lefedettség hatókör). Ebben az esetben lefedettség százalékos azt jelentené a virtuális gépek, a házirend vagy a kezdeményezési hatókör elosztja a virtuális gépek, a fedezeti hatókörében. Egy másik esetben előfordulhat, hogy ki lett zárva néhány virtuális gépet, vagy erőforráscsoportok vagy előfizetést a szabályzat hatóköre. Ha üres, az azt jelzi, hogy a szabályzatot vagy kezdeményezést nem létezik, vagy nem rendelkezik engedélyekkel. Információ alapján **hozzárendelés állapota**.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Az Azure Portalon válassza ki a **figyelő**. 

3. Válasszon **(előzetes verzió) virtuális gépek** a a **Insights** szakaszban.
 
4. Válassza ki a **Ismerkedés** fülre. Válassza ki a lapon **kezelése szabályzati lefedettség**.

5. Válassza ki a felügyeleti csoport vagy egy előfizetés a táblázatból. Válassza ki **hatókör** válassza a három pontra (...). A példában egy hatókör korlátozza a szabályzat-hozzárendelést a csoportosított kényszerítésre kijelölt virtuális gépek.

6. Az a **Azure szabályzat-hozzárendelés** lapon azt rendelkezik előre kitölti az kezdeményezésére **engedélyezése az Azure Monitor-beli virtuális gépek**. 
    A **hozzárendelés neve** mezőt automatikusan kitölti a kezdeményezési nevű, de ezt módosíthatja. Is hozzáadhat egy leírást. A **által hozzárendelt** mezőt a rendszer automatikusan kitölti alapján, aki be van jelentkezve. Ezt az értéket nem kötelező.

7. (Nem kötelező) A hatókör egy vagy több erőforrás eltávolításához jelölje ki **kizárások**.

8. Az a **Log Analytics-munkaterület** legördülő listában a támogatott régió, válassza ki egy munkaterületet.

   > [!NOTE]
   > Ha a munkaterület a hozzárendelés hatókörén kívül esik, biztosítson *Log Analytics-közreműködő* engedélyeket a szabályzat-hozzárendelés egyszerű azonosító. Ha nem így tesz, megjelenhet például üzembe helyezési hibák `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` hozzáférést biztosítani, tekintse át a [hogyan konfigurálhatja manuálisan a felügyelt identitás](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  A **felügyelt identitás** jelölőnégyzet be van jelölve, a kezdeményezés érvényessége a felhasználóhoz egy házirend tartalmazza a *deployIfNotExists* érvénybe.
    
9. Az a **identitás kezelése hely** legördülő listára, válassza ki a megfelelő régiót.

10. Válassza a **Hozzárendelés** elemet.

A hozzárendelés létrehozása után a **szabályzati lefedettség virtuális gépek az Azure Monitor** frissítések lapon **hozzárendelés lefedettség**, **hozzárendelés állapota**, **megfelelő Virtuális gépek**, és **megfelelőségi állapot** az elvégzett módosításoknak megfelelően. 

A következő mátrix leképezi a kezdeményezés minden lehetséges megfelelőségi állapota.  

| Megfelelőségi állapot | Leírás | 
|------------------|-------------|
| **Megfelelő** | A hatókörben lévő összes virtuális gép rendelkezik a Log Analytics és a függőségi ügynököt rájuk.|
| **Nem megfelelő** | A hatókör nem minden virtuális gép rendelkezik a Log Analytics és a függőségi ügynökök ellenőrzésére és szervizelési lehet szükség.|
| **Nem indult el** | Új hozzárendelés lett hozzáadva. |
| **zárolás** | Nem rendelkezik megfelelő jogosultságokkal a felügyeleti csoporthoz. <sup>1</sup> | 
| **Üres** | Nincs szabályzat van hozzárendelve. | 

<sup>1</sup> Ha nincs hozzáférése a felügyeleti csoporthoz, kérje meg a hozzáférést biztosítania a tulajdonosa. Vagy megfelelőségi megtekintheti, és az alárendelt felügyeleti csoportok vagy előfizetések-hozzárendelések kezeléséhez. 

Az alábbi táblázatban minden lehetséges hozzárendelés állapota a kezdeményezés képezi le.

| Hozzárendelés állapota | Leírás | 
|------------------|-------------|
| **Sikeres** | A hatókörben lévő összes virtuális gép rendelkezik a Log Analytics és a függőségi ügynököt rájuk.|
| **Figyelmeztetés** | Az előfizetés nem egy felügyeleti csoportban.|
| **Nem indult el** | Új hozzárendelés lett hozzáadva. |
| **zárolás** | Nem rendelkezik megfelelő jogosultságokkal a felügyeleti csoporthoz. <sup>1</sup> | 
| **Üres** | Nincs virtuális gép létezik, vagy nincs szabályzat hozzárendelve. | 
| **Művelet** | Szabályzat hozzárendelése vagy szerkeszteni a hozzárendelést. | 

<sup>1</sup> Ha nincs hozzáférése a felügyeleti csoporthoz, kérje meg a hozzáférést biztosítania a tulajdonosa. Vagy megfelelőségi megtekintheti, és az alárendelt felügyeleti csoportok vagy előfizetések-hozzárendelések kezeléséhez.

## <a name="review-and-remediate-the-compliance-results"></a>Tekintse át és szervizelése megfelelőségi eredményeit

Az alábbi példa egy Azure virtuális gép, de a virtual machine scale sets is vonatkozik. Tekintse át a megfelelőségi eredmények kezelésével kapcsolatos információkért lásd: [azonosíthatja a meg nem felelés eredmények](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Az a **szabályzati lefedettség virtuális gépek az Azure Monitor** lapra, jelölje be a felügyeleti csoport vagy egy előfizetést a táblából. Válassza ki **nézet megfelelőségi** válassza a három pontra (...).   

![Szabályzatoknak való megfelelés, Azure-beli virtuális gépek](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

A kezdeményezés található házirendek eredményei alapján a virtuális gépek vannak megfelelőként jelenik meg, a következő esetekben:

* Log Analytics-ügynököket vagy függőségi ügynök nincs telepítve.  
    Ebben a forgatókönyvben jellemző, a meglévő virtuális gépek hatókör. Csökkentése érdekében, hogy, üzembe helyezése a szükséges ügynökök által [szervizelési feladatokat hozhat létre](../../governance/policy/how-to/remediate-resources.md) lévő nem megfelelő házirendhez.  
    - \[Előzetes verzió\]: Függőségi ügynök Linux rendszerű virtuális gépek üzembe helyezése
    - \[Előzetes verzió\]: Függőségi ügynök Windows-beli virtuális gépek üzembe helyezése
    - \[Előzetes verzió\]: Linux rendszerű virtuális gépekhez a Log Analytics-ügynök telepítése
    - \[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése

* Virtuálisgép-rendszerképet (OS) nem azonosítja a szabályzat-definícióban.  
    A feltételek a központi telepítésre vonatkozó házirendet, például csak jól ismert Azure Virtuálisgép-rendszerképekből telepített virtuális gépekkel. Ellenőrizze a dokumentációban, megtekintéséhez, hogy támogatott-e a virtuális gép operációs rendszerének. Nem támogatott, ha a központi telepítésre vonatkozó házirendet és a frissítés ismétlődő, vagy módosítsa azt, hogy a rendszerkép megfelelő legyen.  
    - \[Előzetes verzió\]: Függőségi ügynök telepítése – Virtuálisgép-rendszerképet (OS) listán nem szereplő naplózása
    - \[Előzetes verzió\]: A Log Analytics-ügynök telepítése – Virtuálisgép-rendszerképet (OS) listán nem szereplő naplózása

* Virtuális gépek nem jelentkezik be a megadott Log Analytics-munkaterületet.  
    Akkor lehet, hogy néhány virtuális gépet a kezdeményezési hatókörében jelentkeznek be a másik a szabályzat-hozzárendelés megadott Log Analytics-munkaterületet. Ez a szabályzat egy olyan eszköz azonosításához, amely a virtuális gépek egy nem megfelelő munkaterületnek jelentenek.  
    - \[Előzetes verzió\]: Virtuális gép – jelentés eltérés naplózása a Log Analytics-munkaterület

## <a name="edit-an-initiative-assignment"></a>Kezdeményezés-hozzárendelés szerkesztése

Bármikor kezdeményezés hozzárendelése a felügyeleti csoportba vagy előfizetésbe, után szerkesztheti ezt a következő tulajdonságok módosítása:

- Hozzárendelés neve
- Leírás
- Hozzárendelte
- Log Analytics-munkaterület
- Kivételek

## <a name="next-steps"></a>További lépések

Most, hogy a virtuális gépek számára engedélyezve van a figyelés és az Azure Monitor-beli virtuális gépek elemzési érhető el ezt az információt. 

- Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). 
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). 
- Azonosíthatja a szűk keresztmetszeteket és a virtuális gép teljesítményét teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md). 
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
