---
title: "Azure-beli virtuális hálózat – gyakori kérdések |} Microsoft Docs"
description: "A Microsoft Azure virtuális hálózatokról leggyakrabban feltett kérdésekre adott válaszokat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: jdial
ms.openlocfilehash: c71e188b74ebfd9420f840957e83190cf476b584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure-beli virtuális hálózat gyakori kérdések (GYIK)

## <a name="virtual-network-basics"></a>Virtuális hálózati alapjai

### <a name="what-is-an-azure-virtual-network-vnet"></a>Mi az az Azure Virtual Network (VNet)?
Az Azure Virtual Network (VNet) a felhőben saját hálózati ábrázolása. A hálózat az előfizetésre kijelölt Azure-felhő logikai elkülönítése. Használhat Vnetek kiépítését és virtuális magánhálózatok (VPN) kezelése az Azure-ban és, szükség esetén hivatkozásra a Vnetek más Vnetekről az Azure-ban, vagy a helyszíni informatikai infrastruktúrát, hibrid vagy létesítmények közötti megoldások létrehozásához. Minden egyes virtuális hálózat létrehozása a saját CIDR-blokkja rendelkezik, és mindaddig, amíg a CIDR-blokkok nem lehetnek átfedésben más virtuális hálózatokat és a helyszíni hálózatokhoz kapcsolódó. Akkor is, DNS-kiszolgáló beállításainak Vnetek ellenőrzésére és a hálózatok alhálózatokra szegmentálását.

A Vnetek használja:

* Hozzon létre egy dedikált titkos csak felhőalapú VNet néha nem feltétlenül szükséges a létesítmények közötti konfigurációs megoldást. Amikor létrehoz egy Vnetet, a szolgáltatások és a virtuális hálózaton belüli virtuális gépek kommunikálhatnak közvetlenül és biztonságosan egymással a felhőben. Ez tartja a forgalom biztonságosan a virtuális hálózaton belül, de továbbra is lehetővé teszi virtuális gépek és szolgáltatások esetében az internetes kommunikáció a megoldás részeként igénylő végpont kapcsolatok konfigurálását.
* Biztonságos az Adatközpont a Vnetek meghosszabbításához hagyományos pont-pont (S2S) VPN biztonságosan méretezése a datacenter kapacitás hozhat létre. S2S VPN használja az IPSec PROTOKOLLT adja meg a vállalati VPN-átjáró és az Azure közötti biztonságos kapcsolatot.
* Engedélyezze a hibrid felhős rendszerekben Vnetek ad hibrid felhős rendszerekben számos támogatásához szükséges rugalmasságot. Felhőalapú alkalmazások, például Nagyszámítógépek a helyszíni rendszer és a Unix rendszerek bármilyen biztonságosan kapcsolódhatnak.

### <a name="how-do-i-know-if-i-need-a-vnet"></a>Hogyan állapítható meg, hogy ha egy virtuális hálózatot kell-e?
A [virtuális hálózat áttekintése](virtual-networks-overview.md) a cikk ismerteti, amelyek segítséget döntési tábla döntse el, a legjobb az Ön hálózati tervezési beállítás.

### <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?
Látogasson el a [virtuális hálózati dokumentáció](https://docs.microsoft.com/azure/virtual-network/) a kezdéshez. Ez a tartalom minden virtuális hálózat szolgáltatás áttekintése és a központi telepítési információkat nyújt.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Használhatok Vnetek közötti kapcsolatot nyújthassanak nélkül?
Igen. Egy VNet hibrid kapcsolat használata nélkül is használhatja. Ez különösen fontos, ha a Microsoft Windows Server Active Directory-tartományvezérlőket és a SharePoint-farmok futtatja az Azure-ban.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Végezheti a WAN-optimalizálást Vnetekhez vagy egy VNet és a helyszíni adatközpont között?

Igen. Telepíthet egy [WAN-optimalizálást hálózati virtuális készülék](https://azure.microsoft.com/marketplace/?term=wan+optimization) több szállítók az Azure piactéren keresztül.

## <a name="configuration"></a>Konfiguráció

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Milyen eszközök használható hozhat létre egy Vnetet?
A következő eszközök segítségével hozzon létre vagy egy virtuális hálózat konfigurálása:

* Azure-portálra (a klasszikus és Resource Manager Vnetek).
* A hálózati konfigurációs fájl (netcfg - csak a hagyományos Vneteket). Tekintse meg a [egy Vnetet, a hálózati konfigurációs fájl segítségével konfigurálja](virtual-networks-using-network-configuration-file.md) cikk.
* PowerShell (a klasszikus és Resource Manager Vnetekről).
* Az Azure parancssori felület (a klasszikus és Resource Manager Vnetekről).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Milyen címtartományai lehet használni a Vnetekhez?
Meghatározott összes IP-címtartomány [RFC 1918](http://tools.ietf.org/html/rfc1918). Például 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Is nyilvános IP-címeket is rendelkeznek a saját Vnetekhez?
Igen. Nyilvános IP-címtartományok kapcsolatos további információkért tekintse meg a [nyilvános IP-címtér része virtuális hálózatnak](virtual-networks-public-ip-within-vnet.md) cikk. A nyilvános IP-címek csak akkor érhető el közvetlenül az internetről.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>A VNet alhálózatainak száma korlátozva van?
Igen. Olvassa el a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikkben alább. Alhálózati címterek nem átfedik egymást.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Vannak-e bármilyen korlátozás belül ezek alhálózatok IP-címeket használnak?
Igen. Azure fenntartja az egyes IP-címek minden alhálózaton belül. Az alhálózat első és utolsó IP-címét a protokollok megfelelősége érdekében, további 3 címet pedig az Azure-szolgáltatások számára foglal le.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hogyan kis és nagy hogyan lehet virtuális hálózatokat és alhálózatokat kell?
A legkisebb támogatott alhálózat egy /29 pedig a legnagyobb egy /8 (CIDR alhálózati definíciók használatával).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Hozzáadhatom a VLAN-t az Azure Vnetekhez használatával?
Nem. Vnetek 3. rétegbeli átfedések. Azure nem támogatja a 2. rétegbeli szemantikáját.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Egyéni útválasztási házirend megadhatok a Vnetek és alhálózatok?
Igen. Felhasználó definiált útválasztás (UDR) is használhatja. További információt a UDR [felhasználó által megadott útvonalak és IP-továbbítás](virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Csoportos küldéses vagy szórt támogatják Vnetekhez?
Nem. Csoportos küldéses vagy szórt nem támogatott.

### <a name="what-protocols-can-i-use-within-vnets"></a>Milyen protokollok Vnetek belül használható?
TCP, UDP és ICMP TCP/IP protokollt Vnetek belül is használhatja. Csoportos küldés, szórás, IP-be bújtatott IP encapsulated, és Generic Routing Encapsulation (GRE) csomagot a rendszer letiltotta Vnetek belül. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Megpingelheti a saját alapértelmezett útválasztók egy Vneten belül?
Nem.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Tracert segítségével diagnosztizálhatja a kapcsolatot?
Nem.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Hozzáadható alhálózatok, a virtuális hálózat létrejötte után?
Igen. Alhálózatok felveheti a Vnetek bármikor, amíg az alhálózati cím nem része egy másik alhálózatnak a Vneten belül.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Az létrehozása után is módosítani az alhálózat méretét?
Igen. Hozzáadása, eltávolítása, bővítése vagy zsugorítása alhálózatot, ha nincsenek virtuális gépek és szolgáltatások telepítése környezetben.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Miután létrehozott azokat is módosítani alhálózatok?
Igen. Hozzáadhat, távolítsa el, és módosítsa a virtuális hálózat által használt CIDR-blokkok.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Képes vagyok csatlakozni az internethez fut, de a szolgáltatások a Vneten belül?
Igen. Egy Vneten belül telepített összes szolgáltatás képes csatlakozni az internethez. Minden Azure szolgáltatásba telepített felhőszolgáltatás van rendelve nyilvánosan címmel rendelkező virtuális IP-címhez. Adja meg a bemeneti végpontja PaaS szerepkörök és a virtuális gépek számára az internetről érkező kapcsolatok fogadására a szolgáltatások engedélyezésével végpontok kell.

### <a name="do-vnets-support-ipv6"></a>Támogatják a Vnetek IPv6?
Nem. A Vnetek IPv6 jelenleg nem használható.

### <a name="can-a-vnet-span-regions"></a>Egy VNet régiókban is kiterjedhet?
Nem. A virtuális hálózat egy régiót korlátozódik.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Képes kapcsolódni egy Vnetet az Azure VNet között?
Igen. Egy VNet csatlakozni egy másik virtuális hálózatot használ:
- Az Azure VPN-átjáró. Olvassa el a [VNet – VNet-kapcsolatot konfiguráló](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikkben alább. 
- Vnetben társviszony-létesítés. Olvassa el a [Vnetben társviszony-létesítési áttekintése](virtual-network-peering-overview.md) cikkben alább.

## <a name="name-resolution-dns"></a>Névfeloldás (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Mik azok a DNS-beállítások tartozó Vnetek esetében?
A döntési táblázattal a [névfeloldás virtuális gépek és a Szerepkörpéldányok](virtual-networks-name-resolution-for-vms-and-role-instances.md) végigvezeti a DNS lapon elérhető beállítások.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Képes-e meg DNS-kiszolgálók egy virtuális hálózatot?
Igen. DNS-kiszolgáló IP-címeket adhat meg a VNet beállításait. Ez a virtuális hálózat virtuális gépeinek az alapértelmezett DNS-kiszolgálói fog alkalmazni.

### <a name="how-many-dns-servers-can-i-specify"></a>DNS-kiszolgálók számának adhat meg?
Hivatkozás a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikkben alább.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>A hálózati I létrehozása után is módosítani a DNS-kiszolgálók?
Igen. A DNS-kiszolgálók listája bármikor módosíthatja a virtuális hálózat. Ha módosítja a DNS-kiszolgálók listája, szüksége lesz a virtuális hálózat ahhoz, azok esetében az új DNS-kiszolgáló átvételéhez minden egyes virtuális gépek újraindítására.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Mi az Azure által biztosított DNS- és működik a Vnetekhez?
Azure által biztosított DNS-a Microsoft által kínált több-bérlős DNS-szolgáltatás. Azure regisztrál az összes virtuális gépek és felhő szerepkör szolgáltatáspéldány ezt a szolgáltatást. Ezt a szolgáltatást biztosít a névfeloldás virtuális gépek és az ugyanabban a felhőalapú szolgáltatás található szerepkörpéldányok állomásnevét, és FQDN virtuális gépek és a szerepkörpéldányok ugyanazon virtuális. Olvassa el a [névfeloldás virtuális gépek és a Szerepkörpéldányok](virtual-networks-name-resolution-for-vms-and-role-instances.md) DNS szolgáltatással kapcsolatos bővebb.

> [!NOTE]
> Nincs több-bérlős használata az Azure által biztosított DNS-névfeloldás a Vneten belül az első 100 felhőszolgáltatások jelenleg korlátozott. Ha a saját DNS-kiszolgálót használ, ez a korlátozás nem vonatkozik.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Bírálja felül a DNS-beállítások a /-virtuális gép / service alapján?
Igen. A szolgáltatás felhőnként felülírják az alapértelmezett hálózati beállításokat, a DNS-kiszolgálók adhatók meg. Azt javasoljuk azonban, hogy a lehető legnagyobb hálózati kiterjedő DNS használja.

### <a name="can-i-bring-my-own-dns-suffix"></a>Hozzáadhatom a saját DNS-utótag?
Nem. A Vnetek esetében nem adható meg egy egyéni DNS-utótagot.

## <a name="connecting-virtual-machines"></a>Csatlakozás a virtuális gépek

### <a name="can-i-deploy-vms-to-a-vnet"></a>I telepíthet virtuális gépeket egy Vnetet?
Igen. A Resource Manager üzembe helyezési modellben telepített virtuális gép csatlakoztatott összes hálózati adapterek (NIC) kapcsolódnia kell egy virtuális hálózatot. A klasszikus üzembe helyezési modell használatával telepített virtuális gépek opcionálisan egy Vnetet lehet csatlakoztatni.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Mik a különböző típusú IP-címek a virtuális gépek hozzárendelek?
* **Saját:** rendelt egyes hálózati adapterek minden egyes virtuális Gépen belül. A cím hozzá van rendelve, vagy a statikus vagy dinamikus metódus használatával. A tartományhoz, amely a virtuális hálózat alhálózati beállításaiban megadott magán IP-címek rendeli. A klasszikus üzembe helyezési modellben telepített erőforrásokhoz vannak hozzárendelve a magánhálózati IP-címek, még akkor is, ha azok nem csatlakozik egy Vnetet. A magánhálózati IP-címet, dinamikus módszerrel hozzárendelt erőforráshoz hozzárendelt marad, amíg az erőforrás nem (virtuális gépek vagy a Felhőszolgáltatás üzembe helyezési). A magánhálózati IP-címet, dinamikus metódus rendelve módosíthatja a virtuális gép leállított (felszabadított) állapotában elvégzése után újraindításakor. A magánhálózati IP-címet a statikus metódus rendelve marad erőforráshoz hozzárendelt, amíg az erőforrás nem. Ha kell győződjön meg arról, hogy a magánhálózati IP-cím erőforrás soha nem módosítja az erőforrás nem törlik, rendelje hozzá a magánhálózati IP-címet a statikus metódussal.
* **Nyilvános:** opcionálisan rendelve az Azure Resource Manager deployment használatával telepített virtuális gépekhez csatlakoztatott hálózati adapterrel. A cím rendelhetők hozzá a statikus vagy dinamikus kiosztási módszerrel. A klasszikus üzembe helyezési modellben telepített összes virtuális gépek és Felhőszolgáltatások szerepkörpéldányokat vannak egy felhőalapú szolgáltatás, amely hozzá van rendelve egy *dinamikus*, nyilvános virtuális IP-címe. Nyilvános *statikus* IP-cím, az úgynevezett egy [lefoglalt IP-cím](virtual-networks-reserved-public-ip.md), opcionálisan rendelhető virtuális IP-címhez. Nyilvános IP-címek rendelhet az egyes virtuális gépek vagy Felhőszolgáltatások szerepkörpéldányok a klasszikus üzembe helyezési modellben telepített. Ezek az úgynevezett [példány szintű nyilvános IP-cím (ILPIP](virtual-networks-instance-level-public-ip.md) megoldást, és dinamikusan is hozzárendelhető.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Lehet-e foglalni a magánhálózati IP-címet, amely I létrehoz egy későbbi időpontban?
Nem. A magánhálózati IP-címet nem sikerült lefoglalni. Ha a magánhálózati IP-cím áll rendelkezésre az társítható egy virtuális gép vagy szerepkör-példányt a DHCP-kiszolgáló. Előfordulhat, hogy ezt a virtuális Gépet, vagy nem lehet a magánhálózati IP-címet hozzárendelni kívánt. Azonban módosíthatja a magánhálózati IP-cím, egy korábban létrehozott virtuális gép számára elérhető privát IP-cím.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Hajtsa végre a magánhálózati IP-címek módosítása virtuális gépekhez a Vneten belül?
Ez a konkrét licenctől függ. Magánhálózati IP-címek dinamikus marad a virtuális gép a Leállítva (felszabadítva), vagy törölték. Statikus magánhálózati IP-címek nem ki a virtuális gép, amíg nem törli.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>I manuálisan rendelhet IP-címek hálózati adaptert a virtuális gép operációs rendszerben?
Igen, de nem ajánlott. Az IP-cím kézzel megváltoztatása a virtuális gép operációs rendszerében a hálózati adapter eredményezhet, elvesztése kapcsolat a virtuális gép, ha egy hálózati adapterre, az Azure virtuális Gépen belül az IP-cím volt módosítani.

### <a name="what-happens-to-my-ip-addresses-if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system"></a>Mi történik a saját IP-címeket, ha egy felhőalapú szolgáltatás üzembe helyezési pont vagy -leállítás az operációs rendszerben a virtuális gép le?
Semmi sem. Az IP-címek (nyilvános VIP, nyilvános és titkos) maradnak hozzárendelve a felhőalapú szolgáltatás üzembe helyezési pont vagy a virtuális gép. Dinamikus címek csak egy virtuális gép leállítása (felszabadított) kiadott vagy törölték, vagy egy felhőalapú szolgáltatás üzembe helyezési pont törlődik. Kattintson a **leállítása** az egy virtuális Gépet az Azure portálon értékűre állítja be az állapot Leállítva (felszabadítva) gombra. Ebben az esetben a virtuális gép IP-címéhez megszakad.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Áthelyezhető virtuális gépek egyik alhálózatról a Vneten belül egy másik alhálózatnak újbóli telepítése nélkül?
Igen. A további információt a [a virtuális gép vagy szerepkör példánya áthelyezése egy másik alhálózat](virtual-networks-move-vm-role-to-subnet.md) cikk.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Beállítható egy statikus MAC-címet a virtuális géphez?
Nem. A MAC-cím statikusan nem állítható be.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>A MAC-címe ugyanaz marad a virtuális gép létrehozása után?
Igen, a MAC-cím változatlan marad, amíg nem törli a Resource Manager és a klasszikus üzembe helyezési modellek telepített virtuális gépeknél. A MAC-címet jelent korábban, ha a virtuális gép le lett állítva (felszabadított), de most őrzi meg a MAC-cím akkor is, ha a virtuális gép felszabadított állapotban van.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>I csatlakozhatnak az interneten egy virtuális Gépet a Vneten belül?
Igen. Egy Vneten belül telepített összes virtuális gépek és Felhőszolgáltatások szerepkörpéldányokat tud csatlakozni az internethez.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-szolgáltatások Vnetek-hez

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Használható egy Vnetet az Azure App Service Web Apps?
Igen. Webes alkalmazásokat belül egy Vnetet egy ASE (App Service Environment-környezet) segítségével telepíthet. Minden Web Apps biztonságosan csatlakozzon, és az Azure virtuális hálózat a erőforrások elérését, ha a virtuális hálózat beállítása pont – hely kapcsolat. További információkért tekintse át a következő cikkeket:

* [Webalkalmazások létrehozása az App Service-környezet](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Az alkalmazás integrálása az Azure virtuális hálózat](../app-service/web-sites-integrate-with-vnet.md)
* [A webalkalmazások virtuális integráció és a hibrid kapcsolatok használja](../app-service/web-sites-integrate-with-vnet.md#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Központi telepítését Felhőszolgáltatások webes és feldolgozói szerepkörök (PaaS) egy virtuális hálózatot?
Igen. (Opcionális) telepítése Felhőszolgáltatások szerepkörpéldányokat Vnetek belül. Ehhez meg kell adnia a virtuális hálózat nevének és a szerepkör/alhálózati leképezések a szolgáltatáskonfiguráció a hálózati konfigurációs szakaszban. Nem kell minden, a bináris fájlok frissítése.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Képes kapcsolódni a virtuális gép méretezési beállítása (VMSS) egy virtuális hálózatot?
Igen. Egy VMSS csatlakoztatni kell egy virtuális hálózatot.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Áthelyezheti a szolgáltatások mindkét Vnetekhez?
Nem. Mindkét Vnetek szolgáltatások nem helyezhető át. Törölje és hozza létre újra a szolgáltatást, hogy helyezze át egy másik virtuális hálózatot kell.

## <a name="security"></a>Biztonság

### <a name="what-is-the-security-model-for-vnets"></a>Mi az a biztonsági modell Vnetekhez?
Vnetek teljesen elkülönülnek egymástól vagy az Azure-infrastruktúra a más szolgáltatások is. A virtuális hálózat egy megbízhatósági kapcsolat határán.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>I korlátozhatja bejövő vagy kimenő forgalom VNet kapcsolódó erőforrások?
Igen. Alkalmazhat [hálózati biztonsági csoportok](virtual-networks-nsg.md) történik egy Vneten belül az egyes alhálózatok hálózati adapter csatlakozik egy Vnetet, vagy mindkettőt.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Virtuális hálózat kapcsolódó erőforrások között tűzfal is létrehozható?
Igen. Telepíthet egy [tűzfal hálózati virtuális készülék](https://azure.microsoft.com/en-us/marketplace/?term=firewall) több szállítók az Azure piactéren keresztül.

### <a name="is-there-information-available-about-securing-vnets"></a>Van információk védelme Vnetek elérhető?
Igen. Tekintse meg a [Azure biztonsági áttekintése](../security/security-network-overview.md) cikkben alább.

## <a name="apis-schemas-and-tools"></a>API-k, sémákkal és eszközök

### <a name="can-i-manage-vnets-from-code"></a>Kezelhetem Vnetek kódból?
Igen. A Vnetek REST API-kat is használhatja a [Azure Resource Manager](https://msdn.microsoft.com/library/mt163658.aspx) és [klasszikus (kezelő)](http://go.microsoft.com/fwlink/?LinkId=296833)) üzembe helyezési modellek.

### <a name="is-there-tooling-support-for-vnets"></a>Van tooling támogatása Vnetekhez?
Igen. További információ:
- Az Azure-portálon keresztül Vnetek telepítéséhez a [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) és [klasszikus](virtual-networks-create-vnet-classic-pportal.md) üzembe helyezési modellek.
- PowerShell ügyfélgépekre Vnetek kezelheti a [erőforrás-kezelő](/powershell/resourcemanager/azurerm.network/v3.1.0/azurerm.network.md) és [klasszikus](/powershell/module/azure/?view=azuresmps-3.7.0) üzembe helyezési modellek.
- A [Azure parancssori felület (CLI)](../virtual-machines/azure-cli-arm-commands.md#azure-network-commands-to-manage-network-resources) két üzembe helyezési modell telepített Vnetek kezeléséhez.  
