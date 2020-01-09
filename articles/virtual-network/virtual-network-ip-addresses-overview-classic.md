---
title: IP-címek típusai az Azure-ban (klasszikus)
titlesuffix: Azure Virtual Network
description: Ismerje meg a nyilvános és magánhálózati IP-címeket (klasszikus) az Azure-ban.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 7b197e1acf696c2ae6e919ee2eddacfb82ac3802
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646778"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-címek típusai és foglalási módszerei (klasszikus) az Azure-ban
IP-címeket rendelhet az Azure-erőforrásokhoz a többi Azure-erőforrással, a helyszíni hálózattal és az internettel való kommunikáció céljából. Az Azure-ban kétféle IP-cím használható: nyilvános és privát.

A nyilvános IP-címek az internettel való kommunikációhoz használatosak, beleértve az Azure nyilvános szolgáltatásait is.

A magánhálózati IP-címek egy Azure-beli virtuális hálózaton (VNet), egy felhőalapú szolgáltatáson és a helyszíni hálózaton belüli kommunikációhoz használatosak, ha VPN-átjárót vagy ExpressRoute-áramkört használ a hálózat kibővítéséhez az Azure-ban.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések a Resource Managert használják. A Resource Manager IP-címeinek megismeréséhez olvassa el az [IP-címekkel](virtual-network-ip-addresses-overview-arm.md) kapcsolatos cikket.

## <a name="public-ip-addresses"></a>Nyilvános IP-címek
A nyilvános IP-címek lehetővé teszik az Azure-erőforrások számára az internettel és az Azure-beli nyilvános szolgáltatásokkal való kommunikációt, például az [Azure cache-t a Redis, az](https://azure.microsoft.com/services/cache/) [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), az [SQL Database](../sql-database/sql-database-technical-overview.md)és az [Azure Storage](../storage/common/storage-introduction.md)

Egy nyilvános IP-cím a következő erőforrástípusok esetében van társítva:

* Felhőszolgáltatások
* IaaS Virtual Machines (VM)
* Pásti szerepkör-példányok
* VPN Gateway átjárók
* Alkalmazásátjárók

### <a name="allocation-method"></a>Lefoglalási módszer
Ha egy nyilvános IP-címet hozzá kell rendelni egy Azure-erőforráshoz, a rendszer *dinamikusan* kiosztja az elérhető nyilvános IP-címek készletét az erőforrás létrehozási helyén belül. Ez az IP-cím akkor jelenik meg, ha az erőforrás leáll. A Cloud Service esetében ez akkor történik meg, ha minden szerepkör-példány leáll, ami elkerülhető egy *statikus* (fenntartott) IP-cím használatával (lásd: [Cloud Services](#cloud-services)).

> [!NOTE]
> A nyilvános IP-címeket az Azure-erőforrások számára kiosztott IP-tartományok listája az [Azure-adatközpont IP-tartományokban](https://www.microsoft.com/download/details.aspx?id=41653)jelenik meg.
> 
> 

### <a name="dns-hostname-resolution"></a>DNS-állomásnév feloldása
Ha felhőalapú szolgáltatást vagy IaaS virtuális gépet hoz létre, meg kell adnia egy felhőalapú szolgáltatás DNS-nevét, amely az Azure összes erőforrásában egyedi. Ez létrehoz egy leképezést a *dnsname*. cloudapp.net Azure által FELÜGYELt DNS-kiszolgálókon az erőforrás nyilvános IP-címére. Ha például a **contoso**FELHŐBELI DNS-nevével hoz létre felhőalapú szolgáltatást, a teljes tartománynév (FQDN) **contoso.cloudapp.net** a Cloud Service nyilvános IP-címére (VIP) fogja feloldani. Ezt a teljes tartománynevet használhatja az Azure-ban lévő nyilvános IP-címre mutató egyéni tartományi CNAME-rekord létrehozásához.

### <a name="cloud-services"></a>Felhőszolgáltatások
A felhőalapú szolgáltatásoknak mindig van egy nyilvános IP-címe, amelyet virtuális IP-címnek (VIP) nevezzük. A felhőalapú szolgáltatásban végpontokat hozhat létre, hogy a virtuális gépek és a szerepkör-példányok belső portjaihoz a felhőalapú szolgáltatáson belül rendeljen különböző portokat a VIP-ben. 

A felhőalapú szolgáltatás több IaaS virtuális gépet, vagy Péter szerepkör-példányt is tartalmazhat, amelyek mindegyike azonos felhőalapú szolgáltatásbeli VIP-en keresztül érhető el. Több virtuális IP-címet is hozzárendelhet [egy felhőalapú szolgáltatáshoz](../load-balancer/load-balancer-multivip.md), amely több virtuális IP-címet, például több-bérlős környezetet tesz lehetővé SSL-alapú webhelyekkel.

Gondoskodhat arról, hogy a felhőalapú szolgáltatás nyilvános IP-címe azonos maradjon, még akkor is, ha az összes szerepkör-példány le van állítva egy *statikus* nyilvános IP-cím használatával, amelyet [fenntartott IPnak](virtual-networks-reserved-public-ip.md)nevezünk. Létrehozhat egy statikus (fenntartott) IP-erőforrást egy adott helyen, és hozzárendelheti az adott helyen található bármely felhőalapú szolgáltatáshoz. A fenntartott IP-címhez tartozó tényleges IP-címet nem adhatja meg, mert a rendszer kiosztja az elérhető IP-címek készletéből a létrehozott helyen. Ez az IP-cím csak akkor jelenik meg, ha explicit módon törli.

A statikus (fenntartott) nyilvános IP-címeket gyakran használják olyan helyzetekben, ahol a felhőalapú szolgáltatás:

* a tűzfalszabályok a végfelhasználók általi beállítását igénylik.
* a külső DNS-névfeloldástól függ, a dinamikus IP-címek pedig egy rekord frissítését igénylik.
* az IP-alapú biztonsági modellt használó külső webszolgáltatásokat használ.
* egy IP-címhez csatolt SSL-tanúsítványokat használ.

> [!NOTE]
> Klasszikus virtuális gép létrehozásakor az Azure létrehozza a Container *Cloud Service* -t, amely virtuális IP-címmel (VIP) rendelkezik. Ha a létrehozás a portálon keresztül történik, az alapértelmezett RDP-vagy SSH- *végpontot* a portál konfigurálja, így a virtuális géphez a Cloud Service VIP használatával kapcsolódhat. Ez a felhőalapú szolgáltatás virtuális IP-címe fenntartva, ami gyakorlatilag fenntartott IP-címet biztosít a virtuális géphez való kapcsolódáshoz. További portok megnyitásához további végpontokat konfigurálhat.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS virtuális gépek és a Péter szerepkör példányai
Egy nyilvános IP-címet közvetlenül rendelhet egy IaaS [virtuális géphez](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy a Pásti szerepkör-példányhoz egy felhőalapú szolgáltatáson belül. Ezt egy példány szintű nyilvános IP-címnek ([ILPIP](virtual-networks-instance-level-public-ip.md)) nevezzük. Ez a nyilvános IP-cím csak dinamikus lehet.

> [!NOTE]
> Ez különbözik a Cloud Service VIP-től, amely a IaaS virtuális gépek vagy a Péter szerepkör példányainak tárolója, mivel a felhőalapú szolgáltatás több IaaS virtuális gépet vagy Péter szerepkör-példányt is tartalmazhat, amelyek mindegyike ugyanazon a Cloud Service VIP-en keresztül érhető el.
> 
> 

### <a name="vpn-gateways"></a>VPN Gateway átjárók
A [VPN-átjárók](../vpn-gateway/vpn-gateway-about-vpngateways.md) az Azure-VNet más Azure-virtuális hálózatok vagy helyszíni hálózatokhoz való összekapcsolására is használhatók. A VPN-átjárók *dinamikusan*vannak hozzárendelve egy nyilvános IP-címhez, ami lehetővé teszi a kommunikációt a távoli hálózattal.

### <a name="application-gateways"></a>Alkalmazásátjárók
Az Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) használható a Layer7 terheléselosztásához a hálózati forgalom HTTP-alapú útválasztásához. Az Application Gateway *dinamikusan*van hozzárendelve egy nyilvános IP-címhez, amely elosztott terhelésű VIP-ként szolgál.

### <a name="at-a-glance"></a>Áttekintés
Az alábbi táblázat a lehetséges kiosztási módszereket (dinamikus/statikus) és a több nyilvános IP-cím hozzárendelésének lehetőségét mutatja be.

| Erőforrás | Dinamikus | Statikus | Több IP-cím |
| --- | --- | --- | --- |
| Felhőszolgáltatás |Igen |Igen |Igen |
| IaaS VM vagy Péter szerepkör-példány |Igen |Nem |Nem |
| VPN-átjáró |Igen |Nem |Nem |
| Alkalmazásátjáró |Igen |Nem |Nem |

## <a name="private-ip-addresses"></a>Magánhálózati IP-címek
A magánhálózati IP-címek lehetővé teszik az Azure-erőforrások számára, hogy a felhőalapú szolgáltatásban vagy [virtuális hálózaton](virtual-networks-overview.md)(VNet), illetve a helyszíni hálózaton (VPN-átjárón vagy ExpressRoute-áramkören keresztül) keresztül kommunikáljanak az internetről elérhető IP-címek használata nélkül.

A klasszikus Azure üzembe helyezési modellben a magánhálózati IP-cím a következő Azure-erőforrásokhoz rendelhető hozzá:

* IaaS virtuális gépek és a Péter szerepkör példányai
* Belső terheléselosztó
* Alkalmazásátjáró

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS virtuális gépek és a Péter szerepkör példányai
A klasszikus üzemi modellel létrehozott virtuális gépek (VM-EK) mindig felhőalapú szolgáltatásba kerülnek, hasonlóan a Pásti szerepkör példányaihoz. A magánhálózati IP-címek viselkedése így hasonló ehhez az erőforrásokhoz.

Fontos megjegyezni, hogy a Cloud Service kétféleképpen helyezhető üzembe:

* *Önálló* felhőalapú szolgáltatásként, ahol nem a virtuális hálózaton belül van.
* Egy virtuális hálózat részeként.

#### <a name="allocation-method"></a>Lefoglalási módszer
*Önálló* felhőalapú szolgáltatás esetén az erőforrások egy magánhálózati IP-címet kapnak, amely *dinamikusan* van kiosztva az Azure Datacenter magánhálózati IP-címtartomány alapján. Csak az azonos felhőalapú szolgáltatáson belüli más virtuális gépekkel folytatott kommunikációhoz használható. Ez az IP-cím változhat az erőforrás leállítása és elindítása után.

Egy virtuális hálózaton üzembe helyezett felhőalapú szolgáltatás esetében az erőforrások a társított alhálózat (ek) címtartományból lefoglalt magánhálózati IP-címet (ka) kapnak (a hálózati konfigurációban megadott módon). Ez a magánhálózati IP-cím (ek) használható a VNet belüli összes virtuális gép közötti kommunikációhoz.

Emellett a VNet belüli felhőalapú szolgáltatások esetében a magánhálózati IP-címek alapértelmezés szerint *dinamikusan* (DHCP használatával) vannak lefoglalva. Az erőforrás leállítása és elindítása után változhat. Annak biztosítása érdekében, hogy az IP-cím változatlan maradjon, a kiosztási módszert állítsa *statikusra*, és adjon meg egy érvényes IP-címet a megfelelő címtartomány között.

Statikus magánhálózati IP-címeket általában a következő esetekben szoktak használni:

* Tartományvezérlőként vagy DNS-kiszolgálóként működő virtuális gépek esetén.
* Azok a virtuális gépek, amelyek IP-címek használatával tűzfalszabályok szükségesek.
* A más alkalmazások által az IP-címen keresztül elért szolgáltatásokat futtató virtuális gépek.

#### <a name="internal-dns-hostname-resolution"></a>Belső DNS-állomásnév feloldása
Alapértelmezés szerint az összes Azure-beli virtuális gép és a Péter szerepkör-példány az [Azure által felügyelt DNS-kiszolgálókkal](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) van konfigurálva, hacsak nem konfigurálja explicit módon az egyéni DNS-kiszolgálókat. Ezek a DNS-kiszolgálók belső névfeloldást biztosítanak a virtuális gépek és a szerepkör-példányok számára, amelyek ugyanabban a VNet vagy felhőalapú szolgáltatásban találhatók.

Amikor létrehoz egy virtuális gépet, az állomásnév a magánhálózati IP-címére való leképezése fel lesz véve az Azure által felügyelt DNS-kiszolgálókra. A több hálózati adapterrel rendelkező virtuális gépek esetében az állomásnév az elsődleges hálózati adapter magánhálózati IP-címére van leképezve. Ez a leképezési információ azonban az azonos felhőalapú szolgáltatásban vagy VNet lévő erőforrásokra korlátozódik.

*Önálló* felhőalapú szolgáltatás esetén csak a felhőalapú szolgáltatásban lévő összes virtuális gép/szerepkör-példány állomásneve oldható fel. Ha egy VNet belül felhőalapú szolgáltatást használ, a VNet lévő összes virtuális gép/szerepkör-példány állomásneve feloldható lesz.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Belső terheléselosztók (ILB) és alkalmazásátjárók
Hozzárendelhet egy magánhálózati IP-címet az [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) terheléselosztók vagy [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) alkalmazásátjárók **előtér**-konfigurációjához. Ez a magánhálózati IP-cím belső végpontként szolgál, amelyet csak a virtuális hálózatán (VNet) és a VNethez csatlakozó távoli hálózatokon lévő erőforrások érnek el. Hozzárendelhet egy dinamikus vagy egy statikus méretű magánhálózati IP-címet az előtér-konfigurációhoz. Több magánhálózati IP-címet is hozzárendelhet a több VIP-forgatókönyvek engedélyezéséhez.

### <a name="at-a-glance"></a>Áttekintés
Az alábbi táblázat a lehetséges kiosztási módszereket (dinamikus/statikus) és több magánhálózati IP-cím hozzárendelésének lehetőségét mutatja be.

| Erőforrás | Dinamikus | Statikus | Több IP-cím |
| --- | --- | --- | --- |
| Virtuális gép ( *önálló* felhőalapú szolgáltatásban vagy VNet) |Igen |Igen |Igen |
| Péter szerepkör-példány ( *önálló* felhőalapú szolgáltatásban vagy VNet) |Igen |Nem |Nem |
| Belső terheléselosztó kezelőfelülete |Igen |Igen |Igen |
| Application Gateway – előtér |Igen |Igen |Igen |

## <a name="limits"></a>Korlátozások
Az alábbi táblázat az Azure-beli IP-címzésre vonatkozó korlátokat mutatja be az előfizetésben. [Lépjen kapcsolatba a támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), ha növelni szeretné az alapértelmezett korlátot üzleti igényei szerint – akár a maximális korlátig.

|  | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Nyilvános IP-címek (dinamikus) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Fenntartott nyilvános IP-címek |20 |kapcsolatfelvétel az ügyfélszolgálattal |
| Nyilvános VIP/üzembe helyezés (Cloud Service) |5 |kapcsolatfelvétel az ügyfélszolgálattal |
| Privát VIP (ILB) üzembe helyezés (Cloud Service) |1 |1 |

Győződjön meg arról, hogy elolvasta a [hálózatkezelési korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) teljes készletét az Azure-ban.

## <a name="pricing"></a>Díjszabás
A legtöbb esetben a nyilvános IP-címek ingyenesek. A további és/vagy statikus nyilvános IP-címek használatához névleges díj vonatkozik. Győződjön meg arról, hogy megértette a [nyilvános IP-címek díjszabási struktúráját](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>A Resource Manager és a klasszikus üzemelő példányok közötti különbségek
Az alábbiakban a Resource Manager és a klasszikus üzembe helyezési modell IP-címzési funkcióinak összehasonlítása látható.

|  | Erőforrás | Hagyományos | Erőforrás-kezelő |
| --- | --- | --- | --- |
| **Nyilvános IP-cím** |***Virtuális gép*** |Más néven ILPIP (csak dinamikus) |Nyilvános IP-cím (dinamikus vagy statikus) |
|  ||Hozzárendelve egy IaaS virtuális géphez vagy egy Pásti szerepkör-példányhoz |A virtuális gép hálózati adapteréhez társítva |
|  |***Internetkapcsolattal rendelkező Load Balancer*** |Más néven VIP (dinamikus) vagy Fenntartott IP (statikus) |Nyilvános IP-cím (dinamikus vagy statikus) |
|  ||Felhőalapú szolgáltatáshoz rendelve |A terheléselosztó előtér-konfigurációjához van társítva |
|  | | | |
| **Magánhálózati IP-cím** |***Virtuális gép*** |DIP-ként emlegetik |Privát IP-címnek nevezzük |
|  ||Hozzárendelve egy IaaS virtuális géphez vagy egy Pásti szerepkör-példányhoz |A virtuális gép hálózati adapteréhez rendelve |
|  |***Belső terheléselosztó (ILB)*** |Hozzárendelve a ILB (dinamikus vagy statikus) |Hozzárendelve a ILB előtér-konfigurációjához (dinamikus vagy statikus) |

## <a name="next-steps"></a>Következő lépések
* [Helyezzen üzembe egy statikus magánhálózati IP-címmel rendelkező virtuális gépet](virtual-networks-static-private-ip-classic-pportal.md) a Azure Portal használatával.

