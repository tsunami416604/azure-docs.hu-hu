---
title: Az Azure Service Fabric-fürtök létrehozása a Windows Server és Linux rendszeren |} A Microsoft Docs
description: Service Fabric-fürtök a Windows Server és Linux rendszerű, mely azt jelenti, hogy lesz üzembe helyezéséhez és gazdagép Service Fabric-alkalmazások bárhol futtatható Windows Server vagy Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: a968b173357bf8bcb83990b891f38306895b4ca8
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967271"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Service Fabric áttekintése az Azure-fürtök
Service Fabric-fürt, amelybe mikroszolgáltatásokat helyezhet üzembe és felügyelhet virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete áll. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürt csomópontja nevezzük. Fürtök méretezheti több ezer csomóponton. Ha új csomópontot ad hozzá a fürtöt, akkor a Service Fabric csomópontok megnövekedett számú rebalances a szolgáltatás partíció replikák és példányok. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés memória való hozzáféréshez. Ha a fürt csomópontjainak hatékonyan nincsenek használatban, csökkentheti a fürtben található csomópontok számát. A Service Fabric rebalances a partíciók replikáit és példányainak újra a csomópontokat, hogy a hardver jobban kihasználja a csomópontokon csökkent számú.

A csomópont típusa meghatározza a méretét, számát és tulajdonságait, több csomópontok (virtuális gépek) a fürtben. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik. 

## <a name="cluster-components-and-resources"></a>Fürt komponensek és erőforrások
Az Azure Service Fabric-fürt egy Azure-erőforrás, amely használja, és kommunikál a többi Azure-erőforrások:
* Virtuális gépek és virtuális hálózati adapterek
* virtuálisgép-méretezési csoportok
* virtuális hálózatokkal
* Terheléselosztók
* tárfiókok
* public IP addresses

![Service Fabric-fürt][Image]

### <a name="virtual-machine"></a>Virtuális gép
A [virtuális gép](/azure/virtual-machines/) hogy része egy fürtnek csomópontoknak nevezzük, bár technikailag egy fürt csomópontja a Service Fabric futtatókörnyezet folyamat során a rendszer. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). Csomópontok jellemzőkkel, például rendelkeznek [elhelyezési tulajdonságokat](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Minden számítógép vagy virtuális gép rendelkezik egy automatikusan induló szolgáltatás *FabricHost.exe*, amely elindítja a rendszerindítás közben fut, és elindítja a két végrehajtható fájlok, *Fabric.exe* és *FabricGateway.exe* , amely alkotják a csomópontot. Éles környezet egy csomópont egy fizikai vagy virtuális gépen. Tesztelési forgatókönyvek, több példányát futtatja egy egyetlen gépen vagy a virtuális gép több csomópont is üzemeltethet *Fabric.exe* és *FabricGateway.exe*.

Minden virtuális Géphez társítva egy virtuális hálózati kártya (NIC) és minden egyes hálózati adapter magánhálózati IP-cím van hozzárendelve.  Virtuális gép hozzá van rendelve egy virtuális hálózatot és a helyi terheléselosztó keresztül a hálózati adaptert.

A fürt összes virtuális gép virtuális hálózat kerülnek.  Ugyanabban a csomópont típusa vagy méretezési csoportban az összes csomópont kerülnek, a virtuális hálózat ugyanabban az alhálózatban.  Ezek a csomópontok csak magánhálózati IP-címekkel rendelkeznek, nem közvetlenül címezhetővé válnak, a virtuális hálózaton kívülről.  Ügyfelek hozzáférhetnek a csomópontokon szolgáltatások az Azure load balanceren keresztül.

### <a name="scale-setnode-type"></a>Skálázási készlet vagy csomópont típusa
A fürt létrehozásakor megadhat egy vagy több csomópont típusa.  A csomópontok vagy virtuális gépek, a csomópont típusa van, az azonos méretű és jellemzőkkel, például a processzorok, memória, a lemezek és lemez i/o-számát.  Egy csomópont típusa lehet például a kis méretű, előtér-porttal rendelkező virtuális gépek az interneten nyissa meg a másik csomópont típusa lehet nagy, a háttérbeli virtuális gépek, amelyek az adatok feldolgozása közben. Az Azure-fürtökben, mindegyik csomóponttípus le van képezve egy [virtuálisgép-méretezési csoport](/azure/virtual-machine-scale-sets/).

Használhatja a méretezési csoportok üzembe helyezése és kezelése a virtuális gépek csoportként gyűjteménye. Mindegyik csomóponttípus, amelyeket egy Azure Service Fabric-fürtöt állít be egy külön méretezési csoportot. A Service Fabric-futtatókörnyezet minden virtuális gép az Azure Virtuálisgép-bővítmények segítségével a méretezési alakzatot van georeplikációra. Is egymástól függetlenül mindegyik csomóponttípus kisebbre vagy nagyobbra méretezhetők, módosítsa az operációs rendszer Termékváltozata a fürt minden csomópontján fut, más-más részhalmazához nyitott portokkal rendelkezik és használni a különböző kapacitási mérőszámot. Egy méretezési csoportot öt rendelkezik [frissítési tartományok](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) és öt [tartalék tartományok](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) és legfeljebb 100 virtuális gép lehet.  Több mint 100 csomópont virtuálisgép-fürtöket hoz létre több méretezési csoportok/node-típust hozzon létre.

> [!IMPORTANT]
> A fürt csomóponttípusok száma és az egyes csomóponttípusok (mérete, elsődleges, internetre irányuló, virtuális gépeket, stb. száma) tulajdonságainak kiválasztása a fontos feladat.  További információkért olvassa el [fürt kapacitástervezésének szempontjai](service-fabric-cluster-capacity.md).

További információkért olvassa el [Service Fabric-csomóponttípusok és virtuálisgép-méretezési csoportokban](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Virtuálisgép-példányokhoz csatlakoznak mögött egy [az Azure load balancer](/azure/load-balancer/load-balancer-overview), amely társítva van egy [nyilvános IP-cím](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) és a DNS-címkét.  Ha a fürt üzembe helyezése  *&lt;clustername&gt;*, a DNS-név  *&lt;clustername&gt;.&lt; hely&gt;. cloudapp.Azure.com formát követi* a DNS-címkét a méretezési csoport előtt lévő terheléselosztó társítva van.

Egy fürtben lévő virtuális gépek csak rendelkeznek [magánhálózati IP-címek](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  Felügyeleti és a szolgáltatás forgalmat továbbít a nyilvános terheléselosztót.  Hálózati adatforgalmat, ezek a gépek (az ügyfelek az adott csomópontok/példányok kapcsolódni) NAT-szabályok vagy terheléselosztási szabályok (a forgalom irányul, virtuális gépek ciklikus időszeletelés).  Load balancer társított nyilvános IP-címre egy DNS-névvel rendelkezik a következő formátumban:  *&lt;clustername&gt;.&lt; hely&gt;. cloudapp.Azure.com formát követi*.  Nyilvános IP-cím egy másik Azure-erőforrás az erőforráscsoportban.  Ha egy fürtben több csomóponttípus definiálja, egy terheléselosztó jön létre minden egyes csomópont típusa és méretezési csoportot. Vagy beállíthatja, hogy egyetlen terheléselosztó több csomópont esetében.  Az elsődleges csomóponttípushoz rendelkezik a DNS-címke  *&lt;clustername&gt;.&lt; hely&gt;. cloudapp.Azure.com formát követi*, más csomóponttípusok rendelkeznek a DNS-címke  *&lt;clustername&gt;-&lt;nodetype&gt;.&lt; hely&gt;. cloudapp.Azure.com formát követi*.

### <a name="storage-accounts"></a>Tárfiókok
Minden egyes csomópont fürttípus támogatja egy [Azure storage-fiók](/azure/storage/common/storage-introduction) és felügyelt lemezek.

## <a name="cluster-security"></a>Fürtbiztonság
Service Fabric-fürt saját erőforrás.  A feladata biztonságossá tétele a-fürtök segítségével megakadályozhatja a jogosulatlan felhasználókat csatlakozik hozzájuk. Biztonságos fürt különösen fontos, ha a fürtön futtatnak éles számítási feladatokat. 

### <a name="node-to-node-security"></a>Csomópontok közötti biztonsági
Csomópontok közötti biztonsági védi a virtuális gépek és a egy fürtben számítógépek közötti kommunikációt. A biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakoztatáshoz jogosult számítógépek részt vehetnek-üzemeltető, alkalmazások és szolgáltatások a fürtben. Service Fabric-fürt biztonságossá tétele és adja meg az alkalmazás biztonsági funkciók X.509-tanúsítványokat használ.  Egy fürt tanúsítványra szükség a fürt forgalmának biztonságossá tétele és a fürt és a kiszolgáló hitelesíthető.  Önkiszolgáló aláírt-tanúsítványok a tesztfürtök esetében használhatók, de egy megbízható hitelesítésszolgáltatótól származó tanúsítványt használjon biztonságos éles fürtökben.

További információkért olvassa el [csomópontok biztonság](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Ügyfél-csomópont biztonsági
Ügyfél-csomópont biztonsági hitelesíti az ügyfeleket, és segít az ügyfél és a fürt egyes csomópontjain közötti biztonságos kommunikációhoz. Az ilyen típusú biztonsági segítségével biztosíthatja, hogy csak a jogosult felhasználók hozzáférését a fürt és a fürtön üzembe helyezett alkalmazások. Az ügyfelek kapcsolódnak egyedileg azonosított keresztül vagy X.509 tanúsítvány biztonsági hitelesítő adataik. Nem kötelező ügyféltanúsítványok tetszőleges számú használható a fürt rendszergazdai vagy felhasználói ügyfelek hitelesítéséhez.

Ügyfél-tanúsítványok mellett az Azure Active Directory is konfigurálhatja a fürt az ügyfelek hitelesítéséhez.

További információkért olvassa el [ügyfél a csomópont közötti biztonsághoz](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérléssel Azure-erőforrások hozzárendelését teszi lehetővé.  Eltérő hozzáférési szabályokat rendelhet az előfizetések, erőforráscsoportok és erőforrások.  Az RBAC-szabályok az erőforrás-hierarchia mentén öröklődnek, alacsonyabb szintű bírálni.  Minden olyan felhasználó vagy felhasználói csoportokat az RBAC-szabályok az aad-ben, hogy a kijelölt felhasználóknak és a csoportok módosításával a fürt.  További információkért olvassa el a [Azure RBAC áttekintése](/azure/role-based-access-control/overview).

A Service Fabric támogatja a hozzáférés-vezérlés bizonyos fürt műveletek a különböző felhasználói csoportokhoz való hozzáférés korlátozására is. Ez segít a fürt biztonságát. Hozzáférés-vezérlési kétféle fürthöz csatlakozó ügyfelek támogatottak: Rendszergazdai szerepkör és a felhasználói szerepkör.  

További információkért olvassa el [Service Fabric Role-Based hozzáférés-vezérlés (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok) 
Hálózati biztonsági csoportok (NSG) szabályozhatja bejövő és kimenő forgalmát egy alhálózathoz, virtuális gép vagy egy adott hálózati adapteren.  Alapértelmezés szerint amikor több virtuális gép ugyanazon a virtuális hálózaton kerüljenek is kommunikálnak egymással bármely porton keresztül.  Ha szeretné korlátozni a kommunikációt a gépek között meghatározhatja az NSG-ket a hálózatot, vagy a virtuális gépek elkülöníteni egymástól.  Ha egy fürtben több csomóponttípus, az NSG-ket alhálózatokhoz tartozó gépeket replikálhatnak kommunikáljon egymással a különböző csomóponttípusok elkerülése érdekében alkalmazhat.  

További információkért olvassa el [biztonsági csoportok](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Méretezés

Alkalmazások számára az idő előrehaladtával változik. Szükség lehet növelni a fürterőforrások felel meg az alkalmazás nagyobb számítási feladatok vagy a hálózati forgalom, vagy csökkentse a fürterőforrások, ha az igény csökken. Egy Service Fabric-fürt létrehozását követően, horizontálisan a fürt (módosíthatja a csomópontok számát), vagy függőlegesen (módosíthatja a csomópontok az erőforrások). Méretezheti a fürt bármikor, még akkor is, ha a számítási feladatok a fürtön futnak. A fürt skálázható, mivel az alkalmazások automatikus méretezése is.

További információkért olvassa el [méretezés Azure-fürtök](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Frissítés
Azure Service Fabric-fürt egy erőforrás, amelynek a tulajdonosa, de részben a Microsoft felügyeli. A Microsoft felelős az alapul szolgáló operációs rendszer javításait, és a Service Fabric-futtatókörnyezet frissítéseket végez a fürtön. Állítsa be a fürt automatikus futtatókörnyezet frissítését, kapni a Microsoft által kiadott új verzióra, vagy kiválaszthat egy támogatott futtatókörnyezet-verzióra. Futtatókörnyezet frissítését, valamint fürtkonfiguráció, például a tanúsítványokat és alkalmazásportok is frissítheti.

További információkért olvassa el [fürtök frissítése](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Ön létrehozhat fürtöket említett operációs rendszerektől eltérő rendszert futtató virtuális gépeken:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* A Windows Server 1803-as verzióban
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (előzetes verzió támogatása)

> [!NOTE]
> Ha úgy dönt, hogy üzembe helyezése a Service Fabric Windows Server 1709-es, vegye figyelembe, hogy (1), ne legyen olyan hosszú távú karbantartási ág, ezért előfordulhat, hogy a jövőben áthelyezése verziók, és (2) Ha a tárolók üzembe helyezése, a Windows Server 2016-ra épülő tárolók nem működnek a Windows Server  1709-es, és ez fordítva is igaz (kell őket az üzembe helyezés helyszíne újraépítése).
>


## <a name="next-steps"></a>További lépések
Tudjon meg többet [biztonságossá tétele](service-fabric-cluster-security.md), [skálázás](service-fabric-cluster-scaling.md), és [frissítése](service-fabric-cluster-upgrade.md) Azure fürtök.

Ismerje meg [Service Fabric támogatási lehetőségeinek](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG