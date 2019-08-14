---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a627592bdfcbebc3c7fcda911e31c0ae6f4a630f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976622"
---
## <a name="benefits"></a>Előnyök 

A teljes gazdagép megőrzése a következő előnyöket biztosítja:

-   Hardver elkülönítése a fizikai kiszolgáló szintjén. Más virtuális gépek nem lesznek elhelyezve a gazdagépeken. A dedikált gazdagépeket ugyanabban az adatközpontban helyezik üzembe, és ugyanazon a hálózaton és mögöttes tárolási infrastruktúrán osztoznak, mint más, nem elkülönített gazdagépek.
-   Az Azure platform által kezdeményezett karbantartási események szabályozása. Habár a karbantartási események többsége kevés hatással van a virtuális gépekre, vannak olyan érzékeny munkaterhelések, amelyekben a Szüneteltetés minden egyes másodperce hatással lehet. A dedikált gazdagépekkel a karbantartási időszakra is lehetőség van, hogy csökkentse a szolgáltatásra gyakorolt hatást.
-   Az Azure Hybrid benefittel saját licenceket hozhat a Windows és az SQL rendszerhez az Azure-ba. A hibrid előnyök használata további előnyöket biztosít. További információ: [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).



## <a name="groups-hosts-and-vms"></a>Csoportok, gazdagépek és virtuális gépek  

![A dedikált gazdagépek új erőforrásainak megtekintése.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

A **gazda-csoport** egy olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket.

A **gazdagép** egy erőforrás, amely egy Azure-adatközpontban található fizikai kiszolgálóra van leképezve. A fizikai kiszolgáló a gazdagép létrehozásakor van lefoglalva. A gazdagépek egy gazdagépen belül jönnek létre. A gazdagép rendelkezik egy SKU-val, amely leírja, hogy milyen virtuálisgép-méreteket lehet létrehozni. Minden gazdagép különböző méretű virtuális gépeket képes tárolni, feltéve, hogy azok ugyanabból a méretből származnak.

Egy virtuális gép Azure-beli létrehozásakor kiválaszthatja, hogy melyik dedikált gazdagépet kívánja használni a virtuális géphez. Teljes hozzáférése van a gazdagépekre helyezett virtuális gépekhez.


## <a name="high-availability-considerations"></a>Magas rendelkezésre állási megfontolások 

A magas rendelkezésre állás érdekében több virtuális gépet kell üzembe helyeznie, több gazdagép között elosztva (legalább 2). Az Azure dedikált gazdagépekkel több lehetősége van arra, hogy kiépítse az infrastruktúrát a hibák elkülönítési határainak formálásához.

### <a name="use-availability-zones-for-fault-isolation"></a>A Availability Zones használata a hibák elkülönítéséhez

A rendelkezésre állási zónák egy Azure-régióban található egyedi fizikai helyek. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. Egy gazdagép-csoport egyetlen rendelkezésre állási zónában jön létre. A létrehozást követően a rendszer az összes gazdagépet a zónán belül helyezi el. Ha magas rendelkezésre állást szeretne biztosítani a zónák között, létre kell hoznia több gazdagép-csoportot (egy zónán belül), és ennek megfelelően kell elosztania a gazdagépeket.

Ha egy rendelkezésre állási zónához rendel hozzá egy gazdagépet, akkor a gazdagépen létrehozott összes virtuális gépet ugyanabban a zónában kell létrehozni.

### <a name="use-fault-domains-for-fault-isolation"></a>Tartalék tartományok használata a hibák elkülönítéséhez

Egy gazdagép hozható létre egy adott tartalék tartományban. A méretezési csoportokban vagy a rendelkezésre állási csoportokban lévő virtuális gépekhez hasonlóan a különböző tartalék tartományokban lévő gazdagépek az adatközpont különböző fizikai állványokra kerülnek. A gazdagépek létrehozásakor meg kell adnia a tartalék tartományok darabszámát. A gazdagépen belüli gazdagépek létrehozásakor minden gazdagéphez rendeljen tartalék tartományt. A virtuális gépek nem igénylik a tartalék tartomány-hozzárendelést.

A tartalék tartományok nem egyeznek meg a létrehozásakor közös elhelyezés. Ha a két gazdagép azonos tartalék tartománya van, nem jelenti azt, hogy egymáshoz közel vannak.

A tartalék tartományok hatóköre a gazda csoportra terjed ki. A két gazda csoport közötti affinitást nem szabad feltételezni (kivéve, ha a különböző rendelkezésre állási zónákban vannak).

A különböző tartalék tartományokkal rendelkező gazdagépekre telepített virtuális gépeken a mögöttes felügyelt lemezek több tárolási bélyegzőn lesznek, így növelhetik a hibák elkülönítésének védelmét.

### <a name="using-availability-zones-and-fault-domains"></a>Availability Zones és tartalék tartományok használata

Mindkét képességet együtt használva még több hibatűrést érhet el. Ebben az esetben meg kell adnia a rendelkezésre állási zónát és a tartalék tartományok számát az egyes gazdagépek esetében, hozzá kell rendelnie egy tartalék tartományt a csoport minden gazdagépéhez, és hozzá kell rendelnie egy rendelkezésre állási zónát az egyes virtuális gépekhez

Az [itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) található Resource Manager-minta sablon zónák és tartalék tartományok használatával terjeszti a gazdagépeket a maximális rugalmasság érdekében egy adott régióban.

## <a name="maintenance-control"></a>Karbantartási ellenőrzés

A virtuális gépeket támogató infrastruktúra esetenként frissíthető a megbízhatóság, a teljesítmény, a biztonság és az új funkciók megkezdése érdekében. Az Azure platform megpróbálja csökkenteni a platform karbantartásának hatását, amikor csak lehetséges, de a *karbantartási szempontból bizalmas* munkaterheléseket használó ügyfelek még néhány másodpercet sem tudnak elviselni, hogy a virtuális gépet be kell fagyasztani vagy le kell kapcsolni a karbantartáshoz.

A **karbantartási ellenőrzés** lehetővé teszi az ügyfeleknek, hogy kihagyhatják a dedikált gazdagépeken ütemezett rendszeres szoftverfrissítéseket, majd egy 35 napos időszakon belül alkalmazza azt a választásuk időpontjában.

> [!NOTE]
>  A karbantartási szabályozás jelenleg korlátozott előzetes verzióban érhető el, és bevezetési folyamatra van szükség. Az előzetes verzióra vonatkozó [nevezési kérdőív](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)beküldésével érvényes.

## <a name="capacity-considerations"></a>A kapacitás szempontok

A dedikált gazdagép kiosztása után az Azure hozzárendeli azt a fizikai kiszolgálóhoz. Ez garantálja a kapacitás rendelkezésre állását, ha üzembe kell helyeznie a virtuális gépet. Az Azure a régió (vagy zóna) teljes kapacitását használja a gazdagép fizikai kiszolgálójának kiválasztásához. Ez azt is jelenti, hogy az ügyfelek várhatóan növekedni tudnak a dedikált gazdagépek lábnyomán anélkül, hogy a fürtön kívülről kellene kifogyni.

## <a name="quotas"></a>Kvóták

Az alapértelmezett kvóta 3000 vCPU a dedikált gazdagépekhez régiónként. Az üzembe helyezhető gazdagépek számát azonban a gazdagéphez használt virtuálisgép-mérethez tartozó kvóta is korlátozza. Az utólagos elszámolású előfizetések esetében például az USA keleti régiójában csak 10 vCPU lehet elérhető az Dsv3-méretekhez. Ebben az esetben a dedikált gazdagép üzembe helyezése előtt a kvóta növelését legalább 64 vCPU kell megadnia. Ha szükséges, kattintson a jobb felső sarokban található **kérelem növelés** gombra.

![Képernyőkép a portál használati és kvóták oldaláról](./media/virtual-machines-common-dedicated-hosts/quotas.png)

További információ: [virtuális gépek vCPU kvótái](/azure/virtual-machines/windows/quotas).

## <a name="pricing"></a>Díjszabás

A felhasználókat a rendszer dedikált gazdagépre terheli, függetlenül attól, hogy hány virtuális gép van telepítve. A havi utasításban a gazdagépek új számlázható erőforrás-típusa jelenik meg. A dedikált gazdagépen lévő virtuális gépek továbbra is megjelennek az utasításban, de a 0. árat is magukban foglalják.

A gazdagép árát a VM-család, a típus (hardver mérete) és a régió alapján állítja be a rendszer. A gazdagép díja a gazdagépen támogatott legnagyobb virtuálisgép-mérethez képest.

A szoftver licencelése, a tárolás és a hálózat használata a gazdagéptől és a virtuális gépektől függetlenül történik. Ezek a Számlázatlan elemek nem változnak.

További információ: [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>VM-családok és hardveres generációk

Egy gazdagéphez van definiálva SKU, és a virtuális gép mérete adatsorozatot és típust jelöl. A különböző méretű virtuális gépeket egyetlen gazdagépen belül is keverheti, ha azok azonos méretű adatsorozattal rendelkeznek. A típus a jelenleg elérhető hardverek létrehozása a régióban.

`types` Ugyanaz a virtuálisgép-sorozat különböző CPU-szállítóktól származik, és a CPU-generációk és a magok száma eltérő lesz.

További információért tekintse meg a gazdagép díjszabását ismertető [oldalt](https://aka.ms/ADHPricing) .

Az előzetes verzió során a következő gazdagép-SKU\types fogjuk támogatni:  DSv3_Type1 és ESv3_Type1

 
## <a name="host-life-cycle"></a>Gazdagép életciklusa


Az Azure figyeli és kezeli a gazdagépek állapotát. A következő állapotok lesznek visszaadva a gazdagép lekérdezésekor:

| Üzemállapot   | Leírás       |
|----------|----------------|
| Gazdagép elérhető     | A gazdagépen nincsenek ismert problémák.   |
| A vizsgálat alatt álló gazdagép  | Problémák léptek fel a gazdagépen, amit keresünk. Ez egy átmeneti állapot szükséges ahhoz, hogy az Azure kipróbálja és azonosítani tudja az azonosított probléma hatókörét és alapvető okát. A gazdagépen futó virtuális gépek befolyásolhatják a működését. |
| Gazdagép függőben lévő felszabadítása   | Az Azure nem tudja visszaállítani a gazdagépet Kifogástalan állapotba, és megkéri, hogy telepítse újra a virtuális gépeket a gazdagépről. Ha `autoHealingOnFailure` engedélyezve van, a virtuális gépek a *szolgáltatás* Kifogástalan állapotba kerülnek. Ellenkező esetben előfordulhat, hogy a virtuális gép olyan gazdagépen fut, amely hamarosan sikertelen lesz.|
| Gazdagép delefoglalt  | Az összes virtuális gép el lett távolítva a gazdagépről. Ez a gazdagép már nem töltődik fel, mivel a hardver elforgatása nem történt meg.   |

