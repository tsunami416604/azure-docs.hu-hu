---
title: Fürtök létrehozása Windows Server és Linux rendszeren
description: A Service Fabric-fürtök Windows Server és Linux rendszeren futnak, ami azt jelenti, hogy bárhol telepítheti és üzemeltetheti a Service Fabric-alkalmazásokat, ahol windows Server vagy Linux futhat.
services: service-fabric
documentationcenter: .net
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b6942c2a0647401df0d88b83e1b144ca3207a6db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614672"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Az Azure-beli Service Fabric-fürtök áttekintése
A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. A fürt részét képezi gépet vagy virtuális gépet fürtcsomópontnak nevezzük. A fürtök több ezer csomópontra méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, a Service Fabric újraegyensúlyozza a szolgáltatáspartíció-replikákat és példányokat a megnövekedett számú csomópont között. Az alkalmazások általános teljesítménye javul, és csökken a memóriahoz való hozzáférés rekedése. Ha a fürt csomópontjait nem használja hatékonyan, csökkentheti a csomópontszámát a fürtben. A Service Fabric újra egyensúlyba hozza a partícióreplikákat és példányokat a csomópontok számának csökkenésével, hogy jobban kihasználja a hardvert az egyes csomópontokon.

A csomóponttípus határozza meg a fürt csomópontjainak (virtuális gépek) méretét, számát és tulajdonságait. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik. 

## <a name="cluster-components-and-resources"></a>Fürtösszetevők és -erőforrások
Az Azure-beli Service Fabric-fürt olyan Azure-erőforrás, amely más Azure-erőforrásokat használ és kommunikál velük:
* Virtuális gépek és virtuális hálózati kártyák
* virtuálisgép-méretezési csoportok
* virtuális hálózatokkal
* terheléselosztók
* tárfiókok
* nyilvános IP-címek

![Szolgáltatásháló-fürt][Image]

### <a name="virtual-machine"></a>Virtuális gép
A fürt részét használó [virtuális gépeket](/azure/virtual-machines/) csomópontnak nevezzük, bár technikailag a fürtcsomópont egy Service Fabric futásidejű folyamat. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok olyan jellemzőkkel rendelkeznek, mint például [az elhelyezési tulajdonságok.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) Minden gép vagy virtuális gép rendelkezik egy automatikus indítási szolgáltatással, *a FabricHost.exe szolgáltatással,* amely rendszerindításkor indul, majd elindítja a két végrehajtható fájlt, *a Fabric.exe-t* és a *FabricGateway.exe-t,* amelyek a csomópontot alkotják. Az éles környezetben egy csomópont fizikai vagy virtuális gépenként. Tesztelési forgatókönyvek esetén több csomópontot is üzemeltethet egyetlen gépen vagy virtuális gépen a *Fabric.exe* és a *FabricGateway.exe*több példányának futtatásával.

Minden virtuális gép egy virtuális hálózati kártya (NIC) társított, és minden hálózati adapter kap egy privát IP-címet.  A virtuális gép egy virtuális hálózathoz és a helyi elosztón keresztül van hozzárendelve.

A fürtben lévő összes virtuális gép egy virtuális hálózatba kerül.  Az azonos csomóponttípus/méretezési csoport összes csomópontja ugyanazon az alhálózaton van elhelyezve a virtuális hálózaton.  Ezek a csomópontok csak privát IP-címekkel rendelkeznek, és közvetlenül nem címezhetők a virtuális hálózaton kívül.  Az ügyfelek az Azure terheléselosztón keresztül érhetik el a csomópontokon lévő szolgáltatásokat.

### <a name="scale-setnode-type"></a>Méretezési csoport/csomópont típusa
Fürt létrehozásakor egy vagy több csomóponttípust definiál.  A csomóponttípusban lévő csomópontok vagy virtuális gépek mérete és jellemzői megegyeznek például a processzorok számával, a memóriával, a lemezek számával és a lemez I/O-jával.  Például egy csomópont típus lehet a kis, előtér-virtuális gépek portok nyitva az internetre, míg egy másik csomópont típus lehet a nagy, háttér-virtuális gépek, amelyek az adatokat. Az Azure-fürtökben minden csomóponttípus [egy virtuálisgép-méretezési csoporthoz](/azure/virtual-machine-scale-sets/)van rendelve.

A méretezési csoportok segítségével üzembe helyezheti és kezelheti a virtuális gépek gyűjteménye készletként. Az Azure Service Fabric-fürtben definiált minden csomóponttípus külön méretezési csoportot állít be. A Service Fabric futásidejű van bootstrapped-ra minden virtuális gép a méretezési csoport ban az Azure Virtuálisgép-bővítmények használatával. Az egyes csomóponttípusokat egymástól függetlenül skálázhatja fel- vagy le, módosíthatja az egyes fürtcsomópontokon futó operációsrendszer-termékváltozatot, különböző portkészleteket nyithat meg, és különböző kapacitásmetrikákat használhat. Egy méretezési tartomány öt [frissítési tartománnyal](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) és öt [tartalék tartománnyal rendelkezik,](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) és legfeljebb 100 virtuális gépből lehet rendelkezni.  Több mint 100 csomópontból álló fürtöket hozhat létre több méretezési csoport/csomóponttípus létrehozásával.

> [!IMPORTANT]
> A fürt csomóponttípusainak számát és az egyes csomóponttípusok (méret, elsődleges, internet nézés, virtuális gépek száma stb.) tulajdonságait fontos feladat.  További információért olvassa el a [fürtkapacitás-tervezési szempontokat.](service-fabric-cluster-capacity.md)

További információért olvassa el [a Service Fabric-csomóponttípusok és a virtuálisgép-méretezési csoportok című olvasni.](service-fabric-cluster-nodetypes.md)

### <a name="azure-load-balancer"></a>Azure Load Balancer
Virtuálisgép-példányok csatlakozik mögött egy [Azure load balancer,](/azure/load-balancer/load-balancer-overview)amely egy [nyilvános IP-cím](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) és DNS-címke társított.  * &lt;Fürtnévvel&gt;* rendelkező fürt kiépítésekor a DNS-név , * &lt;&gt;fürtnév .&lt; a&gt;.cloudapp.azure.com* hely a méretezési csoport előtti terheléselosztóhoz társított DNS-címke.

A fürtben lévő virtuális gépek csak [privát IP-címmel rendelkeznek.](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)  A forgalmi és szolgáltatási forgalom a nyilvános terheléselosztón keresztül van irányítva.  A hálózati forgalom ezekhez a gépekhez NAT-szabályokon keresztül (az ügyfelek meghatározott csomópontokhoz/példányokhoz csatlakoznak) vagy terheléselosztási szabályokon keresztül (a forgalom a virtuális gépek ciklikus multiplexelésre kerül).  A terheléselosztóhoz kapcsolódó nyilvános IP-cím a következő formátumú DNS-névvel rendelkezik: * &lt;clustername&gt;.&lt; helyen&gt;.cloudapp.azure.com*.  A nyilvános IP-cím egy másik Azure-erőforrás az erőforráscsoportban.  Ha egy fürtben több csomóponttípust határoz meg, minden csomóponttípushoz/méretezési csoporthoz létrejön egy terheléselosztó. Vagy egyetlen terheléselosztót is beállíthat több csomóponttípushoz.  Az elsődleges csomóponttípusa * &lt;&gt;DNS-címkefürtneve&lt; . helyen&gt;.cloudapp.azure.com*, más csomóponttípusok dns-címke * &lt;&gt;-&lt;fürtnév csomóponttípussal&gt;rendelkeznek.&lt; helyen&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Tárfiókok
Minden fürtcsomópont-típust egy [Azure-tárfiók](/azure/storage/common/storage-introduction) és felügyelt lemezek támogatnak.

## <a name="cluster-security"></a>Fürtbiztonság
A Service Fabric-fürt egy saját erőforrás.  Az Ön felelőssége, hogy biztosítsa a fürtök, hogy segítsen megakadályozni illetéktelen felhasználók számára való csatlakozást. A biztonságos fürt különösen fontos, ha éles számítási feladatokat futtat a fürtön. 

### <a name="node-to-node-security"></a>Csomópont-csomópont biztonság
Csomópont-csomópont biztonság biztosítja a kommunikációt a virtuális gépek vagy a fürt számítógépei között. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben lévő alkalmazások és szolgáltatások üzemeltetésében. A Service Fabric X.509-es tanúsítványokat használ a fürt védelméhez és az alkalmazás biztonsági szolgáltatásainak biztosításához.  Fürttanúsítvány szükséges a fürtforgalom védelméhez, valamint a fürt- és kiszolgálóhitelesítés biztosításához.  Saját aláírású tanúsítványok használhatók tesztfürtökhöz, de egy megbízható hitelesítésszolgáltató tanúsítványát kell használni az éles fürtök védelméhez.

További információ: [Csomópont-csomópont biztonság](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Ügyfél-csomópont biztonság
Az ügyfél-csomópont biztonság hitelesíti az ügyfeleket, és segít a fürt ügyfél- és egyéni csomópontjai közötti kommunikáció biztosításában. Ez a fajta biztonság segít biztosítani, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a fürtön telepített alkalmazásokhoz. Az ügyfelek egyedi azonosítása az X.509 tanúsítvány biztonsági hitelesítő adataival. Tetszőleges számú választható ügyféltanúsítvány használható a rendszergazdai vagy felhasználói ügyfelek fürttel való hitelesítésére.

Az ügyféltanúsítványok mellett az Azure Active Directory is konfigurálható az ügyfelek hitelesítésére a fürtön.

További információ: [Ügyfél-csomópont biztonság](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy részletes hozzáférés-vezérlést rendeljen az Azure-erőforrásokhoz.  Az előfizetésekhez, erőforráscsoportokhoz és erőforrásokhoz különböző hozzáférési szabályokat rendelhet.  Az RBAC-szabályok az erőforrás-hierarchia mentén öröklődnek, kivéve, ha alacsonyabb szinten felülbírálják őket.  Az AAD-n lévő bármely felhasználót vagy felhasználói csoportot rbac-szabályokkal rendelheti hozzá, hogy a kijelölt felhasználók és csoportok módosíthassák a fürtöt.  További információkért olvassa el az [Azure RBAC áttekintését.](/azure/role-based-access-control/overview)

A Service Fabric is támogatja a hozzáférés-vezérlést, hogy korlátozza a hozzáférést bizonyos fürtműveletekhez a felhasználók különböző csoportjai számára. Ez segít a fürt biztonságosabbá tétele érdekében. A fürthöz csatlakozó ügyfelek számára két hozzáférés-vezérlési típus támogatott: rendszergazdai szerepkör és felhasználói szerepkör.  

További információ: [Service Fabric szerepköralapú hozzáférés-vezérlés (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok) 
A hálózati biztonsági csoportok (NSG-k) egy alhálózat, virtuális gép vagy adott hálózati adapter bejövő és kimenő forgalmat szabályoznak.  Alapértelmezés szerint, ha több virtuális gép van fel helyezve ugyanazon a virtuális hálózaton tudnak kommunikálni egymással bármely porton keresztül.  Ha korlátozni szeretné a gépek közötti kommunikációt, definiálhatja az NSG-ket a hálózat szegmentálásához vagy a virtuális gépek egymástól való elkülönítéséhez.  Ha egy fürtben több csomóponttípus van, az NSG-ket alhálózatokra alkalmazhatja, hogy megakadályozza a különböző csomóponttípusokhoz tartozó gépek egymással való kommunikációját.  

További információ a [biztonsági csoportokról](/azure/virtual-network/security-overview)

## <a name="scaling"></a>Méretezés

Az alkalmazás igények idővel változnak. Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen a megnövekedett alkalmazásterhelésnek vagy hálózati forgalomnak, vagy csökkentse a fürterőforrásait, ha az igény csökken. A Service Fabric-fürt létrehozása után vízszintesen (módosíthatja a csomópontok számát) vagy függőlegesen skálázhatja a fürtöt (módosíthatja a csomópontok erőforrásait). A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön. A fürt méretezése, az alkalmazások automatikusan skálázható is.

További információért olvassa el [az Azure-fürtök méretezése](service-fabric-cluster-scaling.md)című.

## <a name="upgrading"></a>Frissítés
Az Azure Service Fabric-fürt egy olyan erőforrás, amely az Ön tulajdonában van, de részben a Microsoft kezeli. A Microsoft felelős az alapul szolgáló operációs rendszer javításáért és a Service Fabric futásidejű frissítésének végrehajtásáért a fürtön. Beállíthatja, hogy a fürt automatikus futásidejű frissítéseket kapjon, amikor a Microsoft új verziót ad ki, vagy kiválaszthatja a kívánt támogatott futásidejű verziót. A futásidejű frissítések mellett frissítheti a fürtkonfigurációt, például a tanúsítványokat vagy az alkalmazásportokat is.

További információ: [Fürtök frissítése](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Az alábbi operációs rendszereket futtató virtuális gépeken fürtöket hozhat létre:

| Operációs rendszer | Legkorábbi támogatott Service Fabric-verzió |
| --- | --- |
| Windows Server 2012 R2 | Az összes verzió |
| Windows Server 2016 | Az összes verzió |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

További információ: [Támogatott fürtverziók az Azure-ban](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-operating-systems)

> [!NOTE]
> Ha úgy dönt, hogy a Service Fabric szolgáltatást Windows Server 1709 rendszerre telepíti, vegye figyelembe, hogy (1) ez nem egy hosszú távú karbantartási ág, ezért előfordulhat, hogy a jövőben át kell helyeznie a verziókat, és (2) ha tárolókat telepít, a Windows Server 2016-ra épülő tárolók nem működnek windows Server rendszeren 1709, és fordítva (meg kell újjáépíteni őket telepíteni őket).
>


## <a name="next-steps"></a>További lépések
További információ az [Azure-fürtök védelméről,](service-fabric-cluster-security.md) [méretezéséről](service-fabric-cluster-scaling.md)és [frissítéséről.](service-fabric-cluster-upgrade.md)

További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
