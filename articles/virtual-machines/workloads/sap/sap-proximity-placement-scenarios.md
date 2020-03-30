---
title: Azure közelségi elhelyezési csoportok SAP-alkalmazásokhoz | Microsoft dokumentumok
description: Az SAP üzembe helyezési forgatókönyveit ismerteti az Azure közelségi elhelyezési csoportokkal
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
ms.date: 01/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01ce1599f86082aef3ff53d298cc53896074af66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277595"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure közelségi elhelyezési csoportok az SAP-alkalmazásokkal való optimális hálózati késés érdekében
Az SAP NetWeaver vagy AZ SAP S/4HANA architektúrán alapuló SAP-alkalmazások érzékenyek az SAP alkalmazásréteg és az SAP-adatbázisréteg közötti hálózati késésre. Ez az érzékenység az alkalmazásrétegen futó üzleti logika nagy részének eredménye. Mivel az SAP-alkalmazásréteg az üzleti logikát futtatja, lekérdezéseket ad ki az adatbázisrétegnek magas gyakorisággal, másodpercenként több ezer vagy több tízezer sebességgel. A legtöbb esetben ezek a lekérdezések jellege egyszerű. Gyakran 500 mikroszekundum vagy kevesebb alatt futtathatók az adatbázis-szinten.

A hálózaton az ilyen lekérdezés nek az alkalmazásrétegből az adatbázisrétegbe küldésére és az eredménykészlet visszafogadására fordított idő jelentős hatással van az üzleti folyamatok futtatásához szükséges időre. Ez a hálózati késéssel szembeni érzékenység az oka annak, hogy optimális hálózati késést kell elérnie az SAP központi telepítési projektjeiben. Lásd: [SAP Megjegyzés #1100926 – GYAKORI KÉRDÉSEK: A hálózati teljesítmény](https://launchpad.support.sap.com/#/notes/1100926/E) a hálózati késés besorolására vonatkozó útmutatásért.

Számos Azure-régióban az adatközpontok száma nőtt. Ezt a növekedést a rendelkezésre állási zónák bevezetése is kiváltotta. Ugyanakkor az ügyfelek, különösen a csúcskategóriás SAP-rendszerek, speciális virtuálisgép-skus-t használnak az M sorozatú családban, vagy hana nagy példányokat. Ezek az Azure virtuálisgép-típusok nem érhetők el egy adott Azure-régió összes adatközpontjában. E két tendencia miatt az ügyfelek olyan hálózati késést tapasztaltak, amely nem az optimális tartományban van. Bizonyos esetekben ez a késés az SAP-rendszerek optimálistól elmaradó teljesítményét eredményezi.

A problémák megelőzése érdekében az Azure [közelségelhelyezési csoportokat](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)kínál. Ezt az új funkciót már használták különböző SAP-rendszerek üzembe helyezéséhez. A közelségi elhelyezési csoportokra vonatkozó korlátozásokat lásd az e bekezdés elején említett cikkben. Ez a cikk ismerteti az SAP-forgatókönyvek, amelyben az Azure közelségi elhelyezési csoportok at lehet vagy kell használni.

## <a name="what-are-proximity-placement-groups"></a>Mik azok a közelségelhelyezési csoportok? 
Az Azure-közelség elhelyezési csoport egy logikai konstrukció. Ha egy definiálva van, akkor egy Azure-régióhoz és egy Azure-erőforráscsoporthoz van kötve. Virtuális gépek telepítésekor a közelségelhelyezési csoportra a következő hivatkozás hivatkozik:

- Az adatközpontban üzembe helyezett első Azure-gép. Az első virtuális gép egy "hatókör virtuális gép", amely egy adatközpontban üzembe helyezett, az Azure-foglalási algoritmusok alapján, amelyek végül egy adott rendelkezésre állási zóna felhasználói definícióival kombinálva kerülnek.
- Minden ezt követő virtuális gép üzembe helyezte, amely a közelség-elhelyezési csoportra hivatkozik, hogy az összes később üzembe helyezett Azure-beli virtuális gépet ugyanabban az adatközpontban helyezze el, mint az első virtuális gépet.

> [!NOTE]
> Ha nincs olyan üzembe helyezett gazdagép, amely egy adott virtuális géptípust futtathat na abban az adatközpontban, ahol az első virtuális gépet helyezték el, a kért virtuálisgép-típus üzembe helyezése nem fog sikerülni. Hibaüzenetet fog kapni.

Egyetlen [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) hozhatja hozzá több közelség-elhelyezési csoportok. De egy közelség elhelyezési csoport csak egy Azure-erőforráscsoporthoz rendelhető hozzá.

Közelségelhelyezési csoportok használatakor tartsa szem előtt az alábbi szempontokat:

- Ha az SAP-rendszer optimális teljesítményére törekszik, és a közelségi elhelyezési csoportok használatával egyetlen Azure-adatközpontra korlátozza magát, előfordulhat, hogy nem tudja kombinálni az elhelyezési csoporton belül a virtuálisgép-családok összes típusát. Ezek a korlátozások azért fordulnak elő, mert a gazdagép hardver, amely egy adott virtuális gép típus futtatásához szükséges, előfordulhat, hogy nem található az adatközpontban, amelyhez az elhelyezési csoport "hatókörrel vett virtuális gép" telepítve volt.
- Egy ilyen SAP-rendszer életciklusa során előfordulhat, hogy kénytelen a rendszer áthelyezése egy másik adatközpontba. Ez az áthelyezés akkor szükséges, ha úgy dönt, hogy a kibővített HANA DBMS-réteg például négy csomópontról 16 csomópontra kell lépnie, és nincs elég kapacitás az adatközpontban használt típushoz képest további 12 virtuális gép lekérni.
- A hardver leszerelése miatt a Microsoft előfordulhat, hogy kapacitást épít ki egy másik adatközpontban használt virtuálisgép-típushoz, nem pedig az eredetileg használt hoz. Ebben a forgatókönyvben előfordulhat, hogy át kell helyeznie az összes közelség-elhelyezési csoport virtuális gépeit egy másik adatközpontba.

## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Közelségelhelyezési csoportok olyan SAP-rendszerekkel, amelyek csak Azure virtuális gépeket használnak
A legtöbb SAP NetWeaver és S/4HANA rendszer telepítések az Azure-ban nem használ [HANA nagy példányok.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) A hana nagy példányokat nem használó központi telepítések esetében fontos, hogy optimális teljesítményt biztosítson az SAP-alkalmazásréteg és a DBMS-réteg között. Ehhez csak a rendszerhez definiáljon egy Azure-közelségi elhelyezési csoportot.

A legtöbb ügyfél-telepítésben az ügyfelek egyetlen [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) hoznak létre az SAP-rendszerekhez. Ebben az esetben egy az egyhez kapcsolat van például a termelési ERP-rendszer erőforráscsoportja és a közelségi elhelyezési csoport között. Más esetekben az ügyfelek horizontálisan rendszerezik erőforráscsoportaikat, és egyetlen erőforráscsoportba gyűjtik az összes termelési rendszert. Ebben az esetben egy-a-többhöz kapcsolat az erőforráscsoport éles SAP-rendszerek és több közelség elhelyezési csoportok az éles SAP ERP, SAP BW, és így tovább.

Ne több SAP-termelési vagy nem éles rendszer kötegelését egyetlen közelségi elhelyezési csoportban. Ha egy kis számú SAP-rendszerek vagy egy SAP-rendszer és néhány környező alkalmazások alacsony késleltetésű hálózati kommunikáció, érdemes lehet ezeket a rendszereket egy közelség elhelyezési csoportba. Kerülje a rendszerek kötegeit, mert minél több rendszert csoportosít egy közelségelhelyezési csoportban, annál nagyobb az esélye:

- Hogy szüksége van egy virtuális gép típus, amely nem futtatható az adott adatközpontban, amelybe a közelség elhelyezési csoport hatóköre.
- Hogy a nem általános virtuális gépek erőforrásai, például az M sorozatú virtuális gépek, végül nem teljesíthetők, ha többre van szüksége, mert idővel szoftvert ad hozzá egy közelségelhelyezési csoporthoz.

Így néz ki az ideális konfiguráció:

![Közelségelhelyezési csoportok csak Azure virtuális gépekkel](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

Ebben az esetben egyetlen SAP-rendszerek vannak csoportosítva egy erőforráscsoportban minden, egy-egy közelség elhelyezési csoport. Nincs függőség attól, hogy hana horizontális felskálázási vagy DBMS horizontális felskálázási konfigurációk.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Közelségelhelyezési csoportok és HANA nagy példányok
Ha az SAP-rendszerek egy része [hana nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) az alkalmazásréteg, a hana nagy példányok egység és az Azure virtuális gépek közötti jelentős javulást tapasztalhat, ha hana nagy példányok egységek, amelyek a [4-es verzió4-es sorok vagy bélyegzők.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Az egyik fejlesztés az, hogy a HANA nagy példányok egységek, ahogy ők üzembe helyezett, üzembe helyezett egy közelség elhelyezési csoport. Ezt a közelségi elhelyezési csoportot használhatja az alkalmazásréteg virtuális gépei üzembe helyezéséhez. Ennek eredményeképpen ezek a virtuális gépek ugyanabban az adatközpontban lesznek telepítve, amely a HANA nagy példányok egységét üzemelteti.

Annak megállapításához, hogy a HANA nagy példányok egység telepítve van-e a Revision 4 bélyegző vagy sor, ellenőrizze a cikk [et Azure HANA nagy példányok ellenőrzés az Azure Portalon keresztül.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit) A HANA nagy példányok egység attribútumok áttekintése, azt is meghatározhatja a nevét a közelség elhelyezési csoport, mert akkor jött létre, amikor a HANA nagy példányok egység üzembe helyezése. Az attribútumok áttekintésében megjelenő név annak a közelségelhelyezési csoportnak a neve, amelybe az alkalmazásréteg virtuális gépeit telepítenie kell.

A csak Azure virtuális gépeket használó SAP-rendszerekkel összehasonlítva hana nagy példányok használata esetén kisebb rugalmasságot biztosít annak eldöntésében, hogy hány [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) használjon. A HANA nagy példányok bérlői hana nagy [példányok egységei](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms) egyetlen erőforráscsoportba vannak csoportosítva, a cikk leírása [szerint.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal) Ha nem telepíti a különböző bérlők elkülönítésére, például az éles és nem éles rendszerek vagy más rendszerek, a HANA nagy példányok egységek üzembe helyezése egy HANA nagy példányok bérlő. Ez a bérlő egy-az-egyhez kapcsolatban áll egy erőforráscsoporttal. De egy külön közelség elhelyezési csoport határozza meg az egyes egységek.

Ennek eredményeképpen az Azure-erőforráscsoportok és az egyetlen bérlő közelségi elhelyezési csoportjai közötti kapcsolatok az alábbiak szerint jelennek meg:

![Közelségelhelyezési csoportok és HANA nagy példányok](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Példa a közelségi elhelyezési csoportokkal való telepítésre
Az alábbiakban néhány PowerShell-parancs, amely segítségével üzembe helyezheti a virtuális gépek et az Azure közelségi elhelyezési csoportok.

Az első lépés, miután bejelentkezett az [Azure Cloud Shellbe,](https://azure.microsoft.com/features/cloud-shell/)ellenőrizze, hogy az üzembe helyezéshez használni kívánt Azure-előfizetésben van-e:

<pre><code>
Get-AzureRmContext
</code></pre>

Ha másik előfizetésre kell váltania, ezt a parancs futtatásával teheti meg:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Hozzon létre egy új Azure-erőforráscsoportot a következő parancs futtatásával:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

A parancs futtatásával hozza létre az új közelségelhelyezési csoportot:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Telepítse az első virtuális gép a közelség elhelyezési csoport ban egy parancs, mint ez:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Az előző parancs windows-alapú virtuális gép központi telepítéséhez. Miután ez a virtuális gép telepítése sikeres, a közelség-elhelyezési csoport adatközpont-hatóköre az Azure-régióban van definiálva. A közelségelhelyezési csoportra hivatkozó összes további virtuálisgép-telepítés az előző parancsban látható módon ugyanabban az Azure-adatközpontban lesz üzembe helyezve, feltéve, hogy a virtuálisgép-típus az adott adatközpontban elhelyezett hardveren tárolható, és az adott virtuálisgép-típus kapacitása Elérhető.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>A rendelkezésre állási csoportok és a rendelkezésre állási zónák és a közelségi elhelyezési csoportok egyesítése
Az SAP-rendszerközponti telepítések rendelkezésre állási zónák használatának egyik hátránya, hogy nem telepítheti az SAP-alkalmazásréteget az adott zónán belüli rendelkezésre állási csoportok használatával. Azt szeretné, hogy az SAP-alkalmazásréteg ugyanabban a zónában legyen telepítve, mint a DBMS-réteg. Egyetlen virtuális gép üzembe helyezésekor nem támogatott egy rendelkezésre állási zóna és egy rendelkezésre állási csoport hivatkozása. Így korábban egy zóna hivatkozásával kellett telepítenie az alkalmazásréteget. Elvesztette a képességét, hogy győződjön meg arról, hogy az alkalmazásréteg virtuális gépek között különböző frissítési és hiba tartományok között vannak elosztva.

A közelségi elhelyezési csoportok használatával megkerülheti ezt a korlátozást. Itt van a telepítési sorrend:

- Hozzon létre egy közelségelhelyezési csoportot.
- Telepítse a horgony virtuális gép, általában a DBMS-kiszolgáló, hivatkozva egy rendelkezésre állási zóna.
- Hozzon létre egy rendelkezésre állási készletet, amely az Azure közelségi csoportra hivatkozik. (Lásd a cikk későbbi részében található parancsot.)
- Telepítse az alkalmazásréteg virtuális gépeit a rendelkezésre állási készletre és a közelségelhelyezési csoportra való hivatkozással.

Ahelyett, hogy üzembe helyezne az első virtuális gép, ahogy azt az előző szakaszban, hivatkozhat egy rendelkezésre állási zóna és a közelség elhelyezési csoport, amikor telepíti a virtuális gép:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

A virtuális gép sikeres üzembe helyezése az SAP-rendszer adatbázispéldányát egyetlen rendelkezésre állási zónában üzemeltetné. A közelségelhelyezési csoport hatóköre a megadott rendelkezésre állási zónát képviselő adatközpontok egyikéhez van rögzítve.

Tegyük fel, hogy a központi szolgáltatások virtuális gépeit ugyanúgy telepíti, mint a DBMS virtuális gépeket, ugyanarra a zónára vagy zónákra és ugyanazokra a közelség-elhelyezési csoportokra hivatkozva. A következő lépésben létre kell hoznia az SAP-rendszer alkalmazásrétegéhez használni kívánt rendelkezésre állási csoportokat.

Meg kell határoznia és létre kell hoznia a közelségelhelyezési csoportot. A rendelkezésre állási csoport létrehozásához szükséges parancs további hivatkozást igényel a közelségelhelyezési csoport azonosítójára (nem a névre). A közelségelhelyezési csoport azonosítóját a következő paranccsal szerezheti be:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

A rendelkezésre állási csoport létrehozásakor további paramétereket kell figyelembe vennie a felügyelt lemezek (alapértelmezett, hacsak másként nem) és a közelségelhelyezési csoportok használatakor:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Ideális esetben három tartalék tartományt kell használnia. A támogatott tartalék tartományok száma azonban régiónként változhat. Ebben az esetben az adott régiókban lehetséges tartalék tartományok maximális száma kettő. Az alkalmazásréteg virtuális gépei üzembe helyezéséhez hozzá kell adnia egy hivatkozást az elérhetőségi csoport nevére és a közelségelhelyezési csoport nevére, ahogy az itt látható:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

A telepítés eredménye:
- Egy DBMS-réteg és központi szolgáltatások az SAP-rendszerhez, amely egy adott rendelkezésre állási zónában vagy rendelkezésre állási zónákban található.
- Egy SAP-alkalmazásréteg, amely a rendelkezésre állási csoportok on the same Azure datacenters, mint a DBMS virtuális gép vagy virtuális gépek.

> [!NOTE]
> Mivel egy DBMS virtuális gépet telepít egy zónába, a második DBMS virtuális gépet pedig egy másik zónába a magas rendelkezésre állású konfiguráció létrehozásához, minden zónához más-más közelség-elhelyezési csoportra van szükség. Ugyanez igaz minden rendelkezésre állási készlet, amely a használt.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Meglévő rendszer áthelyezése közelségelhelyezési csoportokba
Ha már telepítettSAP-rendszerekkel rendelkezik, érdemes lehet optimalizálni néhány kritikus rendszer hálózati késését, és megkeresni az alkalmazásréteget és a DBMS-réteget ugyanabban az adatközpontban. Ha egy teljes Azure rendelkezésre állási készlet virtuális gépeit egy már hatókörrel ellátott meglévő közelség-elhelyezési csoportba szeretné áthelyezni, le kell állítania az összes virtuális gépet a rendelkezésre állási csoportból, és hozzá kell rendelnie a rendelkezésre állási készletet a meglévő közelség-elhelyezési csoporthoz az Azure Portalon keresztül, PowerShell vagy CLI. Ha azt szeretné, hogy egy virtuális gép, amely nem része a rendelkezésre állási készlet egy meglévő közelség elhelyezési csoportba, csak le kell állítania a virtuális gép, és hozzá kell rendelniegy meglévő közelség-elhelyezési csoporthoz. 


## <a name="next-steps"></a>További lépések
Nézze meg a dokumentációt:

- [SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [Előzetes verzió: Virtuális gépek telepítése közelségi elhelyezési csoportokba az Azure CLI használatával](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [Előzetes verzió: Virtuális gépek telepítése a közelségi elhelyezési csoportokhoz a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [Az Azure Virtual Machines DBMS üzembe helyezésének szempontjai az SAP-számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

