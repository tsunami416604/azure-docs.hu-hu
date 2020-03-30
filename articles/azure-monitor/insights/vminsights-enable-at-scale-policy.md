---
title: Az Azure Monitor engedélyezése virtuális gépekhez az Azure Policy használatával
description: Ez a cikk ismerteti, hogyan engedélyezi az Azure Monitor virtuális gépek több Azure virtuális gépek vagy virtuális gép méretezési csoportok az Azure Policy használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7069f2cc96b8876f5514acfa4ba49274b61be46f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282936"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Az Azure Monitor engedélyezése virtuális gépekhez az Azure Policy használatával

Ez a cikk bemutatja, hogyan engedélyezheti az Azure Monitor virtuális gépekhez vagy virtuálisgép-méretezési csoportok az Azure Policy használatával. A folyamat végén sikeresen konfigurálta a Log Analytics és a függőségi ügynökök engedélyezését, és azonosította azokat a virtuális gépeket, amelyek nem megfelelőek.

Az Azure-beli virtuális gépek vagy virtuális gép-méretezési csoportok Azure-figyelőjének felderítéséhez, kezeléséhez és engedélyezéséhez használhatja az Azure Policy vagy az Azure PowerShell használatát. Az Azure Policy az a módszer, amit ajánlunk, mert az előfizetések hatékony szabályozásához kezelheti a szabályzatdefiníciókat a konzisztens megfelelőség és az újonnan kiépített virtuális gépek automatikus engedélyezése érdekében. Ezek a házirend-definíciók:

* Telepítse a Log Analytics-ügynököt és a függőségi ügynököt.
* Jelentés a megfelelőségi eredményekről.
* Nem megfelelő virtuális gépek kiújítása.

Ha ezeket a feladatokat az Azure PowerShell vagy egy Azure Resource Manager-sablon segítségével szeretné elvégezni, olvassa el az [Azure Monitor engedélyezése az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával](vminsights-enable-at-scale-powershell.md)lehetőséget.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt a szabályzat ot az Azure-beli virtuális gépek és a virtuális gép pisztránciája a virtuális gépek Azure-figyelése, engedélyeznie kell a VMInsights-megoldás a munkaterületen, amelyet a figyelési adatok tárolására fog használni. Ez a feladat az Azure Monitor **Első lépések** lapjáról hajtható végre a Másik **bevezetési beállítások** lapon.  Válassza **a Munkaterület konfigurálása**lehetőséget, amely a konfigurálandó munkaterület kiválasztását kéri.

![Munkaterület konfigurálása](media/vminsights-enable-at-scale-policy/configure-workspace.png)

A munkaterületet úgy is konfigurálhatja, hogy az **Engedélyezés házirend használatával** lehetőséget választja, majd a **Munkaterület eszköztárának konfigurálása** gombra kattint.  Ez telepíti a VMInsights-megoldást a kiválasztott munkaterületre, amely lehetővé teszi, hogy a munkaterület tárolja a virtuális gépek által küldött figyelési adatokat és a házirend használatával engedélyezött virtuálisgép-méretezési készleteket. 

![Házirend használatának engedélyezése](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Házirend-lefedettség kezelése – áttekintés

Az Azure Monitor virtuális gépek házirend-lefedettség leegyszerűsíti a felderítése, kezelése és engedélyezése nagy méretekben az **Azure Monitor virtuális gépekhez** kezdeményezés, amely magában foglalja a korábban említett szabályzatdefiníciók. A funkció eléréséhez válassza az **Egyéb bevezetési beállítások lehetőséget** az Azure Monitor virtuális gépekhez lap Első **lépések** lapján. Válassza **a Szabályzati lefedettség kezelése lehetőséget** az Azure Monitor virtuális gépek **házirend-lefedettsége lap megnyitásához.**

![Az Azure-figyelő a virtuális gépek első lépések lapjáról](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Itt ellenőrizheti és kezelheti a kezdeményezés lefedettségét a felügyeleti csoportok és az előfizetések között. Megtudhatja, hogy hány virtuális gép létezik az egyes felügyeleti csoportokban és előfizetésekben, valamint azok megfelelőségi állapotában.

![Az Azure Monitor szolgáltatás a virtuális gépek szabályzatának kezelése lap](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Ez az információ hasznos, hogy segítsen megtervezni és végrehajtani a cégirányítási forgatókönyv az Azure Monitor virtuális gépek egy központi helyről. Míg az Azure Policy megfelelőségi nézetet biztosít, ha egy szabályzat vagy kezdeményezés hatókörhöz van rendelve, ezzel az új lappal megtudhatja, hogy a szabályzat vagy a kezdeményezés nincs hozzárendelve, és hozzárendelheti azt. Az összes művelet, például az Azure Policy közvetlen hozzárendelése, megtekintése és szerkesztése. Az **Azure Monitor virtuális gépek házirend-lefedettség** e lap egy kibővített és integrált élményt csak a kezdeményezés engedélyezése az Azure Monitor virtuális **gépekhez.**

Ezen a lapon konfigurálhatja a Log Analytics-munkaterületet az Azure Monitor virtuális gépekhez szolgáltatásához, amely:

- Telepíti a Szolgáltatástérkép megoldást.
- Engedélyezi az operációs rendszer teljesítményszámlálóit, amelyeket a teljesítménydiagramok, munkafüzetek, valamint az egyéni naplólekérdezések és riasztások használnak.

![Az Azure Monitor virtuális gépekhez konfigurálja a munkaterületet](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ez a beállítás nem kapcsolódik semmilyen szakpolitikai művelethez. Elérhető, hogy egy egyszerű módja annak, hogy megfeleljen az azure monitor virtuális gépek hez szükséges [előfeltételek.](vminsights-enable-overview.md)  

### <a name="what-information-is-available-on-this-page"></a>Milyen információk érhetők el ezen az oldalon?

Az alábbi táblázat a házirend-lefedettségi lapon megjelenő információk és azok értelmezésének részletezését tartalmazza.

| Függvény | Leírás | 
|----------|-------------| 
| **Hatókör** | Felügyeleti csoport és előfizetések, amelyek rendelkeznek, vagy örökölt hozzáférést a felügyeleti csoport hierarchiájában leáshat.|
| **Szerepkör** | A hatókörszerepköre, amely lehet olvasó, tulajdonos vagy közreműködő. Bizonyos esetekben üresnek tűnhet, ha azt jelzi, hogy hozzáférhet az előfizetéshez, de nem ahhoz a felügyeleti csoporthoz, amelyhez tartozik. A más oszlopokban található információk a szerepkörtől függően változnak. A szerepkör kulcsfontosságú annak meghatározásában, hogy milyen adatokat láthat, és milyen műveleteket hajthat végre a szabályzatok vagy kezdeményezések (tulajdonos) hozzárendelése, szerkesztése vagy a megfelelőség megtekintése tekintetében. |
| **Összes virtuális gép** | A hatókörhöz tartozó virtuális gépek száma. Egy felügyeleti csoport, az előfizetések vagy a gyermek felügyeleti csoport alá ágyazott virtuális gépek összege. |
| **Hozzárendelés fedezete** | A szabályzat vagy kezdeményezés hatálya alá tartozó virtuális gépek százalékos százaléka. |
| **Hozzárendelés állapota** | Információ a házirend vagy a kezdeményezési megbízás állapotáról. |
| **Megfelelő virtuális gépek** | A szabályzat vagy a kezdeményezés szerint megfelelő virtuális gépek száma. Az **Azure Monitor engedélyezése virtuális gépekhez**kezdeményezés hez ez a Log Analytics-ügynökkel és függőségi ügynökkel egyaránt rendelkező virtuális gépek száma. Bizonyos esetekben üresnek tűnhet, mert nincs hozzárendelés, nincs virtuális gép, vagy nincs elég engedély. Az információkat a **megfelelőségi állapot**tartalmazza. |
| **Megfelelőség** | A teljes megfelelőségi szám a megfelelő erőforrások és az összes különálló erőforrás összegének hányadosa. |
| **Megfelelőségi állapot** | A szabályzat vagy a kezdeményezési hozzárendelés megfelelőségi állapotára vonatkozó információk.|

A házirend vagy kezdeményezés hozzárendelésekor a hozzárendelésben kiválasztott hatókör lehet a felsorolt hatókör vagy annak egy részhalmaza. Előfordulhat például, hogy egy előfizetéshez (szabályzathatókörhöz) hozott létre hozzárendelést, és nem egy felügyeleti csoporthoz (fedezeti hatókörhöz). Ebben az esetben a **hozzárendelési lefedettség** értéke jelzi a virtuális gépek a szabályzat vagy a kezdeményezés hatóköre osztva a virtuális gépek a lefedettségi hatókörben. Egy másik esetben előfordulhat, hogy kizárt néhány virtuális gépet, erőforráscsoportot vagy előfizetést a szabályzat hatóköréből. Ha az érték üres, azt jelzi, hogy a házirend vagy a kezdeményezés nem létezik, vagy nincs engedélye. Az információk a **Hozzárendelés állapota**csoportban vannak megadva.

## <a name="enable-by-using-azure-policy"></a>Engedélyezés az Azure-szabályzat használatával

Az Azure Monitor virtuális gépekhez való engedélyezése az Azure Policy használatával a bérlőben:

- Rendelje hozzá a kezdeményezést egy hatókörhöz: felügyeleti csoporthoz, előfizetéshez vagy erőforráscsoporthoz.
- Tekintse át és orvosolja a megfelelőségi eredményeket.

Az Azure-szabályzat hozzárendeléséről az [Azure-szabályzat áttekintése](../../governance/policy/overview.md#policy-assignment) című témakörben olvashat bővebben, és a folytatás előtt tekintse át a [felügyeleti csoportok áttekintését.](../../governance/management-groups/overview.md)

### <a name="policies-for-azure-vms"></a>Azure-beli virtuális gépek házirendjei

Az Azure virtuális gépek házirend-definíciói az alábbi táblázatban találhatók.

|Név |Leírás |Típus |
|-----|------------|-----|
|Az Azure-figyelő engedélyezése virtuális gépekhez |Engedélyezze az Azure Monitort a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport) lévő virtuális gépekhez. A Log Analytics munkaterületet paraméterként veszi figyelembe. |Kezdeményezés |
|Naplózási függőségi ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán |A virtuális gépeket nem megfelelőként jelenti, ha a virtuálisgép-lemezkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|A Naplóelemzési ügynök központi telepítése – A virtuális gép lemezképe (OS) nem szerepel a tőzsdén |A virtuális gépeket nem megfelelőként jelenti, ha a virtuálisgép-lemezkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Függőségi ügynök üzembe helyezése Linuxos virtuális gépekhez |Telepítse a függőségi ügynök linuxos virtuális gépek, ha a virtuális gép lemezképe (OS) meg van határozva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Függőségi ügynök telepítése Windows virtuális gépekhez |Telepítse a függőségi ügynököt a Windows virtuális gépekhez, ha a virtuálisgép-lemezkép (OS) meg van határozva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Log Analytics-ügynök telepítése Linuxos virtuális gépekhez |Telepítse a Log Analytics-ügynököt linuxos virtuális gépekhez, ha a virtuálisgép-lemezkép (OS) meg van határozva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Log Analytics-ügynök telepítése Windows virtuális gépekhez |Telepítse a Log Analytics-ügynököt a Windows virtuális gépekhez, ha a virtuálisgép-lemezkép (OS) meg van határozva a listában, és az ügynök nincs telepítve. |Szabályzat |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Az Azure virtuálisgép-méretezési csoportjainak szabályzatai

Az Azure virtuálisgép-méretezési készlet házirend-definíciói az alábbi táblázatban találhatók.

|Név |Leírás |Típus |
|-----|------------|-----|
|Az Azure Monitor engedélyezése virtuálisgép-méretezési készletekhez |Engedélyezze az Azure Monitort a megadott hatókörben (felügyeleti csoport, előfizetés vagy erőforráscsoport) lévő virtuálisgép-méretezési csoportokhoz. A Log Analytics munkaterületet paraméterként veszi figyelembe. Megjegyzés: Ha a méretezési készlet frissítési szabályzat a Kézi, alkalmazza a bővítményt a készlet összes virtuális gépére frissítés hívásával rájuk. A CLI, ez `az vmss update-instances`. |Kezdeményezés |
|A naplózási függőségi ügynök központi telepítése a virtuálisgép-méretezési csoportokban – A virtuális gép lemezképe (OS) nem szerepel a listán |Jelentések virtuálisgép méretezési készlet nem megfelelő, ha a virtuális gép lemezképe (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|A Naplóelemzési ügynök üzembe helyezésének naplózása a virtuálisgép-méretezési csoportokban – a virtuálisgép-lemezkép (OS) nem szerepel a tőzsdén |Jelentések virtuálisgép méretezési készlet nem megfelelő, ha a virtuális gép lemezképe (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Függőségi ügynök üzembe helyezése Linux os virtuálisgép-méretezési készletekhez |Telepítse a függőségi ügynök linuxos virtuálisgép-méretezési készletek, ha a virtuális gép lemezképe (OS) meg van határozva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Függőségi ügynök központi telepítése Windows virtuálisgép-méretezési készletekhez |Telepítse a függőségi ügynök a Windows virtuálisgép-méretezési készletek, ha a virtuális gép lemezkép (OS) van definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Log Analytics-ügynök linuxos virtuálisgép-méretezési készletekhez való üzembe helyezése |Telepítse a Log Analytics-ügynök Linux os virtuálisgép-méretezési készletek, ha a virtuális gép lemezkép (OS) van definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|Log Analytics-ügynök telepítése Windows virtuálisgép-méretezési készletekhez |Telepítse a Log Analytics-ügynök a Windows virtuálisgép-méretezési készletek, ha a virtuális gép lemezkép (OS) van definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |

Az önálló politika (amely nem szerepel a kezdeményezésben) itt található:

|Név |Leírás |Típus |
|-----|------------|-----|
|Naplóelemzési munkaterület naplózása – jelentéseltérés |Jelentse a virtuális gépeket nem megfelelőnek, ha nem jelentkeznek be a szabályzatban vagy kezdeményezés-hozzárendelésben megadott Log Analytics-munkaterületre. |Szabályzat |

### <a name="assign-the-azure-monitor-initiative"></a>Az Azure Monitor kezdeményezés hozzárendelése

A szabályzat-hozzárendelés létrehozása az **Azure Monitor virtuális gépek házirend-lefedettség** e lapon, kövesse az alábbi lépéseket. A lépések végrehajtásáról a [Szabályzat-hozzárendelés létrehozása az Azure Portalon című témakörben](../../governance/policy/assign-policy-portal.md)található.

A házirend vagy kezdeményezés hozzárendelésekor a hozzárendelésben kiválasztott hatókör lehet az itt felsorolt hatókör vagy annak egy részhalmaza. Előfordulhat például, hogy az előfizetéshez (szabályzathatókörhez) hozott létre hozzárendelést, és nem a felügyeleti csoporthoz (fedezeti hatókör). Ebben az esetben a fedezeti százalék azt jelzi, hogy a virtuális gépek a szabályzat vagy a kezdeményezés hatóköre osztva a virtuális gépek a fedezeti hatókörben. Egy másik esetben előfordulhat, hogy kizárt néhány virtuális gépet, erőforráscsoportot vagy előfizetést a szabályzat hatóköréből. Ha üres, azt jelzi, hogy a házirend vagy a kezdeményezés nem létezik, vagy ön nem rendelkezik engedélyekkel. Az információk a **Hozzárendelés állapota**csoportban vannak megadva.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Az Azure Portalon válassza a **Figyelő**lehetőséget. 

3. Válassza **a Virtuális gépek** az **Insights** szakaszban.
 
4. Válassza az **Első lépések** lapot. A lapon válassza a **Házirend-lefedettség kezelése**lehetőséget.

5. Válasszon egy felügyeleti csoportot vagy egy előfizetést a táblából. A három pont (...) kiválasztásával válassza a **Hatókör** lehetőséget. A példában egy hatókör korlátozza a szabályzat-hozzárendelés a virtuális gépek egy csoportja a kényszerítéshez.

6. Az **Azure Policy-hozzárendelési** lapon előre ki van töltve az **Azure-figyelő engedélyezése virtuális gépekhez**kezdeményezéssel. 
    A **Hozzárendelés neve** mező automatikusan ki van töltve a kezdeményezés nevével, de módosíthatja. A nem kötelező leírás okat is hozzáadhatja. A **Hozzárendelés mező** automatikusan kitöltődik a bejelentkezett kik alapján. Ez az érték nem kötelező.

7. (Nem kötelező) Ha egy vagy több erőforrást el szeretne távolítani a hatókörből, válassza a **Kizárások**lehetőséget.

8. A **Log Analytics munkaterület** legördülő listájában a támogatott régió, válasszon ki egy munkaterületet.

   > [!NOTE]
   > Ha a munkaterület nem tartozik a hozzárendelés hatókörébe, adjon *loganalytics-közreműködői* engedélyeket a házirend-hozzárendelés elsődleges azonosítójának. Ha ezt nem teszi meg, előfordulhat, hogy `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` egy központi telepítési hiba jelenik meg, például a hozzáférés megadása érdekében tekintse [át, hogyan konfigurálhatja manuálisan a felügyelt identitást.](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)
   > 
   >  A **Felügyelt identitás** jelölőnégyzet be van jelölve, mert a hozzárendelt kezdeményezés tartalmaz egy házirendet a *deployIfNotExists* hatással.
    
9. Az **Identitás helyének kezelése** legördülő listában válassza ki a megfelelő régiót.

10. Válassza a **Hozzárendelés** elemet.

A hozzárendelés létrehozása után az **Azure Monitor virtuális gépek házirend-lefedettség** e lap frissíti hozzárendelés **lefedettség**, **hozzárendelés állapota**, megfelelő **virtuális gépek**, és **megfelelőségi állapot,** hogy tükrözze a változásokat. 

A következő mátrix leképezi a kezdeményezés minden lehetséges megfelelőségi állapotát.  

| Megfelelőségi állapot | Leírás | 
|------------------|-------------|
| **Megfelelő** | A hatókörben lévő összes virtuális gép hez telepítve van a Log Analytics és a Függőségi ügynökök.|
| **Nem megfelelő** | A hatókörben lévő virtuális gépek közül nem mindegyik rendelkezik a Log Analytics és a Függőségi ügynökök üzembe helyezésével, és szükség lehet a szervizelésre.|
| **Nincs elindítva** | Új hozzárendelés lett hozzáadva. |
| **Zár** | Nincs megfelelő jogosultsága a felügyeleti csoporthoz. <sup>1 1</sup> | 
| **Üres** | Nincs házirend hozzárendelve. | 

<sup>1</sup> Ha nem fér hozzá a felügyeleti csoporthoz, kérje meg a tulajdonost, hogy biztosítson hozzáférést. Vagy tekintse meg a megfelelőséget, és kezelje a hozzárendeléseket a gyermekfelügyeleti csoportokon vagy előfizetéseken keresztül. 

Az alábbi táblázat a kezdeményezés minden lehetséges hozzárendelési állapotát leképezi.

| Hozzárendelés állapota | Leírás | 
|------------------|-------------|
| **Siker** | A hatókörben lévő összes virtuális gép hez telepítve van a Log Analytics és a Függőségi ügynökök.|
| **Figyelmeztetés** | Az előfizetés nem egy felügyeleti csoport alatt van.|
| **Nincs elindítva** | Új hozzárendelés lett hozzáadva. |
| **Zár** | Nincs megfelelő jogosultsága a felügyeleti csoporthoz. <sup>1 1</sup> | 
| **Üres** | Nincsenek virtuális gépek, vagy a szabályzat nincs hozzárendelve. | 
| **Művelet** | Házirend hozzárendelése vagy hozzárendelés szerkesztése. | 

<sup>1</sup> Ha nem fér hozzá a felügyeleti csoporthoz, kérje meg a tulajdonost, hogy biztosítson hozzáférést. Vagy tekintse meg a megfelelőséget, és kezelje a hozzárendeléseket a gyermekfelügyeleti csoportokon vagy előfizetéseken keresztül.

## <a name="review-and-remediate-the-compliance-results"></a>A megfelelőségi eredmények áttekintése és kiiktatása

A következő példa egy Azure virtuális gép, de a virtuális gép méretezési csoportok is vonatkozik. A megfelelőségi eredmények áttekintéséről a [Meg nem felelés ieredményeinek azonosítása](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)című témakörben olvashat. Az **Azure Monitor virtuális gépek házirend-lefedettsége** lapon válasszon ki egy felügyeleti csoportot vagy egy előfizetést a táblából. Válassza a **Megfelelőség megtekintése** lehetőséget a három pont (...) kiválasztásával.   

![Azure-beli virtuális gépek házirend-megfelelősége](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

A kezdeményezésben szereplő szabályzatok eredményei alapján a virtuális gépek nem megfelelőként jelennek meg a következő esetekben:

* A Log Analytics-ügynök vagy a Függőségi ügynök nincs telepítve.  
    Ez a forgatókönyv a meglévő virtuális gépek hatókörére jellemző. A mérséklés érdekében telepítse a szükséges ügynököket [szervizelési feladatok létrehozásával](../../governance/policy/how-to/remediate-resources.md) egy nem megfelelő házirenden.  
    - Függőségi ügynök üzembe helyezése Linuxos virtuális gépekhez
    - Függőségi ügynök telepítése Windows virtuális gépekhez
    - Log Analytics-ügynök telepítése Linuxos virtuális gépekhez
    - Log Analytics-ügynök telepítése Windows virtuális gépekhez

* Virtuálisgép-lemezkép (OS) nem azonosítható a szabályzat definíciójában.  
    A telepítési szabályzat feltételei csak a jól ismert Azure virtuálisgép-lemezképekről üzembe helyezett virtuális gépek. Ellenőrizze a dokumentációt, hogy a virtuális gép operációs rendszer támogatott-e. Ha nem támogatott, kettőzze meg a telepítési szabályzatot, és frissítse vagy módosítsa a lemezkép megfelelővé tétele érdekében.  
    - Naplózási függőségi ügynök telepítése – A virtuális gép lemezképe (OS) nem szerepel a listán
    - A Naplóelemzési ügynök központi telepítése – A virtuális gép lemezképe (OS) nem szerepel a tőzsdén

* A virtuális gépek nem jelentkeznek be a megadott Log Analytics-munkaterületre.  
    Lehetséges, hogy a kezdeményezés hatókörében lévő egyes virtuális gépek nem a házirend-hozzárendelésben megadott workspace-be jelentkeznek be. Ez a házirend egy olyan eszköz, amely azonosítja, hogy mely virtuális gépek jelentik a nem megfelelő munkaterületet.  
    - Naplóelemzési munkaterület naplózása – jelentéseltérés

## <a name="edit-an-initiative-assignment"></a>Kezdeményezési hozzárendelés szerkesztése

Miután egy kezdeményezést hozzárendelt egy felügyeleti csoporthoz vagy előfizetéshez, bármikor módosíthatja azt a következő tulajdonságok módosításához:

- Hozzárendelés neve
- Leírás
- Hozzárendelte:
- Log Analytics-munkaterület
- Kivételek

## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek, ez az információ az Azure Monitor virtuális gépek hez érhető el. 

- A felderített alkalmazásfüggőségek megtekintéséhez olvassa el [az Azure-figyelő megtekintése virtuális gépekleképezéséhez.](vminsights-maps.md) 

- A szűk keresztmetszetek és a virtuális gép teljesítményének általános kihasználásának azonosításáról az [Azure virtuális gép teljesítményének megtekintése](vminsights-performance.md)című témakörben nyújt témakört. 
