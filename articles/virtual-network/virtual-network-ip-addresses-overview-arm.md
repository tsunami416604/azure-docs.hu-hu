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
ms.date: 09/18/2017
ms.author: jdial
ms.openlocfilehash: 8ddd582ed159e10add896252c40feb19780c42fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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

A nyilvános IP-címek lehetővé teszik az Azure-erőforrások számára, hogy kommunikáljanak az internettel és az Azure más nyilvános szolgáltatásaival, például az [Azure Redis Cache](https://azure.microsoft.com/services/cache) és az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) szolgáltatásokkal, valamint az [SQL-adatbázisokkal](../sql-database/sql-database-technical-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és az [Azure-tárolóval](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

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
Megadhat egy DNS-tartománynév címkét a nyilvános IP-cím erőforráshoz, amely hozzárendelést hoz létre a *domainnamelabel*.*location*.cloudapp.azure.com és a nyilvános IP-cím között az Azure által felügyelt DNS-kiszolgálókon. Ha például nyilvános IP-cím erőforrást hoz létre a **contoso** *domainnamelabel* értékkel a **West US** *Azure-helyen*, akkor a rendszer a **contoso.westus.cloudapp.azure.com** teljes tartománynevet (FQDN) az erőforrás nyilvános IP-címére oldja fel. A teljes tartománynevet az Azure-ban lévő nyilvános IP-címre mutató egyéni tartományi CNAME rekord létrehozásához használhatja.

> [!IMPORTANT]
> Minden egyes létrehozott tartománynév-címkének egyedinek kell lennie az Azure-helyen.  
>

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A nyilvános IP-címet társíthatja [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális géphez, ha hozzárendeli a **hálózati adapteréhez**. Hozzárendelhet egy dinamikus vagy egy statikus nyilvános IP-címet a virtuális gépekhez. Tudjon meg többet [az IP-címek hálózati adapterekhez való hozzárendeléséről](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Internetkapcsolattal rendelkező terheléselosztók

Bármely [termékváltozattal](#SKU) létrehozott nyilvános IP-címet társíthatja az [Azure Load Balancerhöz](../load-balancer/load-balancer-overview.md), ha hozzárendeli a terheléselosztó **előtér**-konfigurációjához. A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd. Hozzárendelhet egy dinamikus vagy egy statikus nyilvános IP-címet egy terheléselosztó előtérrendszerhez. Több nyilvános IP-címet is hozzárendelhet egy terheléselosztó előtérrendszerhez, ami olyan [több virtuális IP-címből álló](../load-balancer/load-balancer-multivip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) forgatókönyveket tesz lehetővé, mint az SSL-alapú webhelyek több-bérlős környezetei. Az Azure Load Balancer termékváltozataival kapcsolatos további információkért tekintse meg [az Azure Load Balancer standard termékváltozatáról](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szóló cikket.

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

A magánhálózati IP-cím annak az alhálózatnak a címtartományából van lefoglalva, amelyhez az erőforrás csatlakozik. Az alhálózat címtartománya a virtuális hálózat címtartományának része.

Két módszer van, amellyel a magánhálózati IP-címek lefoglalhatók: *dinamikus* vagy *statikus*. Az alapértelmezett lefoglalási módszer a *dinamikus*, amelyben az IP-cím lefoglalása automatikusan történik az erőforrás alhálózatából (a DHCP használatával). Az IP-cím változhat, ha leállítja, majd újraindítja az erőforrást.

A lefoglalási módszert állíthatja *statikusra* is, hogy az IP-címek változatlanok maradjanak. Ha a *statikus* lehetőséget választja, meg kell adnia egy érvényes IP-címet, amely az erőforrás alhálózatának részét képezi.

Statikus magánhálózati IP-címeket általában a következő esetekben szoktak használni:

* Tartományvezérlőként vagy DNS-kiszolgálóként működő virtuális gépek esetén.
* IP-címeket használó tűzfalszabályokat igénylő erőforrások esetén.
* Más alkalmazások/erőforrások által IP-címen keresztül elért erőforrások esetén.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A magánhálózati IP-cím egy [Windows-](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux-](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)virtuális gép **hálózati adapteréhez** van rendelve. Ha a virtuális gép több hálózati adapterrel rendelkezik, egy-egy magánhálózati IP-cím lesz mindegyik hálózati adapterhez rendelve. A lefoglalási módszert hálózati adapter esetében meghatározhatja dinamikusként vagy statikusként is.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Belső DNS-állomásnév feloldás (virtuális gépek esetén)

Minden Azure virtuális gép alapértelmezés szerint az [Azure által felügyelt DNS-kiszolgálókkal](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) van konfigurálva, ha nem konfigurál kifejezetten egyéni DNS-kiszolgálókat. Ezek a DNS-kiszolgálók belső névfeloldást biztosítanak az egyazon virtuális hálózaton található virtuális gépek számára.

Amikor létrehoz egy virtuális gépet, az állomásnév a magánhálózati IP-címére való leképezése hozzá lesz adva az Azure által felügyelt DNS-kiszolgálóhoz. Ha egy virtuális gép több hálózati adapterrel rendelkezik, az állomásnév az elsődleges hálózati adapter magánhálózati IP-címére lesz leképezve.

Az Azure által felügyelt DNS-kiszolgálókkal konfigurált virtuális gépek képesek az egyazon virtuális hálózatban lévő összes virtuális gép állomásnevét feloldani azok magánhálózati IP-címeire.

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
