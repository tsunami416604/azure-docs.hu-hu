---
title: IP-cím-típusok (klasszikus) Azure-ban |} A Microsoft Docs
description: További információ a nyilvános és privát IP-címek (klasszikus) Azure-ban.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 81699764952e50cb18c1f299c9c4f7c524b0a332
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011689"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-cím-típusok és lefoglalási módszereket (klasszikus) Azure-ban
IP-címeket rendelhet az Azure-erőforrásokhoz a többi Azure-erőforrással, a helyszíni hálózattal és az internettel való kommunikáció céljából. Az IP-címek is használhatja az Azure-ban két típusa van: a nyilvános és privát.

Nyilvános IP-címeket használja kommunikációra az internettel, beleértve az Azure nyilvános szolgáltatásait.

Magánhálózati IP-címek a hálózat kiterjesztésére az Azure VPN-átjárót vagy ExpressRoute-kapcsolatcsoport használatakor egy Azure virtuális hálózaton (VNet), egy felhőalapú szolgáltatás és a helyszíni hálózaton belüli kommunikációra szolgálnak.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy új telepítések esetén a Resource Manager használatához. További információ a IP-címek a Resource Managerben, olvassa el a [IP-címek](virtual-network-ip-addresses-overview-arm.md) cikk.

## <a name="public-ip-addresses"></a>Nyilvános IP-címek
Nyilvános IP-címek lehetővé teszik az internetes és az Azure nyilvános szolgáltatások esetén például kommunikálni az Azure erőforrások [Azure Cache redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-adatbázisok](../sql-database/sql-database-technical-overview.md), és [az Azure storage](../storage/common/storage-introduction.md).

Nyilvános IP-címmel társítva a következő erőforrástípusok:

* Felhőszolgáltatások
* Iaas-beli virtuális gépeken (VM)
* PaaS szerepkörpéldányok
* VPN-átjárók
* Alkalmazásátjárók

### <a name="allocation-method"></a>Lefoglalási módszer
Ha hozzá kell rendelni egy Azure-erőforrás nyilvános IP-címre van szüksége, *dinamikusan* számára lefoglalt nyilvános IP-címet a helyre, az erőforrás létrehozása egy készletét. Az IP-cím akkor szabadul fel, ha az erőforrás le van állítva. Abban az esetben egy felhőszolgáltatás, ez történik, ha az összes szerepkörpéldány le lesz állítva, amelyek használatával elkerülhető olyan *statikus* (fenntartott) IP-cím (lásd: [Cloud Services](#Cloud-services)).

> [!NOTE]
> Azure-erőforrások számára kiosztott nyilvános IP-címet, amelyről IP-címtartományok listája közzé van téve [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>DNS-állomásnév feloldása
A felhőszolgáltatás és a egy IaaS-beli virtuális gép létrehozásakor meg kell adnia egy felhőalapú szolgáltatás DNS-neve, ami egyedi el az összes erőforrást az Azure-ban. Ez az Azure által felügyelt DNS-kiszolgálók, a hozzárendelést hoz létre *dnsname*. cloudapp.net az erőforrás nyilvános IP-címet. Például ha hoz létre egy felhőalapú szolgáltatás egy felhőalapú szolgáltatás DNS-neve **contoso**, a teljes tartománynév (FQDN) **contoso.cloudapp.net** nyilvános IP-cím (VIP) a rendszer feloldja a felhőalapú szolgáltatás. Ezt a teljes tartománynevet használhatja az Azure-ban lévő nyilvános IP-címre mutató egyéni tartományi CNAME-rekord létrehozásához.

### <a name="cloud-services"></a>Felhőszolgáltatások
Egy felhőalapú szolgáltatás mindig van egy nyilvános IP-címet virtuális IP-címet (VIP) nevezzük. Egy felhőalapú szolgáltatás társítani a virtuális IP-CÍMEK a virtuális gépek és szerepkörpéldányok felhőszolgáltatáson belül belső portokra különböző portok végpontokat hozhat létre. 

Egy felhőalapú szolgáltatás több IaaS virtuális gépekhez, vagy PaaS szerepkörpéldányt, az összes elérhetővé tett azonos felhőalapú szolgáltatás virtuális IP-címén is tartalmazhat. Is hozzárendelhet [több virtuális IP-címek egy felhőszolgáltatáshoz](../load-balancer/load-balancer-multivip.md), amely lehetővé teszi több virtuális IP-helyzetek, például az SSL-alapú webhelyek több-bérlős környezetben.

A felhőalapú szolgáltatások nyilvános IP-cím változatlan marad, az összes szerepkörpéldány leállása, akkor a használatával gondoskodhat a *statikus* nyilvános IP-cím néven [fenntartott IP-cím](virtual-networks-reserved-public-ip.md). Hozzon létre egy statikus (fenntartott) IP-erőforrást egy adott helyen, és rendelje hozzá minden olyan felhőalapú szolgáltatás, az adott helyen. A fenntartott IP-cím nem adhat meg a konkrét IP-címet, a készlet létrehozása a helyen rendelkezésre álló IP-címek hozzárendelése. Az IP-címet nem jelent meg, amíg explicit módon törli.

Statikus (fenntartott) nyilvános IP-címek gyakran fordulnak elő a forgatókönyvek, ahol egy felhőalapú szolgáltatás:

* tűzfalszabályokat kell beállítani a végfelhasználók által igényel.
* külső DNS-névfeloldás, attól függ, és a egy dinamikus IP-cím miatt frissíteni kellene az egy a rekordot.
* külső webes szolgáltatásokat, amelyek IP-alapú biztonsági modellt használ.
* IP-címhez kötött SSL-tanúsítványokat használ.

> [!NOTE]
> Amikor egy klasszikus virtuális gép, egy tárolót hoz létre *felhőszolgáltatás* jön létre az Azure-ban, amely rendelkezik a virtuális IP-címet (VIP). Amikor a létrehozás egy alapértelmezett RDP vagy SSH-portálon keresztül történik *végpont* a portál által van konfigurálva, így a virtuális gép felhőalapú szolgáltatás virtuális IP-CÍMÉN keresztül csatlakozhat. A felhőalapú szolgáltatás virtuális IP-cím foglalható, amely hatékonyan biztosít egy fenntartott IP-címet a virtuális Géphez való csatlakozáshoz. További portok megnyitásához további végpontok konfigurálása.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS virtuális gépeknél és PaaS szerepkörpéldányok
Hozzárendelhet egy nyilvános IP-címet közvetlenül az IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy PaaS szerepkörpéldányok felhőszolgáltatáson belüli. Ez a neve egy példányszintű nyilvános IP-cím ([ILPIP](virtual-networks-instance-level-public-ip.md)). Csak a nyilvános IP-cím lehet dinamikus.

> [!NOTE]
> Ez különbözik a virtuális IP-CÍMEK a felhőalapú szolgáltatás, amely IaaS virtuális gépek vagy PaaS szerepkörpéldányt, mivel a felhőalapú szolgáltatásként is tartalmazhat több IaaS virtuális gépekhez, vagy PaaS szerepkörpéldányt, mind a felhőalapú szolgáltatás ugyanazon VIP keresztül közzétett tárolója.
> 
> 

### <a name="vpn-gateways"></a>VPN-átjárók
A [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md) más Azure Vnetekhez vagy helyszíni hálózatokhoz csatlakozni egy Azure virtuális hálózat is használható. VPN-átjáró nyilvános IP-cím van hozzárendelve *dinamikusan*, amely lehetővé teszi a kommunikációt a távoli hálózattal.

### <a name="application-gateways"></a>Alkalmazásátjárók
Azure-beli [az Application gateway](../application-gateway/application-gateway-introduction.md) Layer7 terheléselosztást, a HTTP-alapú hálózati forgalom továbbítása is használható. Az Application gateway nyilvános IP-cím van hozzárendelve *dinamikusan*, amely az elosztott terhelésű virtuális IP-CÍMEK szolgál.

### <a name="at-a-glance"></a>Áttekintés
Az alábbi táblázat a lehetséges lefoglalási módszereket (dinamikus/statikus), és lehetővé teszi több nyilvános IP-címek kiosztása az egyes erőforrástípusok.

| Erőforrás | Dinamikus | Statikus | Több IP-cím |
| --- | --- | --- | --- |
| Felhőszolgáltatás |Igen |Igen |Igen |
| IaaS virtuális gép vagy PaaS szerepkörpéldányt |Igen |Nem |Nem |
| VPN-átjáró |Igen |Nem |Nem |
| Alkalmazásátjáró |Igen |Nem |Nem |

## <a name="private-ip-addresses"></a>Magánhálózati IP-címek
Magánhálózati IP-címek lehetővé teszik az Azure-erőforrások más felhőszolgáltatásban lévő erőforrásokkal kommunikálni, vagy egy [virtuális hálózat](virtual-networks-overview.md)(VNet), vagy a helyi hálózathoz (keresztül VPN-átjárót vagy ExpressRoute-kapcsolatcsoport), használata nélkül egy Internetről elérhető IP-cím.

Az Azure klasszikus üzemi modellben a következő Azure-erőforrások rendelhetők magánhálózati IP-cím:

* IaaS virtuális gépeknél és PaaS szerepkörpéldányok
* Belső terheléselosztó
* Alkalmazásátjáró

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS virtuális gépeknél és PaaS szerepkörpéldányok
A klasszikus üzemi modellel létrehozott virtuális gépek (VM) egy felhőszolgáltatás, hasonló PaaS szerepkörpéldányok mindig kerülnek. A magánhálózati IP-címek viselkedését így hasonlóak ezekhez az erőforrásokhoz.

Fontos megjegyezni, hogy egy felhőalapú szolgáltatás két módon telepíthető:

* Mint egy *önálló* felhőalapú szolgáltatás, amennyiben nincs virtuális hálózaton belül.
* Virtuális hálózat része.

#### <a name="allocation-method"></a>Lefoglalási módszer
Az egy *önálló* felhőalapú szolgáltatás, magánhálózati IP-cím lefoglalt erőforrások get *dinamikusan* az Azure-adatközpont magánhálózati IP-címtartományt. Csak a többi virtuális gép ugyanazon a felhőszolgáltatáson belüli kommunikációhoz használható. Az IP-címet módosíthatja, amikor az erőforrás leállítása és elindítása.

Esetén egy virtuális hálózaton belüli üzembe helyezett felhőalapú szolgáltatás az erőforrások beolvasása magánhálózati IP-cím (a megadott hálózati konfigurációjában) a társított alhálózat címtartományából lefoglalva. A magánhálózati IP-cím a virtuális hálózaton belül minden virtuális gép közötti kommunikációhoz használható.

Ezenkívül esetén a felhőszolgáltatások, virtuális hálózaton belül, egy privát IP-címek lefoglalhatók *dinamikusan* (a DHCP használatával) alapértelmezés szerint. Ezt módosíthatja, amennyiben az erőforrás leállítása és elindítása. Annak érdekében, hogy az IP-cím ugyanaz marad, a kiosztási módszert állítja be kell *statikus*, és adjon meg egy érvényes IP-címet a megfelelő címtartományán belül.

Statikus magánhálózati IP-címeket általában a következő esetekben szoktak használni:

* Tartományvezérlőként vagy DNS-kiszolgálóként működő virtuális gépek esetén.
* Virtuális gépek IP-címeket használó tűzfalszabályokat igénylő.
* A virtuális gépek IP-címen keresztül más alkalmazások által elért szolgáltatások vannak telepítve.

#### <a name="internal-dns-hostname-resolution"></a>Belső DNS-állomásnév feloldása
Minden Azure virtuális gépek és PaaS szerepkörpéldányok állítottak [Azure által felügyelt DNS-kiszolgálók](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) alapértelmezés szerint ha nem konfigurál kifejezetten egyéni DNS-kiszolgálókat. Ezen DNS-kiszolgálók belső névfeloldást biztosítanak a virtuális gépek és szerepkörpéldányok található ugyanazt a virtuális hálózat vagy a felhőalapú szolgáltatást.

Amikor létrehoz egy virtuális gépet, az állomásnév a magánhálózati IP-címére való leképezése fel lesz véve az Azure által felügyelt DNS-kiszolgálókra. Egy több hálózati adapterrel rendelkező virtuális gép esetén az állomásnév a magánhálózati IP-címe az elsődleges hálózati lesz leképezve A megfeleltetési adatokat. azonban korlátozódik ugyanazon a felhőszolgáltatáson vagy virtuális hálózaton belüli erőforrásokhoz.

Az egy *önálló* felhőalapú szolgáltatás, az összes virtuális gépeket/szerepkörpéldányokat ugyanazon a felhőszolgáltatáson belül csak állomásnevét feloldani lesz. Felhőszolgáltatás virtuális hálózaton belüli esetén lesz minden a virtuális gépeket/szerepkörpéldányokat a virtuális hálózaton belül állomásnevét feloldani.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Belső terheléselosztók (ILB) és alkalmazásátjárók
Hozzárendelhet egy magánhálózati IP-címet az [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) terheléselosztók vagy [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) alkalmazásátjárók **előtér**-konfigurációjához. Ez a magánhálózati IP-cím belső végpontként szolgál, amelyet csak a virtuális hálózatán (VNet) és a VNethez csatlakozó távoli hálózatokon lévő erőforrások érnek el. Hozzárendelhet egy dinamikus vagy egy statikus méretű magánhálózati IP-címet az előtér-konfigurációhoz. Több virtuális IP-forgatókönyvek engedélyezése több magánhálózati IP-címek is hozzárendelhetők.

### <a name="at-a-glance"></a>Áttekintés
Az alábbi táblázat a lehetséges lefoglalási módszereket (dinamikus/statikus), és lehetővé teszi több magánhálózati IP-címek hozzárendelése az egyes erőforrástípusok.

| Erőforrás | Dinamikus | Statikus | Több IP-cím |
| --- | --- | --- | --- |
| Virtuális gép (az egy *önálló* felhőalapú szolgáltatás, vagy a virtuális hálózat) |Igen |Igen |Igen |
| PaaS szerepkörpéldányt (az egy *önálló* felhőalapú szolgáltatás, vagy a virtuális hálózat) |Igen |Nem |Nem |
| Belső terheléselosztó előtérrendszerhez |Igen |Igen |Igen |
| Application gateway-kezelőfelület |Igen |Igen |Igen |

## <a name="limits"></a>Korlátok
Az alábbi táblázat IP-korlátozások az Azure-előfizetésenként címzés. [Lépjen kapcsolatba a támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), ha növelni szeretné az alapértelmezett korlátot üzleti igényei szerint – akár a maximális korlátig.

|  | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek (dinamikus) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Fenntartott nyilvános IP-címek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános virtuális IP-cím (felhőszolgáltatás) üzemelő példányonként |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Privát virtuális IP-CÍMEK (ilb) Terheléselosztók (felhőszolgáltatás) üzemelő példányonként |1 |1 |

Olvassa el a teljes körű [hálózati korlátozásainak](../azure-subscription-service-limits.md#networking-limits) az Azure-ban.

## <a name="pricing"></a>Díjszabás
A legtöbb esetben a nyilvános IP-címek használata ingyenes. Egy névleges díj további és/vagy statikus nyilvános IP-címek használatára van. Győződjön meg arról, hogy megértette a [struktúra a nyilvános IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Resource Manager és klasszikus üzembe helyezés közötti különbségek
Alább az IP-címzési szolgáltatásainak a Resource Manager és a klasszikus üzemi modell összehasonlítása.

|  | Erőforrás | Klasszikus | Resource Manager |
| --- | --- | --- | --- |
| **Nyilvános IP-cím** |***VM*** |Egy ILPIP (csak dinamikus) néven |Egy nyilvános IP-Címek (dinamikus vagy statikus) néven |
|  ||Egy IaaS-beli virtuális Gépen vagy egy PaaS szerepkörpéldányok hozzárendelve |A virtuális gép hálózati adapterhez hozzárendelt | |
|  |***Az internetre irányuló terheléselosztó*** |Virtuális IP-CÍMEK (dinamikus) vagy fenntartott IP-Címek (statikus) |Egy nyilvános IP-Címek (dinamikus vagy statikus) néven | |
|  ||Cloud service rendelve |A load balancer előtér-konfiguráció hozzárendelve | |
|  | | | |
| **Magánhálózati IP-cím** |***VM*** |Egy DIP néven |Magánhálózati IP-cím néven |
|  ||Egy IaaS-beli virtuális Gépen vagy egy PaaS szerepkörpéldányok hozzárendelve |A virtuális gép hálózati adapterhez hozzárendelt | |
|  |***Belső load balancer (ILB)*** |Az ILB (dinamikus vagy statikus) rendelve |Az ILB előtér-konfiguráció (dinamikus vagy statikus) rendelve | |

## <a name="next-steps"></a>További lépések
* [Statikus magánhálózati IP-címmel rendelkező virtuális gép üzembe helyezése](virtual-networks-static-private-ip-classic-pportal.md) az Azure portal használatával.

