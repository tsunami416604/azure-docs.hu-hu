---
title: "IP-cím típusok (klasszikus) Azure-ban |} Microsoft Docs"
description: "További információk a nyilvános és magánhálózati IP-címek (klasszikus) Azure-ban."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: d5eea5e4499b9de40002ce2fc6aac39239c41b19
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-cím típusok és elosztási módszert (klasszikus) Azure-ban
IP-címeket rendelhet az Azure-erőforrásokhoz a többi Azure-erőforrással, a helyszíni hálózattal és az internettel való kommunikáció céljából. Az IP-címek is használhatja az Azure-ban két típusa van: nyilvános és titkos.

Nyilvános IP-címeket az interneten, beleértve a nyilvánosan elérhető Azure services szolgáltatással való kommunikációhoz használt.

Magán IP-címek folyik kommunikáció egy Azure virtuális hálózatot (VNet), a felhőszolgáltatás és a helyszíni hálózat kiterjesztése a hálózat az Azure-bA egy VPN-átjáró vagy ExpressRoute-kapcsolatcsoportot használatakor.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager. IP-cím erőforrás-kezelő információ összetevőről a [IP-címek](virtual-network-ip-addresses-overview-arm.md) cikk.

## <a name="public-ip-addresses"></a>Nyilvános IP-címek
A nyilvános IP-címek lehetővé teszik az Azure-erőforrások számára, hogy kommunikáljanak az internettel és az Azure más nyilvános szolgáltatásaival, például az [Azure Redis Cache](https://azure.microsoft.com/services/cache/) és az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásokkal, valamint az [SQL-adatbázisokkal](../sql-database/sql-database-technical-overview.md) és az [Azure-tárolóval](../storage/common/storage-introduction.md).

A nyilvános IP-cím társítva a következő típusú erőforrások:

* Felhőszolgáltatások
* Infrastruktúra-szolgáltatási virtuális gépeket (VM)
* PaaS szerepkörpéldányok
* VPN-átjárók
* Alkalmazásátjárók

### <a name="allocation-method"></a>Lefoglalási módszer
Ha egy nyilvános IP-címet kell hozzá kell rendelni egy Azure-erőforrás, akkor *dinamikusan* foglalja le a helyre, az erőforrás létrehozása elérhető nyilvános IP-címek készletét. Az IP-cím kiadása történik, amikor az erőforrás le van állítva. Abban az esetben, ha egy felhőszolgáltatás, ez történik, ha a szerepkör összes példányát le van állítva, amelyek segítségével elkerülhetők a *statikus* (fenntartott) IP-cím (lásd: [Felhőszolgáltatások](#Cloud-services)).

> [!NOTE]
> Nyilvános IP-címek, amelyből Azure-erőforrások számára kiosztott IP-címtartománylista közzé van téve a [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>DNS-állomásnév feloldása
Egy felhőalapú szolgáltatás, vagy az infrastruktúra-szolgáltatási virtuális gép létrehozásakor meg kell adnia egy felhőalapú szolgáltatás DNS-neve, ami esetében egyedi összes erőforrást az Azure-ban. Ez az Azure által kezelt DNS-kiszolgálóinak leképezéseket hoz létre *dnsname*. a nyilvános IP-cím erőforrás cloudapp.net. Például ha hoz létre egy felhőszolgáltatás egy felhőalapú szolgáltatás DNS-neve **contoso**, a teljesen minősített tartománynevét (FQDN) **contoso.cloudapp.net** egy nyilvános IP-címére (VIP) fel a a felhőalapú szolgáltatás. Ezt a teljes tartománynevet használhatja az Azure-ban lévő nyilvános IP-címre mutató egyéni tartományi CNAME-rekord létrehozásához.

### <a name="cloud-services"></a>Felhőszolgáltatások
Egy felhőalapú szolgáltatás mindig van egy nyilvános IP-cím, egy virtuális IP-cím (VIP) néven. Rendelje hozzá a virtuális gépek és a felhőszolgáltatás belül szerepkörpéldányok belső portokká VIP különböző portok felhőszolgáltatásban végpontok is létrehozhat. 

Egy felhőalapú szolgáltatás több IaaS virtuális gépeket, vagy PaaS szerepkörpéldányt, minden elérhetővé a felhőalapú szolgáltatás ugyanazon VIP keresztül is tartalmazhat. Is rendelhet [több virtuális IP-címek egy felhőszolgáltatásra történő](../load-balancer/load-balancer-multivip.md), amely lehetővé teszi több virtuális IP-CÍMES helyzetekben, például a webhely SSL-alapú több-bérlős környezet.

Egy felhőalapú szolgáltatás nyilvános IP-címe változatlan marad, akkor is, ha a szerepkör összes példányát le van állítva, használatával biztosítható a *statikus* nyilvános IP-cím, a továbbiakban [foglalt IP-cím](virtual-networks-reserved-public-ip.md). A statikus (fenntartott) IP-erőforrás létrehozása egy adott helyen, és rendelje hozzá a felhőalapú szolgáltatás azon a helyen. A foglalt IP-cím nem lehet meghatározni az aktuális IP-cím, a készlet létrehozása a helyen rendelkezésre álló IP-címek hozzárendelése. Az IP-cím kiadása nem történik, amíg explicit módon törlése.

Statikus (fenntartott) nyilvános IP-címek gyakran fordulnak elő a forgatókönyvekben, ahol egy felhőalapú szolgáltatás:

* tűzfalszabályok végfelhasználók telepíteni kell.
* külső DNS-névfeloldás, attól függ, és egy dinamikus IP-cím A rekordok frissítése szükséges.
* IP-alapú biztonsági modellt használó külső webes szolgáltatások igényel.
* IP-címhez társított SSL-tanúsítványokat használ.

> [!NOTE]
> Amikor létrehoz egy klasszikus virtuális Gépet, a tároló *felhőalapú szolgáltatás* jön létre az Azure-ban, a virtuális IP-cím (VIP). Amikor a létrehozás cseréjén keresztül történik egy alapértelmezett RDP és az SSH-portálon *végpont* a portál van beállítva, ezért a virtuális Gépet, a cloud service VIP keresztül is elérheti. A felhőalapú szolgáltatás VIP foglalható le, amely hatékonyan biztosítja a fenntartott IP-cím, amellyel a virtuális Géphez csatlakozik. További portokat úgy is megnyithatja, hogy további végpontok konfigurálásakor.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Infrastruktúra-szolgáltatási virtuális gépeknél és PaaS szerepkörpéldányok
Rendelhet egy nyilvános IP-címet közvetlenül egy infrastruktúra-szolgáltatási [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy PaaS szerepkörpéldányt felhőszolgáltatásban. Ezt nevezzük példányszintű nyilvános IP-címet ([ILPIP](virtual-networks-instance-level-public-ip.md)). A nyilvános IP-cím csak dinamikus lehet.

> [!NOTE]
> Ez eltér a VIP, a felhőalapú szolgáltatás, amely IaaS virtuális gépeket vagy PaaS szerepkörpéldányt beállítani, mivel egy felhőalapú szolgáltatás több IaaS virtuális gépeket is tartalmazhat, vagy PaaS szerepkörpéldányt, a felhőalapú szolgáltatás ugyanazon VIP keresztül közzétett minden tárolója.
> 
> 

### <a name="vpn-gateways"></a>VPN-átjárók
A [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md) segítségével más Azure Vnetekhez vagy a helyszíni hálózatokhoz való kapcsolódásának egy Azure virtuális hálózatot. VPN-átjáró hozzá van rendelve egy nyilvános IP-cím *dinamikusan*, amely lehetővé teszi a távoli hálózati kommunikáció.

### <a name="application-gateways"></a>Alkalmazásátjárók
Egy Azure [Alkalmazásátjáró](../application-gateway/application-gateway-introduction.md) Layer7 terheléselosztás a HTTP-alapú hálózati forgalom is használható. Alkalmazásátjáró hozzá van rendelve egy nyilvános IP-cím *dinamikusan*, amely az elosztott terhelésű VIP funkcionál.

### <a name="at-a-glance"></a>Áttekintés
Az alábbi táblázat a lehetséges, hogy foglalási módszerek (dinamikus vagy statikus), és képes több nyilvános IP-cím hozzárendelése minden erőforrástípus.

| Erőforrás | Dinamikus | Statikus | Több IP-cím |
| --- | --- | --- | --- |
| A felhőalapú szolgáltatás |Igen |Igen |Igen |
| Infrastruktúra-szolgáltatási virtuális gép vagy PaaS szerepkörpéldányt |Igen |Nem |Nem |
| VPN-átjáró |Igen |Nem |Nem |
| Alkalmazásátjáró |Igen |Nem |Nem |

## <a name="private-ip-addresses"></a>Magánhálózati IP-címek
Magán IP-címek engedélyezése az Azure-erőforrások kommunikálni az egyéb erőforrások egy felhőalapú szolgáltatás, vagy egy [virtuális hálózati](virtual-networks-overview.md)(VNet), vagy a helyi hálózathoz (keresztül egy VPN-átjáró vagy ExpressRoute-kapcsolatcsoportot) használata nélkül egy Internet-elérhető IP-címre.

Az Azure klasszikus üzembe helyezési modellel egy magánhálózati IP-cím a következő Azure-erőforrások rendelhetők:

* Infrastruktúra-szolgáltatási virtuális gépeknél és PaaS szerepkörpéldányok
* Belső terheléselosztó
* Alkalmazásátjáró

### <a name="iaas-vms-and-paas-role-instances"></a>Infrastruktúra-szolgáltatási virtuális gépeknél és PaaS szerepkörpéldányok
A klasszikus üzembe helyezési modellel létrehozott virtuális gépeket (VM) mindig kerülnek egy felhőalapú szolgáltatás, a PaaS szerepkörpéldányok hasonló. Magánhálózati IP-címek viselkedését így hasonlóak az ezekhez az erőforrásokhoz.

Fontos megjegyezni, hogy a felhőszolgáltatás két módon telepíthető:

* Mint egy *önálló* felhőalapú szolgáltatás, amennyiben nincs a virtuális hálózaton belül.
* A virtuális hálózat része.

#### <a name="allocation-method"></a>Lefoglalási módszer
Esetében a *önálló* felhőalapú szolgáltatás, egy magánhálózati IP-cím lefoglalt erőforrások get *dinamikusan* titkos Azure datacenter IP-címtartományt. Csak a többi virtuális gép belül az azonos felhőszolgáltatás-kommunikációhoz használható. Az IP-címet módosíthatja, amikor az erőforrás leáll, majd elindítani.

Esetén egy felhőalapú szolgáltatás, a virtuális hálózaton belül telepített erőforrások (a megadott hálózati konfigurációja) tartozó alhálózat címtartományából lefoglalt privát IP-címek lekérése. A privát IP-címei a Vneten belül az összes virtuális gépek közötti kommunikációra használható.

Emellett esetén felhőszolgáltatások történik egy Vneten belül, egy magánhálózati IP-cím van lefoglalva *dinamikusan* (a DHCP használatával) alapértelmezés szerint. Ha az erőforrás leáll, majd elindítani módosíthatja azt. Gondoskodjon arról, hogy az IP-címe ugyanaz marad, a kiosztási módszer beállítása kell *statikus*, és adjon meg egy érvényes IP-címet a megfelelő címtartományán belül.

Statikus magánhálózati IP-címeket általában a következő esetekben szoktak használni:

* Tartományvezérlőként vagy DNS-kiszolgálóként működő virtuális gépek esetén.
* Virtuális gépek IP-címeket használnak tűzfalszabályokat igénylő.
* Futó szolgáltatások IP-címet egyéb alkalmazások által elért virtuális gépeket.

#### <a name="internal-dns-hostname-resolution"></a>Belső DNS-állomásnév feloldása
Minden Azure virtuális gépek és PaaS szerepkörpéldányok konfigurált [Azure által kezelt DNS-kiszolgálók](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) alapértelmezés szerint csak explicit módon egyéni DNS-kiszolgálók. A DNS-kiszolgálók belső névfeloldást biztosít virtuális gépek és a szerepkörpéldányok szolgáltatásának ugyanazt a virtuális hálózat vagy a felhőben található.

Amikor létrehoz egy virtuális gépet, az állomásnév a magánhálózati IP-címére való leképezése fel lesz véve az Azure által felügyelt DNS-kiszolgálókra. A több hálózati Adapterrel virtuális gépek esetén az állomásnév készletre van leképezve a magánhálózati IP-címe a elsődleges hálózati adaptert. Azonban a hozzárendelési információkat a felhőalapú szolgáltatás- vagy virtuális hálózaton belüli erőforrások korlátozódik.

Esetében a *önálló* felhőalapú szolgáltatás, oldja meg az összes virtuális gépeket/szerepkörpéldányokat belül csak az ugyanabban a felhőalapú szolgáltatás állomásnevek lesz. Egy felhőalapú szolgáltatás egy Vneten belül, ha fogja tudni oldania az összes a virtuális gépeket/szerepkörpéldányokat a Vneten belül állomásnevek.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Belső terheléselosztók (ILB) és alkalmazásátjárók
Hozzárendelhet egy magánhálózati IP-címet az [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) terheléselosztók vagy [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) alkalmazásátjárók **előtér**-konfigurációjához. Ez a magánhálózati IP-cím belső végpontként szolgál, amelyet csak a virtuális hálózatán (VNet) és a VNethez csatlakozó távoli hálózatokon lévő erőforrások érnek el. Hozzárendelhet egy dinamikus vagy egy statikus méretű magánhálózati IP-címet az előtér-konfigurációhoz. Több privát IP-címek lehetővé több virtuális IP-címes forgatókönyveket is hozzárendelhetők.

### <a name="at-a-glance"></a>Áttekintés
Az alábbi táblázat a lehetséges, hogy foglalási módszerek (dinamikus vagy statikus), és képes több privát IP-cím hozzárendelése minden erőforrástípus.

| Erőforrás | Dinamikus | Statikus | Több IP-cím |
| --- | --- | --- | --- |
| Virtuális gép (a egy *önálló* felhőalapú szolgáltatás, vagy a virtuális hálózat) |Igen |Igen |Igen |
| A PaaS szerepkörpéldányt (az egy *önálló* felhőalapú szolgáltatás, vagy a virtuális hálózat) |Igen |Nem |Nem |
| Belső terheléselosztó előtér |Igen |Igen |Igen |
| Átjáró előtér-alkalmazás |Igen |Igen |Igen |

## <a name="limits"></a>Korlátok
Az alábbi táblázat mutatja a határoz meg küszöbértéket IP-címzés az Azure-ban előfizetésenként. [Lépjen kapcsolatba a támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), ha növelni szeretné az alapértelmezett korlátot üzleti igényei szerint – akár a maximális korlátig.

|  | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek (dinamikus) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Fenntartott nyilvános IP-címek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános VIP (felhőszolgáltatás) üzemelő példányonként |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Magán a VIP (ILB) a (felhőszolgáltatás) üzemelő példányonként |1 |1 |

Győződjön meg arról, hogy olvassa el a teljes készletének [korlátozza a hálózati](../azure-subscription-service-limits.md#networking-limits) az Azure-ban.

## <a name="pricing"></a>Díjszabás
A legtöbb esetben a nyilvános IP-címek szabadon. Nincs további és/vagy a statikus nyilvános IP-címek névleges járnak. Győződjön meg arról, hogy tudomásul veszi a [struktúra árképzési nyilvános IP-címek](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Erőforrás-kezelő és a klasszikus üzembe helyezés közötti különbségek
Alább az IP-címzési szolgáltatásainak az erőforrás-kezelő és a klasszikus telepítési modell összehasonlítása.

|  | Erőforrás | Klasszikus | Resource Manager |
| --- | --- | --- | --- |
| **Nyilvános IP-cím** |***VIRTUÁLIS GÉP*** |Egy ILPIP (csak dinamikus) néven |Egy nyilvános IP-cím (dinamikus vagy statikus) néven |
|  ||Az infrastruktúra-szolgáltatási virtuális gép vagy egy PaaS szerepkörpéldányt rendelt |A virtuális hálózati adapterhez társított | |
|  |***Az Internet felé néző terheléselosztó*** |Néven VIP (dinamikus) vagy fenntartott IP (statikus) |Egy nyilvános IP-cím (dinamikus vagy statikus) néven | |
|  ||A felhőalapú szolgáltatáshoz |A terheléselosztó előtér-config társított | |
|  | | | |
| **Magánhálózati IP-cím** |***VIRTUÁLIS GÉP*** |Egy DIP néven |A magánhálózati IP-cím néven |
|  ||Az infrastruktúra-szolgáltatási virtuális gép vagy egy PaaS szerepkörpéldányt rendelt |A virtuális hálózati adapterhez hozzárendelt | |
|  |***Belső terheléselosztón (ILB)*** |A ILB (dinamikus vagy statikus) rendelt |A ILB előtér konfigurációs (dinamikus vagy statikus) rendelt | |

## <a name="next-steps"></a>Következő lépések
* [Telepítse a virtuális Gépet egy statikus magánhálózati IP-cím](virtual-networks-static-private-ip-classic-pportal.md) az Azure portál használatával.

