---
title: Az Azure virtuális hálózatról – gyakori kérdések
titlesuffix: Azure Virtual Network
description: Válaszok a Microsoft Azure virtuális hálózatokkal kapcsolatos leggyakoribb kérdésekre.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: e45d5393833973889b28a95ec86b89593a091f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244809"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure Virtual Network – Gyakori kérdések (GYIK)

## <a name="virtual-network-basics"></a>A virtuális hálózat alapjai

### <a name="what-is-an-azure-virtual-network-vnet"></a>Mi az Azure virtuális hálózat (VNet)?
Az Azure virtuális hálózat (VNet) a saját hálózatának a felhőben reprezentációja. A hálózat az előfizetésre kijelölt Azure-felhő logikai elkülönítése. Virtuális hálózatok használatával üzembe helyezheti és kezelheti a virtuális magánhálózatokat (VPN-eket) az Azure-ban, és adott esetben összekapcsolhatja a virtuális hálózatokat más virtuális hálózatokkal az Azure-ban, vagy a helyszíni informatikai infrastruktúrával hibrid vagy létesítmények közötti megoldások létrehozásához. Minden létrehozott virtuális hálózat saját CIDR-blokk, és összekapcsolható más virtuális hálózatok és a helyszíni hálózatok, mindaddig, amíg a CIDR-blokkok nem fedik egymást. A virtuális hálózatok DNS-kiszolgálójának beállításait is szabályozhatja, és a virtuális hálózat alhálózatokra történő szegmentálását is.

A virtuális hálózatok használatával:

* Hozzon létre egy dedikált, csak privát felhőalapú virtuális hálózatot. Előfordulhat, hogy nincs szükség a megoldás több helyiségen átnyúló konfigurációra. Virtuális hálózat létrehozásakor a szolgáltatások és a virtuális hálózatokon belüli virtuális gépek közvetlenül és biztonságosan kommunikálhatnak egymással a felhőben. Továbbra is konfigurálhatja a végpontkapcsolatokat a virtuális gépek hez és az internetes kommunikációt igénylő szolgáltatásokhoz a megoldás részeként.

* Biztonságosan bővítse adatközpontját. A virtuális hálózatok segítségével hagyományos helyek közötti (S2S) VPN-eket hozhat létre az adatközpont-kapacitás biztonságos méretezéséhez. Az S2S VPN-ek az IPSEC használatával biztosítbiztonságos kapcsolatot a vállalati VPN-átjáró és az Azure között.

* Hibrid felhőbeli forgatókönyvek engedélyezése. A virtuális hálózatok rugalmasságot biztosítanak a hibrid felhőbeli forgatókönyvek széles körének támogatásához. A felhőalapú alkalmazásokat biztonságosan csatlakoztathatja bármilyen helyszíni rendszerhez, például nagyszámítógépekhez és Unix-rendszerekhez.

### <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
A kezdéshez látogasson el a [virtuális hálózat dokumentációjára.](https://docs.microsoft.com/azure/virtual-network/) Ez a tartalom áttekintést és telepítési információkat nyújt az összes virtuális hálózat szolgáltatásához.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Használhatom a virtuális hálózatokat több telephelyen átnyúló kapcsolat nélkül?
Igen. A virtuális hálózat használata anélkül, hogy csatlakoztatja a helyiséghez. Például futtathatja a Microsoft Windows Server Active Directory tartományvezérlők és a SharePoint-farmok kizárólag egy Azure virtuális hálózat.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Végrehajthatok WAN-optimalizálást a virtuális hálózatok vagy a virtuális hálózat és a helyszíni adatközpont között?
Igen. A [WAN-optimalizálási hálózati virtuális berendezés](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) térhet üzembe több gyártótól az Azure Marketplace-en keresztül.

## <a name="configuration"></a>Konfiguráció

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Milyen eszközöket használhatok a virtuális hálózat létrehozásához?
A következő eszközökkel hozhat létre vagy konfigurálhat virtuális hálózatot:

* Azure portál
* PowerShell
* Azure CLI
* Hálózati konfigurációs fájl (netcfg - csak klasszikus virtuális hálózatokhoz). Tekintse meg a [Virtuális hálózat konfigurálása hálózati konfigurációs fájl használatával](virtual-networks-using-network-configuration-file.md) cikket.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Milyen címtartományokat használhatok a virtuális hálózatokban?
Az [1918.RFC-ben](https://tools.ietf.org/html/rfc1918)meghatározott bármely IP-címtartomány. Például 10.0.0.0/16. A következő címtartományok nem adhatók hozzá:
* 224.0.0.0/4 (csoportos küldés)
* 255.255.255.255/32 (Adás)
* 127.0.0.0/8 (Visszacsatolás)
* 169.254.0.0/16 (Link-helyi)
* 168.63.129.16/32 (Belső DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Lehet nyilvános IP-címeket a virtuális hálózatokban?
Igen. A nyilvános IP-címtartományokról további információt a [Virtuális hálózat létrehozása](manage-virtual-network.md#create-a-virtual-network)című témakörben talál. A nyilvános IP-címek nem érhetők el közvetlenül az internetről.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Korlátozva van az alhálózatok száma a virtuális hálózaton?
Igen. A részleteket az [Azure-korlátokban](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) találja. Az alhálózati címterek nem fedhetik át egymást.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Vannak-e korlátozások az IP-címek használatára ezeken az alhálózatokon belül?
Igen. Az Azure minden alhálózaton lefoglal 5 IP-címet. These are x.x.x.0-x.x.x.3 and the last address of the subnet. x.x.x.1-x.x.x.3 minden alhálózatban az Azure-szolgáltatások számára van fenntartva.   
- x.x.x.0: Hálózati cím
- x.x.x.1: Az Azure az alapértelmezett átjáró számára van fenntartva
- x.x.x.2, x.x.x.3: Az Azure számára fenntartva az Azure DNS IP-k leképezése a virtuális hálózat területére
- x.x.x.255: Hálózati szórási cím

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Milyen kicsi és milyen nagy lehet a virtuális hálózatok és az alhálózatok?
A legkisebb támogatott IPv4-alhálózat /29, a legnagyobb pedig a /8 (CIDR alhálózati definíciók használatával).  Az IPv6-alhálózatoknak pontosan /64 méretűnek kell lenniük.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Vihetem a VLAN-okat az Azure-ba a virtuális hálózatok használatával?
Nem. A virtuális hálózatok layer-3 átfedések. Az Azure nem támogatja a Layer-2 szemantika.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Megadhatok egyéni útválasztási szabályzatokat a virtuális hálózatokon és az alhálózatokon?
Igen. Létrehozhat egy útvonaltáblát, és társíthatja azt egy alhálózathoz. Az Azure-beli útválasztásról az [Útválasztás áttekintése című témakörben olvashat bővebben.](virtual-networks-udr-overview.md#custom-routes)

### <a name="do-vnets-support-multicast-or-broadcast"></a>A virtuális hálózatok támogatják a csoportos küldést vagy a szórást?
Nem. A csoportos küldés és a szórás nem támogatott.

### <a name="what-protocols-can-i-use-within-vnets"></a>Milyen protokollokat használhatok a virtuális hálózatokon belül?
A Virtuális hálózatokon belül tcp, UDP és ICMP TCP/IP protokollok használhatók. Az egyedi küldés a virtuális hálózatokon belül támogatott, kivéve a DYNAMIC Host Configuration Protocol (DHCP) protokollt az egyedi küldésen keresztül (UDP/68 forrásport / UDP/67 célport). A csoportcímes, szórásos, IP-in-IP-ben beágyazott csomagok és az általános útválasztási beágyazási (GRE) csomagok blokkolva vannak a virtuális hálózatokon belül. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Pingelhetem az alapértelmezett útválasztókat a virtuális hálózaton belül?
Nem.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Használhatom a tracert-t a kapcsolat diagnosztizálására?
Nem.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Hozzáadhatok alhálózatokat a virtuális hálózat létrehozása után?
Igen. Alhálózatok bármikor hozzáadhatók a virtuális hálózatokhoz, amíg az alhálózati címtartomány nem része egy másik alhálózatnak, és a virtuális hálózat címtartományában van szabad hely.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Módosíthatom az alhálózat méretét a létrehozása után?
Igen. Egy alhálózatot akkor vehet fel, távolíthat el, bővíthet vagy szűkíthet, ha nincsenek benne üzembe helyezett virtuális gépek vagy szolgáltatások.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Módosíthatom az alhálózatokat a létrehozásuk után?
Igen. Hozzáadhatja, eltávolíthatja és módosíthatja a virtuális hálózat által használt CIDR-blokkokat.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Ha virtuális hálózaton futtatom a szolgáltatásaim, csatlakozhatok az internethez?
Igen. A virtuális hálózaton belül telepített összes szolgáltatás képes csatlakozni az internethez. Ha többet szeretne tudni az Azure-beli kimenő internetkapcsolatokról, olvassa el a [Kimenő kapcsolatok témakört.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha az Erőforrás-kezelőn keresztül telepített erőforráshoz szeretne bejövő csatlakozni, az erőforráshoz nyilvános IP-címmel kell rendelkeznie. A nyilvános IP-címekről a [Nyilvános IP-címek](virtual-network-public-ip-address.md)című témakörben olvashat bővebben. Az Azure-ban üzembe helyezett minden Azure Cloud Service rendelkezik egy nyilvánosan címezhető VIP-vel. A PaaS-szerepkörök bemeneti végpontjait és a virtuális gépek végpontjait definiálja, hogy ezek a szolgáltatások az internetről érkező kapcsolatokat fogadhassanak.

### <a name="do-vnets-support-ipv6"></a>A virtuális hálózatok támogatják az IPv6-ot?
Igen, a virtuális hálózatok lehetnek csak IPv4-es vagy kétverű (IPv4+IPv6).  További információt az [IPv6 azure virtuális hálózatokhoz való – áttekintése című témakörben talál.](./ipv6-overview.md)

### <a name="can-a-vnet-span-regions"></a>A virtuális hálózat span régiók?
Nem. A virtuális hálózat egyetlen régióra korlátozódik. A virtuális hálózat azonban átfogja a rendelkezésre állási zónákat. A rendelkezésre állási zónákkal kapcsolatos további információkért tekintse meg [a rendelkezésre állási zónák áttekintését](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A virtuális hálózati társviszony-létesítéssel különböző régiókban lévő virtuális hálózatokat is csatlakoztathatja. További információt a [Virtuális hálózati társviszony-létesítés – áttekintés című témakörben talál.](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Csatlakoztathatok egy virtuális hálózatot egy másik virtuális hálózathoz az Azure-ban?
Igen. Egy virtuális hálózatot egy másik virtuális hálózathoz csatlakoztathat a következő konhasználatával:
- **Virtuális hálózati társviszony-létesítés**: A virtuális [hálózatok társviszony-létesítésének áttekintése című témakörben olvashat részletesen.](virtual-network-peering-overview.md)
- **Egy Azure VPN-átjáró:** A részletekért [lásd: VNet-vnet kapcsolat konfigurálása.](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

## <a name="name-resolution-dns"></a>Névfeloldás (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Milyen DNS-beállítások vannak a virtuális hálózatokhoz?
Használja a döntési táblázat a névfeloldás a [virtuális gépek és a szerepkörpéldányok](virtual-networks-name-resolution-for-vms-and-role-instances.md) lapon végigvezeti az összes rendelkezésre álló DNS-beállítások.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Megadhatok DNS-kiszolgálókat a virtuális hálózathoz?
Igen. A DNS-kiszolgáló IP-címeit a virtuális hálózat beállításaiban adhatja meg. A beállítás a virtuális hálózat összes virtuális gépének alapértelmezett DNS-kiszolgálójaként lesz alkalmazva.

### <a name="how-many-dns-servers-can-i-specify"></a>Hány DNS-kiszolgálót adhatok meg?
Referencia [Azure-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Módosíthatom a DNS-kiszolgálóimat a hálózat létrehozása után?
Igen. A virtuális hálózat DNS-kiszolgálói listáját bármikor módosíthatja. Ha módosítja a DNS-kiszolgálók listáját, az új DNS-beállítások érvénybe léptetéséhez a virtuális hálózat összes érintett virtuális gépén végre kell hajtania a DHCP-címbérlet-megújítást. Windows operációs rendszerű virtuális gépek esetén `ipconfig /renew` ezt közvetlenül a virtuális gépbe való beírással teheti meg. Más operációsrendszer-típusok esetén tekintse meg az adott operációsrendszer-típus DHCP-címbérlet-megújítási dokumentációját. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Mi az Azure által biztosított DNS, és működik a virtuális hálózatok?
Az Azure által biztosított DNS a Microsoft által kínált több-bérlős DNS-szolgáltatás. Az Azure regisztrálja az összes virtuális gépés a felhőalapú szolgáltatás szerepkörpéldányait ebben a szolgáltatásban. Ez a szolgáltatás az ugyanazon felhőszolgáltatásban található virtuális gépek és szerepkörpéldányok állomásnév szerinti névfeloldását, valamint az ugyanazon virtuális hálózatok és szerepkörpéldányok teljes tartományneve alapján biztosítja. A DNS-ről a [Virtuális gépek és a Felhőszolgáltatások szerepkörpéldányok névfeloldása című témakörben olvashat bővebben.](virtual-networks-name-resolution-for-vms-and-role-instances.md)

Van egy korlátozás, hogy az első 100 felhőszolgáltatások egy virtuális hálózat a bérlők közötti névfeloldás az Azure által biztosított DNS használatával. Ha saját DNS-kiszolgálót használ, ez a korlátozás nem érvényes.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Felülbírálhatom a DNS-beállításokat virtuális gépenként vagy felhőalapú szolgáltatásonként?
Igen. Beállíthatja, hogy a VIRTUÁLIS GÉPvagy a felhőalapú szolgáltatás dns-kiszolgálói felülbírálják az alapértelmezett hálózati beállításokat. Javasoljuk azonban, hogy a lehető legnagyobb mértékben használja a hálózati DNS-t.

### <a name="can-i-bring-my-own-dns-suffix"></a>Hozhatok saját DNS-utótagot?
Nem. A virtuális hálózatokhoz nem adhat meg egyéni DNS-utótagot.

## <a name="connecting-virtual-machines"></a>Virtuális gépek csatlakoztatása

### <a name="can-i-deploy-vms-to-a-vnet"></a>Telepíthetem a virtuális gépeket egy virtuális hálózatra?
Igen. Az Erőforrás-kezelő telepítési modelljén keresztül üzembe helyezett virtuális géphez csatlakoztatott összes hálózati adaptert (NIC) egy virtuális hálózathoz kell csatlakoztatni. A klasszikus üzembe helyezési modellen keresztül üzembe helyezett virtuális gépek opcionálisan csatlakoztathatók egy virtuális hálózathoz.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Milyen típusú IP-címeket rendelhetek a virtuális gépekhez?
* **Privát:** Az egyes virtuális gépeken belüli egyes hálózati adapterek hozzárendelt. A cím statikus vagy dinamikus módszerrel van hozzárendelve. A privát IP-címek a virtuális hálózat alhálózati beállításaiban megadott tartományból vannak hozzárendelve. A klasszikus üzembe helyezési modellen keresztül üzembe helyezett erőforrások privát IP-címek vannak hozzárendelve, még akkor is, ha nem csatlakozik a virtuális hálózathoz. A foglalási módszer viselkedése attól függően eltérő, hogy egy erőforrást az Erőforrás-kezelővel vagy a klasszikus központi telepítési modellel telepítettek-e: 

  - **Erőforrás-kezelő**: A dinamikus vagy statikus metódussal hozzárendelt magánhálózati IP-cím az erőforrás törléséig egy virtuális géphez (Erőforrás-kezelőhöz) van rendelve. A különbség az, hogy válassza ki a címet hozzárendelni statikus használata esetén, és az Azure választja, ha dinamikus használatával. 
  - **Klasszikus**: A dinamikus metódussal hozzárendelt privát IP-cím változhat, ha egy virtuális gép (klasszikus) virtuális gép újraindul, miután a leállított (felszabadított) állapotban van. Ha meg kell győződnie arról, hogy a klasszikus központi telepítési modellen keresztül üzembe helyezett erőforrás privát IP-címe soha nem változik, rendeljen hozzá egy privát IP-címet a statikus metódussal.

* **Nyilvános:** Igény szerint az Azure Resource Manager üzembe helyezési modelljén keresztül üzembe helyezett virtuális gépekhez csatlakoztatott hálózati adapterekhez van hozzárendelve. A cím hozzárendelhető a statikus vagy dinamikus elosztási módszerrel. A klasszikus üzembe helyezési modellen keresztül telepített összes virtuális gép és felhőszolgáltatások szerepkörpéldánya egy felhőszolgáltatáson belül található, amely *dinamikus*, nyilvános virtuális IP-címhez van rendelve. A nyilvános *statikus* IP-cím, az úgynevezett [fenntartott IP-cím,](virtual-networks-reserved-public-ip.md)opcionálisan vip-címként is hozzárendelhető. Nyilvános IP-címeket rendelhet az egyes virtuális gépekhez vagy a Cloud Services szerepkörpéldányokhoz a klasszikus üzembe helyezési modellen keresztül. Ezeket a címeket [példányszintű nyilvános IP-címnek (ILPIP-címeknek)](virtual-networks-instance-level-public-ip.md) nevezzük, és dinamikusan rendelhetők hozzá.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Foglalhatok le egy privát IP-címet egy virtuális géphez, amelyet később fogok létrehozni?
Nem. Privát IP-cím nem foglalható le. Ha egy privát IP-cím elérhető, a DHCP-kiszolgáló hozzárendeli azt egy virtuális géphez vagy szerepkörpéldányhoz. A virtuális gép lehet, hogy nem az, amelyhez hozzá kívánja rendelni a privát IP-címet. Azonban módosíthatja a már létrehozott virtuális gép privát IP-címét bármely elérhető privát IP-címre.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>A privát IP-címek változnak a virtuális hálózatokban lévő virtuális gépeknél?
Attól függ. Ha a virtuális gép az Erőforrás-kezelőn keresztül lett telepítve, nem, függetlenül attól, hogy az IP-cím a statikus vagy dinamikus foglalási módszerrel van-e hozzárendelve. Ha a virtuális gép a klasszikus üzembe helyezési modellen keresztül lett telepítve, a dinamikus IP-címek változhatnak, amikor egy virtuális gép elindul, miután a leállított (felszabadított) állapotban van. A cím a virtuális gép bármelyik telepítési modellen keresztül üzembe helyezett virtuális gépről szabadul fel, amikor a virtuális gép törlődik.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Manuálisan rendelhetek IP-címeket a virtuális gép operációs rendszerén belüli hálózati adapterekhez?
Igen, de nem ajánlott, kivéve, ha szükséges, például ha több IP-címet rendel egy virtuális géphez. További információt a [Több IP-cím hozzáadása virtuális géphez című témakörben talál.](virtual-network-multiple-ip-addresses-portal.md#os-config) Ha a virtuális géphez csatlakoztatott Azure NIC-hez rendelt IP-cím megváltozik, és a virtuális gép operációs rendszerében az IP-cím eltérő, elveszíti a virtuális géphez való kapcsolódást.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Ha leállítok egy Felhőszolgáltatás központi telepítési helytárolóját, vagy leállítok egy virtuális gépet az operációs rendszeren belülről, mi történik az IP-címekkel?
Semmi. Az IP-címek (nyilvános, nyilvános és privát) továbbra is hozzá vannak rendelve a felhőalapú szolgáltatás üzembe helyezési helyhez vagy virtuális géphez.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Áthelyezhetem a virtuális gépeket az egyik alhálózatból a másikba egy virtuális hálózatban újratelepítés nélkül?
Igen. További információkat a [Virtuálisgép vagy szerepkörpéldány áthelyezése egy másik alhálózati cikkben](virtual-networks-move-vm-role-to-subnet.md) található.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Konfigurálhatok statikus MAC-címet a virtuális gépemhez?
Nem. A MAC-cím nem konfigurálható statikusan.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>A MAC-cím ugyanaz marad a virtuális gép, miután létrehozta?
Igen, a MAC-cím ugyanaz marad a virtuális gép üzembe helyezett mind az Erőforrás-kezelő és a klasszikus üzembe helyezési modellek, amíg el nem törlik. Korábban a MAC-cím akkor lett feladva, ha a virtuális gép le lett állítva (felszabadítva), de most a MAC-cím akkor is megmarad, ha a virtuális gép felszabadított állapotban van. A MAC-cím mindaddig hozzá van rendelve a hálózati adapterhez, amíg a hálózati adaptert meg nem törlik, vagy meg nem változtatja az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt magánhálózati IP-címet. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Csatlakozhatok az internethez egy virtuális gép ről egy virtuális hálózatban?
Igen. A virtuális hálózaton belül telepített összes virtuális gép és felhőszolgáltatások szerepkör-példány a virtuális hálózaton belül csatlakozhat az internethez.

## <a name="azure-services-that-connect-to-vnets"></a>Virtuális hálózatokhoz csatlakozó Azure-szolgáltatások

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Használhatom az Azure App Service Web Apps alkalmazást virtuális hálózattal?
Igen. Web-alkalmazások at egy virtuális hálózaton belül egy ASE (App Service Environment), csatlakoztassa a háttér-az alkalmazások a virtuális hálózatok virtuális hálózat integrációval, és a bejövő forgalom zárolását az alkalmazáshoz a szolgáltatás végpontok használatával. További információkért tekintse át a következő cikkeket:

* [Az App Service hálózati szolgáltatásai](../app-service/networking-features.md)
* [Webalkalmazások létrehozása App Service-környezetben](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Alkalmazás integrálása egy Azure-beli virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Az App Service hozzáférési korlátozásai](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Üzembe helyezhetem a felhőszolgáltatásokat webes és feldolgozói szerepkörökkel (PaaS) egy virtuális hálózatban?
Igen. A Felhőszolgáltatások szerepkörpéldányait (tetszés szerint) telepítheti a virtuális hálózatokon belül. Ehhez adja meg a virtuális hálózat nevét és a szerepkör/alhálózati hozzárendeléseket a szolgáltatáskonfiguráció hálózati konfigurációs szakaszában. Nem kell frissítenie a bináris fájlokat.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Csatlakoztathatok egy virtuálisgép-méretezési készletet egy virtuális hálózathoz?
Igen. Virtuálisgép-méretezési készletet kell csatlakoztatnia egy virtuális hálózathoz.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Van-e teljes lista az Azure-szolgáltatások, amelyek erőforrásoküzembe helyezhetők egy virtuális hálózat?
Igen, További információt az [Azure-szolgáltatások virtuális hálózati integrációja .Yes,](virtual-network-for-azure-services.md)For details, see Virtual Network integration for Azure services.

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Hogyan korlátozhatja az Azure PaaS-erőforrásokhoz való hozzáférést egy virtuális hálózatról?

Egyes Azure PaaS-szolgáltatásokon (például az Azure Storage-on és az Azure SQL Database-en) keresztül üzembe helyezett erőforrások korlátozhatják a virtuális hálózathoz való hálózati hozzáférést a virtuális hálózati szolgáltatás végpontjainak vagy az Azure Private Link használatával. További részletek: [Virtuális hálózati szolgáltatásvégpontok –](virtual-network-service-endpoints-overview.md)Azure Private Link – [áttekintés](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Áthelyezhetem a szolgáltatásaim at a virtuális hálózatokba és ki is vihető?
Nem. A virtuális hálózatokba nem helyezhet át szolgáltatásokat. Ha egy erőforrást át szeretne helyezni egy másik virtuális hálózatba, törölnie kell és újra kell telepítenie az erőforrást.

## <a name="security"></a>Biztonság

### <a name="what-is-the-security-model-for-vnets"></a>Mi a virtuális hálózatok biztonsági modellje?
Virtuális hálózatok vannak elkülönítve egymástól, és az Azure-infrastruktúrában üzemeltetett egyéb szolgáltatások. A virtuális hálózat egy megbízhatósági határ.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Korlátozhatja a bejövő vagy kimenő forgalom a virtuális hálózathoz csatlakoztatott erőforrások?
Igen. [Hálózati biztonsági csoportokat](security-overview.md) alkalmazhat a virtuális hálózaton, a virtuális hálózathoz csatlakoztatott hálózati adaptereken vagy mindkettőn belüli egyes alhálózatokra.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Létrehozhatok tűzfalat a virtuális hálózathoz csatlakoztatott erőforrások között?
Igen. Az Azure Piactéren keresztül több gyártótól is üzembe helyezhet egy [tűzfalhálózati virtuális berendezést.](https://azure.microsoft.com/marketplace/?term=firewall)

### <a name="is-there-information-available-about-securing-vnets"></a>Van információ a virtuális hálózatok biztonságossá arról?
Igen. További információt az [Azure Network Security – áttekintés című témakörben talál.](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="apis-schemas-and-tools"></a>API-k, sémák és eszközök

### <a name="can-i-manage-vnets-from-code"></a>Kezelhetem a virtuális hálózatokat a kódból?
Igen. Használhatja a REST API-k virtuális hálózatok az [Azure Resource Manager](/rest/api/virtual-network) és a [klasszikus](https://go.microsoft.com/fwlink/?LinkId=296833) üzembe helyezési modellek.

### <a name="is-there-tooling-support-for-vnets"></a>Van-e eszköztámogatás a virtuális hálózatokhoz?
Igen. További információ a használatról:
- Az Azure Portalon virtuális hálózatok üzembe helyezéséhez az [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) és a [klasszikus](virtual-networks-create-vnet-classic-pportal.md) üzembe helyezési modellek.
- PowerShell az [Erőforrás-kezelőn](/powershell/module/az.network) és a [klasszikus](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) üzembe helyezési modelleken keresztül üzembe helyezett virtuális hálózatok kezeléséhez.
- Az Azure parancssori felület (CLI) üzembe helyezéséhez és kezeléséhez virtuális hálózatok az [Erőforrás-kezelő](/cli/azure/network/vnet) és a [klasszikus](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) üzembe helyezési modellek en keresztül üzembe helyezett.  

## <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

### <a name="what-is-vnet-peering"></a>Mi az a virtuális hálózat-társviszony-létesítés?
A virtuális hálózati társviszony-létesítés (vagy virtuális hálózati társviszony-létesítés) lehetővé teszi a virtuális hálózatok csatlakoztatását. A virtuális hálózatok közötti virtuális hálózati társviszony-létesítési kapcsolat lehetővé teszi, hogy a közöttük lévő forgalmat az IPv4-címeken keresztül irányítsa. A társviszonyba épített virtuális hálózatokban lévő virtuális gépek úgy kommunikálhatnak egymással, mintha ugyanazon a hálózaton belül lennének. Ezek a virtuális hálózatok lehetnek ugyanabban a régióban, vagy különböző régiókban (más néven globális virtuális társviszony-létesítés). Virtuális hálózat társviszony-létesítési kapcsolatok is létrehozható az Azure-előfizetések között.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Létrehozhatok társviszony-létesítési kapcsolatot egy másik régióban lévő virtuális hálózattal?
Igen. A globális virtuális hálózatok társviszony-létesítése lehetővé teszi, hogy a különböző régiókban lévő virtuális hálózatok társviszonyt létesítsen. A globális virtuális hálózatok társviszony-létesítése az Azure összes nyilvános régiójában, Kína felhőrégióiban és kormányzati felhőrégióiban érhető el. Az Azure nyilvános régióiból nem lehet globálisan társviszonyt létesíteni a nemzeti felhőrégiókközött.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Melyek a globális virtuális társviszony-létesítési és terheléselosztók kapcsolatos korlátozások?
Ha a két virtuális hálózat két különböző régióban a globális virtuális hálózat társviszony-létesítés, nem tud csatlakozni az erőforrások, amelyek mögött az alapszintű terheléselosztó a terheléselosztó front-end IP-címén keresztül. Ez a korlátozás nem létezik standard terheléselosztó esetén.
A következő erőforrások alapszintű terheléselosztókat használhatnak, ami azt jelenti, hogy nem érheti el őket a terheléselosztó front-end IP-címén keresztül a globális virtuális társviszony-létesítésen keresztül. A globális virtuális hálózatok társviszony-létesítése azonban közvetlenül a privát virtuális hálózati IP-címekkel érheti el az erőforrásokat, ha ez megengedett. 
- Virtuális gépek az alapterhelés-elosztók mögött
- Virtuálisgép-méretezési készletek alapterhelés-elosztókkal 
- Redis Cache 
- Alkalmazásátjáró (v1) Termékváltozat
- Service Fabric
- SQL MI
- API Management
- Active Directory tartományi szolgáltatás (ADDS)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service Environment

Ezekhez az erőforrásokhoz az ExpressRoute vagy a VNet-vNet virtuális hálózatok on keresztül virtuális hálózatok keresztül csatlakozhat.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Engedélyezhetem a virtuális hálózati társviszony-létesítést, ha a virtuális hálózataim különböző Azure Active Directory-bérlők előfizetéseihez tartoznak?
Igen. Lehetőség van virtuális hálózati társviszony-létesítés (akár helyi, akár globális) létesítése, ha az előfizetések különböző Azure Active Directory-bérlőkhöz tartoznak. Ezt a PowerShell vagy a CLI segítségével teheti meg. A portál még nem támogatott.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A virtuális hálózat társviszony-létesítési *kapcsolata kezdeményezett* állapotban van, miért nem tudok csatlakozni?
Ha a társviszony-létesítési kapcsolat *kezdeményezett* állapotban van, az azt jelenti, hogy csak egy hivatkozást hozott létre. A sikeres kapcsolat létrehozásához kétirányú kapcsolatot kell létrehozni. Például a társ VNet A és a VNet B, egy kapcsolatot kell létrehozni a VNetA a VNetB és a VNetB a VNetA. A két hivatkozás létrehozása állapotát Összekapcsolt állapotra *módosítja.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>A virtuális hálózat társviszony-létesítési kapcsolata *leválasztott* állapotban van, miért nem tudok társviszony-létesítési kapcsolatot létrehozni?
Ha a virtuális hálózat társviszony-létesítési kapcsolat *leválasztott* állapotban van, az azt jelenti, hogy az egyik létrehozott hivatkozás törlődött. A társviszony-létesítési kapcsolat visszaállításához törölnie kell a hivatkozást, majd újra létre kell hoznia azt.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Társviszony-átvehetek a virtuális hálózatomat egy másik előfizetésben lévő virtuális hálózattal?
Igen. A virtuális hálózatok az előfizetések és a régiók között társviszony-le.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Társviszony-átvehetek két virtuális hálózatot egyező vagy egymást átfedő címtartományokkal?
Nem. A címterek nem fedhetik át egymást a virtuális hálózati társviszony-létesítés engedélyezéséhez.

### <a name="how-much-do-vnet-peering-links-cost"></a>Mennyibe kerülnek a virtuális hálózat társviszony-létesítési kapcsolatok?
A virtuális hálózati társviszony-létesítési kapcsolat létrehozása díjmentes. A társviszony-létesítési kapcsolatok közötti adatátvitel díja kerül. [Lásd itt](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Titkosítva van a virtuális hálózat társviszony-létesítési forgalma?
Nem. A társviszonyba vett virtuális hálózatok erőforrások közötti forgalom privát és elkülönített. Továbbra is teljesen a Microsoft Gerinc.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Miért van a társviszony-létesítési kapcsolat *leválasztott* állapotban?
Virtuális hálózati társviszony-létesítési kapcsolatok megy *leválasztott* állapotba, ha egy virtuális hálózat társviszony-létesítési kapcsolat törlődik. A sikeres társviszony-létesítési kapcsolat visszaállításához mindkét hivatkozást törölnie kell.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Ha a társ VNetA a VNetB és én peer VNetB a VNetC, ez azt jelenti, VNetA és VNetC társviszony-létesített?
Nem. A tranzitív társviszony-létesítés nem támogatott. Ehhez társtársi VNetA és VNetC kell.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Vannak-e sávszélesség-korlátozások a társviszony-létesítési kapcsolatokhoz?
Nem. Virtuális hálózatok társviszony-létesítése, akár helyi, akár globális, nem szab meg sávszélesség-korlátozásokat. Sávszélesség csak korlátozza a virtuális gép vagy a számítási erőforrás.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Hogyan háríthatók el a virtuális hálózatok társviszony-létesítésével kapcsolatos problémák?
Itt van egy [hibaelhárító útmutató,](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) amelyet kipróbálhat.

## <a name="virtual-network-tap"></a>Virtuális hálózat TAP-jai

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Mely Azure-régiók érhetők el a virtuális hálózati TAP számára?
A virtuális hálózati TAP előzetes verzió minden Azure-régióban elérhető. A figyelt hálózati adaptereket, a virtuális hálózati TAP-erőforrást, valamint a gyűjtő- vagy elemzési megoldást ugyanabban a régióban kell telepíteni.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Támogatja a Virtual Network TAP a tükrözött csomagok szűrési lehetőségeit?
A virtuális hálózati TAP előzetes verzió nem támogatja a szűrési lehetőségeket. Amikor egy TAP-konfigurációt ad hozzá egy hálózati adapterhez, a hálózati csatolón lévő összes be- és kimenő forgalom mély másolata kerül a TAP-célba.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Több TAP-konfiguráció is hozzáadható egy figyelt hálózati adapterhez?
A figyelt hálózati adapternek csak egy TAP-konfigurációja lehet. Az egyes [partnermegoldásoknál](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) érdeklődjön atap-forgalom több példányának streameléséhez az Ön által kiválasztott elemzési eszközökre.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Ugyanaz a virtuális hálózati TAP-erőforrás összesítheti a figyelt hálózati adapterek forgalmát egynél több virtuális hálózatban?
Igen. Ugyanaz a virtuális hálózati TAP-erőforrás használható a figyelt hálózati csatolók tükrözött forgalmának összesítésére társviszony-létesített virtuális hálózatokban ugyanabban az előfizetésben vagy egy másik előfizetésben. A virtuális hálózati TAP-erőforrásnak és a célterhelés-elosztónak vagy a célhálózati adapternek ugyanabban az előfizetésben kell lennie. Minden előfizetésnek azonos Azure Active Directory-bérlő alatt kell lennie.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Vannak-e teljesítményszempontok az éles forgalommal kapcsolatban, ha engedélyezem a virtuális hálózati TAP-konfigurációt a hálózati adapteren?

Virtuális hálózat TAP előzetes verzióban érhető el. Az előzetes verzió során nincs szolgáltatásiszint-szerződés. A képesség nem használható éles számítási feladatokhoz. Ha egy virtuálisgép hálózati illesztője engedélyezve van egy TAP-konfigurációval, ugyanazokat az erőforrásokat az Azure-állomás a virtuális gép számára lefoglalt a termelési forgalom küldéséhez a tükrözési funkció végrehajtásához és a tükrözött csomagok küldéséhez. Válassza ki a megfelelő [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretét annak érdekében, hogy elegendő erőforrás álljon rendelkezésre a virtuális gép számára az éles forgalom és a tükrözött forgalom küldéséhez.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>A gyorsított hálózatépítés [Linux](create-vm-accelerated-networking-cli.md) vagy [Windows](create-vm-accelerated-networking-powershell.md) támogatja a virtuális hálózat TAP?

A gyorsított hálózatkezeléssel engedélyezett virtuális géphez csatlakoztatott hálózati adapteren tap-konfigurációt adhat hozzá. A virtuális gép teljesítményét és késését azonban befolyásolja a TAP-konfiguráció hozzáadása, mivel a tükrözési forgalom kiszervezését jelenleg nem támogatja az Azure gyorsított hálózatba adása.

## <a name="virtual-network-service-endpoints"></a>Virtuális hálózati szolgáltatásvégpontok

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Mi a megfelelő műveletsorozat a szolgáltatásvégpontok beállítása egy Azure-szolgáltatáshoz?
Két lépésből áll az Azure-szolgáltatás-erőforrások szolgáltatásvégpontokon keresztüli biztonságossá tétele:
1. Kapcsolja be az Azure-szolgáltatás szolgáltatásvégpontjait.
2. VNet-acl-ok beállítása az Azure-szolgáltatásban.

Az első lépés egy hálózati oldali művelet, a második lépés pedig egy szolgáltatás-erőforrás oldali művelet. Mindkét lépést elvégezheti ugyanaz a rendszergazda vagy különböző rendszergazdák alapján a rendszergazdai szerepkörhöz megadott RBAC-engedélyek alapján. Azt javasoljuk, hogy először kapcsolja be a szolgáltatásvégpontok a virtuális hálózat beállítása előtt vnet-es ACL-ek az Azure szolgáltatásoldalon. Ezért a lépéseket a fent felsorolt sorrendben kell végrehajtani a virtuális hálózat szolgáltatásvégpontjainak beállításához.

>[!NOTE]
> Mindkét fent leírt műveleteket el kell végezni, mielőtt korlátozhatja az Azure-szolgáltatás hozzáférését az engedélyezett virtuális hálózatra és alhálózatra. Csak az Azure-szolgáltatás szolgáltatásvégpontok bekapcsolása a hálózati oldalon nem biztosítja a korlátozott hozzáférést. Emellett be kell állítania a VNet-acl-okat az Azure szolgáltatási oldalán is.

Bizonyos szolgáltatások (például az SQL és a CosmosDB) az **IgnoreMissingVnetServiceEndpoint** jelzőn keresztül kivételeket engedélyeznek a fenti sorrend alól. Miután a jelző **értéke Igaz,** a Virtuálishálózati ACL-ek beállíthatók az Azure szolgáltatás oldalán a szolgáltatásvégpontok beállítása előtt a hálózati oldalon. Az Azure-szolgáltatások biztosítják ezt a jelzőt, amely segítséget nyújt az ügyfeleknek azokban az esetekben, amikor az adott IP-tűzfalak konfigurálva vannak az Azure-szolgáltatásokban, és a hálózati oldali szolgáltatásvégpontok bekapcsolása kapcsolódási csökkenéshez vezethet, mivel a forrás IP-címe nyilvános IPv4-címről egy privát címet. Virtuális hálózati acl-ok beállítása az Azure szolgáltatás oldalán, mielőtt a szolgáltatás végpontok beállítása a hálózati oldalon segíthet elkerülni a kapcsolat csökkenése.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Minden Azure-szolgáltatás az ügyfél által biztosított Azure virtuális hálózatban található? Hogyan működik a VNet-szolgáltatás végpontja az Azure-szolgáltatásokkal?

Nem, nem minden Azure-szolgáltatás található az ügyfél virtuális hálózatában. Az Azure-adatszolgáltatások többsége, például az Azure Storage, az Azure SQL és az Azure Cosmos DB több-bérlős szolgáltatások, amelyek nyilvános IP-címeken érhetők el. Az Azure-szolgáltatások virtuális hálózati integrációjáról itt olvashat [bővebben.](virtual-network-for-azure-services.md) 

When you use the VNet service endpoints feature (turning on VNet service endpoint on the network side and setting up appropriate VNet ACLs on the Azure service side), access to an Azure service is restricted from an allowed VNet and subnet.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Hogyan biztosítja a VNet-szolgáltatás végpontja a biztonságot?

A VNet szolgáltatásvégpont-szolgáltatás (a hálózati oldalon a VNet-szolgáltatásvégpont bekapcsolása és a megfelelő vnet-hozzáférés-hozzáférés-hozzáférés-hozzáférés beállítása az Azure szolgáltatásoldalán) korlátozza az Azure-szolgáltatás hozzáférést az engedélyezett virtuális hálózatra és alhálózatra, így biztosítva a hálózati szintű biztonságot és az Azure szolgáltatásforgalmának elkülönítése. A VNet-szolgáltatás végpontjait használó összes forgalom a Microsoft gerinchálózatán keresztül áramlik, így biztosítva a nyilvános internettől való elkülönítés egy másik rétegét. Ezenkívül az ügyfelek dönthetnek úgy, hogy teljes mértékben eltávolítják a nyilvános internet-hozzáférést az Azure-szolgáltatás erőforrásaihoz, és csak a virtuális hálózatukról engedélyezik a forgalmat az IP-tűzfal és a VNet-hozzáférés-hozzáférés-hozzáférés-ellenőrző eszközök kombinációjával, így megvédve az Azure-szolgáltatás erőforrásait a jogosulatlan Hozzáférés.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Mit véd a VNet-szolgáltatás végpontja – virtuális hálózat-erőforrások vagy Az Azure-szolgáltatás?
A VNet szolgáltatásvégpontok segítenek megvédeni az Azure-szolgáltatás erőforrásait. A virtuális hálózatok erőforrásait a hálózati biztonsági csoportok (NSG-k) védik.

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Van-e költsége a Virtuálishálózat-szolgáltatás végpontjainak használatáért?

Nem, a virtuális hálózat szolgáltatásvégpontok használatáért nincs további költség.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Bekapcsolhatom a VNet-szolgáltatás végpontjait, és beállíthatom a Virtuálishálózati ACL-eket, ha a virtuális hálózat és az Azure-szolgáltatás erőforrásai különböző előfizetésekhez tartoznak?

Igen, ez lehetséges. Virtuális hálózatok és az Azure-szolgáltatás erőforrásait lehet azonos vagy különböző előfizetések. Az egyetlen követelmény az, hogy mind a virtuális hálózat, mind az Azure-szolgáltatás erőforrásainak ugyanabban az Active Directory (AD) bérlőben kell lenniük.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Bekapcsolhatom a Virtuálishálózati szolgáltatás végpontjait, és beállíthatom a Virtuálishálózati ACL-eket, ha a virtuális hálózat és az Azure-szolgáltatás erőforrásai különböző AD-bérlőkhöz tartoznak?
Nem, a VNet-szolgáltatás végpontjai és a Virtuálishálózati ACL-ek nem támogatottak az AD-bérlők számára.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Egy helyszíni eszköz IP-címe, amely az Azure virtuális hálózati átjárón (VPN) vagy expressroute-átjárón keresztül csatlakozik az Azure PaaS-szolgáltatás virtuális hálózati végpontokon keresztül?
Alapértelmezés szerint a virtuális hálózatokhoz biztosított Azure-szolgáltatási erőforrások nem érhetők el helyszíni hálózatokról. Ha engedélyezni szeretné a helyszíni forgalmat, nyilvános (általában NAT) IP-címeket is engedélyeznie kell a helyszíni vagy ExpressRoute-ból. Ezek az IP-címek az Azure-szolgáltatás erőforrásainak IP-tűzfal-konfigurációján keresztül adhatók hozzá.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Használhatom a VNet-szolgáltatásvégpont-szolgáltatást az Azure-szolgáltatás több alhálózathoz való védelméhez egy virtuális hálózaton belül vagy több virtuális hálózaton keresztül?
Ha az Azure-szolgáltatásokat egy virtuális hálózaton belül vagy több virtuális hálózaton belül több alhálózatszámára szeretné biztonságossá tenni, engedélyezze a hálózati oldalon lévő szolgáltatásvégpontokat egymástól függetlenül, majd biztosítsa az Azure-szolgáltatások erőforrásait az összes alhálózat számára a megfelelő VNet-acl-ok az Azure szolgáltatási oldalán.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Hogyan szűrhetem ki a kimenő forgalmat egy virtuális hálózatról az Azure-szolgáltatásokba, és továbbra is használhatom a szolgáltatásvégpontokat?
Ha meg szeretné vizsgálni vagy szűrni szeretné az Azure-szolgáltatásba irányuló forgalmat egy virtuális hálózatról, üzembe helyezhet egy hálózati virtuális berendezést a virtuális hálózaton belül. Ezután szolgáltatásvégpontokat alkalmazhat arra az alhálózatra, ahol a hálózati virtuális berendezés telepítve van, és csak erre az alhálózatra biztosíthatja az Azure-szolgáltatás erőforrásait a Virtuálishálózati acL-eken keresztül. Ez a forgatókönyv akkor is hasznos lehet, ha szeretné korlátozni az Azure szolgáltatás hozzáférést a virtuális hálózatcsak bizonyos Azure-erőforrások segítségével hálózati virtuális berendezés szűrés. További információkért lásd a [kimenő forgalommal és a hálózati virtuális berendezésekkel](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha) foglalkozó témakört.

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Mi történik, ha olyan Azure-szolgáltatásfiókhoz fér hozzá, amelyen a virtuális hálózaton kívülről engedélyezve van egy virtuális hálózati hozzáférés-vezérlési lista??
A RENDSZER a HTTP 403- vagy HTTP 404-es hibát adja vissza.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>A különböző régiókban létrehozott virtuális hálózat alhálózatai hozzáférhetnek egy Azure-szolgáltatásfiókhoz egy másik régióban? 
Igen, a legtöbb Azure-szolgáltatások, a különböző régiókban létrehozott virtuális hálózatok azure-szolgáltatások egy másik régióban a virtuális hálózat végpontokon keresztül érhető el. Ha például egy Azure Cosmos DB-fiók USA nyugati régiójában vagy USA keleti régiójában található, és a virtuális hálózatok több régióban találhatók, a virtuális hálózat hozzáférhet az Azure Cosmos DB-hez. A storage és az SQL kivételek, és regionális jellegűek, és mind a virtuális hálózatnak, mind az Azure-szolgáltatásnak ugyanabban a régióban kell lennie.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Egy Azure-szolgáltatás rendelkezhet virtuális hálózati acl és IP-tűzfal lal is?
Igen, a virtuális hálózati acl és az IP-tűzfal együtt létezhet. Mindkét funkció kiegészíti egymást az elkülönítés és a biztonság érdekében.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Mi történik, ha töröl egy virtuális hálózatot vagy alhálózatot, amelynek szolgáltatásvégpontja be van kapcsolva az Azure-szolgáltatáshoz?
A virtuális hálózatok és alhálózatok törlése független műveletek, és akkor is támogatottak, ha az Azure-szolgáltatások szolgáltatásvégpontjai be vannak kapcsolva. Azokban az esetekben, ahol az Azure-szolgáltatások virtuális hálózati acl-ok beállítása, az ilyen virtuális hálózatok és alhálózatok, az Azure-szolgáltatáshoz társított virtuális hálózat ACL-adatok le van tiltva, ha egy virtuális hálózat vagy alhálózat, amely a Virtuálishálózat-szolgáltatás végpont be van kapcsolva törlődik.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Mi történik, ha egy Olyan Azure-szolgáltatásfiók törlődik, amelynek engedélyezve van a VNet-szolgáltatás végpontja?
Az Azure-szolgáltatásfiók törlése független művelet, és akkor is támogatott, ha a szolgáltatásvégpont engedélyezve van a hálózati oldalon, és a Virtuálishálózati AC-ek az Azure szolgáltatási oldalán vannak beállítva. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Mi történik egy olyan erőforrás (például egy alhálózatban lévő virtuális gép) forrás IP-címével, amelynek a virtuális hálózat végpontja engedélyezve van?
Ha a virtuális hálózati szolgáltatás végpontjai engedélyezve vannak, a virtuális hálózat alhálózatában lévő erőforrások forrás IP-címei a nyilvános IPV4-címek használatáról az Azure virtuális hálózat privát IP-címeire váltanak az Azure-szolgáltatásba irányuló forgalomhoz. Vegye figyelembe, hogy ez okozhat adott IP-tűzfalak, amelyek az Azure-szolgáltatások korábbi nyilvános IPV4-címére vannak beállítva, sikertelenek lehetnek. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>A szolgáltatásvégpont útvonala mindig elsőbbséget élvez?
A szolgáltatásvégpontok olyan rendszerútvonalat adnak hozzá, amely elsőbbséget élvez a BGP-útvonalakkal szemben, és optimális útválasztást biztosít a szolgáltatás végponti forgalmához. A szolgáltatásvégpontok mindig közvetlenül a virtuális hálózatról a szolgáltatásba viszik a szolgáltatásba a Microsoft Azure gerinchálózaton. Ha többet szeretne tudni arról, hogy az Azure hogyan választ útvonalat, olvassa el [az Azure virtuális hálózati forgalom útválasztása című témakört.](virtual-networks-udr-overview.md)
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Hogyan működik az NSG egy alhálózaton a szolgáltatásvégpontokkal?
Az Azure-szolgáltatás eléréséhez az NSG-knek engedélyeznie kell a kimenő kapcsolatot. Ha az NSG-k meg vannak nyitva az összes internetes kimenő forgalom, majd a szolgáltatás végponti forgalom működnie kell. A kimenő forgalmat csak a Szolgáltatáscímkék használatával korlátozhatja a szolgáltatás IP-címére.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Milyen engedélyekre van szükségem a szolgáltatásvégpontok beállításához?
A szolgáltatásvégpontokat a virtuális hálózaton a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználó egymástól függetlenül konfigurálhatja. Az Azure-szolgáltatás erőforrásainak virtuális hálózathoz való biztonságossá tétele érdekében a felhasználónak engedéllyel kell rendelkeznie a **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** számára a hozzáadott alhálózatokhoz. Ez az engedély alapértelmezés szerint a beépített szolgáltatás-rendszergazdai szerepkörrésze, és egyéni szerepkörök létrehozásával módosítható. További információk a beépített szerepkörökről és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json) való hozzárendeléséről.
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Szűrhetem a virtuális hálózati forgalmat az Azure-szolgáltatásokba, és csak bizonyos azure-szolgáltatás-erőforrásokat engedélyezhetek a Virtuálishálózat-szolgáltatás végpontjain keresztül? 

Virtuális hálózati (VNet) szolgáltatás végpontszabályzatok lehetővé teszi, hogy az Azure-szolgáltatások virtuális hálózati forgalmának szűrése, amely lehetővé teszi, hogy csak bizonyos Azure-szolgáltatás erőforrásait a szolgáltatás végpontok on. A végpontszabályzatok részletes hozzáférés-vezérlést biztosítanak a virtuális hálózati forgalomból az Azure-szolgáltatásokba. A szolgáltatásvégpont-házirendekről itt olvashat [bővebben.](virtual-network-service-endpoint-policies-overview.md)

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Az Azure Active Directory (Azure AD) támogatja a virtuális hálózatok szolgáltatásvégpontjait?

Az Azure Active Directory (Azure AD) nem támogatja a szolgáltatás végpontok natívan. A virtuális hálózat szolgáltatásvégpontjait támogató Azure-szolgáltatások teljes listája [itt](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)látható. Vegye figyelembe, hogy a "Microsoft.AzureActiveDirectory" címke a szolgáltatásvégpontokat támogató szolgáltatások ban található az ADLS Gen 1 szolgáltatásvégpontjainak támogatására. Az ADLS Gen 1 esetében az Azure Data Lake Storage Gen1 virtuális hálózati integrációja a virtuális hálózat és az Azure Active Directory (Azure AD) közötti virtuális hálózati végpont-biztonságot használja további biztonsági jogcímek létrehozásához a hozzáférési jogkivonatban. Ezután e jogcímek használatával hitelesíti a virtuális hálózatot az 1. generációs Data Lake Storage-fiókkal, és engedélyezi a hozzáférést. További információ az [Azure Data Lake Store Gen 1 virtuális hálózat integrációjáról](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Korlátozva van, hogy hány virtuálishálózat-szolgáltatás-végpontot állíthatok be a virtuális hálózatból?
A virtuális hálózat virtuális hálózati végpontjainak teljes száma nincs korlátozva. Egy Azure-szolgáltatás erőforrás (például egy Azure Storage-fiók) a szolgáltatások korlátozhatják az erőforrás védelmére használt alhálózatok száma. Az alábbi táblázat néhány példakorlátot mutat be: 

|||
|---|---|
|Azure-szolgáltatás| A virtuális hálózatra vonatkozó szabályok korlátozása|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Áruház V1|  100|
 
>[!NOTE]
> A korlátok az Azure-szolgáltatás belátása szerint változnak. A szolgáltatások részleteit a megfelelő szolgáltatási dokumentációban találja. 




  



