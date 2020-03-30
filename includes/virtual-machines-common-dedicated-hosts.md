---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129171"
---
## <a name="limitations"></a>Korlátozások

- A virtuálisgép-méretezési csoportok jelenleg nem támogatottak dedikált állomásokon.

## <a name="benefits"></a>Előnyök 

A teljes gazdagép lefoglalása a következő előnyökkel jár:

-   Hardverelkülönítés a fizikai kiszolgáló szintjén. Más virtuális gépek nem kerülnek a gazdagépekre. Dedikált gazdagépek vannak telepítve az azonos adatközpontokban, és ugyanazt a hálózatot és az alapul szolgáló tárolási infrastruktúra, mint más, nem elszigetelt gazdagépek.
-   Az Azure platform által kezdeményezett karbantartási események vezérlése. A karbantartási események többsége kevés vagy semmilyen hatással van a virtuális gépekre, vannak olyan bizalmas számítási feladatok, ahol a szünet minden másodperce hatással lehet. A dedikált gazdagépek segítségével engedélyezheti a karbantartási időszakot, hogy csökkentse a szolgáltatásra gyakorolt hatást.
-   Az Azure hibrid előnyeivel saját Windows- és SQL-licenceket hozhat az Azure-ba. A hibrid előnyök használata további előnyöket biztosít. További információ: [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Csoportok, állomások és virtuális gépek  

![Tekintse meg a dedikált állomások új erőforrásait.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

A **gazdagépcsoport** olyan erőforrás, amely dedikált gazdagépek gyűjteményét képviseli. Hozzon létre egy gazdagépcsoportot egy régióban és egy rendelkezésre állási zónát, és adjon hozzá állomásokat.

Az **állomás** egy erőforrás, amely egy Azure-adatközpontban lévő fizikai kiszolgálóhoz van leképezve. A fizikai kiszolgáló az állomás létrehozásakor kerül lefoglalva. Az állomás egy gazdagépcsoporton belül jön létre. A gazdagép rendelkezik egy termékváltozat, amely leírja, hogy mely virtuálisgép-méretek hozhatók létre. Minden állomás több különböző méretű virtuális gépet is üzemeltethet, feltéve, hogy azonos méretű sorozatból származnak.

Virtuális gép létrehozása az Azure-ban, kiválaszthatja, hogy melyik dedikált gazdagép a virtuális gép hez használható. Teljes mértékben szabályozhatja, hogy mely virtuális gépek vannak elhelyezve a gazdagépeken.


## <a name="high-availability-considerations"></a>Magas rendelkezésre állási szempontok 

A magas rendelkezésre állás érdekében több virtuális gépet kell üzembe helyeznie, több gazdagép között elosztva (legalább 2). Az Azure dedikált gazdagépekkel számos lehetősége van az infrastruktúra kiépítésére a hibaelkülönítési határok alakításához.

### <a name="use-availability-zones-for-fault-isolation"></a>Rendelkezésre állási zónák használata a hibák elkülönítésére

A rendelkezésre állási zónák egyedi fizikai helyek egy Azure-régióban. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. Egyetlen rendelkezésre állási zónában jön létre egy gazdagépcsoport. A létrehozás után az összes állomás az adott zónán belül lesz elhelyezve. A zónák közötti magas rendelkezésre állás elérése érdekében több gazdagépcsoportot kell létrehoznia (zónánként egyet), és ennek megfelelően el kell osztania a gazdagépeket.

Ha egy állomáscsoportot rendel egy rendelkezésre állási zónához, az adott állomáson létrehozott összes virtuális gépet ugyanabban a zónában kell létrehozni.

### <a name="use-fault-domains-for-fault-isolation"></a>Tartalék tartományok használata a hibaelkülönítéshez

Egy állomás létrehozható egy adott tartalék tartományban. Csakúgy, mint a virtuális gép egy méretezési csoport vagy rendelkezésre állási csoport, a különböző tartalék tartományok gazdagépei az adatközpont különböző fizikai állványokon lesznek elhelyezve. Állomáscsoport létrehozásakor meg kell adnia a tartaléktartomány-számot. Amikor állomásokat hoz létre a gazdagépcsoporton belül, minden állomáshoz tartalék tartományt kell hozzárendelni. A virtuális gépek nem igényelnek tartalék tartomány-hozzárendelést.

A tartalék tartományok nem azonosak a helymegosztási területekkel. Az, hogy két állomás esetében ugyanaz a tartalék tartomány van, nem jelenti azt, hogy közel vannak egymáshoz.

A tartalék tartományok hatóköre a gazdagépcsoportra terjed ki. Ne feltételezz semmit a két gazdagépcsoport közötti affinitás-ellenességről (kivéve, ha különböző elérhetőségi zónákban vannak).

A különböző tartalék tartományokkal rendelkező állomásokra telepített virtuális gépek az alapul szolgáló felügyelt lemezszolgáltatások több tárolási bélyegzőn lesznek, így növelhetik a hibaelkülönítés elleni védelmet.

### <a name="using-availability-zones-and-fault-domains"></a>Rendelkezésre állási zónák és tartalék tartományok használata

Mindkét funkció együtt használható még több hibaelkülönítés eléréséhez. Ebben az esetben minden állomáscsoporthoz megadja a rendelkezésre állási zóna és a tartalék tartományszámát, a csoport minden állomásához hozzárendel egy tartalék tartományt, és minden virtuális géphez hozzárendel egy rendelkezésre állási zónát.

Az [itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) található Erőforrás-kezelő mintasablon zónákat és tartalék tartományokat használ az állomások szétosztására a maximális rugalmasság érdekében egy régióban.

## <a name="maintenance-control"></a>Karbantartásszabályozás

A virtuális gépeket támogató infrastruktúra időnként frissíthető a megbízhatóság, a teljesítmény, a biztonság javítása és az új funkciók bevezetése érdekében. Az Azure platform igyekszik minimalizálni a platform karbantartásának hatását, amikor csak lehetséges, de a *karbantartás-érzékeny* számítási feladatokat használó ügyfelek még néhány másodpercig sem tolerálják, hogy a virtuális gép le kell fagyasztani vagy le kell választani a karbantartáshoz.

**A Karbantartás vezérlés** lehetővé teszi az ügyfelek számára, hogy kihagyják a dedikált gazdagépeken ütemezett rendszeres platformfrissítéseket, majd az általuk választott időpontban alkalmazzák azt egy 35 napos gördülő ablakon belül.

> [!NOTE]
>  A karbantartás vezérlése jelenleg nyilvános előzetes verzióban érhető el. További információt a **Frissítések vezérlése karbantartás-vezérléssel [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) vagy PowerShell használatával című [témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)**

## <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok

Miután egy dedikált állomás ki van építve, az Azure hozzárendeli azt a fizikai kiszolgálóhoz. Ez garantálja a kapacitás rendelkezésre állását, amikor a virtuális gép kiépítése szükséges. Az Azure a régió (vagy zóna) teljes kapacitását használja a gazdagép fizikai kiszolgálójának kiválasztásához. Ez azt is jelenti, hogy az ügyfelek számíthatnak arra, hogy képesek növelni a dedikált gazdagép lábnyoma anélkül, hogy a fürtben elfogyott a hely.

## <a name="quotas"></a>Kvóták

Régiónként 3000 vCPU alapértelmezett kvótakorlát van a dedikált állomásokra. De a telepíthető állomások számát is korlátozza a kvótát a virtuális gép méretcsaládja a gazdagép használt. Előfordulhat például, hogy egy használatra kapható fizetéses előfizetés csak 10 **vCPU-s** kvótával rendelkezik a Dsv3 méretsorozathoz, az USA keleti régiójában. Ebben az esetben legalább 64 vCPU-ra kell kérnie a kvótanövelését, mielőtt egy dedikált állomást üzembe helyezne. Szükség esetén a kérelem benyújtásához válassza a **Kérés növelése** gombot a jobb felső sarokban.

![Képernyőkép a portál használati és kvóták lapjáról](./media/virtual-machines-common-dedicated-hosts/quotas.png)

További információ: [Virtual machine vCPU quotas](/azure/virtual-machines/windows/quotas).

Az ingyenes próbaverzió és az MSDN-előfizetések nem rendelkeznek kvótával az Azure dedikált gazdagépekhez.

## <a name="pricing"></a>Díjszabás

A felhasználók nak dedikált gazdagépenként kell fizetni, függetlenül attól, hogy hány virtuális gép van telepítve. A havi kimutatásban egy új számlázható erőforrástípusú állomás látható. A dedikált gazdagépen lévő virtuális gépek továbbra is megjelennek a kivonatban, de 0-s árat fognak viselni.

A gazdagép ár a virtuális gép család, a típus (hardverméret) és a régió alapján van beállítva. A gazdagép ár a gazdagépen támogatott legnagyobb virtuális gép méretéhez viszonyítva érhető el.

A szoftverlicencelést, a tárolást és a hálózati használatot a gazdagéptől és a virtuális gépektől elkülönítve számlázom. Ezek a számlázható cikkek nem változnak.

További információ: [Azure dedicated host pricing](https://aka.ms/ADHPricing).

Az [Azure-ra fenntartott állomások fenntartott példányával](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)is megtakaríthatja a költségeket.
 
## <a name="sizes-and-hardware-generations"></a>Méretek és hardvergenerációk

Egy termékváltozat van definiálva egy gazdagép, és képviseli a virtuális gép méretsorozata és típusa. Egy gazdagépen belül több különböző méretű virtuális gépet is keverhet, amennyiben azonos méretű ek. 

A *típus* a hardver generációja. Az azonos virtuálisgép-sorozat különböző hardvertípusai különböző CPU-gyártóktól származnak, és különböző CPU-generációkkal és magok számával rendelkeznek. 

A méretek és a hardvertípusok régiónként eltérőek lehetnek. További információ a gazdagép [díjszabási oldalán.](https://aka.ms/ADHPricing)


## <a name="host-life-cycle"></a>A gazdagép életciklusa


Az Azure figyeli és kezeli a gazdagépek állapotát. A gazdagép lekérdezésekénél a következő állapotok jelennek meg:

| Egészségügyi állam   | Leírás       |
|----------|----------------|
| Elérhető állomás     | Nincsenek ismert problémák a gazdagép.   |
| Vizsgálat alatt áll a házigazda  | Van egy kis problémánk a házigazdával, amit vizsgálunk. Ez egy átmeneti állapot, amely az Azure-nak meg kell próbálnia azonosítani az azonosított probléma hatókörét és kiváltó okát. A gazdagépen futó virtuális gépek érintettek lehetnek. |
| A felszabadításra váró állomás   | Az Azure nem állíthatja vissza a gazdagépet kifogástalan állapotba, és megkérheti, hogy telepítse újra a virtuális gépeket a gazdagépből. Ha `autoReplaceOnFailure` engedélyezve van, a virtuális gépek szolgáltatás kifogástalan hardverre *van gyógyítva.* Ellenkező esetben előfordulhat, hogy a virtuális gép egy sikertelen állomáson fut.|
| Állomás felszabadított  | Az összes virtuális gépet eltávolították a gazdagépből. A hardver rotációból való kiváltása óta már nem számítunk fel díjat a gazdagépért.   |

