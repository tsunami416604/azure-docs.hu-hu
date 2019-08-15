---
title: Infrastruktúra-konfigurációk és-műveletek SAP HANA az Azure-ban | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatásban üzembe helyezett SAP HANA rendszerek üzemeltetési útmutatója.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b55e979c3056f89eb76a1d2c86f9a770d2d3e05
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935395"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban
Ez a dokumentum útmutatást nyújt az Azure-infrastruktúra és az Azure natív virtuális gépeken üzembe helyezett operációs SAP HANA rendszerek konfigurálásához. A dokumentum a M128s virtuális gép SKU-jának SAP HANA kibővíthető konfigurációs adatait is tartalmazza. Ez a dokumentum nem helyettesíti a szabványos SAP-dokumentációt, amely a következő tartalmakat tartalmazza:

- [SAP felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatók](https://service.sap.com/instguides)
- [SAP-megjegyzések](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató használatához a következő Azure-összetevők alapszintű ismerete szükséges:

- [Azure-alapú virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-Hálózatkezelés és virtuális hálózatok](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Az SAP NetWeaver és az Azure-beli SAP-összetevőkkel kapcsolatos további információkért tekintse meg az [Azure dokumentációjának](https://docs.microsoft.com/azure/) [SAP on Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) című szakaszát.

## <a name="basic-setup-considerations"></a>Alapszintű telepítési szempontok
Az alábbi szakaszok a SAP HANA rendszerek Azure-beli virtuális gépeken való üzembe helyezésének alapvető telepítési szempontjait ismertetik.

### <a name="connect-into-azure-virtual-machines"></a>Kapcsolódjon az Azure Virtual Machines szolgáltatáshoz
Az [Azure Virtual Machines tervezési útmutatójában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)leírtaknak megfelelően két alapszintű módszer áll rendelkezésre az Azure-beli virtuális gépekhez való csatlakozáshoz:

- Kapcsolódjon az interneten és a nyilvános végpontokon egy Jump VM vagy SAP HANA rendszert futtató virtuális gépen.
- Csatlakozás [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) -vagy Azure- [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

A helyek közötti kapcsolat VPN-en vagy ExpressRoute szükséges az éles környezetekben. Az ilyen típusú kapcsolatok olyan nem éles környezetekben is szükségesek, amelyek olyan éles környezetbe kerülnek, ahol az SAP szoftver használatban van. Az alábbi képen egy példa látható a helyek közötti kapcsolatra:

![Helyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure-beli virtuális gépek típusának kiválasztása
Az éles környezetekben használható Azure-beli virtuálisgép-típusok listáját a [IAAS SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)-dokumentációja tartalmazza. A nem éles környezetekben a natív Azure-beli virtuálisgép-típusok szélesebb választéka érhető el.

>[!NOTE]
> Nem éles környezetekben használja az [SAP-megjegyzés #1928533ban](https://launchpad.support.sap.com/#/notes/1928533)felsorolt virtuálisgép-típusokat. Az Azure-beli virtuális gépek éles környezetekben való használatához tekintse meg a hitelesített virtuális gépek SAP HANA az SAP published [Certified IaaS Platforms listán](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

A virtuális gépek üzembe helyezése az Azure-ban a használatával:

- A Azure Portal.
- Azure PowerShell parancsmagok.
- Az Azure CLI-vel.

Az [SAP Cloud platformon](https://cal.sap.com/)üzembe helyezhet egy teljes körűen telepített SAP HANA platformot is az Azure-beli virtuálisgép-szolgáltatásokhoz. A telepítési folyamat az [SAP S/4HANA vagy a BW/4HANA üzembe helyezése az Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) -ban, illetve az [itt](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)megjelent Automation szolgáltatásban olvasható.

>[!IMPORTANT]
> A M208xx_v2 virtuális gépek használatához körültekintően kell kiválasztania a SUSE Linux-rendszerképet az Azure virtuálisgép-rendszerkép-katalógusból. A részletek olvasásához olvassa el a [memória-optimalizált virtuális gépek méretét](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)ismertető cikket. A Red Hat még nem támogatott a HANA on Mv2 Family virtuális gépeken való használatához. A jelenlegi tervezés célja, hogy támogatást nyújtson a HANA-t futtató Red Hat-verziókhoz a Mv2 VM-családban (Q4/CY2019) 
> 


### <a name="storage-configuration-for-sap-hana"></a>SAP HANA tárolási konfigurációja
Az Azure-ban SAP HANA használható tárolási konfigurációk és tárolási típusok esetén olvassa el a dokumentumot az [Azure-beli virtuális gépek tárolási konfigurációjának SAP HANA](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Azure-beli virtuális hálózatok beállítása
Ha a helyek közötti kapcsolatot VPN-en vagy ExpressRoute keresztül csatlakoztatja az Azure-hoz, rendelkeznie kell legalább egy olyan Azure-beli virtuális hálózattal, amely virtuális átjárón keresztül csatlakozik a VPN-vagy ExpressRoute-áramkörhöz. Az egyszerű telepítések során a virtuális átjáró üzembe helyezhető az Azure Virtual Network (VNet) azon alhálózatán is, amely az SAP HANA példányokat is üzemelteti. SAP HANA telepítéséhez két további alhálózatot hoz létre az Azure virtuális hálózaton belül. Az egyik alhálózat tárolja a virtuális gépeket az SAP HANA példányok futtatásához. A másik alhálózat Jumpbox-vagy felügyeleti virtuális gépeket futtat SAP HANA Studio, más felügyeleti szoftver vagy az alkalmazás szoftverének üzemeltetéséhez.

> [!IMPORTANT]
> A funkcionalitás, de még ennél is fontosabb, hogy az [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) konfigurálása a SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP adatbázis-kezelői rétege közötti kommunikációs útvonalon nem támogatott. rendszer. Az SAP-alkalmazás réteg és az adatbázis-kezelő réteg közötti kommunikációnak közvetlennek kell lennie. A korlátozás nem tartalmazza az [Azure ASG és a NSG szabályokat](https://docs.microsoft.com/azure/virtual-network/security-overview) , amennyiben ezek a ASG-és NSG-szabályok közvetlen kommunikációt tesznek lehetővé. További forgatókönyvek, amelyek esetében a NVA nem támogatottak olyan Azure-beli virtuális gépek közötti kommunikációs útvonalakban, amelyek a Linux pacemaker-fürtcsomópontok és a SBD-eszközök magas rendelkezésre állását mutatják be az Azure-beli [virtuális SUSE Linux Enterprise Server gépeken az SAP NetWeaver szolgáltatásban alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Vagy az Azure-beli virtuális gépek és a Windows Server-SOFS közötti kommunikációs útvonalakban az Azure-beli [fájlmegosztás használatával a Windows feladatátvevő fürtben a fürt SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)-példányának beállítása című témakörben leírtak szerint. A kommunikációs elérési utak NVA könnyedén megduplázhatja a két kommunikációs partner közötti hálózati késést, így az SAP-alkalmazási réteg és az adatbázis-kezelő réteg közötti kritikus útvonalakon korlátozhatja az átviteli sebességet. Az ügyfelekkel megfigyelt egyes forgatókönyvekben a NVA a pacemaker linuxos fürtök meghibásodását okozhatják abban az esetben, ha a Linux pacemaker-fürtcsomópontok közötti kommunikációnak egy NVA keresztül kell kommunikálnia a SBD-eszközzel.  
> 

> [!IMPORTANT]
> Egy másik kialakítás, amely **nem** támogatott, az SAP-alkalmazás rétegének és az adatbázis-kezelő rétegnek a különböző Azure- [beli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuális hálózatokra való elkülönítése, amelyek nincsenek egymással társítva. Azt javasoljuk, hogy a különböző Azure-beli virtuális hálózatok használata helyett az SAP-alkalmazás rétegét és az adatbázis-kezelő réteget az Azure virtuális hálózatban lévő alhálózatok használatával különítse el. Ha úgy dönt, hogy nem követi a javaslatot, és ehelyett elkülöníti a két réteget a különböző virtuális hálózatok között, a két virtuális hálózatnak [egyenrangúnak](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)kell lennie. Vegye figyelembe, hogy a két, egymástól független Azure- [beli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuális hálózat közötti hálózati forgalom az átvitel költségeinek hatálya alá tartozik. Az SAP-alkalmazási réteg és az adatbázis-kezelő réteg jelentős költségeinek kiváltásával az SAP-alkalmazás rétegében és az adatbázis-kezelő rétegben a nagy mennyiségű adatmennyiség felhalmozható. 

Ha a virtuális gépeket a SAP HANA futtatására telepíti, a virtuális gépeknek a következőkre van szükségük:

- Két virtuális hálózati adapter van telepítve: egy hálózati adaptert a felügyeleti alhálózathoz, és egy hálózati adaptert a helyszíni hálózatról vagy más hálózatokról az Azure-beli virtuális gép SAP HANA példányához való kapcsolódáshoz.
- Statikus magánhálózati IP-címek, amelyek mindkét virtuális hálózati adapterhez telepítve vannak.

> [!NOTE]
> Statikus IP-címeket kell hozzárendelni az Azure-ban az egyes Vnic. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy vNIC. Bizonyos Azure-szolgáltatások, például a Azure Backup-szolgáltatás arra támaszkodnak, hogy legalább az elsődleges vNIC DHCP-re van beállítva, és nem statikus IP-címekre. Tekintse meg az [Azure-beli virtuális gépek biztonsági mentését](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)ismertető dokumentumot is. Ha több statikus IP-címet kell hozzárendelni egy virtuális géphez, több Vnic kell hozzárendelnie egy virtuális géphez.
>
>

A tartós üzembe helyezések esetében azonban létre kell hoznia egy virtuális adatközpont-hálózati architektúrát az Azure-ban. Ez az architektúra javasolja az Azure VNet-átjáró elkülönítését, amely a helyszíni rendszerhez csatlakozik egy különálló Azure-VNet. Ez a különálló VNet az összes olyan forgalmat üzemelteti, amely a helyszíni vagy az internet felé irányul. Ez a módszer lehetővé teszi szoftver központi telepítését az Azure-beli virtuális adatközpontba bekerülő forgalom naplózására és naplózására ebben a különálló hub-VNet. Tehát van egy VNet, amely az Azure-beli üzembe helyezéshez kapcsolódó összes szoftvert és konfigurációt üzemelteti.

Az Azure [Virtual Datacenter cikkei: A hálózati perspektíva](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) és [Az Azure Virtual Datacenter és a nagyvállalati vezérlési sík](https://docs.microsoft.com/azure/architecture/vdc/) további információkat biztosít a virtuális adatközpontok megközelítéséről és a kapcsolódó Azure VNet kialakításáról.


>[!NOTE]
>A hub-VNet és az [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) -VNet használó küllős közötti forgalom további [költségek](https://azure.microsoft.com/pricing/details/virtual-network/)alá esik. A költségek alapján előfordulhat, hogy meg kell fontolnia, hogy a szigorú elosztó és a küllős hálózati kialakítás futtatása során felmerülő kompromisszumok között a VNet-társítás megkerülése érdekében több [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) -átjárót is futtat, amelyekhez a küllők csatlakoznak. Az Azure ExpressRoute-átjárók azonban további [költségeket](https://azure.microsoft.com/pricing/details/vpn-gateway/) is bevezetnek. A hálózati forgalom naplózásához, naplózásához és figyeléséhez használt harmadik féltől származó szoftverek további költségei is előfordulhatnak. Az adatcsere költségeitől függ az egyoldalas VNet keresztül, valamint a további Azure ExpressRoute-átjárók és a további szoftverlicenc által létrehozott költségektől függően, ha az alhálózatok elkülönítési egységként való használatát is eldöntheti, dönthet az egyes VNet belüli Virtuális hálózatok helyett.


Az IP-címek hozzárendelésének különböző módszereiről az [IP-címek típusai és a foglalási módszerek az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)című cikk nyújt áttekintést. 

SAP HANA rendszert futtató virtuális gépek esetén a hozzárendelt statikus IP-címekkel kell működnie. Ennek oka, hogy a HANA-hivatkozások IP-címeinek néhány konfigurációs attribútuma.

Az [Azure hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) az SAP HANA-példányhoz vagy a Jumpbox irányított forgalom irányítására szolgálnak. A NSG és a végül az [alkalmazás biztonsági csoportjai](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) a SAP HANA alhálózathoz és a felügyeleti alhálózathoz vannak társítva.

Az alábbi képen egy központi telepítési séma áttekintése látható, amely a hub és a küllős VNet architektúrát követően SAP HANA:

![A SAP HANA durva üzembe helyezési sémája](media/hana-vm-operations/hana-simple-networking.PNG)

Ha helyek közötti kapcsolat nélkül szeretné üzembe helyezni SAP HANA az Azure-ban, akkor továbbra is a nyilvános internetről kell elvégeznie a SAP HANA példány védelmét, és el kell rejtenie egy továbbító proxy mögött. Ebben az alapvető forgatókönyvben az üzemelő példány az Azure beépített DNS-szolgáltatásaiban támaszkodik az állomásnevek feloldására. A nyilvános IP-címeket használó összetettebb üzembe helyezésnél különösen fontos az Azure beépített DNS-szolgáltatásai. Az Azure NSG és az [Azure NVA](https://azure.microsoft.com/solutions/network-appliances/) segítségével felügyelheti az internetről érkező útválasztást az Azure-beli Azure VNet-architektúrában. Az alábbi képen egy durva séma látható, amellyel SAP HANA helyezhetők üzembe helyek közötti kapcsolat nélkül egy központhoz és küllő VNet architektúrához:
  
![Durva üzembe helyezési séma SAP HANA helyek közötti kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Egy másik leírás arról, hogyan használható az Azure NVA az internetről a hub és a küllő VNet architektúra nélküli hozzáférés vezérlésére és figyelésére a következő cikkben: a [nagymértékben elérhető hálózati virtuális berendezések üzembe helyezése](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Az Azure-infrastruktúra konfigurálása SAP HANA kibővíthető
A Microsoft egy M sorozatú VM SKU-t tartalmaz, amely SAP HANA kibővített konfigurációhoz van hitelesítve. A virtuális gép típusa legfeljebb 16 csomópont M128s kapott. Az Azure-beli virtuális gépeken SAP HANA kibővíthető minősítések változásainál tekintse meg a [Certified IaaS-platformok listáját](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

A kibővített konfigurációk Azure-beli virtuális gépeken való üzembe helyezéséhez szükséges minimális operációsrendszer-verziók a következők:

- SUSE Linux 12 SP3
- Red Hat Linux 7,4

A 16 csomópontos Felskálázási tanúsítvány

- Az egyik csomópont a fő csomópont
- A munkavégző csomópontok legfeljebb 15 csomópontot használnak

>[!NOTE]
>Az Azure-beli virtuális gépek kibővíthető üzembe helyezése esetén nem lehet készenléti csomópontot használni
>

A készenléti csomópont konfigurálásának oka kettős:

- Az Azure ezen a ponton nem rendelkezik natív NFS-szolgáltatással. Ennek eredményeképpen az NFS-megosztásokat harmadik féltől származó funkciók segítségével kell konfigurálni.
- A harmadik féltől származó NFS-konfigurációk egyike sem tudja teljesíteni az Azure-ban üzembe helyezett megoldásokkal SAP HANA tárolási késési feltételeit.

Ennek eredményeképpen a **/Hana/Data** és a **/Hana/log** kötetek nem oszthatók meg. Ha nem osztja meg ezeket a köteteket az egyes csomópontokon, meggátolja egy SAP HANA készenléti csomópont használatát egy kibővíthető konfigurációban.

Ennek eredményeképpen a kibővíthető konfiguráció egyetlen csomópontjának alapszintű kialakítása a következőhöz fog hasonlítani:

![Egyetlen csomópont kibővíthető alapjai](media/hana-vm-operations/scale-out-basics.PNG)

A SAP HANA kibővíthető virtuálisgép-csomópontok alapszintű konfigurációja a következőhöz hasonlóan néz ki:

- A **/Hana/Shared**a SUSE Linux 12 SP3-alapú, magasan elérhető NFS-fürtöt épít ki. Ez a fürt tárolja a kibővíthető konfiguráció és az SAP NetWeaver vagy a BW/4HANA központi szolgáltatások **/Hana/Shared** NFS-megosztását. Az ilyen konfiguráció létrehozásához szükséges dokumentáció az [NFS magas rendelkezésre állása az Azure-beli virtuális gépeken SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Az összes többi lemez mennyisége **nem** oszlik meg a különböző csomópontok között, és **nem** az NFS-en alapul. A nem megosztott **/Hana/Data** és **/Hana/log** rendelkező, kibővíthető HANA-példányok telepítési konfigurációi és lépései a jelen dokumentum további részében olvashatók.

>[!NOTE]
>Az eddig a grafikában megjelenő, nagy rendelkezésre állású NFS-fürt csak SUSE Linux rendszeren támogatott. A Red Hat-on alapuló, magasan elérhető NFS-megoldást később fogjuk tájékoztatni.

A csomópontok köteteinek méretezése ugyanaz, mint a **/Hana/Shared**, kivéve a méretezést. A M128s VM SKU esetében a javasolt méretek és típusok a következőképpen néznek ki:

| VM SKU | RAM | Legfeljebb VM I/O<br /> Teljesítmény | /hana/data | /hana/log | /root-kötet | /usr/sap | Hana/Backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Győződjön meg arról, hogy a különböző javasolt kötetek tárolási átviteli sebessége megfelel-e a futtatni kívánt munkaterhelésnek. Ha a munkaterhelés nagyobb köteteket igényel a **/Hana/Data** és a **/Hana/log**számára, növelje az Azure Premium Storage VHD-k számát. Az Azure-beli virtuálisgép-típus korlátain belül a kötetek méretezése a IOPS és az I/O-átviteli sebesség növelésével növekszik. Az Azure írásgyorsító is alkalmazza a **/Hana/log** kötetet alkotó lemezekre.
 
A (z) [SAP HANA TDI tárolási követelményei](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)című dokumentum egy nevű képletet határoz meg, amely meghatározza, hogy a **/Hana/Shared** -kötet mekkora méretű legyen a felskálázáshoz, mivel a memória mérete egy feldolgozói csomópont számára négy feldolgozó csomóponton.

Ha feltételezi, hogy az Azure-beli, nagyjából 2 TB-os memóriával rendelkező SAP HANA kibővített tanúsítvánnyal rendelkező M128s, az SAP-javaslatok a következőképpen foglalhatók össze:

- Egy főcsomópontnak és legfeljebb négy feldolgozó csomópontnak, a **/Hana/Shared** kötetnek 2 TB méretűnek kell lennie. 
- Az egyik főcsomópontnak és öt – nyolc feldolgozó csomópontnak a **/Hana/Shared** mérete 4 TB. 
- Egy főcsomópont és 9 – 12 feldolgozó csomópont, a **/Hana/Shared** 6 TB-os méretre lesz szükség. 
- Egy főcsomópont, és 12 és 15 feldolgozó csomópont között kell megadnia a 8 TB méretű **/Hana/Shared** -kötetet.

A kibővíthető SAP HANA virtuális gép egycsomópontos konfigurációjának grafikájában megjelenő másik fontos kialakítás a VNet, vagy jobb az alhálózat konfigurációja. Az SAP kifejezetten azt ajánlja, hogy a HANA-csomópontok közötti kommunikációból elkülönítse az ügyfél/alkalmazás felé irányuló forgalmat. Ahogy az ábrán is látható, ezt a célt úgy érheti el, hogy két különböző Vnic van csatlakoztatva a virtuális géphez. Mindkét Vnic különböző alhálózatokban van, két különböző IP-címmel rendelkezik. Ezután a NSG vagy a felhasználó által megadott útvonalak használatával szabályozhatja az útválasztási szabályok forgalmának áramlását.

Különösen az Azure-ban nincs mód a szolgáltatás minőségének és a kvótáknak az adott Vnic való betartatására. Ennek eredményeképpen az ügyfél/alkalmazás és a csomóponton belüli kommunikáció elkülönítése nem nyit meg semmilyen lehetőséget, hogy az egyik forgalmi adatfolyamot a másikon rangsorolja. Ehelyett a szétválasztás a biztonság mértéke marad a kibővíthető konfigurációk csomóponton belüli kommunikációjának védelmében.  

>[!IMPORTANT]
>Az SAP azt javasolja, hogy a jelen dokumentumban leírtak szerint elkülönítse a hálózati forgalmat az ügyfél, az alkalmazás és a csomóponton belüli forgalom között. Ezért a rendszer a legutóbbi ábrán látható módon helyez üzembe egy architektúrát, és kifejezetten ajánlott.
>

A hálózati szempontból a minimálisan szükséges hálózati architektúra az alábbihoz hasonló lesz:

![Egyetlen csomópont kibővíthető alapjai](media/hana-vm-operations/scale-out-networking-overview.PNG)

Az eddig támogatott korlátok az egyetlen főcsomóponton további 15 feldolgozó.

A tárolási architektúra a következőhöz hasonlóan néz ki:


![Egyetlen csomópont kibővíthető alapjai](media/hana-vm-operations/scale-out-storage-overview.PNG)

A **/Hana/Shared** kötet a rendelkezésre álló NFS-megosztási konfigurációban található. Míg az összes többi meghajtó "helyileg" van csatlakoztatva az egyes virtuális gépekhez. 

### <a name="highly-available-nfs-share"></a>Kiválóan elérhető NFS-megosztás
A nagy rendelkezésre állású NFS-fürt eddig csak SUSE Linux rendszeren működik. Az Azure-beli [virtuális gépeken a SUSE Linux Enterprise Server az NFS magas rendelkezésre állását](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) ismertető dokumentum azt ismerteti, hogyan lehet beállítani. Ha nem osztja meg az NFS-fürtöt minden olyan HANA-konfigurációval, amely az SAP HANA példányokat futtató Azure-VNet kívül esik, akkor ugyanazt a VNet kell telepítenie. Telepítse a saját alhálózatán, és győződjön meg arról, hogy nem minden tetszőleges forgalom fér hozzá az alhálózathoz. Ehelyett korlátozni szeretné az adott alhálózatra irányuló forgalmat azon virtuális gép IP-címére, amely a **/Hana/Shared** -kötetre irányuló forgalmat hajtja végre.

A **/Hana/Shared** -forgalmat átirányító HANA kibővíthető virtuális gép vNIC kapcsolatos javaslatok a következők:

- Mivel a **/Hana/Shared** felé irányuló forgalom mérsékelt, a minimális konfigurációban az ügyfél-hálózathoz rendelt vNIC irányítja át.
- Végül a **/Hana/Shared**irányuló adatforgalom esetén helyezzen üzembe egy harmadik alhálózatot a SAP HANA VNet, és rendeljen hozzá egy harmadik, az alhálózaton üzemeltetett vNIC. Használja a harmadik vNIC és a hozzá tartozó IP-címet az NFS-megosztás forgalmához. Ezután alkalmazhat külön hozzáférési és útválasztási szabályokat is.

>[!IMPORTANT]
>A hálózati forgalom olyan virtuális gépek között, amelyek kibővíthető módon vannak SAP HANAve, és a rendelkezésre álló NFS-eszközök semmilyen körülmények között nem irányíthatók át [NVA](https://azure.microsoft.com/solutions/network-appliances/) vagy hasonló virtuális készülékeken keresztül. Míg az Azure NSG nincsenek ilyen eszközök. Ellenőrizze az útválasztási szabályokat, hogy megbizonyosodjon arról, hogy a NVA vagy hasonló virtuális berendezések le vannak-e hajtva, amikor a SAP HANA rendszert futtató virtuális gépekről fér hozzá a rendelkezésre álló NFS-megosztáshoz.
> 

Ha meg szeretné osztani a magasan elérhető NFS-fürtöt SAP HANA konfigurációk között, helyezze át az összes HANA-konfigurációt ugyanabba a VNet. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>SAP HANA kibővíthető n Azure telepítése
A kibővíthető SAP-konfiguráció telepítése során a következők részletes lépéseit kell végrehajtania:

- Új vagy meglévő Azure VNet-infrastruktúra átalakítása
- Az új virtuális gépek üzembe helyezése az Azure Managed Premium Storage kötetek használatával
- Új vagy meglévő, magasan elérhető NFS-fürt üzembe helyezése
- Igazítsa a hálózati útválasztást, és győződjön meg róla, hogy például a virtuális gépek közötti csomóponton belüli kommunikáció nem egy [NVA](https://azure.microsoft.com/solutions/network-appliances/)keresztül van átirányítva. Ugyanez érvényes a virtuális gépek és a magasan rendelkezésre álló NFS-fürt közötti forgalomra.
- Telepítse a SAP HANA fő csomópontot.
- A SAP HANA fő csomópont konfigurációs paramétereinek igazítása
- Folytassa a SAP HANA munkavégző csomópontok telepítésével

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>SAP HANA telepítése kibővíthető konfigurációban
Az Azure-beli virtuálisgép-infrastruktúrák üzembe helyezése és az egyéb előkészületek elvégzése után a következő lépésekben telepítenie kell a SAP HANA kibővítő konfigurációit:

- Az SAP HANA fő csomópont telepítése az SAP dokumentációjának megfelelően
- **A telepítés után módosítania kell a Global. ini fájlt, és hozzá kell adnia a "basepath_shared = No" paramétert a Global. ini fájlhoz**. Ez a paraméter lehetővé teszi, hogy a SAP HANA a "Shared" **/Hana/Data** és a csomópontok közötti **/Hana/log** -kötetek nélkül fusson. A részleteket az [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991)dokumentációja ismerteti.
- A Global. ini paraméter módosítása után indítsa újra a SAP HANA példányt
- További munkavégző csomópontok hozzáadása. Lásd még:. <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html> Itt adhatja meg a belső hálózatot SAP HANA csomópontok közötti kommunikációhoz a telepítés során, vagy később, például a helyi hdblcm használatával. Részletesebb dokumentáció: [SAP-megjegyzés #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

A beállítási rutin után a telepített kibővítő konfiguráció a **/Hana/Data** és a **/Hana/log**futtatásához nem megosztott lemezeket fog használni. A **/Hana/Shared** -kötetet a rendszer a magasan elérhető NFS-megosztásra helyezi.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Az Azure Virtual Machines szolgáltatáshoz készült 2,0-es dinamikus előSAP HANA

Az Azure M sorozatú virtuális gépeken SAP HANA minősítéseken kívül SAP HANA a dinamikus rétegek 2,0 is támogatott a Microsoft Azureban (lásd: SAP HANA dinamikus rétegű dokumentációs hivatkozások lejjebb). Habár nem különbözik a termék telepítésének vagy működtetésének, például SAP HANA pilótafülke egy Azure-beli virtuális gépen belül, néhány fontos elem van, amelyek kötelezőek az Azure hivatalos támogatásához. Ezeket a főbb pontokat alább találja. A cikk során a "DT 2,0" rövidítést a rendszer a teljes név, a dinamikus és a 2,0-es érték helyett használni fogja.

SAP BW vagy S4HANA nem támogatja SAP HANA a dinamikus rétegek 2,0ét. A fő használati esetek mostantól natív HANA-alkalmazások.


### <a name="overview"></a>Áttekintés

Az alábbi kép áttekintést nyújt a DT 2,0 támogatásáról Microsoft Azureon. A kötelező követelmények egy készlete, amelyet követni kell a hivatalos minősítés betartása érdekében:

- A DT 2,0-et dedikált Azure-beli virtuális gépre kell telepíteni. Előfordulhat, hogy nem ugyanazon a virtuális gépen fut, amelyen SAP HANA fut
- A SAP HANA és a DT 2,0 virtuális gépeket ugyanazon Azure-vnet belül kell telepíteni
- A SAP HANA és a DT 2,0 virtuális gépeket az Azure gyorsított hálózatkezelés beállítással kell telepíteni
- A DT 2,0 virtuális gépek tárolási típusának Azure-Premium Storage kell lennie
- Több Azure-lemezt kell csatlakoztatni a DT 2,0 virtuális géphez
- A szoftveres RAID/csíkozott kötet (LVM vagy mdadm használatával) létrehozásához az Azure-lemezeken keresztüli csíkozás szükséges.

További részleteket a következő szakaszban talál.

![SAP HANA DT 2,0 architektúrájának áttekintése](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedikált Azure-beli virtuális gép SAP HANA DT 2,0

Az Azure IaaS-on a DT 2,0 csak dedikált virtuális gépen támogatott. A DT 2,0-es verzióját nem lehet ugyanazon az Azure-beli virtuális gépen futtatni, amelyen a HANA-példány fut. Kezdetben két virtuálisgép-típust lehet használni SAP HANA DT 2,0-es futtatásához:

- M64-32ms 
- E32sv3 

Lásd a virtuális gép [](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) típusának leírását

A 2,0-es DT alapgondolata miatt a "meleg" adatok kiszervezése a költségek megtakarítása érdekében ésszerű a megfelelő virtuálisgép-méretek használata. A lehetséges kombinációkkal kapcsolatban azonban nincs szigorú szabály. Ez az adott ügyfél munkaterheléstől függ.

Az ajánlott konfigurációk a következőek:

| SAP HANA virtuális gép típusa | DT 2,0 virtuális gép típusa |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


A SAP HANA-tanúsítvánnyal rendelkező M sorozatú virtuális gépek összes kombinációja támogatott DT 2,0 virtuális gépekkel (M64-32ms és E32sv3) lehetséges.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure Networking és SAP HANA DT 2,0

A DT 2,0 dedikált virtuális gépen való telepítéséhez hálózati átviteli sebességre van szükség a DT 2,0 virtuális gép és a SAP HANA 10 GB-os virtuális gép között. Ezért a virtuális gépeket az Azure-vnet belül kell elhelyezni, és engedélyezni kell az Azure gyorsított hálózatkezelését.

Az Azure gyorsított hálózatkezeléssel kapcsolatos további információk [itt](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) találhatók

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM Storage SAP HANA DT 2,0

A DT 2,0 ajánlott eljárási útmutatója alapján a lemez i/o-átviteli sebességének legalább 50 MB/s-nak kell lennie a fizikai mag esetében. Tekintse meg a két Azure-beli virtuálisgép-típusra vonatkozó SPECT, amelyek a DT 2,0-as verziójával támogatottak

- E32sv3 :   768 MB/s (nem gyorsítótárazott), ami a fizikai mag 48 MB/s arányát jelenti.
- M64-32ms :  1000 MB/s (nem gyorsítótárazott), ami a fizikai mag 62,5 MB/s arányát jelenti.

Több Azure-lemez csatlakoztatására van szükség a DT 2,0 virtuális géphez, és az operációs rendszer szintjén létre kell hoznia egy szoftveres RAID-t (csíkozást), hogy a maximálisan engedélyezett legyen a lemez sebessége virtuális gépenként. Egyetlen Azure-lemez nem tudja biztosítani az átviteli sebességet a virtuális gép maximális korlátjának eléréséhez. Az Azure Premium Storage használata kötelező a DT 2,0 futtatásához. 

- Az elérhető Azure-lemezekre vonatkozó részletek [itt](../../windows/disks-types.md) találhatók
- A szoftveres RAID mdadm használatával történő létrehozásával kapcsolatos részletek [itt](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) találhatók
- [Itt](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) talál részletes információt az LVM konfigurálásáról egy csíkozott kötet létrehozásához a maximális átviteli sebesség érdekében

A méretre vonatkozó követelményektől függően különböző lehetőségek érhetők el egy virtuális gép maximális átviteli sebességének eléréséhez. Az alábbi lehetséges adatmennyiség-lemezes konfigurációk minden DT 2,0 virtuálisgép-típus esetében elérhetők a virtuális gép maximális átviteli sebességének eléréséhez. A E32sv3 virtuális gépet a kisebb munkaterhelések belépési szintjére kell tekinteni. Ha úgy gondolja, hogy nem elég gyors, szükséges lehet a virtuális gép átméretezése a M64-32ms.
Mivel a M64-32ms virtuális gép sok memóriát tartalmaz, előfordulhat, hogy az i/o-terhelés nem éri el a korlátot, különösen az olvasási igényű számítási feladatok esetében. Emiatt a Stripe-készlet kevesebb lemeze is elegendő lehet az ügyfél-specifikus munkaterheléstől függően. A biztonságos oldalon azonban az alábbi lemez-konfigurációk garantálják a maximális átviteli sebességet:


| VM SKU | Lemez konfigurációja 1 | Lemez konfigurációja 2 | Lemez konfigurációja 3 | 4\. lemez konfigurációja | Lemez konfigurációja 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 – > 8 TB | 5 x P30 – > 5 TB | 7 x P20 – > 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 – > 6 TB | 4 x P30 – 4 TB > | 5 x P20 – > 2,5 TB | 6 x P15 – > 1,5 TB | 


Különösen abban az esetben, ha a munkaterhelés olvasása intenzív, az i/o-teljesítmény növelése érdekében az Azure-gazdagép gyorsítótárának "írásvédett" értékre való bekapcsolásához ajánlott az adatbázis-szoftverek adatmennyiségének használata. Míg a tranzakciónapló Azure-gazdagépének gyorsítótára "None" lehet. 

A naplózási kötet méretével kapcsolatban javasolt kiindulási pont az adatméret 15%-a. A naplózási kötet létrehozása a költségeket és az átviteli sebességre vonatkozó követelményektől függően különböző Azure-lemezek használatával végezhető el. A naplózási kötet magas I/O-átviteli sebességére van szükség.  A M64-32ms virtuális gép típusának használata esetén erősen ajánlott a [írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)engedélyezése. Az Azure írásgyorsító a tranzakciós napló optimális lemez-írási késését biztosítja (csak az M sorozat esetében érhető el). Néhány elemet figyelembe kell venni, bár a virtuális gépek maximális száma a virtuálisgép-típusok esetében. A írásgyorsító részletei [itt](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) találhatók


Íme néhány példa a naplózási kötet méretezésére:

| az adatmennyiség mérete és a lemez típusa | a naplózási kötet és a lemez típusa konfiguráció 1 | naplózási kötet és lemez típusa 2. konfiguráció |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 – > 2,5 TB | 3 x P30 – > 3 TB |
| 6 x P15 – > 1,5 TB | 4 x P6 – > 256 GB | 1 x P15 -> 256 GB |


A SAP HANA skálázáshoz hasonlóan a/Hana/Shared könyvtárat is meg kell osztani a SAP HANA virtuális gép és a DT 2,0 virtuális gép között. Ugyanazt az architektúrát használja, mint a SAP HANA kibővíthető dedikált virtuális gépekkel, amelyek egy magasan elérhető NFS-kiszolgálóként működnek. A megosztott biztonsági mentési kötetek megadásához az azonos kialakítás is használható. Az ügyfél azonban akkor is igénybe vehető, ha szükség lenne rá, vagy ha elég, hogy csak egy dedikált virtuális gépet használjon, amely elegendő tárolókapacitással rendelkezik ahhoz, hogy biztonsági mentési kiszolgálóként működjön.



### <a name="links-to-dt-20-documentation"></a>Hivatkozások a DT 2,0 dokumentációra 

- [SAP HANA a dinamikus rétegek telepítési és frissítési útmutatója](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [A dinamikus rétegek SAP HANA oktatóanyagok és erőforrások](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinamikus rétegű PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2,0 SPS 02 dinamikus rétegek fejlesztései](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>SAP HANA Azure-beli virtuális gépeken való üzembe helyezésének műveletei
A következő szakaszok ismertetik az Azure-beli virtuális gépeken SAP HANA rendszerek üzembe helyezésével kapcsolatos műveleteket.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentésével és visszaállításával kapcsolatos műveletek
A következő dokumentumok azt ismertetik, hogyan lehet biztonsági másolatot készíteni és visszaállítani a SAP HANA üzembe helyezését:

- [SAP HANA biztonsági mentés áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA fájl szintű biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA tárolási Pillanatképek teljesítményteszte](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANAt tartalmazó virtuális gépek elindítása és újraindítása
Az Azure nyilvános felhő Kiemelt funkciója, hogy csak a számítástechnikai percért kell fizetnie. Ha például egy SAP HANA rendszert futtató virtuális gépet állít le, akkor az adott idő alatt csak a tárolási költségekért kell fizetnie. Egy másik funkció akkor érhető el, ha statikus IP-címeket ad meg a virtuális gépekhez a kezdeti üzembe helyezés során. SAP HANAt tartalmazó virtuális gép újraindításakor a virtuális gép a korábbi IP-címeivel indul újra. 


### <a name="use-saprouter-for-sap-remote-support"></a>A SAProuter használata az SAP távoli támogatásához
Ha helyek közötti kapcsolattal rendelkezik a helyszíni helyek és az Azure között, és SAP-összetevőket futtat, akkor valószínűleg már fut a SAProuter. Ebben az esetben végezze el a következő elemeket a távoli támogatáshoz:

- Tartsa karban a SAProuter-konfigurációban SAP HANA üzemeltető virtuális gép magánhálózati és statikus IP-címét.
- Konfigurálja a HANA virtuális gépet üzemeltető alhálózat NSG a 3299-es TCP/IP-porton keresztüli forgalom engedélyezéséhez.

Ha az internetről csatlakozik az Azure-hoz, és nem rendelkezik SAP HANA-vel rendelkező virtuális géphez tartozó SAP-útválasztóval, akkor telepítenie kell az összetevőt. Telepítse a SAProuter-t egy különálló virtuális gépen a felügyeleti alhálózaton. Az alábbi képen egy durva séma látható a SAP HANA helyek közötti kapcsolat és a SAProuter nélküli üzembe helyezéséhez:

![Durva üzembe helyezési séma SAP HANA helyek közötti kapcsolat és SAProuter nélkül](media/hana-vm-operations/hana-simple-networking3.PNG)

Ügyeljen arra, hogy a SAProuter külön virtuális gépre telepítse, és ne a Jumpbox virtuális gépén. A különálló virtuális gépnek statikus IP-címmel kell rendelkeznie. A SAProuter az SAP által üzemeltetett SAProuter való összekapcsolásához forduljon az SAP-hoz egy IP-cím használatával. (Az SAP által üzemeltetett SAProuter a virtuális gépre telepített SAProuter-példány párja.) A SAProuter-példány konfigurálásához használja az SAP IP-címét. A konfigurációs beállításokban az egyetlen szükséges port a 3299-as TCP-port.

A SAProuter-en keresztüli távoli támogatási kapcsolatok beállításával és karbantartásával kapcsolatos további információkért tekintse meg az [SAP dokumentációját](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Magas rendelkezésre állás a SAP HANA Azure-beli natív virtuális gépeken
Ha a SUSE Linux Enterprise Server for SAP Applications 12 SP1 vagy újabb rendszert futtat, létrehozhat egy pacemaker-fürtöt STONITH-eszközökkel. Az eszközök használatával olyan SAP HANA konfigurációt állíthat be, amely szinkron replikációt használ a HANA rendszerreplikációval és az automatikus feladatátvételsel. További információ a telepítési eljárásról: [SAP HANA magas rendelkezésre állású útmutató az Azure Virtual Machines szolgáltatáshoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
