---
title: "Azure Virtual Networkök | Microsoft Docs"
description: "Tudnivalók az Azure-beli virtuális hálózatokról."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 75c5b8d3d8c8f389b8cee7d5d304b6e9704252fc
ms.openlocfilehash: a57805510d5e84fcdc6c4521ae9443ec72de59e1


---
# <a name="virtual-networks"></a>Virtuális hálózatok
Az Azure virtuális hálózat (VNet) a saját, felhőben található hálózatának megfelelője.  A hálózat az előfizetésre kijelölt Azure-felhő logikai elkülönítése. A hálózaton belül teljes mértékben irányíthatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útválasztási táblázatokat. A VNetet emellett tovább oszthatja alhálózatokra, valamint Azure IaaS virtuális gépeket (VM-ek) és/vagy [felhőszolgáltatásokat (PaaS szerepkörpéldányok)](../cloud-services/cloud-services-choose-me.md) indíthat. A virtuális hálózatot ezen felül a helyszíni hálózathoz is csatlakoztathatja az Azure-ban elérhető [kapcsolati lehetőségek](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-connections) egyikével. Lényegében kiterjesztheti a hálózatot az Azure-ra, az IP-címblokkok teljes körű irányítása és a vállalati méretű Azure által nyújtott előnyök mellett.

A VNetek jobb megértése érdekében tekintse meg az alábbi ábrát, amely egy egyszerűsített helyszíni hálózatot mutat be.

![Helyszíni hálózat](./media/virtual-networks-overview/figure01.png)

A fenti ábrán egy helyszíni hálózat látható, ami egy útválasztón keresztül csatlakozik a nyilvános internethez. Egy tűzfal is látható az útválasztó és szegélyhálózat (DMZ) között, amely egy DNS-kiszolgálót és egy webkiszolgáló farmot is üzemeltet. A webkiszolgáló farm a belső alhálózat erőforrásait fogyasztja, a terheléselosztását pedig egy internettel érintkező hardveres terheléselosztó hajtja végre. A belső alhálózat Active Directory-tartományvezérlő kiszolgálókat, adatbázis-kiszolgálókat és alkalmazáskiszolgálókat üzemeltet, és egy másik tűzfal választja el a szegélyhálózattól (DMZ-től).

Ugyanezt a hálózatot az Azure-ban is lehet üzemeltetni a lenti ábrán látható módon.

![Azure virtuális hálózat](./media/virtual-networks-overview/figure02.png)

Figyelje meg, hogy az Azure infrastruktúra felveszi az útválasztó szerepkörét, és ezzel konfigurálás igénye nélkül engedélyezi a hozzáférést a VNet számára a nyilvános internethez. A tűzfalakat az alhálózatokra külön-külön alkalmazott hálózati biztonsági csoportokkal (NSG-k) lehet helyettesíteni. A fizikai terheléselosztókat az internetkapcsolattal rendelkező belső terheléselosztók helyettesítik az Azure-ban.

> [!NOTE]
> Az Azure-ban két üzembe helyezési mód létezik: a klasszikus (más néven szolgáltatásfelügyelet) és az Azure Resource Manager (ARM). A hagyományos VNeteket hozzá lehet adni egy affinitáscsoporthoz, vagy regionális VNetként is létre lehet hozni. Ha talál egy VNetet az egyik affinitáscsoportban, ajánlott [áttelepíteni egy regionális VNetre](virtual-networks-migrate-to-regional-vnet.md).
>

## <a name="benefits"></a>Előnyök
* **Elkülönítés**. A VNetek teljesen elkülönülnek egymástól. Ez lehetővé teszi, hogy különálló hálózatokat hozzon létre a fejlesztés, a tesztelés és a termelés számára, amelyek ugyanazokat a CIDR címblokkokat használják.
* **Hozzáférés a nyilvános internethez**. Egy VNet minden IaaS virtuális gépe és PaaS szerepkörpéldánya alapértelmezés szerint hozzáférhet a nyilvános internethez. A hozzáférést hálózati biztonsági csoportokkal (NSG-k) lehet irányítani.
* **Hozzáférés a virtuális gépekhez a VNeten belül**. A PaaS szerepkörpéldányokat és IaaS virtuális gépeket el lehet indítani ugyanabban a virtuális hálózatban, és privát IP-címek használatával képesek csatlakozni egymáshoz, akkor is, ha külön alhálózatokon vannak, anélkül, hogy átjárót kellene konfigurálni vagy nyilvános IP-címeket kellene használni.
* **Névfeloldás**. Az Azure [belső névfeloldás](virtual-networks-name-resolution-for-vms-and-role-instances.md)t biztosít a VNetben üzembe helyezett IaaS virtuális gépek és PaaS szerepkörpéldányok számára. Saját DNS-kiszolgálókat is üzembe helyezhet, és konfigurálhatja a VNetet a használatukra.
* **Biztonság**. A VNeten belül a virtuális gépekre és PaaS szerepkörpéldányokra érkező, illetve onnan kifelé irányuló forgalmat hálózati biztonsági csoportok használatával lehet irányítani.
* **Kapcsolatok**. A virtuális hálózatok hálózati átjárókkal vagy virtuális hálózatok közötti társviszony-létesítéssel összekapcsolhatók. A virtuális hálózatok helyek közötti VPN-hálózatokon vagy az Azure ExpressRoute-on keresztül csatlakozhatnak helyszíni adatközpontokhoz. A helyek közötti VPN-kapcsolatokról bővebben lásd: [Tudnivalók a VPN-átjárókról](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-connections). Ha többet szeretne megtudni az ExpressRoute-ról:[ExpressRoute technical overview](../expressroute/expressroute-introduction.md) (ExpressRoute műszaki áttekintés). A virtuális hálózatok közötti társviszonnyal kapcsolatos további tudnivalókért lásd: [Társviszony-létesítés virtuális hálózatok között](virtual-network-peering-overview.md).

  > [!NOTE]
  > Győződjön meg arról, hogy létrehoz egy VNetet, mielőtt IaaS virtuális gépet, vagy PaaS szerepkörpéldányt helyezne üzembe az Azure környezetben. Az ARM alapú virtuális gépeknek szüksége van egy VNetre, és ha nem ad meg egy meglévő VNetet, az Azure létrehoz egy alapértelmezett VNetet, amelynek a CIDR címblokkja ütközhet a helyszíni hálózattal. Így a VNetet nem lehet majd csatlakoztatni a helyszíni hálózathoz.
  >

## <a name="subnets"></a>Alhálózatok
Az alhálózatok IP-címtartományok a VNeten belül. A VNetet a szervezés és a biztonság érdekében több alhálózatra lehet osztani. Egy VNeten belül az alhálózatokra üzembe helyezett virtuális gépek és a PaaS szerepkörpéldányok (ugyanaz vagy különböző) további konfigurálás nélkül is tudnak egymással kommunikálni. Egy alhálózathoz útválasztási táblázatokat és NSG-ket is lehet konfigurálni.

## <a name="ip-addresses"></a>IP-címek
Az Azure-ban az erőforrásokhoz rendelt IP-címeknek két típusa van: *nyilvános* és *privát*. A nyilvános IP-címek lehetővé teszik az Azure-erőforrások számára, hogy kommunikáljanak az internettel és az Azure más nyilvános szolgáltatásaival, például az [Azure Redis Cache](https://azure.microsoft.com/services/cache/) és az [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/) szolgáltatásokkal. A privát IP-címek lehetővé teszik az erőforrások közti kommunikációt egy virtuális hálózatban, az egymáshoz VPN-en keresztül csatlakozó erőforrásokkal együtt, internetről elérhető IP-címek használata nélkül.

Ha többet szeretne megtudni az Azure-ban használt IP-címekről: [IP addresses in virtual network](virtual-network-ip-addresses-overview-arm.md) (IP-címek a virtuális hálózatban)

## <a name="azure-load-balancers"></a>Azure Load Balancer terheléselosztók
Egy virtuális hálózatban a virtuális gépek és a felhőszolgáltatások is kommunikálhatnak az internettel az Azure Load Balancer terheléselosztók segítségével. A belső hálózatra irányuló üzleti alkalmazások terheléselosztása csak belső terheléselosztó használatával lehetséges.

* **Külső terheléselosztó**. A nyilvános internetről hozzáférhető IaaS virtuális gépeknél és PaaS szerepkörpéldányoknál a magas rendelkezésre állás biztosításához használhat külső terheléselosztót.
* **Belső terheléselosztó**. A VNeten található más szolgáltatásokkal hozzáférhető IaaS virtuális gépeknél és PaaS szerepkörpéldányoknál a magas rendelkezésre állás biztosításához használhat belső terheléselosztót.

Ha többet szeretne megtudni a terheléselosztásról az Azure-ban: [Load balancer overview](../load-balancer/load-balancer-overview.md) (A Load Balancer áttekintése).

## <a name="network-security-groups-nsg"></a>Hálózati biztonsági csoportok (NSG)
Létrehozhat NSG-ket a hálózati adapterek (NIC-k), virtuális gépek és alhálózatok bejövő és kimenő hozzáférésének irányítására. Minden NSG tartalmaz egy vagy több szabályt, amelyek az IP-forráscím, a forrásport, az IP-célcím és a célport alapján megadják, hogy a forgalom jóvá van hagyva, vagy el van utasítva. Ha többet szeretne megtudni az NSG-kről: [Mi az a hálózati biztonsági csoport (NSG)](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Virtuális készülékek
A virtuális készülék valójában egy másik virtuális gép a VNetben, amely egy szoftveralapú készülékfunkciót futtat, például tűzfalat, WAN-optimalizálást vagy behatolásérzékelést. Az Azure-ban létrehozhat egy útvonalat, hogy a VNet forgalmát átirányítsa egy virtuális készülékre a készülék képességeinek használatához.

Például az NSG-ket lehet használni arra, hogy biztosítsák a VNet biztonságát. Az NSG-k azonban csak 4. rétegbeli hozzáférés-vezérlési listát (ACL) biztosítanak a bejövő és kimenő csomagoknak. Ha 7. rétegbeli biztonsági modellt szeretne alkalmazni, tűzfalat kell használnia.

A virtuális készülékek a [felhasználó által megadott útvonalaktól és az IP-továbbítástól](virtual-networks-udr-overview.md) függenek.

## <a name="limits"></a>Korlátok
Az előfizetés által engedélyezett virtuális hálózatok száma korlátozott, további információért olvassa el az [Azure Networking limits](../azure-subscription-service-limits.md#networking-limits) (Azure hálózatkezelés korlátok) szakaszt.

## <a name="pricing"></a>Díjszabás
A Virtual Networks az Azure-ban ingyenesen használható. A VNetben belül indított számítási példányokért az [Azure virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/) szerinti standard díjat kell fizetni. A VNetben használt [VPN Gatewayekért](https://azure.microsoft.com/pricing/details/vpn-gateway/) és[nyilvános IP-címekért](https://azure.microsoft.com/pricing/details/ip-addresses/) szintén a standard díjat kell fizetni.

## <a name="next-steps"></a>Következő lépések
* [Hozzon létre egy VNetet](virtual-networks-create-vnet-arm-pportal.md) és az alhálózatokat.
* [Hozzon létre egy virtuális gépet a VNeten belül](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
* Olvassa el az [NSG-k](virtual-networks-nsg.md) ismertetését.
* Tekintse meg a [felhasználó által megadott útvonalakról és az IP-továbbításról](virtual-networks-udr-overview.md) szóló leírást.



<!--HONumber=Feb17_HO4-->


