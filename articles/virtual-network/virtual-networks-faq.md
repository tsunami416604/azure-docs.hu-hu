---
title: Az Azure Virtual Network – gyakori kérdések |} A Microsoft Docs
description: A Microsoft Azure virtuális hálózatok leggyakrabban feltett kérdésekre adott válaszok.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: e92c099d9e0dfacff71c13382059acb06037bb1e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999868"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Az Azure Virtual Network – gyakori kérdések (GYIK)

## <a name="virtual-network-basics"></a>Virtuális hálózat alapjai

### <a name="what-is-an-azure-virtual-network-vnet"></a>Mi az az Azure Virtual Network (VNet)?
Az Azure Virtual Network (VNet) saját felhőbeli hálózatának egy reprezentációja. A hálózat az előfizetésre kijelölt Azure-felhő logikai elkülönítése. Virtuális hálózatok kiépítéséhez használt, és kezelheti a virtuális magánhálózatok (VPN) az Azure-ban és, csatol a a virtuális hálózat más virtuális hálózatok az Azure-ban, vagy a helyszíni informatikai infrastruktúra, hibrid vagy létesítmények közötti megoldásokat hozhat létre. Minden egyes virtuális hálózatot hoz létre a saját CIDR-blokkja rendelkezik, és mindaddig, amíg a CIDR-blokkok ne legyenek átfedésben más virtuális hálózatok és a helyszíni hálózatokhoz társított. Akkor is, irányítását a virtuális hálózat DNS-kiszolgálójának beállításait, és a virtuális hálózat szegmentálási alhálózatra.

A virtuális hálózatok használata:

* Hozzon létre egy dedikált privát csak felhőalapú VNet néha nem feltétlenül szükséges a létesítmények közötti konfigurációkhoz a megoldáshoz. Amikor létrehoz egy virtuális hálózathoz, a szolgáltatások és a virtuális hálózaton belüli virtuális gépek kommunikálhatnak közvetlenül és biztonságosan egymással a felhőben. Virtuális gépek és szolgáltatások esetében az internetes kommunikáció a megoldás részeként igénylő végpont kapcsolatok továbbra is konfigurálhatja.
* Biztonságosan kiterjesztheti adatközpontját a virtuális hálózatok, a hagyományos helyek közötti (S2S) VPN-EK biztonságos méretezése az Adatközpont-kapacitás hozhat létre. S2S VPN-eket használja az IPSec PROTOKOLLT adja meg a vállalati VPN-átjáró és az Azure közötti biztonságos kapcsolatot.
* Enable hibrid felhős rendszerekben virtuális hálózatok a támogatja a hibrid felhős rendszerekben számos rugalmasságot biztosítanak. Nagyszámítógépek például a helyszíni rendszer és a Unix rendszerek bármilyen felhőalapú alkalmazásokat biztonságos módon kapcsolhatja össze.

### <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
Látogasson el a [Virtual network dokumentációja](https://docs.microsoft.com/azure/virtual-network/) a kezdéshez. Ez a tartalom a virtuális hálózat a funkciók áttekintése és a központi telepítési információkat nyújt.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Használható a virtuális hálózatok közötti kapcsolatot nélkül?
Igen. Egy virtuális hálózathoz csatlakoztatja a helyszíni eredetű nélkül is használhatja. Például futtathatja a Microsoft Windows Server Active Directory-tartományvezérlőket és a SharePoint-farmok kizárólag egy Azure virtuális hálózat található.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Virtuális hálózatok vagy egy virtuális hálózat és a helyszíni adatközpont közötti WAN-optimalizálás hajthat végre?
Igen. Telepíthet egy [WAN-optimalizálás Hálózati virtuális berendezésen](https://azure.microsoft.com/marketplace/?term=wan+optimization) az Azure piactéren több szállítóktól származó.

## <a name="configuration"></a>Konfiguráció

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Milyen eszközöket használhatok egy virtuális hálózat létrehozásához?
A következő eszközök segítségével hozzon létre vagy a virtuális hálózat konfigurálása:

* Azure Portal
* PowerShell
* Azure CLI
* A hálózat konfigurációs fájljában (a netcfg - csak a klasszikus virtuális hálózatok). Tekintse meg a [konfigurálása egy Vnethez a hálózati konfigurációs fájlt](virtual-networks-using-network-configuration-file.md) cikk.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Milyen címtartományok használható a saját virtuális hálózatokat?
Minden IP-címtartomány meghatározott [RFC 1918](http://tools.ietf.org/html/rfc1918). Ha például a 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Használhatok nyilvános IP-címek a virtuális Hálózataimnak?
Igen. További információ a nyilvános IP-címtartomány: [hozzon létre egy virtuális hálózatot](manage-virtual-network.md#create-a-virtual-network). Nyilvános IP-címek nem érhetők el közvetlenül az internetről.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>A virtuális hálózat alhálózatainak száma korlátozva van?
Igen. Lásd: [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) részleteiről. Alhálózati címteret nem lehet átfedésben egymással.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Vannak ezen alhálózatok belüli IP-címek használatával korlátozások?
Igen. Az Azure lefoglalja minden egyes alhálózaton belül néhány IP-cím. Minden egyes alhálózat első és utolsó IP-címét a protokollok megfelelősége érdekében, a x.x.x.1-x.x.x.3 minden egyes alhálózat címét, az Azure-szolgáltatásokhoz használt számára vannak fenntartva.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hogyan kis- és milyen méretű lehet virtuális hálózatokat és alhálózatokat kell?
A legkisebb támogatott alhálózat akár/29 méretű, és a legnagyobb /8 (CIDR alhálózati definíciók használatával).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Hozzáadhatom a saját virtuális helyi hálózatok az Azure virtuális hálózatok használatával?
Nem. Virtuális hálózatok – 3. rétegbeli átfedések találhatóak. Az Azure nem támogatja a bármely 2. rétegbeli szemantikáját.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Meghatározható, hogy egyéni útválasztási házirendek a saját virtuális hálózatok és alhálózatok?
Igen. Hozzon létre egy útválasztási táblázatot, és társíthatja azt egy alhálózathoz. Az Azure-ban útválasztás kapcsolatos további információkért lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Támogatják-e a virtuális hálózatok, csoportos küldésű vagy szórási?
Nem. Csoportos küldés és a közvetítés nem támogatottak.

### <a name="what-protocols-can-i-use-within-vnets"></a>Milyen protokollokat belüli virtuális hálózatok használható?
TCP, UDP és ICMP TCP/IP-protokollok belüli virtuális hálózatok is használhatja. Egyedi küldésű belüli virtuális hálózatok, kivételével Dynamic Host Configuration Protocol (DHCP) egyedi küldésű keresztül támogatott (UDP/68-as portot a forrás / cél UDP vagy 67-es port). Csoportos küldés, üzenetküldéssel, IP-az-IP-beágyazású csomagok és Generic Routing Encapsulation (GRE) csomagok belüli virtuális hálózatok blokkolva van. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Megpingelheti a saját alapértelmezett útválasztók egy virtuális hálózaton?
Nem.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Használhatok tracert kapcsolatok diagnosztizálása?
Nem.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Adhatok hozzá alhálózatokat a virtuális hálózat létrehozása után?
Igen. Alhálózatok hozzáadhat virtuális hálózatok bármikor mindaddig, amíg az alhálózat címtartománya nem része egy másik alhálózatot, és a virtuális hálózat címtartományának left rendelkezésre álló területet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Annak létrehozását követően is módosítani az alhálózat méretét?
Igen. Adja hozzá, távolítsa el, bontsa ki a vagy zsugorítani alhálózat nem virtuális gépek vagy a benne található telepített szolgáltatások esetén nem.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Is módosítani alhálózatok után hoztam létre?
Igen. Adhat hozzá, távolítsa el, és a egy virtuális hálózat által használt CIDR-blokkok módosítása.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Ha saját szolgáltatások futtatok egy virtuális hálózaton, is tudok csatlakozni az internethez?
Igen. Egy virtuális hálózaton üzembe helyezett összes szolgáltatás kapcsolódhatnak kimenő internetkapcsolattal. Az Azure-ban a kimenő internetes kapcsolatok kapcsolatos további információkért lásd: [kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha csatlakozni kíván a Resource Managerrel üzembe helyezett erőforrás bejövő, az erőforrás rendelkeznie kell hozzárendelt nyilvános IP-címet. Nyilvános IP-címek kapcsolatos további információkért lásd: [nyilvános IP-címek](virtual-network-public-ip-address.md). Minden Azure-ban üzembe helyezett Azure-Felhőszolgáltatás egy nyilvánosan címezhető virtuális IP-cím rendelve van. Bemeneti végpontok a PaaS-szerepkörök és a virtuális gépek számára az internetről érkező kapcsolatok fogadására a szolgáltatások engedélyezése végpontok meghatározása.

### <a name="do-vnets-support-ipv6"></a>Támogatják a virtuális hálózatok IPv6?
Nem. Jelenleg nem használható IPv6-alapú virtuális hálózatok. De a saját, rendelje hozzá az IPv6-címek Azure-terheléselosztók betölteni a virtuális gépek terheléselosztása. További információkért lásd: [áttekintése az IPv6 és az Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Virtuális hálózat régióban is kiterjedhet?
Nem. Virtuális hálózat egyetlen régióban történő korlátozódik. Virtuális hálózat, azonban span rendelkezésre állási zónák. A rendelkezésre állási zónákkal kapcsolatos további információkért tekintse meg [a rendelkezésre állási zónák áttekintését](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Különböző régiókban található virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony. További információkért lásd: [virtuális hálózati társviszony-létesítési áttekintése](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Összekapcsolhatok egy virtuális hálózat egy másik virtuális hálózathoz az Azure-ban?
Igen. Egy másik virtuális hálózat vagy egy virtuális hálózat kapcsolódhat:
- **Virtuális hálózatok közötti társviszony**: további információkért lásd: [virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md)
- **Az Azure VPN Gateway**: további információkért lásd: [VNet – VNet kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Névfeloldás (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Mi az a virtuális hálózatok DNS lehetőségeim?
A döntési tábla használata a [virtuális gépek és Szerepkörpéldányok névfeloldása](virtual-networks-name-resolution-for-vms-and-role-instances.md) végigvezeti a DNS lapon elérhető beállítások.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Meghatározható, hogy DNS-kiszolgálók egy virtuális hálózat címtere?
Igen. A virtuális hálózat beállításait a DNS-kiszolgáló IP-címek is megadhat. A beállítás a virtuális hálózaton lévő virtuális gépekre vonatkozik, az alapértelmezett DNS-kiszolgálói.

### <a name="how-many-dns-servers-can-i-specify"></a>DNS-kiszolgálók számának adhat meg?
Hivatkozás [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Miután létrehoztam a hálózat is módosítani saját DNS-kiszolgálók?
Igen. A virtuális hálózat bármikor módosíthatja a DNS-kiszolgálók listáját. Ha módosítja a DNS-kiszolgálók listáját, szüksége lesz a virtuális hálózat számára az új DNS-kiszolgáló begyűjti a virtuális gépek mindegyike újraindítására.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Mi az Azure által biztosított DNS és működik a virtuális hálózatokat?
Azure által biztosított DNS-egy több-bérlős DNS szolgáltatás a Microsoft által kínált. Az Azure ezt a szolgáltatást az összes virtuális gépek és cloud service-szerepkörpéldányok regisztrálja. Ez a szolgáltatás névfeloldása a virtuális gépek és szerepkörpéldányok ugyanazon a felhőszolgáltatáson belüli állomásnév és az FQDN virtuális gépek és szerepkörpéldányok ugyanabban a vnetben. A DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [virtuális gépek és Felhőszolgáltatások szerepkörpéldányok névfeloldása](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Van egy korlátozás az első 100 cloud Services egy virtuális hálózat az Azure által biztosított DNS-sel, több-bérlős névfeloldásra. Ha a saját DNS-kiszolgálót használ, ez a korlátozás nem vonatkozik.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Felülbírálhatja a saját DNS-beállítások és a virtuális gépenkénti vagy cloud service alapon?
Igen. DNS-kiszolgálók felülírják az alapértelmezett hálózati beállításokat a virtuális gépek és felhőszolgáltatások szolgáltatásonként állíthatja be. Ajánlott azonban hálózatra kiterjedő DNS és a lehető legnagyobb mértékben használt.

### <a name="can-i-bring-my-own-dns-suffix"></a>Hozzáadhatom a saját DNS-utótag?
Nem. A virtuális hálózatok esetében nem adható meg egy egyéni DNS-utótagot.

## <a name="connecting-virtual-machines"></a>Csatlakozás virtuális gépekhez

### <a name="can-i-deploy-vms-to-a-vnet"></a>Telepíthetek virtuális gépeket egy virtuális hálózaton?
Igen. A Resource Manager-alapú üzemi modellel üzembe helyezett virtuális Géphez csatolt minden hálózati adapterek (NIC) egy virtuális hálózathoz kell csatlakozniuk. A klasszikus üzemi modellel üzembe helyezett virtuális gépek is összeköthető virtuális hálózathoz.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Mik azok a különböző típusú virtuális gépeket is hozzárendelhetők IP-címek?
* **Személyes:** belül minden virtuális gép minden hálózati Adapterhez rendelve. A cím hozzá van rendelve, vagy a statikus vagy dinamikus metódus használatával. Magánhálózati IP-címek vannak rendelve a tartomány, amelyet a virtuális hálózat alhálózati beállításokat. A klasszikus üzemi modellel üzembe helyezett erőforrásokhoz rendelt magánhálózati IP-címek, még akkor is, ha azok nem csatlakozik virtuális hálózathoz. A lefoglalási módszert működése attól függően változik, e telepítve lett egy erőforrást a Resource Manager vagy a klasszikus üzemi modell: 

  - **Erőforrás-kezelő**: módszerrel oszthatók ki a dinamikus vagy statikus magánhálózati IP-cím hozzárendelve virtuális géphez (Resource Manager) maradjon, az erőforrás törléséig. A különbség az, hogy statikus használatakor hozzárendeléséhez cím kiválasztásához, és az Azure úgy dönt, a dinamikus használatakor. 
  - **Klasszikus**: a dinamikus módszerrel oszthatók ki magánhálózati IP-cím változhat, ha a virtuális gépek (klasszikus) virtuális gép leállított (felszabadított) állapotba elvégzése után újraindul. Győződjön meg arról, hogy soha nem módosul a magánhálózati IP-címet a klasszikus üzemi modellel üzembe helyezett erőforrás van szüksége, ha rendelje hozzá a magánhálózati IP-cím statikus módszerrel.

* **Nyilvános:** igény szerint hozzárendelt, az Azure Resource Manager-alapú üzemi modellel üzembe helyezett virtuális gépekhez csatolt hálózati adapterekhez. A címet a statikus vagy dinamikus kiosztási módszerrel oszthatók ki is hozzárendelhető. A klasszikus üzemi modellel üzembe helyezett virtuális gépek és a Felhőszolgáltatások szerepkörpéldányok keretén belül egy felhőalapú szolgáltatás, amely hozzá van rendelve egy *dinamikus*, nyilvános virtuális IP-címéhez. Nyilvános *statikus* nevű IP-cím egy [fenntartott IP-cím](virtual-networks-reserved-public-ip.md), szükség esetén egy virtuális IP-cím rendelhető. Nyilvános IP-címeket rendelhet az egyes virtuális gépek vagy Cloud Services szerepkör példányai a klasszikus üzemi modellel üzembe helyezett. Ezek az úgynevezett [példány szintű nyilvános IP-Címek (ILPIP](virtual-networks-instance-level-public-ip.md) címek, és dinamikusan lehet hozzárendelni.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Fenntarthatók-e egy magánhálózati IP-címet, amely egy későbbi időpontban fog létrehozhatok virtuális gép?
Nem. Magánhálózati IP-cím nem foglalható le. Magánhálózati IP-cím áll rendelkezésre, ha ezt a jogosultságot egy virtuális gép vagy szerepkörpéldány példányt a DHCP-kiszolgáló. Előfordulhat, hogy a virtuális gép, vagy nem lehet használni kívánt magánhálózati IP-cím rendelve egy. Azonban módosíthatja egy már létrehozott virtuális gép magánhálózati IP-címét az elérhető magánhálózati IP-címek.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Hajtsa végre a magánhálózati IP-címek módosul egy virtuális hálózaton található virtuális gépek számára?
Ez a konkrét licenctől függ. Ha a virtuális gép üzemel a Resource Manageren keresztül, nem, függetlenül attól hozzá volt rendelve az IP-címet a statikus vagy dinamikus kiosztási módszerrel oszthatók ki. Ha a virtuális gép üzemel, a klasszikus üzemi modellel, a dinamikus IP-címek módosíthatja, ha a virtuális gép leállított (felszabadított) állapotba elvégzése után elindult. A cím akkor szabadul fel, a virtuális gép törlésekor vagy-alapú üzemi modellel üzembe helyezett virtuális gépből.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Is manuálisan hozzárendelhetők IP-címek hálózati adaptereket a virtuális gép operációs rendszerén belül?
Igen, de nem ajánlott, kivéve, ha szükséges, például ha több IP-címek hozzárendelése virtuális géphez. További információkért lásd: [több IP-Címének hozzáadása egy virtuális géphez címek](virtual-network-multiple-ip-addresses-portal.md#os-config). Az Azure hálózati adapterhez rendelt IP-cím csatlakoztatva egy virtuális géphez módosításokat, és az IP-címet a virtuális gép operációs rendszerén belül különböző, megszakad a kapcsolat a virtuális gép.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Ha le egy felhőalapú szolgáltatás üzembe helyezési pont vagy az operációs rendszerből a virtuális gép leállítása, mi történik saját IP-címek?
Nincs érték. Az IP-címek (nyilvános virtuális IP-cím, nyilvános és titkos) maradnak hozzárendelve a felhőalapú szolgáltatás üzembe helyezési pont vagy a virtuális Géphez.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Áthelyezhető virtuális gépeket az egyik alhálózatból egy másik alhálózatot a virtuális hálózathoz való újbóli telepítése nélkül?
Igen. A további információt talál a [áthelyezése egy másik alhálózatot a virtuális gép vagy szerepkörpéldány példány](virtual-networks-move-vm-role-to-subnet.md) cikk.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Konfigurálhatok statikus MAC-címet a virtuális gépem?
Nem. A MAC-cím statikusan nem lehet konfigurálni.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>A MAC-cím ugyanaz marad a virtuális gép létrehozása után?
Igen, a MAC-cím ugyanaz marad, amíg nem törli a Resource Manager és a klasszikus üzemi modellel üzembe helyezett virtuális gép számára. Korábban a MAC-cím fel lett oldva, ha a virtuális gép leállítva (felszabadítva), de most már a MAC-címet is, ha a virtuális gép felszabadított állapotban maradnak.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Is tudok csatlakozni az internethez a virtuális hálózat egy virtuális gépről?
Igen. Egy virtuális hálózaton üzembe helyezett virtuális gépek és a Felhőszolgáltatások szerepkörpéldányok képes csatlakozni az internethez.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-szolgáltatásokat, amelyhez csatlakozni a virtuális hálózatok

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Használható az Azure App Service Web Apps egy virtuális hálózattal?
Igen. Telepítheti a Web Apps egy ASE (App Service-környezet) használatával virtuális hálózaton belül. Ha rendelkezik konfigurált a virtuális hálózathoz pont – hely kapcsolat, minden webes alkalmazás biztonságos kapcsolódást és a virtuális hálózatban található erőforrások eléréséhez. További információkért tekintse át a következő cikkeket:

* [Webalkalmazások létrehozása az App Service Environment-környezetben](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Az alkalmazás integrálása az Azure-beli virtuális hálózathoz](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [A Web Apps VNet-integrációval és hibrid kapcsolatok használatával](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Telepíthetek Cloud Services webes és feldolgozói szerepkör (PaaS) egy virtuális hálózaton?
Igen. Telepítheti a Felhőszolgáltatások szerepkörpéldányok belüli virtuális hálózatok (opcionális). Ehhez az szükséges, megadhatja a virtuális hálózat neve és a szerepkör, illetve alhálózati leképezések a hálózati konfigurációs szakasz az szolgáltatás konfigurációját. Nem kell minden, a bináris fájlok frissítése.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Összekapcsolhatok egy virtuális gép méretezési beállítása (VMSS) egy virtuális hálózaton?
Igen. Egy VMSS kell kapcsolódni egy virtuális hálózathoz.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>-E teljes listáját az Azure-szolgáltatások, hogy üzembe helyezhetem az erőforrások egy vnetbe?
Igen, a további részletek: [virtuális hálózat integrációja Azure-szolgáltatások](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Melyik Azure PaaS-erőforrásokhoz korlátozhatom a hozzáférést a virtuális hálózaton?

Egyes Azure PaaS-szolgáltatások (például Azure Storage és Azure SQL Database) keresztül telepített erőforrások hálózati hozzáférést korlátozhatja csak egy virtuális hálózat virtuális hálózati Szolgáltatásvégpontok használatával erőforrásokat. További információkért lásd: [virtuális hálózati Szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Áthelyezheti a virtuális hálózatok kívüli szolgáltatások?
Nem. Szolgáltatások és virtuális hálózatok nem helyezhető át. Erőforrás áthelyezése egy másik virtuális hálózathoz, akkor törölje, és telepítse újra az erőforrás.

## <a name="security"></a>Biztonság

### <a name="what-is-the-security-model-for-vnets"></a>Mi az a virtuális hálózatok biztonsági modellje?
Virtuális hálózatok egymáshoz, és más az Azure infrastruktúra-ban üzemeltetett szolgáltatások elkülönülnek. Virtuális hálózat egy megbízhatósági kapcsolat határán.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Korlátozhatom az erőforrásokhoz kapcsolódó virtuális hálózatok közötti bejövő vagy kimenő forgalmat?
Igen. Alkalmazhat [hálózati biztonsági csoportok](security-overview.md) a virtuális hálózaton belül az egyes alhálózatok hálózati adapter csatlakozik egy virtuális hálózathoz, vagy mindkettőt.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Megvalósíthat egy virtuális hálózathoz csatlakozó erőforrás között tűzfal?
Igen. Telepíthet egy [hálózati virtuális készüléken](https://azure.microsoft.com/marketplace/?term=firewall) az Azure piactéren több szállítóktól származó.

### <a name="is-there-information-available-about-securing-vnets"></a>Van információ elérhető virtuális hálózatok védelmével kapcsolatos?
Igen. További információkért lásd: [Azure hálózati biztonság – áttekintés](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API-k, sémákkal és eszközök

### <a name="can-i-manage-vnets-from-code"></a>Felügyelhető virtuális hálózatok code-ból?
Igen. A virtuális hálózatok, a REST API-k is használhatja a [Azure Resource Manager](/rest/api/virtual-network) és [klasszikus (szolgáltatásfelügyelet)](http://go.microsoft.com/fwlink/?LinkId=296833) üzembe helyezési modellel.

### <a name="is-there-tooling-support-for-vnets"></a>Van-e a virtuális hálózatok eszközös támogatást?
Igen. További információ:
- Az Azure Portalon keresztül a virtuális hálózatok üzembe helyezéséhez a [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) és [klasszikus](virtual-networks-create-vnet-classic-pportal.md) üzembe helyezési modellel.
- A PowerShell használatával telepített virtuális hálózatok kezelése a [Resource Manager](/powershell/module/azurerm.network) és [klasszikus](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) üzembe helyezési modellel.
- Az Azure parancssori felület (CLI) üzembe helyezése és kezelése a virtuális hálózatok használatával telepített a [Resource Manager](/cli/azure/network/vnet) és [klasszikus](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) üzembe helyezési modellel.  

## <a name="vnet-peering"></a>Társviszony létesítése virtuális hálózatok között

### <a name="what-is-vnet-peering"></a>Mi az virtuális hálózatok közötti társviszony?
Virtuális hálózatok közötti társviszony-létesítés (vagy virtuális hálózatok közötti társviszony) lehetővé teszi, hogy a virtuális hálózatok összekapcsolása. Virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti kapcsolat lehetővé teszi őket közvetlenül a Microsoftnak IPv4-címeken keresztül közötti forgalom irányítására. A virtuális Társhálózatokba tartozó virtuális gépek kommunikálhatnak egymással, mintha ugyanazon a hálózaton belül vannak. Ezek a virtuális hálózatok ugyanabban a régióban vagy eltérő régiókban (más néven globális virtuális társhálózatok létesítésének) lehet. Azure-előfizetések virtuális Társhálózat-kapcsolatot is létrehozhatók.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Egy másik régióban is létrehozhatok egy virtuális hálózatok közötti társviszony-létesítési kapcsolat?
Igen. Globális virtuális társhálózatok létesítése lehetővé teszi a különböző régiókban található virtuális hálózatok társviszonyba állítása. Globális virtuális társhálózatok létesítésének érhető el minden nyilvános Azure-régióban. Ön nem globális társviszonyt a nyilvános Azure-régióban országos felhőkhöz. Globális társviszony-létesítés jelenleg nem áll rendelkezésre a nemzeti felhőkben.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Lehet engedélyezni a virtuális hálózatok közötti Társviszonyt, ha a virtuális hálózatok különböző Azure Active Directory-bérlőn belül előfizetések tartoznak?
Ez jelenleg nem lehet megállapítani a virtuális hálózatok közötti társviszony-létesítés (akár helyi, akár globális) Ha az előfizetés másik Azure Active Directory-bérlő tartozik.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A virtuális hálózatok közötti társviszony-létesítési kapcsolaton egy *kezdeményezve* állapot, miért nem tudok csatlakozni?
Ha a társviszony-létesítési kapcsolat által kezdeményezett állapotban van, ez azt jelenti, csak egy hivatkozás létrehozott. Egy kétirányú kapcsolat jöjjön létre kell hozni egy sikeres kapcsolat. Például a virtuális hálózat A b virtuális hálózatok közötti társviszonyt, hivatkozást kell létrehozni a b c és a b, c. Mindkét kapcsolat létrehozása módosítja a állapot *csatlakoztatva.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>A virtuális hálózatok közötti társviszony-létesítési kapcsolaton egy *leválasztott* állapot, miért nem hozható létre egy társviszony-létesítési kapcsolat?
Ha a virtuális hálózatok közötti társviszony-létesítési kapcsolat megszakad, az azt jelenti, a létrehozott hivatkozások egyikét törölték. Annak érdekében, hogy a társviszony-létesítési kapcsolatot létesíteni, szüksége lesz a hivatkozás törlése, és hozza létre újra.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Is saját, egy másik előfizetésben található virtuális hálózathoz virtuális hálózatok közötti társviszonyt?
Igen. Társviszonyt virtuális hálózatok között, előfizetések és -régiók között.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Társviszonyt két virtuális hálózat egyező vagy egymást átfedő címtartományai is?
Nem. Címterek engedélyezése a virtuális hálózatok közötti Társviszony nem overalap kell.

### <a name="how-much-do-vnet-peering-links-cost"></a>Virtuális hálózatok közötti társviszony költség hivatkozások mennyi tenni?
Nem jár költségekkel virtuális hálózatok közötti társviszony-létesítési kapcsolat létrehozásához. Társviszony-létesítési kapcsolaton keresztül történő adatátvitel után kell fizetni. [Itt látható](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Virtuális hálózatok közötti társviszony-létesítési forgalom titkosítva van?
Nem. Erőforrások társviszonyban lévő virtuális hálózatok közötti adatforgalom a magán- és elkülönített. A Microsoft Backbone teljesen marad.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>A társviszony-létesítési kapcsolat megszakad miért van?
Virtuális hálózatok közötti társviszony-kapcsolatok lépnek *leválasztott* törlése egy virtuális hálózatok közötti társviszony-létesítési csatolás állapota. Annak érdekében, hogy sikeresen létrejött a társviszony-létesítési kapcsolat helyreállítani a mindkét csatolást kell törölnie.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Ha e társviszonyba állítása a b, c, és szeretnék társviszonyba állítása a b hálózat között, jelent hálózattal és Társviszonyban társviszonyban állnak?
Nem. Tranzitív társviszony-létesítés nem támogatott. Meg kell társviszonyt létesíteni, hálózattal és a Társviszonyban kerül sor.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Minden társviszony-kapcsolatok sávszélesség-korlátozások vannak?
Nem. Virtuális hálózatok közötti társviszonyt, hogy a helyi vagy globális, sávszélesség-korlátozások nem ír elő. A virtuális gép vagy a számítási erőforrás által csak korlátok a sávszélesség.

## <a name="virtual-network-tap"></a>Virtuális hálózati TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Mely Azure-régiók érhetők el a virtuális hálózati TAP?
Fejlesztői előzetes verzióban a funkció érhető el az USA nyugati középső régiójában. A figyelt hálózati adapterek, a virtuális hálózati TAP erőforrás és a gyűjtő vagy analytics megoldás ugyanabban a régióban kell telepíteni.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Virtuális hálózati TAP támogat bármilyen szűrési képességek a tükrözött csomagok?
A virtuális hálózati TAP-előzetes verzió használata nem támogatott szűrési képességek. Amikor egy KOPPINTÁSSAL konfigurációt adnak hozzá egy hálózati adaptert a bejövő forgalom mély másolatát, és KOPPINTSON a célhelyre irányuló kimenő adatforgalmat a hálózati adapter adatfolyamként.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Hozzáadhat több KOPPINTSON konfiguráció egy figyelt hálózati adapter?
A figyelt hálózati adapter rendelkezhet egyetlen KOPPINTÁSSAL konfiguráció. Az egyes egyeztessen [partneri megoldások](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) számára teszi, hogy a választott elemzőeszközök KOPPINTSON a forgalmat több példányának adatfolyam.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Összesítheti ugyanazon virtuális hálózati TAP erőforrás forgalmat a figyelt hálózati adapterek egynél több virtuális hálózaton?
Igen. Az azonos virtuális hálózatban KOPPINTSON erőforrás az alábbiakra használhatók porttükrözéses adatokra összesíti a figyelt hálózati adapterek ugyanabban az előfizetésben vagy egy másik előfizetésben lévő virtuális társhálózaton belüli. A virtuális hálózati TAP erőforrás és a cél terheléselosztók, illetve a cél hálózati adapter ugyanabban az előfizetésben kell lennie. Az összes előfizetés ugyanahhoz az Azure Active Directory-bérlőhöz kell tartozniuk.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Vannak-e bármilyen teljesítménnyel kapcsolatos megfontolások az éles forgalmat, ha egy hálózati adapter egy virtuális hálózati TAP konfigurációját engedélyezni?

Virtuális hálózati TAP van fejlesztői előzetes verzióban érhető el. Előzetes verzióban nem nincs szolgáltatásszint-szerződés. A funkció nem használható éles számítási feladatok esetében. Ha egy virtuális gép hálózati adapteréhez TAP-konfigurációval rendelkező engedélyezve van, ugyanazokat az erőforrásokat az Azure-beli gazdagépre a virtuális gép számára lefoglalt az éles forgalmat a tükrözési funkció végrehajtására és a tükrözött csomagok küldése szolgál. Válassza ki a megfelelő [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) annak érdekében, hogy elegendő erőforrással az éles és a tükrözött forgalom küldése a virtuális gép számára elérhető virtuálisgép-méretet.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>A gyorsított hálózatkezelése [Linux](create-vm-accelerated-networking-cli.md) vagy [Windows](create-vm-accelerated-networking-powershell.md) virtuális hálózati TAP támogatott?

Akkor lesz, amelyen engedélyezve van a gyorsított hálózatkezelés virtuális géphez csatolt hálózati adapter egy KOPPINTÁSSAL konfigurációt adhat hozzá. Azonban a teljesítmény és a késés, a virtuális gépen, mivel az Azure jelenleg nem támogatja a kiszervezési az adatforgalom tükrözés a következő KOPPINTSON konfigurációs gyorsított hálózatkezelés hozzáadásával érinti.