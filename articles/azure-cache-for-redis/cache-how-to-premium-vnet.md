---
title: Virtuális hálózat konfigurálása prémium szintű Azure redis Cache |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és a prémium szintű Azure Cache a Redis-példány támogatása virtuális hálózat kezelése
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: d4b8fd6ccb3fc7cb2627d4bd3e103239181e4d9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994389"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Virtual Network támogatása a prémium szintű Azure Cache Redis konfigurálása
Az Azure Cache redis rendelkezik másik Cache gyorsítótárazási szolgáltatások, ami rugalmasságot biztosít a gyorsítótár méretét és a szolgáltatásait, beleértve a Prémiumszintű funkciókkal, például a fürtözés, az adatmegőrzés és a virtuálishálózat-támogatást is nyújt. Virtuális hálózat egy magánhálózat a felhőben. Ha az Azure Cache Redis-példányt a virtuális hálózathoz van konfigurálva, még nem nyilvánosan címezhető és a virtuális gépek és az alkalmazások a virtuális hálózaton belül csak érhetők el. Ez a cikk ismerteti, hogyan kell egy prémium szintű Azure Cache Redis-példányt a virtuális hálózat támogatásának konfigurálása.

> [!NOTE]
> Az Azure Cache a redis támogatja mind a klasszikus és Resource Manager virtuális hálózatok.
> 
> 

Más prémiumszintű gyorsítótár funkcióival kapcsolatos tudnivalókért lásd: [Bevezetés az Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Miért érdemes virtuális hálózatok közötti?
[Az Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) telepítési biztosítja a fokozott biztonságot és elszigeteltséget az Azure Cache Redis, valamint alhálózatokat, hozzáférés-vezérlési szabályzatokkal és további más szolgáltatások elérésének korlátozása.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása
Virtuális hálózat (VNet) támogatás konfigurálva van a **új Azure Cache redis** panel gyorsítótár létrehozása során. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Prémium tarifacsomag kiválasztása után beállíthatja a Redis VNet-integráció, amely ugyanazt az előfizetést és helyet a gyorsítótár, a virtuális hálózat kiválasztásával. Egy új virtuális hálózat használatához hozzon létre, először a lépéseket követve [hozzon létre egy virtuális hálózatot az Azure portal használatával](../virtual-network/manage-virtual-network.md#create-a-virtual-network) vagy [(klasszikus) virtuális hálózat létrehozása az Azure portal használatával](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) majd térjen vissza a **Új Azure Cache redis** létrehozása és a prémium szintű gyorsítótár konfigurálása panelen.

A virtuális hálózat számára az új gyorsítótár konfigurálásához kattintson **virtuális hálózat** a a **új Azure Cache redis** panelre, és válassza ki a kívánt virtuális hálózat, a legördülő listából.

![Virtuális hálózat][redis-cache-vnet]

Válassza ki a kívánt alhálózatot a a **alhálózati** legördülő listában, és adja meg a kívánt **statikus IP-cím**. Ha egy klasszikus virtuális hálózatot használ a **statikus IP-cím** mező kitöltése nem kötelező, és ha nincs megadva, az egyik a kijelölt alhálózatból választja.

> [!IMPORTANT]
> Amikor üzembe helyezése az Azure Cache redis Resource Manager virtuális hálózathoz, a gyorsítótár, amely nem tartalmaz más erőforrásokat kivéve az Azure Cache a Redis-példány dedikált alhálózatán kell lennie. Ha kísérlet történik az Azure Cache Redis virtuális hálózathoz a Resource Manager üzembe helyezése egy alhálózathoz, amely más erőforrásokat tartalmaz, a központi telepítés sikertelen lesz.
> 
> 

![Virtuális hálózat][redis-cache-vnet-ip]

> [!IMPORTANT]
> Az Azure lefoglalja minden egyes alhálózaton belül néhány IP-cím, és ezek a címek nem használható. Az alhálózatok első és utolsó IP-címét a protokollok megfelelősége érdekében, az Azure-szolgáltatásokhoz használt három további címek számára vannak fenntartva. További információkért lásd: [vannak ezen alhálózatok belüli IP-címek használatával korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Mellett az Azure-beli virtuális infrastruktúra által használt IP-címeket minden egyes Redis példány, a partíciónkénti alhálózatot használ két IP-címek és a egy további IP-címet a terheléselosztóhoz. Egy nem fürtözött gyorsítótár egyik adatszilánkba író szerkezetűnek tekinthető.
> 
> 

A gyorsítótár létrehozása után, kattintva megtekintheti a VNet konfigurációját **virtuális hálózat** származó a **erőforrás menüben**.

![Virtuális hálózat][redis-cache-vnet-info]

Csatlakozhassanak az Azure Cache a Redis-példányt, ha a vnet-et használ, adja meg a gazdagép a gyorsítótár nevét, a kapcsolati karakterláncban a következő példában látható módon:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Az Azure Cache Redis virtuális hálózat – gyakori kérdések
Az alábbi lista az Azure Cache Redis méretezéshez kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* Mik az Azure Cache a Redis és a virtuális hálózatok Virtual Network szolgáltatás hibás gyakori problémákat?
* [Hogyan ellenőrizhetem, hogy működik-e a gyorsítótár egy virtuális hálózaton?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Ha szeretne csatlakozni az Azure gyorsítótár egy virtuális hálózaton, miért jelenik meg a távoli tanúsítvány nem érvényes érvénytelenségét jelző hibaüzenet Redis?
* [Használható a virtuális hálózatok a gyorsítótárat standard vagy alapszintűre?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Miért nem az Azure Cache redis nem lehet létrehozni az egyes alhálózatok, de nem más?
* [Mik azok az alhálózat címterekkel kapcsolatos követelmények?](#what-are-the-subnet-address-space-requirements)
* [A virtuális hálózat gyorsítótár üzemeltetése esetén működnek minden gyorsítótár-szolgáltatás?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Mik az Azure Cache a Redis és a virtuális hálózatok Virtual Network szolgáltatás hibás gyakori problémákat?
Azure Cache redis egy virtuális hálózaton jöhet szóba, ha a rendszer az alábbi táblázatban a portokat használja. 

>[!IMPORTANT]
>Az alábbi táblázatban a portok le vannak tiltva, ha lehetséges, hogy a gyorsítótár nem működik megfelelően. A Virtual Network szolgáltatás hibás leggyakoribb probléma egy vagy több letiltott portokon kellene akkor, ha az Azure Cache redis egy virtuális hálózat használatával.
> 
> 

- [Kimenő port követelményei](#outbound-port-requirements)
- [Bejövő port követelményei](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Kimenő port követelményei

Hét kimenő port követelmény vonatkozik.

- Az összes kimenő kapcsolatok az interneten keresztül egy ügyfél lehet tenni eszköz naplózás a helyszínen.
- A portok három irányítják a forgalmat a karbantartás az Azure Storage és az Azure DNS Azure-beli.
- A fennmaradó alkalmazásport-tartományok és a belső Redis alhálózati kommunikációhoz. Nincs alhálózat NSG-szabályok a Redis-alhálózat belső kommunikációs szükségesek.

| Port(s) | Irány | Átviteli protokoll | Cél | Helyi IP-cím | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Kimenő |TCP |Redis Cache függőségek az Azure Storage/nyilvános kulcsokra épülő infrastruktúra (Internet) | (Alhálózat redis) |* |
| 53 |Kimenő |TCP/UDP |A redis-függőségek a DNS (Internet/VNet) | (Alhálózat redis) |* |
| 8443 |Kimenő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) | (Alhálózat redis) |
| 10221-10231 |Kimenő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) | (Alhálózat redis) |
| 20226 |Kimenő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Alhálózat redis) |
| 13000-13999 |Kimenő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Alhálózat redis) |
| 15000-15999 |Kimenő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Alhálózat redis) |
| 6379-6380 |Kimenő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Alhálózat redis) |


#### <a name="inbound-port-requirements"></a>Bejövő port követelményei

Nincsenek nyolc bejövő port tartományon. Bejövő kérelmek ezen tartományok vagy más, ugyanazon a VNETEN lévő üzemeltetett szolgáltatások bejövő vagy a Redis-alhálózat kommunikáció belső.

| Port(s) | Irány | Átviteli protokoll | Cél | Helyi IP-cím | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Bejövő |TCP |Ügyfél-kommunikációhoz a Redishez, Azure terheléselosztási | (Alhálózat redis) | (Redis alhálózat), virtuális hálózat, az Azure Load Balancer |
| 8443 |Bejövő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Alhálózat redis) |
| 8500 |Bejövő |TCP/UDP |Az Azure load balancing | (Alhálózat redis) |Azure Load Balancer |
| 10221-10231 |Bejövő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Redis subnet), Azure Load Balancer |
| 13000-13999 |Bejövő |TCP |Redis-fürtök, az Azure terheléselosztási ügyfél-kommunikáció | (Alhálózat redis) |Virtual Network, Azure Load Balancer |
| 15000-15999 |Bejövő |TCP |Ügyfél-kommunikációhoz a Redis-fürtök, az Azure load terheléselosztás | (Alhálózat redis) |Virtual Network, Azure Load Balancer |
| 16001 |Bejövő |TCP/UDP |Az Azure load balancing | (Alhálózat redis) |Azure Load Balancer |
| 20226 |Bejövő |TCP |A Redis a belső kommunikáció | (Alhálózat redis) |(Alhálózat redis) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>További virtuális hálózatok közötti hálózati kapcsolati követelmények

Nincsenek hálózati kapcsolatot az Azure Cache a Redis, amelyeket lehet, hogy nem kezdetben teljesíteni a virtuális hálózatban. Az Azure Cache redis szükséges a következő elemeket, hogy megfelelően működnek, ha a virtuális hálózaton belül.

* Globális Azure Storage-végpontokra irányuló kimenő hálózati kapcsolat. Ez magában foglalja a végpontok és a Redis-példányt az Azure Cache ugyanabban a régióban található, valamint a tárolási végpontok található **más** Azure-régióban. Azure Storage-beli oldja meg a következő DNS-tartományok alatt: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*, és *file.core.windows.net*. 
* A kimenő hálózati kapcsolat *ocsp.msocsp.com*, *mscrl.microsoft.com*, és *crl.microsoft.com*. Ez a kapcsolat SSL funkciók támogatásához van szükség.
* A DNS-konfiguráció a virtuális hálózat összes végpontok és a korábbi abban tartományokat megoldásának képesnek kell lennie. Ezek a DNS-követelmények úgy, hogy egy érvényes DNS-infrastruktúra karbantartása a virtuális hálózat és konfigurált érheti el.
* Kimenő hálózati kapcsolat a következő Azure Monitoring-végpontokra irányuló, amely alatt a következő DNS-tartományok feloldása: shoebox2-black.shoebox2.metrics.nsatc.net, Észak-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, kelet-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hogyan ellenőrizhetem, hogy működik-e a gyorsítótár egy virtuális hálózaton?

>[!IMPORTANT]
>Egy virtuális hálózaton lévő üzemeltetett Redis-példány az Azure Cache való csatlakozáskor a gyorsítótárügyfélhez kell ugyanazon a virtuális hálózaton vagy virtuális hálózaton a virtuális hálózatok közötti társviszony-létesítés engedélyezve van. Ez magában foglalja a teszt alkalmazások és diagnosztikai pingelése eszközök. Függetlenül attól, ahol az ügyfélalkalmazásnak üzemel a hálózati biztonsági csoportok kell konfigurálni, hogy az ügyfél hálózati forgalom engedélyezve van a Redis-példány eléréséhez.
>
>

A port követelményei az előző szakaszban leírtak szerint lettek konfigurálva, miután ellenőrizheti, hogy működik-e a gyorsítótár a következő lépések végrehajtásával.

- [Újraindítás](cache-administration.md#reboot) összes gyorsítótár-csomópont. Ha összes függőséget szükséges gyorsítótár nem érhető el (leírtak szerint [portkövetelményekről bejövő](cache-how-to-premium-vnet.md#inbound-port-requirements) és [kimenő port követelményei](cache-how-to-premium-vnet.md#outbound-port-requirements)), a gyorsítótár nem tudják újraindítása sikeresen megtörtént.
- A gyorsítótár-csomópontok (mivel az Azure Portalon a gyorsítótár állapotát jelentette) újraindítása, ha a következő ellenőrzés hajthatja végre:
  - a (port 6380 használatával) gyorsítótár végpontjához, amely az egyazon vneten, mint a gyorsítótár gépről pingelni használatával [tcping](https://www.elifulkerson.com/projects/tcping.php). Példa:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Ha a `tcping` eszköz jelenti, hogy a port nyitva, a gyorsítótár nem érhető el a virtuális hálózaton lévő ügyféltől érkező kapcsolatot.

  - Tesztelje egy másik módja, ha a gyorsítótár tesztügyfél (ami lehet egy egyszerű konzolalkalmazást StackExchange.Redis használatával), amely a gyorsítótárhoz csatlakozik, és hozzáadja és kérdezi le azokat az elemeket a gyorsítótárból. Telepítse a mintaalkalmazás ügyfél be egy virtuális Gépet, amely ugyanabban a vnetben, mint a gyorsítótár, és futtassa azt a gyorsítótárba való kapcsolat ellenőrzésére.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Ha szeretne csatlakozni az Azure gyorsítótár egy virtuális hálózaton, miért jelenik meg a távoli tanúsítvány nem érvényes érvénytelenségét jelző hibaüzenet Redis?

Kapcsolódás az Azure Cache redis egy virtuális hálózaton, a tanúsítvány érvényesítési hiba jelenik meg:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Az OK lehet, a fogadó által az IP-cím csatlakozik. A hostname használatát javasoljuk. Más szóval használja a következőt:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Kerülje az IP-cím a következő kapcsolati karakterláncot hasonló:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Ha nem tudja feloldani a DNS-nevet, néhány klienskódtárak tartalmazza-e a konfigurációs beállítások, például a `sslHost` a StackExchange.Redis ügyfél levelezőalkalmazást. Ez lehetővé teszi, hogy a tanúsítvány érvényesítéséhez használt állomásnév felülbírálása. Példa:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Használható a virtuális hálózatok a gyorsítótárat standard vagy alapszintűre?
Virtuális hálózatok csak a prémium gyorsítótárak használható.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Miért nem az Azure Cache redis nem lehet létrehozni az egyes alhálózatok, de nem más?
Az Azure Cache redis egy Resource Manager virtuális hálózatot helyez üzembe, a gyorsítótár dedikált alhálózatán, amely tartalmazza a többi erőforrás típusa nem kell lennie. Ha üzembe helyezéséhez az Azure Cache redis Resource Managerbeli virtuális hálózat alhálózatához tett kísérlet, amely tartalmazza az egyéb erőforrásokat, a központi telepítés sikertelen lesz. Egy új Azure Cache redis létrehozása előtt törölnie kell a meglévő erőforrások, az alhálózaton belül.

Telepíthet több típusú erőforrások klasszikus virtuális hálózathoz, mindaddig, amíg elegendő IP-cím van.

### <a name="what-are-the-subnet-address-space-requirements"></a>Mik azok az alhálózat címterekkel kapcsolatos követelmények?
Az Azure lefoglalja minden egyes alhálózaton belül néhány IP-cím, és ezek a címek nem használható. Az alhálózatok első és utolsó IP-címét a protokollok megfelelősége érdekében, az Azure-szolgáltatásokhoz használt három további címek számára vannak fenntartva. További információkért lásd: [vannak ezen alhálózatok belüli IP-címek használatával korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Mellett az Azure-beli virtuális infrastruktúra által használt IP-címeket minden egyes Redis példány, a partíciónkénti alhálózatot használ két IP-címek és a egy további IP-címet a terheléselosztóhoz. Egy nem fürtözött gyorsítótár egyik adatszilánkba író szerkezetűnek tekinthető.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>A virtuális hálózat gyorsítótár üzemeltetése esetén működnek minden gyorsítótár-szolgáltatás?
Ha a gyorsítótár része virtuális Hálózatnak, csak a virtuális hálózaton lévő ügyfelek hozzáférhet a gyorsítótárban. Ennek eredményeképpen a következő gyorsítótár-felügyeleti funkciók jelenleg nem működnek.

* Redis konzol – a Redis konzol fut a helyi böngészőben, amely a virtuális hálózaton kívülről, mert nem tud kapcsolódni a gyorsítótárhoz.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Használja az ExpressRoute az Azure Cache-ben a redis Cache

Ügyfelek csatlakozhatnak egy [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolatcsoport virtuális hálózati infrastruktúrán, így kiterjesztése a helyszíni hálózat az Azure-bA. 

Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolatcsoport nem hajt végre a kényszerített bújtatás (alapértelmezett útvonal, a hirdetés 0.0.0.0/0) egy virtuális hálózaton. Ennek eredményeképpen közvetlenül a virtuális hálózatról kimenő Internet-kapcsolat engedélyezett, és ügyfélalkalmazások képesek-e csatlakozni a többi Azure-beli többek között az Azure Cache redis.

Azonban egy közös ügyfél-konfigurációs-e használni a kényszerített bújtatás (alapértelmezett útvonal hirdetése) amely arra kényszeríti a kimenő internetes forgalmat inkább a helyszíni. A forgalom áramlását működésképtelenné válik a kapcsolatot az Azure Cache redis, ha a kimenő forgalom, majd a helyszíni blokkolja, hogy az Azure Cache Redis-példány nem tud kommunikálni a hozzá tartozó függőségek.

A megoldás célja meghatározása egy (vagy több) felhasználó által megadott útvonalak (udr-EK), amely tartalmazza az Azure Cache redis az alhálózaton. Egy UDR határozza meg az alhálózat-specifikus útvonalakat, amely az alapértelmezett útvonal helyett lesznek érvényesek.

Ha lehetséges javasoljuk, hogy az alábbi konfigurációt használja:

* Az ExpressRoute-konfiguráció hirdet 0.0.0.0/0, és alapértelmezés szerint kényszerített alagutak minden kimenő forgalmat a helyszíni.
* Az udr-t az alhálózathoz, amely tartalmazza az Azure Cache redis alkalmazza a TCP/IP-forgalmat útvonal működő 0.0.0.0/0 a nyilvános interneten határozza meg. például a beállítás a következő ugrási típus: Internet".

A rendszer ezeket a lépéseket a kombinált hatását, hogy az alhálózat-szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatást végez, ezáltal biztosítható a kimenő Internet-hozzáférést az Azure Cache a redis.

Egy Redis-példányt az Azure Cache csatlakozik egy helyszíni ExpressRoute-tal, alkalmazás nem tipikus használati eset miatt a teljesítmény javítása érdekében (a legjobb teljesítmény érdekében az Azure Cache redis-ügyfelek az Azure redis Cache és ugyanabban a régióban kell lennie) .

>[!IMPORTANT] 
>Egy UDR-ben meghatározott **kell** lenniük, hogy az ExpressRoute-konfiguráció által meghirdetett bármely útvonallal szemben elsőbbséget. Az alábbi példa a széles 0.0.0.0/0 címtartományt használja, és mint ilyen potenciálisan véletlenül felülbírálhatja használatával pontosabb címtartományok útvonallal kapcsolatos hirdetéseket.

>[!WARNING]  
>Az Azure Cache redis nem támogatott olyan ExpressRoute-konfigurációk, amelyek **helytelenül keresztbe hirdetnek útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonal**. Rendelkezik a nyilvános társviszony-létesítés konfigurálása ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsoft számára a Microsoft Azure IP-címtartományok nagy készletét. Ha a címtartományokat helytelenül keresztbe hirdetik a privát társviszony-létesítési útvonal, az eredménye, hogy az összes alhálózat a Redis-példányt az Azure Cache származó kimenő hálózati csomag-e helytelenül kényszerített bújtatással jut el az ügyfél helyszíni hálózati infrastruktúra. Ezt a hálózati kialakítást Azure Cache redis működésképtelenné válik. A megoldás erre a problémára, ha leállítja az útvonalak keresztbe hirdetését a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési elérési utat.


Háttér-információkat a felhasználó által megadott útvonalak érhető el a jelen [áttekintése](../virtual-network/virtual-networks-udr-overview.md).

Az ExpressRoute kapcsolatos további információkért lásd: [ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan használja a további prémiumszintű gyorsítótár funkcióival.

* [Bevezetés az Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

