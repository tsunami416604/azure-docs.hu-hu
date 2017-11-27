---
title: "IP-cím-típusok az Azure-ban | Microsoft Docs"
description: "Információk a nyilvános és privát IP-címekről az Azure-ban."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2017
ms.author: jdial
ms.openlocfilehash: 3840ed000d5a9fe5d3c8fd01c061bf13674c0ce5
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP-cím-típusok és lefoglalási módszerek az Azure-ban

IP-címeket rendelhet az Azure-erőforrásokhoz a többi Azure-erőforrással, a helyszíni hálózattal és az internettel való kommunikáció céljából. Az Azure-ban két típusú IP-címet használhat:

* **Nyilvános IP-címek**: Az internettel való kommunikációhoz használatosak, beleértve az Azure nyilvános szolgáltatásait.
* **Magánhálózati IP-címek**: Egy Azure virtuális hálózaton (VNet) belüli és a helyszíni hálózaton belüli kommunikációra használatos, ha VPN-átjárót vagy ExpressRoute-kapcsolatcsoportot használ a hálózat kiterjesztésére az Azure-ban.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](virtual-network-ip-addresses-overview-classic.md) helyett javasol.
> 

Ha a klasszikus üzemi modellt ismeri, tekintse meg a következőt: [Az IP-címkezelés különbségei a klasszikus és a Resource Manager modellek között](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Nyilvános IP-címek

A nyilvános IP-címek lehetővé teszik az internetes erőforrások bejövő kommunikációját az Azure-erőforrásokkal. A nyilvános IP-címek emellett lehetővé teszik az Azure-erőforrások kimenő kommunikációját az internettel és a nyilvános Azure-szolgáltatásokkal, az erőforráshoz rendelt IP-címet használva. A cím az erőforrás számára van kijelölve, ameddig vissza nem vonja a hozzárendelést. Ha az erőforráshoz nem lett nyilvános IP-cím hozzárendelve, az erőforrás továbbra is képes kimenő kommunikációra az internettel, az Azure azonban dinamikusan társít hozzá egy elérhető IP-címet, amely nincs az erőforrás számára kijelölve. További információt a [kimenő kapcsolatokat ismertető](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikkben talál az Azure kimenő kapcsolatairól.

Az Azure Resource Manager szolgáltatásban a [nyilvános IP-cím](virtual-network-public-ip-address.md) olyan erőforrás, amely saját tulajdonságokkal rendelkezik. A nyilvános IP-cím-erőforrást például a következő erőforrásokhoz rendelheti hozzá:

* Virtuális gépek hálózati adapterei
* Internetkapcsolattal rendelkező terheléselosztók
* VPN-átjárók
* Alkalmazásátjárók

### <a name="ip-address-version"></a>IP-cím verziója

A nyilvános IP-címek IPv4- vagy IPv6-címekkel jönnek létre. A nyilvános IPv6-címek csak internetkapcsolattal rendelkező terheléselosztókhoz rendelhetők hozzá.

### <a name="sku"></a>SKU

A nyilvános IP-címek a következő termékváltozatok valamelyikével jönnek létre:

#### <a name="basic"></a>Basic

A termékváltozatok bevezetése előtt létrehozott minden nyilvános IP-cím alapszintű termékváltozatú nyilvános IP-cím. A termékváltozatok bevezetésének köszönhetően mostantól megadhatja, hogy milyen termékváltozatú legyen a nyilvános IP-cím. Az alapszintű termékváltozatú címek jellemzői:

- A statikus vagy a dinamikus kiosztási módszerrel oszthatók ki.
- Kioszthatók bármely Azure-erőforrás számára, amelynek kiosztható nyilvános IP-cím, például hálózati adapterek, VPN-átjárók, alkalmazásátjárók és internetkapcsolattal rendelkező terheléselosztók számára.
- Hozzárendelhetők egy adott zónához.
- Nem zónaredundánsak. A rendelkezésre állási zónákkal kapcsolatos további információkért tekintse meg [a rendelkezésre állási zónák áttekintését](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standard

A standard termékváltozatú nyilvános IP-címek jellemzői:

- Csak a statikus kiosztási módszerrel oszthatók ki.
- Hálózati adapterek vagy standard, internetkapcsolattal rendelkező terheléselosztók számára oszthatók ki. Az Azure Load Balancer termékváltozataival kapcsolatos további információkért tekintse meg [az Azure Load Balancer standard termékváltozatáról](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szóló cikket.
- Alapértelmezés szerint zónaredundánsak. Létrehozhatóak zónásan, és garantálhatóak adott rendelkezésre állási zónákban.  A rendelkezésre állási zónákkal kapcsolatos további információkért tekintse meg [a rendelkezésre állási zónák áttekintését](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](security-overview.md#network-security-groups).  Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.

A standard termékváltozat előzetes kiadásban érhető el. Standard termékváltozatú nyilvános IP-cím létrehozásához először regisztrálnia kell az előzetes kiadásra, és a címet egy támogatott helyen kell létrehoznia. Az előzetes kiadásra való regisztrációval kapcsolatban tekintse meg [a standard termékváltozat előzetes kiadására való regisztrációt ismertető](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview) cikket. A támogatott helyek (régiók) listájáért tekintse meg a [régiónkénti elérhetőséget](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) részletező szakaszt, és a további támogatott régiókért kövesse az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.


### <a name="allocation-method"></a>Lefoglalási módszer

Az IP-címek két különféle módszerrel oszthatók ki egy nyilvános IP-cím erőforrás számára: *dinamikus* vagy *statikus* módon. Az alapértelmezett lefoglalási módszer a *dinamikus*, amelyben az IP-cím **nincs** lefoglalva a létrehozás idején. Ehelyett a nyilvános IP-címet akkor foglalja le a rendszer, amikor elindítja (vagy létrehozza) a hozzárendelt erőforrást (pl. virtuális gépet vagy terheléselosztót). Az IP-cím akkor szabadul fel, ha leállítja (vagy törli) az erőforrást. Az A erőforrásból való felszabadítását követően például az IP-cím kiosztható egy másik erőforrás számára. Ha az IP-címet hozzárendeli egy másik erőforráshoz, amikor az A erőforrás le van állítva, az A erőforrás újraindításakor egy eltérő IP-cím lesz kiosztva számára.

A lefoglalási módszert állíthatja *statikusra* is, hogy a kapcsolódó erőforráshoz tartozó IP-címek változatlanok maradjanak. A rendszer azonnal kioszt egy statikus IP-címet. A cím csak akkor szabadul fel, ha törli az erőforrást, vagy *dinamikusra* változtatja a kiosztási módszert.

> [!NOTE]
> Akkor sem adhatja meg a nyilvános IP-cím erőforráshoz rendelt konkrét IP-címet, ha a kiosztási módszert *statikusra* állítja. Ehelyett az Azure az elérhető IP-címek készletéből foglalja le az IP-címet azon az Azure-helyen, ahol az erőforrást létrehozták.
>

Statikus nyilvános IP-címeket általában a következő esetekben szoktak használni:

* Amikor frissíteni kell a tűzfalszabályokat, hogy kommunikálhassanak az Azure-erőforrásaival.
* DNS-névfeloldás, ahol az IP-cím változása miatt frissíteni kellene az A-rekordokat.
* Az Ön Azure-erőforrásai más alkalmazásokkal és szolgáltatásokkal is kommunikálnak, amelyek IP-címalapú biztonsági modellt használnak.
* Ön IP-címhez kötött SSL-tanúsítványokat használ.

> [!NOTE]
> Az Azure a nyilvános IP-címeket az egyes Azure-régiók esetében egyedi tartományból foglalja le. További részletek: [Azure-adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>DNS-állomásnév feloldása
Megadhat egy DNS-tartománynév címkét a nyilvános IP-cím erőforráshoz, amely hozzárendelést hoz létre a *domainnamelabel*.*location*.cloudapp.azure.com és a nyilvános IP-cím között az Azure által felügyelt DNS-kiszolgálókon. Ha például nyilvános IP-cím erőforrást hoz létre a **contoso** *domainnamelabel* értékkel a **West US** *Azure-helyen*, akkor a rendszer a **contoso.westus.cloudapp.azure.com** teljes tartománynevet (FQDN) az erőforrás nyilvános IP-címére oldja fel. A teljes tartománynevet az Azure-ban lévő nyilvános IP-címre mutató egyéni tartományi CNAME rekord létrehozásához használhatja. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.

> [!IMPORTANT]
> Minden egyes létrehozott tartománynév-címkének egyedinek kell lennie az Azure-helyen.  
>

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A nyilvános IP-címet társíthatja [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális géphez, ha hozzárendeli a **hálózati adapteréhez**. Hozzárendelhet egy dinamikus vagy egy statikus nyilvános IP-címet a virtuális gépekhez. Tudjon meg többet [az IP-címek hálózati adapterekhez való hozzárendeléséről](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Internetkapcsolattal rendelkező terheléselosztók

Bármely [termékváltozattal](#SKU) létrehozott nyilvános IP-címet társíthatja az [Azure Load Balancerhöz](../load-balancer/load-balancer-overview.md), ha hozzárendeli a terheléselosztó **előtér**-konfigurációjához. A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd. Hozzárendelhet egy dinamikus vagy egy statikus nyilvános IP-címet a terheléselosztó előtérrendszerhez. Több nyilvános IP-címet is hozzárendelhet egy terheléselosztó előtérrendszerhez, ami olyan [több virtuális IP-címes](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) forgatókönyveket tesz lehetővé, mint az SSL-alapú webhelyek több-bérlős környezetei. Az Azure Load Balancer termékváltozataival kapcsolatos további információkért tekintse meg [az Azure Load Balancer standard termékváltozatáról](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szóló cikket.

### <a name="vpn-gateways"></a>VPN-átjárók

Az [Azure VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy Azure virtuális hálózatot (VNet) kapcsol össze más Azure virtuális hálózatokkal vagy egy helyszíni hálózattal. A rendszer egy nyilvános IP-címet rendel a VPN-átjáróhoz, hogy az képes legyen kommunikálni a távoli hálózattal. Csak *dinamikus* nyilvános IP-címeket rendelhet a VPN-átjárókhoz.

### <a name="application-gateways"></a>Alkalmazásátjárók

A nyilvános IP-címet társíthatja egy [Azure Application Gateway átjáróval](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ha hozzárendeli az átjáró **előtér**-konfigurációjához. Ez a nyilvános IP-cím terheléselosztásos virtuális IP-címként szolgál majd. Csak *dinamikus* nyilvános IP-címeket rendelhet az alkalmazásátjárók előtér-konfigurációjához.

### <a name="at-a-glance"></a>Egy pillantásra
A következő táblázat bemutatja azokat a konkrét tulajdonságokat, amelyekkel a nyilvános IP-címek legfelsőbb szintű erőforráshoz társíthatók, továbbá a lehetséges használható kiosztási módszereket (dinamikus vagy statikus).

| Legfelső szintű erőforrás | IP-cím társítása | Dinamikus | Statikus |
| --- | --- | --- | --- |
| Virtuális gép |Hálózati illesztő |Igen |Igen |
| Internetkapcsolattal rendelkező terheléselosztó |Előtér-konfiguráció |Igen |Igen |
| VPN-átjáró |Átjáró IP-konfigurációja |Igen |Nem |
| Alkalmazásátjáró |Előtér-konfiguráció |Igen |Nem |

## <a name="private-ip-addresses"></a>Magánhálózati IP-címek
A magánhálózati IP-címek segítségével az Azure-erőforrások képesek a helyszíni vagy a [virtuális hálózaton](virtual-networks-overview.md) lévő erőforrásokkal VPN-átjárón vagy ExpressRoute-kapcsolatcsoporton keresztül kommunikálni az internetről elérhető IP-címek használata nélkül is.

Az Azure Resource Manager-alapú üzemi modellben a magánhálózati IP-címek az alábbi Azure-erőforrástípusokkal vannak társítva:

* Virtuális gépek hálózati adapterei
* Belső terheléselosztók (ILB-k)
* Alkalmazásátjárók

### <a name="ip-address-version"></a>IP-cím verziója

A magánhálózati IP-címek IPv4- vagy IPv6-címekkel jönnek létre. A magánhálózati IPv6-címekhez csak dinamikus kiosztási módszer használható. A magánhálózati IPv6-címek közötti kommunikáció nem lehetséges virtuális hálózatokon. A magánhálózati IPv6-címek felé bejövő kommunikációt folytathat az internetről egy internetkapcsolattal rendelkező terheléselosztón keresztül. Tovább részleteket az [internetkapcsolattal rendelkező terheléselosztó IPv6-tal való létrehozását ismertető](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikkben találhat.

### <a name="allocation-method"></a>Lefoglalási módszer

A magánhálózati IP-cím a virtuális hálózat azon alhálózatának a címtartományából van lefoglalva, amelyben az erőforrás üzembe van helyezve. Az Azure lefoglalja minden egyes alhálózat címtartományának első négy címét, ezért ezeket nem lehet erőforrásokhoz társítani. Ha például az alhálózat címtartománya 10.0.0.0/16, a 10.0.0.0–10.0.0.3 címek nem rendelhetők erőforrásokhoz. Az alhálózati címtartományon belüli IP-címeket egyszerre csak egy erőforráshoz lehet rendelni. 

Két módszer van, amellyel a magánhálózati IP-címek lefoglalhatók:

- **Dinamikus**: Az Azure az alhálózat címtartományának következő elérhető, nem hozzárendelt vagy nem lefoglalt IP-címét rendeli hozzá. Például az Azure a 10.0.0.10 címet rendeli hozzá egy új erőforráshoz, ha a 10.0.0.4–10.0.0.9 címek már más erőforrásokhoz lettek rendelve. Az alapértelmezett lefoglalási módszer a dinamikus. Kiosztás után a dinamikus IP-címek csak a hálózati adapter törlésekor, a virtuális hálózaton belüli másik alhálózatra történő kiosztáskor vagy a kiosztási módszer statikusra váltása és másik IP-cím megadása esetén szabadulnak fel. Alapértelmezés szerint, amikor a lefoglalási módszert dinamikusról statikusra váltja, az Azure statikus címként osztja ki az előzőleg dinamikusan kiosztott címet.
- **Statikus**: Ön választhat ki és rendelhet hozzá az alhálózat címtartományából egy nem hozzárendelt vagy nem lefoglalt IP-címet. Ha például az alhálózat címtartománya 10.0.0.0/16, és a 10.0.0.4–10.0.0.9 címek már más erőforrásokhoz lettek rendelve, a 10.0.0.10–10.0.255.254 tartományból rendelhet hozzá tetszőleges címet. A statikus címek csak egy hálózati adapter törlése esetén szabadulnak fel. Amennyiben a kiosztási módszert statikusra váltja, az Azure az előzőleg hozzárendelt statikus IP-címeket dinamikus IP-címként osztja ki akkor is, ha a cím nem az alhálózat címtartományának következő elérhető címe. A cím akkor is megváltozik, ha a hálózati adapter ugyanazon a virtuális hálózaton belül egy másik alhálózathoz lesz kiosztva, de ahhoz, hogy a hálózati adaptert egy másik alhálózathoz ossza ki, a kiosztási módszert először statikusról dinamikusra kell váltani. Miután hozzárendelte a hálózati adaptert egy másik alhálózathoz, a kiosztási módszer visszaváltható statikusra, és hozzárendelhet egy IP-címet az új alhálózat címtartományából.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

Egy vagy több magánhálózati IP-cím egy vagy több [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális gép **hálózati adapteréhez** van rendelve. A lefoglalási módszert minden magánhálózati IP-cím esetében meghatározhatja dinamikusként vagy statikusként is.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Belső DNS-állomásnév feloldás (virtuális gépek esetén)

Minden Azure virtuális gép alapértelmezés szerint az [Azure által felügyelt DNS-kiszolgálókkal](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) van konfigurálva, ha nem konfigurál kifejezetten egyéni DNS-kiszolgálókat. Ezek a DNS-kiszolgálók belső névfeloldást biztosítanak az egyazon virtuális hálózaton található virtuális gépek számára.

Amikor létrehoz egy virtuális gépet, az állomásnév a magánhálózati IP-címére való leképezése hozzá lesz adva az Azure által felügyelt DNS-kiszolgálóhoz. Ha egy virtuális gép több hálózati adapterrel vagy a hálózati adapter több IP konfigurációval rendelkezik, az eszköznév az elsődleges hálózati adapter elsődleges IP konfigurációjának magánhálózati IP-címére lesz leképezve.

Az Azure által felügyelt DNS-kiszolgálókkal konfigurált virtuális gépek képesek az egyazon virtuális hálózatban lévő összes virtuális gép állomásnevét feloldani azok magánhálózati IP-címeire. A csatlakoztatott virtuális hálózatokban található virtuális gépek eszközneveinek feloldásához egyéni DNS-kiszolgálóra lesz szüksége.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Belső terheléselosztók (ILB) és alkalmazásátjárók

Hozzárendelhet egy magánhálózati IP-címet az [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) terheléselosztók vagy [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) alkalmazásátjárók **előtér**-konfigurációjához. Ez a magánhálózati IP-cím belső végpontként szolgál, amelyet csak a virtuális hálózatán és a virtuális hálózathoz csatlakozó távoli hálózatokon lévő erőforrások érnek el. Hozzárendelhet egy dinamikus vagy egy statikus magánhálózati IP-címet az előtér-konfigurációhoz.

### <a name="at-a-glance"></a>Egy pillantásra
A következő táblázat bemutatja azokat a konkrét tulajdonságokat, amelyekkel a magánhálózati IP-címek legfelsőbb szintű erőforráshoz társíthatók, továbbá a lehetséges használható kiosztási módszereket (dinamikus vagy statikus).

| Legfelső szintű erőforrás | IP-cím társítása | Dinamikus | Statikus |
| --- | --- | --- | --- |
| Virtuális gép |Hálózati illesztő |Igen |Igen |
| Terheléselosztó |Előtér-konfiguráció |Igen |Igen |
| Alkalmazásátjáró |Előtér-konfiguráció |Igen |Igen |

## <a name="limits"></a>Korlátok
Az IP-címkezelésre vonatkozó korlátokat a [hálózati korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) teljes csomagjában jelezzük az Azure-ban. A korlátok régiónként és előfizetésenként értendőek. [Lépjen kapcsolatba a támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), ha növelni szeretné az alapértelmezett korlátot üzleti igényei szerint – akár a maximális korlátig.

## <a name="pricing"></a>Díjszabás
A nyilvános IP-címek kapcsán névleges díjak merülhetnek fel. Ha többet szeretne megtudni az Azure-ban használt IP-címek díjszabásáról, tekintse át az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt.

## <a name="next-steps"></a>Következő lépések
* [Statikus nyilvános IP-címmel rendelkező virtuális gép telepítése az Azure Portal használatával](virtual-network-deploy-static-pip-arm-portal.md)
* [Statikus nyilvános IP-címmel rendelkező virtuális gép telepítése sablon használatával](virtual-network-deploy-static-pip-arm-template.md)
* [Statikus magánhálózati IP-címmel rendelkező virtuális gép telepítése az Azure Portal használatával](virtual-networks-static-private-ip-arm-pportal.md)
