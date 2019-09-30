---
title: Azure Proximity-elhelyezési csoportok optimális hálózati késéshez SAP-alkalmazásokkal | Microsoft Docs
description: Az Azure Proximity-elhelyezési csoportokkal kapcsolatos SAP-telepítési forgatókönyvek ismertetése
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c56bfda2b4f74bf31ce847f1fdb42f77f43eb372
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677981"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure Proximity-elhelyezési csoportok optimális hálózati késéshez SAP-alkalmazásokkal
Az SAP NetWeaver vagy az SAP S/4HANA architektúrán alapuló SAP-alkalmazások érzékenyek az SAP-alkalmazási rétegek és az SAP-adatbázis szintjei közötti hálózati késésre. Ez az érzékenység az alkalmazási rétegben futó üzleti logika legnagyobb része. Mivel az SAP-alkalmazás rétege futtatja az üzleti logikát, nagy gyakorisággal kérdezi le a lekérdezéseket az adatbázis-rétegre, és másodpercenként több ezer vagy több tízezer értéket ad meg. A legtöbb esetben a lekérdezések természete egyszerű. Általában 500-s vagy annál kisebb adatbázis-szinten futnak.

A hálózaton töltött idő a lekérdezés az alkalmazás szintjéről az adatbázis szintjére való elküldéséhez, és a visszaadott eredményhalmaz jelentős hatással van az üzleti folyamatok futtatásához szükséges időre. Ez a hálózati késésre való érzékenység oka, hogy optimális hálózati késést kell elérnie az SAP üzembe helyezési projektjeiben. Lásd: @no__t – 0SAP Megjegyzés #1100926 – gyakori kérdések: Hálózati teljesítmény @ no__t – 0 a hálózati késés besorolásával kapcsolatos útmutatásért.

Számos Azure-régióban az adatközpontok száma nőtt. Ezt a növekedést a Availability Zones bevezetésével is elindították. Ugyanakkor az ügyfelek, különösen a magas szintű SAP-rendszerek esetében, több speciális VM SKU-t használnak az M sorozatú családban, vagy a HANA nagyméretű példányain. Ezek az Azure-beli virtuálisgép-típusok nem érhetők el egy adott Azure-régió összes adatközpontjában. A két tendencia miatt az ügyfelek olyan hálózati késést tapasztaltak, amely nem az optimális tartományban van. Bizonyos esetekben ez a késés az SAP-rendszerek inoptimális teljesítményét eredményezi.

Ezen problémák elkerülése érdekében az Azure [közelségi csoportokat](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)biztosít. Ez az új funkció már használatban van különböző SAP-rendszerek üzembe helyezéséhez. A közelségi elhelyezési csoportokra vonatkozó korlátozásokért tekintse meg a bekezdés elején említett cikket. Ez a cikk azokat az SAP-forgatókönyveket ismerteti, amelyekben az Azure Proximity-elhelyezési csoportok használhatók.

## <a name="what-are-proximity-placement-groups"></a>Mik azok a közelségi elhelyezési csoportok? 
Az Azure Proximity-elhelyezési csoport egy logikai szerkezet. Ha meg van adva, az egy Azure-régióhoz és egy Azure-erőforráscsoporthoz van kötve. Virtuális gépek telepítésekor a rendszer a következőkre hivatkozik:

- Az adatközpontban üzembe helyezett első Azure-beli virtuális gép. Azt is megteheti, hogy az első virtuális gépet egy olyan Azure-beli kiosztási algoritmusok alapján üzembe helyezett "anchor VM"-ként telepíti, amelyek végül egy adott rendelkezésre állási zónához tartozó felhasználói definíciókkal együtt települnek.
- A közelségi elhelyezési csoportra hivatkozó összes további virtuális gép, amely a későbbiekben üzembe helyezett Azure-beli virtuális gépeket helyezi el ugyanabban az adatközpontban, mint az első virtuális gép.

> [!NOTE]
> Ha nincs olyan gazdagép-hardver telepítve, amely egy adott virtuálisgép-típust futtat az adatközpontban, ahol az első virtuális gép el lett helyezve, a kért virtuálisgép-típus telepítése sikertelen lesz. Hibaüzenetet kap.

Egyetlen [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) több közelségi elhelyezési csoporttal is rendelkezhet. A közelségi elhelyezési csoportok azonban csak egy Azure-erőforráscsoport számára rendelhetők hozzá.

Az elhelyezési csoportok használatakor vegye figyelembe az alábbi szempontokat:

- Ha az SAP-rendszer optimális teljesítményére törekszik, és a közelségi csoportok használatával korlátozza a rendszer egyetlen Azure-adatközpontját, előfordulhat, hogy nem fogja tudni egyesíteni az elhelyezési csoporton belül az összes virtuálisgép-családot. Ezek a korlátozások azért fordulnak elő, mert előfordulhat, hogy az adott virtuálisgép-típus futtatásához szükséges gazdagép-hardver nem szerepel az adatközpontban, amelybe az elhelyezési csoport horgony virtuális gépe telepítve lett.
- Egy ilyen SAP-rendszer életciklusa során kénytelen lehet áthelyezni a rendszert egy másik adatközpontba. Erre a lépésre akkor lehet szükség, ha úgy dönt, hogy a kibővíthető HANA adatbázis-kezelő rétege például négy csomópontról 16 csomópontra van áthelyezve, és nincs elegendő kapacitás ahhoz, hogy az adatközpontban használt típushoz további 12 virtuális gépet kapjon.
- A hardver leszerelése miatt a Microsoft létrehozhat egy másik adatközpontban használt virtuálisgép-típust, nem pedig azt, amelyet eredetileg használt. Ebben az esetben előfordulhat, hogy át kell helyeznie az összes közelségi elhelyezési csoport virtuális gépe egy másik adatközpontba.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Földrajzi elhelyezési csoportok olyan SAP-rendszerekkel, amelyek csak Azure-beli virtuális gépeket használnak
Az Azure-ban a legtöbb SAP NetWeaver és S/4HANA rendszer telepítése nem használ [HANA nagyméretű példányokat](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). A HANA nagyméretű példányokat nem használó központi telepítések esetében fontos, hogy optimális teljesítményt nyújtson az SAP-alkalmazás rétege és az adatbázis-kezelői réteg között. Ehhez Definiáljon egy Azure közelségi elhelyezési csoportot a rendszer számára.

A legtöbb ügyfél-telepítésben az ügyfeleknek egyetlen [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kell kiépíteniük az SAP-rendszerekhez. Ebben az esetben egy-az-egyhez kapcsolat áll fenn, például az éles környezetben futó ERP rendszererőforrás-csoport és a közelségi elhelyezési csoport között. Más esetekben az ügyfelek horizontálisan rendszerezik az erőforráscsoportot, és az összes éles rendszert összegyűjtik egyetlen erőforráscsoporthoz. Ebben az esetben egy-a-többhöz kapcsolatot kell kialakítania az erőforráscsoport és az éles környezetben futó SAP-rendszerek, valamint az éles környezetben futó SAP ERP, SAP BW és egyéb földrajzi elhelyezési csoportok között.

Ne adjon meg több SAP-éles vagy nem éles rendszert egyetlen közelségi elhelyezési csoportban. Ha kis számú SAP-rendszerre vagy egy SAP-rendszerre van szükség, és néhány alkalmazásnak alacsony késésű hálózati kommunikációra van szüksége, érdemes lehet áthelyezni ezeket a rendszereket egy közelségi elhelyezési csoportba. Érdemes elkerülni a rendszerek kötegeit, mert annál több rendszer van csoportosítva a közelségi elhelyezési csoportban, annál nagyobb eséllyel:

- Ehhez olyan virtuálisgép-típusra van szükség, amely nem futtatható az adott adatközpontban, amelybe a közelségi elhelyezési csoportot rögzítette.
- A nem mainstream virtuális gépek, például az M-sorozatú virtuális gépek erőforrásai végül nem teljesíthetők, ha többre van szükség, mert az idő múlásával szoftvereket vesznek fel egy közeli elhelyezési csoportba.

A következőképpen néz ki az ideális konfiguráció:

![Közelségi elhelyezési csoportok csak Azure-beli virtuális gépekkel](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Ebben az esetben egyetlen SAP-rendszer van csoportosítva az egyik erőforráscsoporthoz, egy közelségi elhelyezési csoporttal. Nem függ attól, hogy a HANA kibővített vagy az adatbázis-kezelői Felskálázási konfigurációkat használja-e.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Proximity elhelyezési csoportok és HANA nagyméretű példányok
Ha az SAP-rendszerek némelyike az alkalmazási réteg [Hana nagyméretű példányaira](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) támaszkodik, jelentős mértékű fejlesztéseket tapasztalhat a Hana nagyméretű példányok egysége és az Azure-beli virtuális gépek között, ha olyan Hana nagyméretű példány-egységeket használ, amelyek a 4. [változatban vagy bélyegzőben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)van telepítve. Az egyik javítás az, hogy a HANA Large instances egységeket üzembe helyezik, központi elhelyezési csoporttal. Az alkalmazási rétegbeli virtuális gépek üzembe helyezéséhez használhatja az adott közelségi csoportot. Ennek eredményeképpen ezek a virtuális gépek ugyanabban az adatközpontban lesznek telepítve, amely a HANA Large instances egységet üzemelteti.

Annak megállapításához, hogy a HANA nagyméretű példányainak egysége 4. változatban van-e telepítve, tekintse meg az [Azure HANA nagyméretű példányok vezérlése a Azure Portal használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)című cikket. A HANA Large instances egység attribútumok áttekintésében a közelségi elhelyezési csoport nevét is meghatározhatja, mivel a HANA nagyméretű példányok egységének telepítésekor jött létre. Az attribútumok áttekintésében megjelenő név annak a közelségi helynek a neve, amelyre az alkalmazási rétegbeli virtuális gépeket telepíteni kell.

A csak az Azure-beli virtuális gépeket használó SAP-rendszerekhez képest, ha HANA nagyméretű példányokat használ, kevesebb rugalmasságot biztosít annak eldöntésében, hogy hány [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) használ. A [Hana Large instances bérlő](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) összes Hana Large instances egysége egyetlen erőforráscsoport szerint van csoportosítva, a [jelen cikkben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)leírtak szerint. Hacsak nem a különböző bérlők számára végez telepítést, például üzemi és nem éles rendszerek vagy más rendszerek esetében, az összes HANA Large instances-egység egy HANA Large instances-bérlőn lesz üzembe helyezve. A bérlő egy-az-egyhez kapcsolattal rendelkezik egy erőforráscsoporthoz. Azonban minden egyes egységhez külön közelségi elhelyezési csoport lesz meghatározva.

Ennek eredményeképpen az Azure-erőforráscsoportok és a közelségi elhelyezési csoportok közötti kapcsolatok egyetlen bérlő esetében az alábbiak szerint jelennek meg:

![Proximity elhelyezési csoportok és HANA nagyméretű példányok](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Központi telepítésre példa Proximity elhelyezési csoportokkal
Az alábbiakban néhány PowerShell-parancsot találhat, amelyekkel üzembe helyezheti a virtuális gépeket az Azure közelségi csoportok használatával.

Az első lépés a [Azure Cloud Shellba](https://azure.microsoft.com/features/cloud-shell/)való bejelentkezés után annak ellenőrzését kéri, hogy a központi telepítéshez használni kívánt Azure-előfizetést használja-e:

<pre><code>
Get-AzureRmContext
</code></pre>

Ha másik előfizetésre van szüksége, a következő parancs futtatásával teheti meg:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Hozzon létre egy új Azure-erőforráscsoportot a következő parancs futtatásával:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Hozza létre az új közelségi elhelyezési csoportot a következő parancs futtatásával:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Az alábbihoz hasonló parancs használatával üzembe helyezheti első virtuális gépét a közelségi csoportba:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Az előző parancs egy Windows-alapú virtuális gépet telepít. Miután a virtuális gép üzembe helyezése sikeres volt, a közelségi elhelyezési csoport adatközpont-hatóköre az Azure-régióban van meghatározva. A közelségi elhelyezési csoportra hivatkozó összes további virtuálisgép-telepítés ugyanabban az Azure-adatközpontban lesz üzembe helyezve, amíg a virtuális gép típusa az adott adatközpontba helyezett hardveren üzemeltethető, és a virtuálisgép-típus kapacitása elérhető.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Rendelkezésre állási csoportok és Availability Zonesek egyesítése Proximity elhelyezési csoportokkal
Az SAP-rendszertelepítések Availability Zones használatának egyik hátránya, hogy az SAP-alkalmazás rétegét az adott zónán belüli rendelkezésre állási csoportok használatával nem lehet telepíteni. Azt szeretné, hogy az SAP-alkalmazás rétege ugyanabban a zónában legyen telepítve, mint az adatbázis-kezelő réteg. Egy rendelkezésre állási zónára és egy rendelkezésre állási csoportra hivatkozva egyetlen virtuális gép telepítése nem támogatott. Ezért korábban arra kényszerült, hogy egy zónára hivatkozó alkalmazási réteget helyezzen üzembe. Elveszítette, hogy az alkalmazás rétegének virtuális gépei különböző frissítési és meghibásodási tartományok között legyenek elosztva.

A Proximity elhelyezési csoportok használatával megkerülheti ezt a korlátozást. Az üzembe helyezési folyamat a következő:

- Hozzon létre egy közelségi elhelyezési csoportot.
- A rendelkezésre állási zónákra hivatkozva üzembe helyezheti a horgony virtuális gépet, általában az adatbázis-kezelő kiszolgálót.
- Hozzon létre egy rendelkezésre állási csoportot, amely az Azure közelségi csoportjára hivatkozik. (Lásd a cikk későbbi részében található parancsot.)
- Telepítse az alkalmazás rétegbeli virtuális gépeket a rendelkezésre állási csoportra és a közelségi elhelyezési csoportra való hivatkozással.

Ahelyett, hogy üzembe helyezte az első virtuális gépet az előző szakaszban bemutatott módon, a virtuális gép telepítésekor a rendelkezésre állási zónára és a közelségi elhelyezési csoportra kell hivatkoznia:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

A virtuális gép sikeres üzembe helyezése egy rendelkezésre állási zónában futtatja az SAP-rendszer adatbázis-példányát. A közelségi elhelyezési Csoport hatóköre rögzített a megadott rendelkezésre állási zónát képviselő adatközpontok egyikén.

Tegyük fel, hogy a központi szolgáltatások virtuális gépei ugyanúgy telepíthetők, mint az adatbázis-kezelő virtuális gépek, ugyanarra a zónára vagy zónákra hivatkoznak, és ugyanaz a közelségi elhelyezési csoportok. A következő lépésben létre kell hoznia az SAP-rendszer alkalmazási rétegéhez használni kívánt rendelkezésre állási csoportokat.

Meg kell határoznia és létre kell hoznia a közelségi elhelyezési csoportot. A rendelkezésre állási csoport létrehozásához szükséges parancshoz további hivatkozást kell megadni a közelségi elhelyezési csoport AZONOSÍTÓjának (nem a név). A közelségi elhelyezési csoport AZONOSÍTÓját a következő paranccsal kérheti le:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

A rendelkezésre állási csoport létrehozásakor további paramétereket kell figyelembe vennie, ha felügyelt lemezeket használ (ha nincs más megadva) és a közelségi elhelyezési csoportok:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Ideális esetben három tartalék tartományt kell használnia. A támogatott tartalék tartományok száma azonban régiónként eltérő lehet. Ebben az esetben az adott régiókban lehetséges tartalék tartományok maximális száma két. Az alkalmazási rétegbeli virtuális gépek üzembe helyezéséhez hozzá kell adnia egy hivatkozást a rendelkezésre állási készlet nevére és a közelségi csoport nevére, ahogy az itt látható:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

A központi telepítés eredménye a következő:
- Egy adott rendelkezésre állási zónában vagy Availability Zones található, az SAP-rendszerhez tartozó adatbázis-kezelői réteg és központi szolgáltatások.
- Egy SAP-alkalmazás rétege, amely a rendelkezésre állási csoportokon keresztül található ugyanazon Azure-adatközpontban, mint az adatbázis-kezelő virtuális gép vagy virtuális gépek.

> [!NOTE]
> Mivel egy adatbázis-kezelő virtuális gépet egy zónába és a második adatbázis-kezelő virtuális gépre helyez üzembe egy másik zónában, hogy magas rendelkezésre állási konfigurációt hozzon létre, az egyes zónákhoz eltérő közelségi csoportok szükségesek. Ugyanez érvényes az Ön által használt rendelkezésre állási csoportokra is.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Meglévő rendszer áthelyezése földrajzi elhelyezési csoportokba
Ha már rendelkezik telepített SAP-rendszerekkel, érdemes lehet optimalizálni néhány kritikus rendszer hálózati késését, és megkeresni az alkalmazás réteget és az adatbázis-kezelő réteget ugyanabban az adatközpontban. A közelségi elhelyezési csoportok nyilvános előzetese alatt törölnie kell a virtuális gépeket, és újakat kell létrehoznia, hogy áthelyezze a rendszer helyét a közelségi csoportokba. Jelenleg nem lehet leállítani a virtuális gépeket, és hozzájuk rendelni őket a közelségi csoportokba.


## <a name="next-steps"></a>További lépések
Tekintse meg a dokumentációt:

- [SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Előnézet Virtuális gépek üzembe helyezése a közelségi csoportokba az Azure CLI használatával](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Előnézet Virtuális gépek üzembe helyezése a közeli elhelyezési csoportokban a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterhelésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

