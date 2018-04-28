---
title: Virtuális hálózat konfigurálása a Premium Azure Redis gyorsítótár |} Microsoft Docs
description: Megtudhatja, hogyan hozhatja létre és kezelheti a Premium szint Azure Redis Cache példány virtuális hálózati támogatása
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: wesmc
ms.openlocfilehash: 250c66c3a39519a6eddc1ecb51259ec1944c88a9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Virtuális hálózati támogatásának konfigurálása prémium szintű Azure Redis Cache
Azure Redis Cache rendelkezik másik gyorsítótármappa ajánlatokat, amelyek gyorsítótár mérete és a funkciót, beleértve a prémium réteg szolgáltatások, például a fürtszolgáltatás, az adatmegőrzésre és a virtuális hálózat támogatásának rugalmasságot biztosítanak. A virtuális hálózat egy magánhálózaton a felhőben. Azure Redis Cache példány konfigurálásakor a virtuális hálózaton nincs nyilvánosan megcímezhető, és csak érhetők el a virtuális gépek és az alkalmazások a Vneten belül. Ez a cikk ismerteti a premium Azure Redis Cache példányt virtuális hálózat támogatásának konfigurálása.

> [!NOTE]
> Azure Redis Cache támogatja mindkét klasszikus és Resource Manager Vnetek.
> 
> 

Más prémium gyorsítótár funkciókról további információért lásd: [az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Miért virtuális hálózatot?
[Az Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) telepítés biztosítja, magasabb védelmet és elszigeteltséget az Azure Redis Cache, valamint a alhálózatok, hozzáférés-vezérlési házirendeket, és további egyéb szolgáltatások elérésének korlátozása.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása
Virtual Network (VNet) támogatása konfigurálva van a **új Redis Cache** panel gyorsítótár létrehozása során. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Miután kiválasztotta a prémium tarifacsomag, beállíthatja a Redis virtuális integráció egy Vnetet, amely ugyanazt az előfizetést és a gyorsítótár helyének kiválasztásával. Egy új virtuális hálózat használatához először hozza létre azt a lépések [hozzon létre egy virtuális hálózatot az Azure portál használatával](../virtual-network/manage-virtual-network.md#create-a-virtual-network) vagy [(klasszikus) virtuális hálózat létrehozása az Azure portál használatával](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) és térjen vissza a **Új Redis Cache** létrehozása és beállítása a prémium szintű gyorsítótára panel.

Az új gyorsítótár hálózatok konfigurálásához kattintson **virtuális hálózati** a a **új Redis Cache** panelt, és válassza ki a kívánt VNet a legördülő listából.

![Virtuális hálózat][redis-cache-vnet]

Jelölje ki a kívánt alhálózatot a **alhálózati** legördülő listában, és adja meg a kívánt **statikus IP-cím**. Ha a klasszikus virtuális hálózatot használ a **statikus IP-cím** mező kitöltése nem kötelező, és ha nincs megadva, egy kiválasztotta a kiválasztott alhálózatnak.

> [!IMPORTANT]
> Az Azure Redis Cache egy erőforrás-kezelő virtuális hálózaton történő telepítéskor a gyorsítótár az Azure Redis Cache példány kivételével nincs más erőforrásokat tartalmazó dedikált alhálózat kell lennie. Ha központi telepítése egy Azure Redis Cache egy erőforrás-kezelő virtuális hálózat alhálózathoz tett kísérlet, amely más erőforrások, a központi telepítés sikertelen lesz.
> 
> 

![Virtuális hálózat][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure fenntartja az egyes IP-címek minden alhálózaton belül, és ezeknél a címeknél nem használható. Az első és utolsó IP-címek alhálózatok protokoll megfelelési, valamint három további címek az Azure-szolgáltatásokhoz használt számára vannak fenntartva. További információkért lásd: [vannak-e bármilyen korlátozás belül ezek alhálózatok IP-címeket használnak?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Mellett az IP-címeit az Azure virtuális hálózat infrastruktúra minden egyes Redis példány / shard alhálózati használja a két IP-címek és egy további IP-címet a terheléselosztóhoz. Egy nem fürtözött gyorsítótár egy shard tekinthető.
> 
> 

A gyorsítótár létrehozása után meg kattintva megtekintheti a VNet konfigurációját **virtuális hálózati** a a **erőforrás menü**.

![Virtuális hálózat][redis-cache-vnet-info]

Csatlakozni az Azure Redis cache példány egy virtuális hálózat használatával, adja meg a gyorsítótár állomás nevét a kapcsolati karakterláncban a következő példában látható módon:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet – gyakori kérdések
A következő lista az Azure Redis Cache skálázás gyakran feltett kérdésekre adott válaszokat tartalmazza.

* [Mik azok a gyakori hiba a konfiguráció problémákat az Azure Redis Cache és Vnetekhez?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Hogyan ellenőrizhetem, hogy működik-e a gyorsítótár a VNETEN belül?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Kapcsolódás egy VNETET az a Redis gyorsítótár, miért jelenik meg egy hiba, amely meghatározza, hogy a távoli tanúsítvány nem érvényes?](#when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid)
* [Használható alapszintű vagy standard gyorsítótárával Vnetekhez?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Miért nem a Redis gyorsítótár létrehozása sikertelen lesz az egyes alhálózatok, de nem mások?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Mik azok az alhálózati kapcsolatos követelmények?](#what-are-the-subnet-address-space-requirements)
* [Működnek-e minden gyorsítótár-funkciók egy VNETET a gyorsítótárhoz esetén?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Mik azok a gyakori hiba a konfiguráció problémákat az Azure Redis Cache és Vnetekhez?
Azure Redis Cache a Vneten belül helyezkedik el, amikor a rendszer az alábbi táblázatban a portokat használja. 

>[!IMPORTANT]
>Ha blokkolja az alábbi táblázatban a portokat, előfordulhat, hogy a gyorsítótár nem működik megfelelően. Rendelkezik egy vagy több ezeket a portokat, blokkolja a leggyakrabban használt helytelen konfiguráció probléma használata esetén egy Vnetet az Azure Redis Cache.
> 
> 

- [Kimenő portokra vonatkozó követelmények](#outbound-port-requirements)
- [Bejövő portokra vonatkozó követelmények](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Kimenő portokra vonatkozó követelmények

Nincsenek hét kimenő port.

- Ha a kívánt, az összes kimenő kapcsolatok az interneten keresztül módosítható egy ügyfél a helyszíni naplózási eszköz.
- Azure-végpontok Azure Storage és az Azure DNS-karbantartási forgalom átirányítása három port.
- A fennmaradó porttartományok és a belső Redis alhálózati kommunikációhoz. Nincs alhálózat NSG-szabályok a belső Redis alhálózati kommunikációhoz szükségesek.

| Port(s) | Irány | Átviteli protokoll | Cél | Helyi IP | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Kimenő |TCP |Redis függőségek az Azure Storage/nyilvános kulcsokra épülő infrastruktúra (Internet) | (Redis alhálózati) |* |
| 53 |Kimenő |TCP/UDP |A DNS-(Internet/VNet) függőségek redis | (Redis alhálózati) |* |
| 8443 |Kimenő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) | (Redis alhálózati) |
| 10221-10231 |Kimenő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) | (Redis alhálózati) |
| 20226 |Kimenő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati) |
| 13000-13999 |Kimenő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati) |
| 15000-15999 |Kimenő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati) |
| 6379-6380 |Kimenő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati) |


#### <a name="inbound-port-requirements"></a>Bejövő portokra vonatkozó követelmények

Nincsenek nyolc bejövő port tartományon. Bejövő kérelmek ezen tartományok, vagy más, ugyanazon virtuális üzemeltetett szolgáltatások bejövő vagy belső a Redis alhálózati kommunikáció.

| Port(s) | Irány | Átviteli protokoll | Cél | Helyi IP | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Bejövő |TCP |Ügyfél-kommunikációt kíván Redis, Azure terheléselosztás | (Redis alhálózati) | (Redis alhálózati), virtuális hálózat, az Azure Load Balancer |
| 8443 |Bejövő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati) |
| 8500 |Bejövő |TCP/UDP |Az Azure terheléselosztás | (Redis alhálózati) |Azure Load Balancer |
| 10221-10231 |Bejövő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati), Azure Load Balancer |
| 13000-13999 |Bejövő |TCP |Ügyfél-kommunikáció Redis-fürtök, Azure terheléselosztás | (Redis alhálózati) |Virtuális hálózat, az Azure terheléselosztó |
| 15000-15999 |Bejövő |TCP |Ügyfél-kommunikáció Redis-fürtök, Azure betöltése terheléselosztás | (Redis alhálózati) |Virtuális hálózat, az Azure terheléselosztó |
| 16001 |Bejövő |TCP/UDP |Az Azure terheléselosztás | (Redis alhálózati) |Azure Load Balancer |
| 20226 |Bejövő |TCP |Belső Redis-kommunikáció | (Redis alhálózati) |(Redis alhálózati) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>További virtuális hálózat hálózati kapcsolat követelmények

Nincsenek hálózati kapcsolat az Azure Redis Cache, előfordulhat, hogy kezdetben jutott virtuális hálózatban. Azure Redis Cache-gyorsítótár a következő elemek megfelelően működnek, ha a virtuális hálózaton belül van szükség.

* Azure Storage végpontok világszerte kimenő hálózati kapcsolatot. Ez magában foglalja a végpontok Azure Redis Cache példányt ugyanabban a régióban található, valamint tárolási végpontok található **más** Azure-régiók. Azure Storage-végpontok oldja meg a következő DNS-tartományok alatt: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*, és *file.core.windows.net*. 
* Kimenő hálózati kapcsolattal *ocsp.msocsp.com*, *mscrl.microsoft.com*, és *crl.microsoft.com*. Ez az összekapcsolhatóság SSL támogatásához van szükség.
* A DNS-beállításait a virtuális hálózat összes végpontok és a korábbi pontban tartományok megoldásának képesnek kell lennie. A DNS-követelmények érheti el, egy érvényes DNS-infrastruktúra van beállítva, és a virtuális hálózat megmarad biztosításával.
* Kimenő hálózati kapcsolata a következő Azure figyelési végpontok, amelyek alapján a következő DNS-tartományok megoldásához: shoebox2-black.shoebox2.metrics.nsatc.net, Észak-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, kelet-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hogyan ellenőrizhetem, hogy működik-e a gyorsítótár a VNETEN belül?

>[!IMPORTANT]
>Az Azure Redis Cache példány egy virtuális hálózat alkotóelem való csatlakozáskor a gyorsítótár-ügyfelek vagy kell lennie ugyanazon virtuális a VNETEN belül a VNETBEN társviszony-létesítés engedélyezve van. Ez magában foglalja a teszt alkalmazásokhoz és diagnosztikai pingelés eszközök. Függetlenül attól, ahol az ügyfélalkalmazás tárolása hálózati biztonsági csoportok kell konfigurálni úgy, hogy az ügyfél hálózati forgalom engedélyezve van a Redis-példány eléréséhez.
>
>

Az előző szakaszban leírtak a portokra vonatkozó követelmények konfigurálása után ellenőrizheti, hogy működik-e a gyorsítótár a következő lépések elvégzésével.

- [Újraindítás](cache-administration.md#reboot) minden gyorsítótár-csomópont. Ha az összes szükséges gyorsítótár-függőségeit nem érhető el (ahogy [portokra vonatkozó követelmények bejövő](cache-how-to-premium-vnet.md#inbound-port-requirements) és [kimenő portokra vonatkozó követelmények](cache-how-to-premium-vnet.md#outbound-port-requirements)), a gyorsítótár nem fog tudni újraindítása sikeresen megtörtént.
- Ha a gyorsítótár-csomópontok (mint a gyorsítótár állapotát, az Azure portálon által jelentett) újraindítása, a következő teszteket hajthat végre:
  - Pingelje meg a gyorsítótár végpont (port 6380) a gép, amely ugyanazt a virtuális Hálózatot, mint a gyorsítótár belül használatával [tcping](https://www.elifulkerson.com/projects/tcping.php). Példa:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Ha a `tcping` eszköz jelzi, hogy a port meg nyitva, a gyorsítótár érhető el a virtuális hálózaton lévő ügyfelek közötti kapcsolat.

  - Tesztelheti egy másik módja, ha a teszt gyorsítótárügyfél (amely lehet egy egyszerű konzolalkalmazást StackExchange.Redis használatával), amely a gyorsítótár csatlakozik, és hozzáadja, és néhány elemet lekéri a gyorsítótárból. A mintaalkalmazás ügyfél, amely ugyanazt a virtuális Hálózatot, mint a gyorsítótárban, és futtassa ellenőrzésére a gyorsítótár-kiszolgálóra telepítse.


### <a name="when-trying-to-connect-to-my-redis-cache-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Kapcsolódás egy VNETET az a Redis gyorsítótár, miért jelenik meg egy hiba, amely meghatározza, hogy a távoli tanúsítvány nem érvényes?

Kapcsolódás a VNETEN belül egy Redis gyorsítótárhoz, például a tanúsítvány érvényesítési hiba jelenik meg:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Az OK lehet az IP-címe a host való kapcsolódás esetén. Az állomásnév használatát javasoljuk. Ez azt jelenti, az alábbi parancsokat használja:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Kerülje az IP-cím a következő kapcsolati karakterlánc hasonlít:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Ha nem tudja feloldani a DNS-nevet, néhány ügyfél tárak konfigurációs beállítások, például a `sslHost` amely a StackExchange.Redis ügyfél biztosítja. Ez lehetővé teszi, hogy a tanúsítvány érvényesítéséhez használt állomásnév felülbírálása. Példa:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Használható alapszintű vagy standard gyorsítótárával Vnetekhez?
Vnetek csak prémium szintű gyorsítótárak használható.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Miért nem a Redis gyorsítótár létrehozása sikertelen lesz az egyes alhálózatok, de nem mások?
Ha telepíti az Azure Redis Cache egy erőforrás-kezelő virtuális hálózatba, a gyorsítótár nincs más erőforrástípus tartalmazó dedikált alhálózat kell lennie. Ha központi telepítése egy Azure Redis Cache erőforrás-kezelő virtuális hálózat alhálózathoz tett kísérlet, amely más erőforrások, a telepítés sikertelen lesz. Egy új Redis gyorsítótár létrehozása előtt törölnie kell a meglévő erőforrásokat az alhálózaton belül.

Telepítene többféle típusú erőforrások egy klasszikus virtuális hálózatot, amíg elegendő elérhető IP-cím van.

### <a name="what-are-the-subnet-address-space-requirements"></a>Mik azok az alhálózati kapcsolatos követelmények?
Azure fenntartja az egyes IP-címek minden alhálózaton belül, és ezeknél a címeknél nem használható. Az első és utolsó IP-címek alhálózatok protokoll megfelelési, valamint három további címek az Azure-szolgáltatásokhoz használt számára vannak fenntartva. További információkért lásd: [vannak-e bármilyen korlátozás belül ezek alhálózatok IP-címeket használnak?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Mellett az IP-címeit az Azure virtuális hálózat infrastruktúra minden egyes Redis példány / shard alhálózati használja a két IP-címek és egy további IP-címet a terheléselosztóhoz. Egy nem fürtözött gyorsítótár egy shard tekinthető.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Működnek-e minden gyorsítótár-funkciók egy VNETET a gyorsítótárhoz esetén?
Ha a gyorsítótár egy virtuális hálózat része, csak a virtuális hálózaton lévő ügyfelek is gyorsítótár-hozzáféréshez. Ennek eredményeképpen a következő gyorsítótár-felügyeleti funkciók jelenleg nem működnek.

* Konzol redis - konzol Redis fut a helyi böngészőben, amely a virtuális hálózaton kívül, mert nem tud kapcsolódni a gyorsítótárhoz.


## <a name="use-expressroute-with-azure-redis-cache"></a>Azure Redis gyorsítótár ExpressRoute használata

Az ügyfelek kapcsolódhatnak egy [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolat a virtuális hálózati infrastruktúrára, így kiterjesztése a helyszíni hálózat az Azure-bA. 

Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolatcsoport nem végzi el a kényszerített bújtatás (az alapértelmezett útvonal hirdetmény 0.0.0.0/0) egy virtuális hálózaton. Ennek eredményeképpen kimenő internetkapcsolat engedélyezett a VNET-ről és ügyfélalkalmazások csatlakozni más Azure-végpontok Azure Redis Cache beleértve.

Azonban egy közös felhasználói konfigurálása, hogy használja kényszerített bújtatás (az alapértelmezett útvonal hirdetése) amely arra kényszeríti a kimenő Internet forgalmat inkább a helyszíni. A forgalom áramlását a kimenő forgalom esetén az Azure Redis Cache kapcsolat megsérti, majd a helyszíni blokkolva, például úgy, hogy az Azure Redis Cache példány nem tud kommunikálni a függőségeit.

A megoldás, hogy egy (vagy több) felhasználó által definiált útvonalak (udr-EK) adja meg az alhálózatot, amely tartalmazza az Azure Redis Cache. Egy UDR helyett az alapértelmezett útvonal szembeni szerződéses kötelezettségeket vonatkozó alhálózati útvonalakat határozza meg.

Ha lehetséges javasoljuk, hogy az alábbi konfigurációt használja:

* Az ExpressRoute konfigurációs hirdeti 0.0.0.0/0, és alapértelmezés szerint kényszerített bújtatja minden kimenő forgalom helyszíni.
* Az alhálózatot, amely tartalmazza az Azure Redis Cache alkalmazott UDR a TCP/IP-forgalmat olyan működő útvonalat a 0.0.0.0/0 meghatározása a nyilvános internethez; például az beállítása a következő ugrás típusa "Internet".

A kombinált hatását, hogy ezeket a lépéseket az, hogy az alhálózat-szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatás, biztosítva ezzel kimenő internetkapcsolattal az Azure Redis Cache.

Azure Redis Cache példány csatlakozik egy helyszíni alkalmazásból ExpressRoute segítségével nincs tipikus használati eset miatt teljesítmény érdekében (a legjobb teljesítmény érdekében Azure Redis Cache-ügyfelek és az Azure Redis Cache ugyanabban a régióban kell lennie).

>[!IMPORTANT] 
>Egy UDR definiált útvonalak **kell** kellően specifikus elsőbbséget élveznek a bármely az ExpressRoute-konfiguráció által hirdetett útvonalakat. A következő példa a széles körű 0.0.0.0/0 címtartományt használja, és ilyen potenciálisan véletlenül felülbírálhatja útvonal-hirdetéseinek pontosabb címtartomány segítségével.

>[!WARNING]  
>Azure Redis Cache ExpressRoute-konfigurációk használata nem támogatott, amelyek **helytelenül kereszt-hirdetményt a magánhálózati társviszony-létesítési elérési utat a nyilvános társviszony-létesítési elérési útvonalak**. ExpressRoute konfigurációkat, amelyek rendelkeznek a nyilvános társviszony konfigurálva, a Microsoft Azure IP-címtartományok számos útvonal-hirdetéseinek kapni a Microsofttól. Ha a címtartomány helytelenül határokon meghirdetett a magánhálózati társviszony-létesítési útvonalon, az eredménye, hogy minden kimenő hálózati rendszer érkező csomagokat, az Azure Redis Cache példány alhálózati helytelenül kényszerített-tunneled az ügyfél a helyi hálózati infrastruktúra . A hálózati folyamata Azure Redis Cache megszakad. Ez a probléma megoldása, hogy állítsa le a kereszt-hirdetési útvonalak a nyilvános társviszony-létesítési elérési útról a magánhálózati társviszony-létesítési elérési utat.


Háttér-információkat a felhasználó által definiált útvonalak érhető el ezen [áttekintése](../virtual-network/virtual-networks-udr-overview.md).

ExpressRoute kapcsolatos további információkért lásd: [ExpressRoute műszaki áttekintés](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan további premium gyorsítótár-funkciók használatára.

* [Az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

