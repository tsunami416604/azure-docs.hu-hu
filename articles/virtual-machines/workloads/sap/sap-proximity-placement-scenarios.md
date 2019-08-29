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
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077990"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure-beli közelségi elhelyezési csoportok az optimális hálózati késés érdekében SAP-alkalmazások használatával
Az SAP NetWeaver vagy az SAP S/4HANA architektúrán alapuló SAP-alkalmazások érzékenyek az SAP-alkalmazási rétegek és az SAP-adatbázis szintjei közötti hálózati késésre. Ezen architektúrák érzékenységének oka az, hogy a legtöbb üzleti logikát az alkalmazási rétegben hajtják végre. Az üzleti logika végrehajtásának eredményeképpen az SAP-alkalmazás rétege nagy gyakorisággal kérdezi le az adatbázis-réteget. A legtöbb esetben a lekérdezések természete egyszerű. És gyakran az adatbázis-szinten is végrehajtható, kevesebb, mint 500 másodperc, vagy akár kevesebb is. A hálózaton töltött idő a lekérdezés az alkalmazás szintjéről az adatbázis szintjére való elküldéséhez, és az adatbázis-szinten visszaállított eredményhalmaz jelentős hatással van az üzleti folyamatok végrehajtásához szükséges idő szintjére. Ez a hálózati késésre való érzékenység az optimális hálózati késés eléréséhez szükséges idő az SAP-alapú üzembe helyezési projektekben. Az [SAP-Megjegyzés #1100926 – gyakori kérdések: Hálózati teljesítmény](https://launchpad.support.sap.com/#/notes/1100926/E), az SAP közzétett néhány iránymutatást a hálózati késés besorolásához.

Az egyik oldalon számos Azure-régióban az adatközpontok száma is megnőtt, és a Availability Zones bevezetésével is aktiválódik. A másik oldalon az ügyfelek, különösen a magas szintű SAP-rendszerek esetében, speciális virtuális gépeket használnak fel az M sorozatú családból vagy a HANA nagyméretű példányaiból. Olyan Azure-beli virtuálisgép-típusok, amelyek nem szerepelnek egy adott Azure-régió összes adatközpontjában. A két tendencia eredményeként az ügyfelek tapasztalt eseteket észleltek, ahol a hálózati késés nem volt az optimális tartományban, és bizonyos esetekben az SAP-rendszerek optimális teljesítményéhez vezetett.

A probléma megelőzése érdekében az Azure egy [Azure közelségi elhelyezési csoportnak](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)nevezett konstrukciót kínál. Ez az új funkció már számos különböző SAP-rendszer üzembe helyezésére használatos. Keresse meg a közelségi elhelyezési csoportok korlátozására hivatkozott cikket. Ez a cikk azokat a különböző SAP-forgatókönyveket ismerteti, amelyekben az Azure Proximity-elhelyezési csoportok használhatók vagy használhatók.

## <a name="what-are-proximity-placement-groups"></a>Mik a közelségi elhelyezési csoportok 
Az Azure Proximity-elhelyezési csoport egy logikai szerkezet, amely a definíciós fázisban egy Azure-régióhoz és egy Azure-erőforráscsoporthoz van kötve. A virtuális gépek üzembe helyezése során a rendszer a földrajzi elhelyezési csoportra hivatkozik:

- Az első üzembe helyezett Azure-beli virtuális gép az adatközpontban való rendezéshez. Az első virtuális gép olyan Anchor VM-ként tekinthető meg, amely az Azure-kiosztási algoritmusokon alapuló adatközpontban üzembe helyezhető, és végül egy adott Azure rendelkezésre állási zónához tartozó felhasználói definíciókkal együtt történik.
- Az összes olyan további virtuális gép, amely a közelségi elhelyezési csoportra hivatkozik, hogy az összes későbbi üzembe helyezett Azure-beli virtuális gépet ugyanabba az adatközpontba helyezi, ahol az első virtuális gép be lett helyezve.

> [!NOTE]
> Ha nincs olyan gazdagéphez tartozó hardver, amely egy adott virtuálisgép-típust futtatott ugyanabban az adatközpontban, amelyben az első virtuális gép be lett helyezve, akkor a megkövetelt virtuálisgép-típus üzembe helyezése sikertelen lesz, és a rendszer hibaüzenettel végződik. Ezek lehetnek olyan esetek, amikor több nem mainstream virtuális gép, például a GPU-k vagy a HPC virtuálisgép-típussal rendelkező virtuális gépek, például egy, az első virtuálisgép-típusként üzembe helyezett M sorozatú VM.

Egyetlen [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) több közelségi elhelyezési csoporttal is rendelkezhet. Azonban az egyik földrajzi elhelyezési csoport csak egy Azure-erőforráscsoport számára rendelhető hozzá.

A Proximity elhelyezési csoportok használatakor a következőket kell figyelembe vegye:

- Ha az SAP-rendszer legoptimálisabb teljesítményét hajtja végre, és a közelségi csoportok használatával korlátozza a rendszer egyetlen Azure-adatközpontját, akkor előfordulhat, hogy nem tudja egyesíteni az adott közelségi csoportba tartozó összes virtuálisgép-családot. Ennek az az oka, hogy a kizárólag bizonyos virtuálisgép-típusok futtatásához szükséges gazdagép-hardverek nem jelennek meg az adatközpontban az üzembe helyezett elhelyezési csoport "horgony VM"
- Egy ilyen SAP-rendszer életciklusa során kénytelen lehet áthelyezni a rendszert egy másik adatközpontba. Az ilyen áthelyezés kényszeríthető arra az esetre, ha úgy döntött, hogy a kibővíthető HANA adatbázis-kezelő rétege például négy csomópontról 16 csomópontra mozdul. Azonban nem áll rendelkezésre elegendő kapacitás ahhoz, hogy egy további 12 virtuális gépet szerezzen be az azonos adatközpontban már használt típussal.
- A hardver leszerelése miatt a Microsoft létrehozhat egy másik adatközpontban használt virtuálisgép-típus (ok) kapacitását ugyanazon adatközpont helyett. Ilyen esetben előfordulhat, hogy egy másik adatközpontba helyezi át a közelségi elhelyezési csoport összes virtuális gépet.


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Azure Proximity-elhelyezési csoportok SAP-rendszerekkel kizárólag Azure-beli virtuális gépek használatával
Az Azure-beli SAP NetWeaver és S/4HANA rendszerek többsége nem használ [HANA nagyméretű példányokat](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Az ilyen rendszerek üzembe helyezéséhez fontos, hogy az SAP-alkalmazás rétege és az adatbázis-kezelő réteg közötti optimális teljesítményt biztosítsa. Ehhez a rendszerhez csak az Azure Proximity-elhelyezési csoportot kell megadnia. A legtöbb ügyfél-telepítés esetén az ügyfelek egyetlen [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) építenek ki az SAP-rendszerekhez. Ilyen esetben 1:1 kapcsolat áll fenn például az éles ERP rendszererőforrás-csoportok és a hozzá tartozó földrajzi elhelyezési csoport között. Más üzembe helyezési esetekben az ügyfelek az erőforráscsoportot vízszintesen rendezik, és egyetlen erőforráscsoport összes éles rendszerét összegyűjtötték. Ebben az esetben az erőforráscsoport 1 – több kapcsolattal rendelkezik az éles környezetben futó SAP-rendszerek és az éles környezetben futó SAP ERP, SAP BW stb. között. El kell kerülni a több, vagy akár az összes SAP-éles vagy nem éles rendszerbe foglalt rendszerek egyetlen közelségi elhelyezési csoportban való árukapcsolását. A kivételek között, ahol kevés az SAP-rendszer vagy egy SAP-rendszer, és néhány környező alkalmazásnak kis késleltetésű hálózati kommunikációra van szüksége, érdemes lehet áthelyezni ezeket a rendszereket egy közelségi elhelyezési csoportba. Az ehhez hasonló javaslat oka az, hogy minél több rendszer van csoportosítva a közelségi elhelyezési csoportban, annál nagyobb az esélye:

- Ha olyan virtuálisgép-típusra van szüksége, amely nem futtatható az adott adatközpontban, a közelségi elhelyezési csoport a következőhöz van rögzítve:.
- Bizonyos nem mainstream virtuális gépek, például az M sorozatok erőforrásai nem teljesíthetők többé, ha további szoftverek hozzáadására van szükség, amikor az idő múlásával további szoftvereket vesz fel egy meglévő közelségi csoportba.

A leírt ideális használat a következőképpen néz ki:

![Proximity placemen-csoportok az összes Azure-beli virtuális géphez](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Ebben az esetben egyetlen SAP-rendszer van csoportosítva az egyik erőforráscsoporthoz, és mindegyikhez egy közelségi elhelyezési csoport tartoznak. Nem függ attól, hogy a HANA kibővített vagy az adatbázis-kezelői Felskálázási konfigurációkat használja-e.


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure Proximity-elhelyezési csoportok és HANA nagyméretű példányok
Azokban az esetekben, amikor az SAP-rendszerek némelyike az alkalmazási réteg [Hana nagyméretű példányaira](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) támaszkodik, a hálózati késésnek a Hana nagyméretű példány egysége és az Azure-beli virtuális gépek közötti jelentős javulása akkor érhető el, ha olyan Hana nagyméretű példány-egységeket használ, amelyek a 4. [változatban vagy bélyegzőben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)van telepítve. Az egyik újítás az, hogy a HANA nagyméretű példányai egységei, ahogy az üzembe helyezésük megtörtént, már üzembe helyezi a közelségi elhelyezési csoportot. Az alkalmazási rétegbeli virtuális gépek üzembe helyezéséhez használhatja az adott közelségi csoportot. Ennek eredményeképpen ezeket a virtuális gépeket ugyanabban az adatközpontban helyezik üzembe, amely a HANA nagyméretű példányának egységét üzemelteti.

Annak megállapításához, hogy a HANA nagyméretű példánya egysége 4. változatban van-e telepítve, olvassa el az [Azure HANA nagyméretű példányok vezérlése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)a Azure Portalon című cikket. A HANA nagyméretű példány-egység attribútumainak áttekintése során megtalálhatja a közelségi elhelyezési csoport nevét is, ahogy azt a HANA nagyméretű példány-egység üzembe helyezési ideje hozta létre. Az attribútumok áttekintésében megjelenő név a közelségi elhelyezési csoport neve, amelynek használatával üzembe helyezheti az alkalmazási rétegbeli virtuális gépeket a-ben.

A csak az Azure-beli virtuális gépeket használó SAP-rendszerekkel szemben a HANA nagyméretű példányok használatakor a döntés, hogy hány [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) kell használni. A [Hana nagyméretű példány-bérlő](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) összes Hana nagyméretű példány-egysége egyetlen Azure-erőforráscsoport szerint van csoportosítva az [itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)leírtak szerint. Hacsak nem szeretné, hogy a központi telepítés különböző bérlők számára legyen elkülönítve, például üzemi és nem éles környezetben, illetve egyes rendszerek esetében, az összes HANA nagyméretű példány-egység egy HANA nagyméretű példány-bérlőn lesz üzembe helyezhető, amely ismét 1:1-kapcsolattal rendelkezik az Azure-ban erőforráscsoport. Míg az egyetlen egység külön közelségi elhelyezési csoporttal fog rendelkezni. 

Ennek eredményeképpen az egyes bérlők számára az Azure-erőforráscsoportok és a közelségi elhelyezési csoportok közötti csoportosítás a következőképpen fog kinézni:

![Proximity elhelyezési csoportok az összes Azure-beli virtuális géphez](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>Rövid példa az Azure Proximity-elhelyezési csoportok üzembe helyezésére
Annak bemutatásához, hogyan használhatja az Azure Proximity-elhelyezési csoportokat a virtuális gép üzembe helyezéséhez, Itt láthatja a PowerShell-parancsok listáját, amely bemutatja az Azure közelségi csoportok használatának első kis gyakorlatát.

Miután bejelentkezett a Azure Cloud Shellba, a [](https://azure.microsoft.com/features/cloud-shell/) következő lépéssel tekintheti meg, hogy a megfelelő Azure-előfizetésben kívánja-e telepíteni a parancsot:

<pre><code>
Get-AzureRmContext
</code></pre>

Ha másik előfizetésre van szüksége, a következő parancs végrehajtásával teheti meg:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Harmadik lépésként létre kell hoznia egy új Azure-erőforráscsoportot a következő paranccsal:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Az új Proximity-elhelyezési csoportot most már az alábbiakkal is létrehozhatja:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Most már megkezdheti az első virtuális gép üzembe helyezését a közelségi csoportba egy olyan paranccsal, mint például:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

A fenti paranccsal egy Windows-alapú virtuális gép van telepítve. A virtuális gép sikeres üzembe helyezése után a közelségi elhelyezési csoport adatközponti hatóköre az Azure-régióban van definiálva. Az összes későbbi, a közelségi elhelyezési csoportra hivatkozó, az utolsó parancsnak megfelelően üzembe helyezett virtuálisgép-telepítés ugyanabban az Azure-adatközpontban lesz telepítve, amíg a virtuális gép típusa az adott adatközpontba helyezett hardveren üzemeltethető, és/vagy a virtuálisgép-típus kapacitása elérhető.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Rendelkezésre állási csoportok és Availability Zonesek egyesítése Proximity elhelyezési csoportokkal 
Availability Zones használata az SAP-rendszer központi telepítéséhez, az egyik hátránya az, hogy az SAP-alkalmazás rétege nem állítható be az adott zónán belüli rendelkezésre állási csoportok használatával. Mivel azt szeretné, hogy az SAP-alkalmazás rétege ugyanabban a zónában legyen telepítve, mint az adatbázis-kezelő réteg, és egy rendelkezésre állási zónára és egy rendelkezésre állási csoportra hivatkozik, ha egyetlen virtuális gép telepítésekor nem támogatott, az alkalmazási réteget a zónákra hivatkozó módon kellett telepíteni. így elveszíti az alkalmazási rétegbeli virtuális gépek különböző frissítési és meghibásodási tartományokban való terjesztésének lehetőségét. A közelségi elhelyezési csoportok segítségével leküzdheti ezt a korlátozást. Az üzembe helyezések sorrendjének a következőképpen kell kinéznie:

- Közelségi elhelyezési csoport létrehozása
- A "horgony VM", általában az adatbázis-kezelő kiszolgáló üzembe helyezése egy bizonyos Azure rendelkezésre állási zónára hivatkozva
- Hozzon létre egy rendelkezésre állási csoportot, amely az Azure közelségi csoportjára hivatkozik (lásd alább)
- Az alkalmazás rétegbeli virtuális gépek üzembe helyezése a rendelkezésre állási csoport és a közelségi elhelyezési csoport hivatkozásával

A fentiekben bemutatott első virtuális gép telepítése helyett az Azure rendelkezésre állási zónára és a közelségi csoportra hivatkozik a virtuális gép üzembe helyezésekor, például:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

A virtuális gép sikeres üzembe helyezése, amely egy Azure-beli rendelkezésre állási zónában futtatja az SAP-rendszer adatbázis-példányát, a közelségi elhelyezési csoport hatókörét az Ön által meghatározott rendelkezésre állási zónát képviselő adatközpontok egyikére rögzíti. .

Tegyük fel, hogy a központi szolgáltatások virtuális gépei ugyanúgy települnek, mint az adatbázis-kezelő virtuális gépek, az adatbázis-kezelő virtuális gépek és az azonos földrajzi elhelyezési csoportok esetében pedig ugyanarra a zónára hivatkozik. A következő lépésben létre kell hoznia az SAP-rendszer alkalmazási rétegéhez használni kívánt rendelkezésre állási készlet (eke) t.
A közelségi elhelyezési csoportot definiálni és létrehozni kell. A rendelkezésre állási csoport létrehozásához szükséges parancshoz további hivatkozást kell megadni a közelségi elhelyezési csoport AZONOSÍTÓjának (nem a név). A közelségi elhelyezési csoport AZONOSÍTÓját az alábbiakkal érheti el:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

A rendelkezésre állási csoport létrehozásakor további paramétereket kell figyelembe vennie, ha felügyelt lemezeket használ (az alapértelmezett beállítás, ha másképp van megadva) és a közelségi elhelyezési csoportok:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Ideális esetben három tartalék tartományt kell használnia. A támogatott tartalék tartományok száma azonban régiónként eltérő lehet. Ebben az esetben az adott régiók számára lehetséges, hogy a tartalék tartományok maximális száma két volt. Az alkalmazási rétegbeli virtuális gépek üzembe helyezéséhez hozzá kell adnia egy hivatkozást a rendelkezésre állási készlet nevére és a közelségi csoport nevére, ahogy az itt látható:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Ennek a folyamatnak a végeredménye egy olyan adatbázis-kezelő réteg és a SAP-rendszer központi szolgáltatásai, amely egy adott rendelkezésre állási zónában és egy olyan SAP-alkalmazási rétegben található, amely ugyanabban az Azure-adatközpontban van, mint az adatbázis-kezelő VM (k). telepített.

> [!NOTE]
> Amikor egy adatbázis-kezelő virtuális gépet telepít egy zónába és a második adatbázis-kezelő virtuális gépre egy másik zónába, hogy magas rendelkezésre állási konfigurációt hozzon létre, minden zónához különböző közelségi elhelyezési csoportokat fog megkövetelni. A használni kívánt rendelkezésre állási csoport azonos si-értéke

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>Meglévő rendszer beszerzése az Azure közelségi elhelyezési csoportjaiba
A már telepített SAP-rendszerek esetében érdemes lehet optimalizálni néhány kritikus rendszer hálózati késését, és megkeresni az alkalmazás réteget és az adatbázis-kezelő réteget ugyanabban az adatközpontban. A közelségi csoportok funkciójának nyilvános előzetes verziójának szakaszában a virtuális gépek törlése és a virtuális gépek új létrehozása szükséges ahhoz, hogy ilyen áthelyezést lehessen végezni a közelségi csoportokba. A funkció ezen szakaszában nem elég jó ahhoz, hogy leállítsa a virtuális gépeket, hogy hozzá lehessen rendelni ezeket a leállítási virtuális gépeket a közeli elhelyezési csoportokhoz.


## <a name="next-steps"></a>További lépések
Olvassa el a dokumentációt:

- [SAP számítási feladatok az Azure tervezési és üzembe helyezési ellenőrzőlistáján](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Előnézet Virtuális gépek üzembe helyezése a közelségi csoportokba az Azure CLI használatával](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Előnézet Virtuális gépek üzembe helyezése a közeli elhelyezési csoportokban a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

