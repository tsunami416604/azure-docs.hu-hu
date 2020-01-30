---
title: Virtual Network prémium szintű Azure cache konfigurálása a Redis-hez
description: Ismerje meg, hogyan hozhat létre és kezelhet Virtual Network támogatást a prémium szintű Azure cache Redis-példányokhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 6c7c041565f6376e7f8b8b84f5076b30c1eec7bf
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846403"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>A prémium szintű Azure cache Virtual Network támogatásának konfigurálása a Redis-hez
A Redis készült Azure cache különböző gyorsítótár-ajánlatokat tartalmaz, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és funkcióinak, beleértve a prémium szintű funkciókat, például a fürtözést, az adatmegőrzést és a virtuális hálózatok támogatását. A VNet a felhőben található magánhálózat. Ha egy Azure cache for Redis-példány VNet van konfigurálva, nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el. Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális hálózatok támogatását egy prémium szintű Azure cache-hez a Redis-példányhoz.

> [!NOTE]
> A Redis készült Azure cache a klasszikus és a Resource Manager virtuális hálózatok is támogatja.
> 
> 

További információ a prémium szintű gyorsítótár-funkciókról: [Bevezetés az Azure cache for Redis Premium](cache-premium-tier-intro.md)csomagba.

## <a name="why-vnet"></a>Miért VNet?
Az [azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure cache Redis, valamint az alhálózatok, hozzáférés-vezérlési házirendek és egyéb funkciók számára a hozzáférés további korlátozásához.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása
A Virtual Network (VNet) támogatása a gyorsítótár létrehozásakor a **Redis panel új Azure-gyorsítótárában** van konfigurálva. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Miután kiválasztotta a prémium szintű árképzési szintet, a Redis VNet-integrációt úgy is konfigurálhatja, hogy kijelöl egy olyan VNet, amely ugyanabban az előfizetésben és helyen található, mint a gyorsítótár. Ha új VNet szeretne használni, először hozza létre a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/manage-virtual-network.md#create-a-virtual-network) vagy a [virtuális hálózat létrehozása (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) című témakörben ismertetett lépéseket a Azure Portal használatával, majd térjen vissza az **új Azure cache for Redis** panelre a prémium szintű gyorsítótár létrehozásához és konfigurálásához.

Az új gyorsítótár VNet konfigurálásához kattintson a **Virtual Network** elemre a **Redis új Azure cache** -paneljén, és válassza ki a kívánt VNet a legördülő listából.

![Virtuális hálózat][redis-cache-vnet]

Válassza ki a kívánt alhálózatot az **alhálózat** legördülő listából.  Ha szükséges, **statikus IP-címet**kell megadni. A **statikus IP-cím** mező nem kötelező, és ha nincs megadva, a rendszer a kiválasztott alhálózatból választ egyet.

> [!IMPORTANT]
> Ha Azure-gyorsítótárat telepít a Redis egy Resource Manager-VNet, a gyorsítótárnak olyan dedikált alhálózaton kell lennie, amely nem tartalmaz más erőforrásokat, kivéve a Redis-példányok Azure gyorsítótárát. Ha kísérlet történt egy Azure cache üzembe helyezésére a Redis egy Resource Manager-VNet egy olyan alhálózatra, amely más erőforrásokat is tartalmaz, a telepítés meghiúsul.
> 
> 

![Virtuális hálózat][redis-cache-vnet-ip]

> [!IMPORTANT]
> Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Az Azure VNET-infrastruktúra által használt IP-címeken kívül az alhálózat minden Redis-példánya két IP-címet használ, és egy további IP-címet a terheléselosztó számára. A nem fürtözött gyorsítótár egy szegmensnek tekintendő.
> 
> 

A gyorsítótár létrehozása után megtekintheti a VNet konfigurációját, ha az **erőforrás menüben**a **Virtual Network** lehetőségre kattint.

![Virtuális hálózat][redis-cache-vnet-info]

Ha egy VNet használatakor szeretne csatlakozni az Azure cache-hez a Redis-példányhoz, adja meg a gyorsítótár állomásnevét a kapcsolati karakterláncban a következő példában látható módon:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Azure cache a Redis VNet – gyakori kérdések
Az alábbi lista az Azure cache Redis skálázási szolgáltatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* Melyek az Azure cache-vel kapcsolatos gyakori hibák a Redis és a virtuális hálózatok esetében?
* [Hogyan tudom ellenőrizni, hogy működik-e a gyorsítótáram egy virtuális hálózaton?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Amikor megpróbálok csatlakozni az Azure-gyorsítótárhoz a Redis egy VNET, miért kapok hibaüzenetet arról, hogy a távoli tanúsítvány érvénytelen?
* [Használhatom a virtuális hálózatok standard vagy alapszintű gyorsítótárral?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Miért nem sikerül létrehozni egy Azure cache-t a Redis-hez egyes alhálózatokban, de másokat nem?
* [Mik az alhálózati címtartomány követelményei?](#what-are-the-subnet-address-space-requirements)
* [Működik az összes gyorsítótár-funkció a gyorsítótár VNET való üzemeltetése során?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Melyek az Azure cache-vel kapcsolatos gyakori hibák a Redis és a virtuális hálózatok esetében?
Ha a Redis tartozó Azure cache-t egy VNet üzemelteti, a rendszer a következő táblázatban található portokat használja. 

>[!IMPORTANT]
>Ha a következő táblázatokban lévő portok le vannak tiltva, előfordulhat, hogy a gyorsítótár nem működik megfelelően. Ha a portok közül egy vagy több blokkolva van, akkor a leggyakoribb hiba a probléma, amikor az Azure cache-t használja a Redis egy VNet.
> 
> 

- [Kimenő portokkal kapcsolatos követelmények](#outbound-port-requirements)
- [Bejövő portokkal kapcsolatos követelmények](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Kimenő portokra vonatkozó követelmények

Kilenc kimenő portra vonatkozó követelmény van. Az ezekben a tartományokban lévő kimenő kérelmek vagy más olyan szolgáltatásoknak vannak kihagyva, amelyek szükségesek a gyorsítótár működéséhez vagy a belső Redis-alhálózathoz a csomópontok közötti kommunikációhoz. Földrajzi replikálás esetén további kimenő követelmények is léteznek az elsődleges és másodlagos gyorsítótár alhálózatai közötti kommunikációhoz.

| Port (ok) | Irány | Átviteli protokoll | Rendeltetés | Helyi IP-cím | Távoli IP-cím |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Kimenő |TCP |Redis-függőségek az Azure Storage-ban/PKI-ben (Internet) | (Redis alhálózat) |* |
| 443 | Kimenő | TCP | Redis függőség Azure Key Vault | (Redis alhálózat) | <sup>1</sup> . AzureKeyVault |
| 53 |Kimenő |TCP/UDP |Redis-függőségek a DNS-ben (Internet/VNet) | (Redis alhálózat) | 168.63.129.16 és 169.254.169.254 <sup>2</sup> és bármely egyéni DNS-kiszolgáló a <sup>3</sup> . alhálózathoz |
| 8443 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) | (Redis alhálózat) |
| 10221-10231 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) | (Redis alhálózat) |
| 20226 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |
| 13000-13999 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |
| 15000-15999 |Kimenő |TCP |Belső kommunikáció a Redis és a Geo-replikáláshoz | (Redis alhálózat) |(Redis alhálózat) (Geo-replika társ alhálózat) |
| 6379-6380 |Kimenő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |

<sup>1</sup> a "AzureKeyVault" szolgáltatási címkét a Resource Manager hálózati biztonsági csoportjaival használhatja.

<sup>2</sup> ezek az IP-címek a Microsoft tulajdonában vannak, és a Azure DNSt kiszolgáló virtuális gép kezelésére szolgálnak.

<sup>3</sup> nem szükséges egyéni DNS-kiszolgálót nem tartalmazó alhálózatokhoz vagy olyan újabb Redis cache-hez, amely figyelmen kívül hagyja az egyéni DNS-t.

#### <a name="geo-replication-peer-port-requirements"></a>Geo-replikációs társ portjának követelményei

Ha az Azure Virtual Networks gyorsítótárai között replikáció használ, vegye figyelembe, hogy az ajánlott konfiguráció a teljes alhálózat 15000-15999-as portjának feloldása mind a bejövő, mind a kimenő irányban mindkét gyorsítótárban, így a replika összes összetevője az alhálózaton közvetlenül is kommunikálhatnak egymással, még egy jövőbeli földrajzi feladatátvétel esetén is.

#### <a name="inbound-port-requirements"></a>Bejövő portokra vonatkozó követelmények

Nyolc bejövő porttartomány-követelmény van. Az ezekben a tartományokban lévő bejövő kérelmek vagy más, ugyanazon a VNET üzemeltetett, vagy a Redis alhálózaton belüli kommunikációban lévő szolgáltatásokból érkeznek be.

| Port (ok) | Irány | Átviteli protokoll | Rendeltetés | Helyi IP-cím | Távoli IP-cím |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Bejövő |TCP |Redis-alapú ügyfél-kommunikáció, Azure-terheléselosztás | (Redis alhálózat) | (Redis alhálózat), Virtual Network, Azure Load Balancer <sup>1</sup> |
| 8443 |Bejövő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |
| 8500 |Bejövő |TCP/UDP |Azure-terheléselosztás | (Redis alhálózat) |Azure Load Balancer |
| 10221-10231 |Bejövő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat), Azure Load Balancer |
| 13000-13999 |Bejövő |TCP |Ügyfél-kommunikáció Redis-fürtökkel, Azure-terheléselosztás | (Redis alhálózat) |Virtual Network, Azure Load Balancer |
| 15000-15999 |Bejövő |TCP |Ügyfél-kommunikáció Redis-fürtökkel, Azure terheléselosztás és geo-replikáció | (Redis alhálózat) |Virtual Network, Azure Load Balancer, (Geo-replika társ alhálózat) |
| 16001 |Bejövő |TCP/UDP |Azure-terheléselosztás | (Redis alhálózat) |Azure Load Balancer |
| 20226 |Bejövő |TCP |Belső kommunikáció a Redis | (Redis alhálózat) |(Redis alhálózat) |

<sup>1</sup> a NSG szabályok létrehozásához használhatja a "AzureLoadBalancer" (Resource Manager) (vagy a klasszikus) "AZURE_LOADBALANCER" nevű szolgáltatási címkét is.

#### <a name="additional-vnet-network-connectivity-requirements"></a>További VNET hálózati kapcsolati követelmények

Az Azure cache hálózati kapcsolati követelményei olyan Redis esetében fordulnak elő, amelyek esetleg nem feltétlenül teljesülnek egy virtuális hálózaton. A Redis készült Azure cache használatához a következő elemek megfelelő működéséhez szükség van a virtuális hálózaton belüli használathoz.

* A kimenő hálózati kapcsolat az Azure Storage-végpontokkal világszerte. Ide tartoznak a Redis-példányhoz tartozó Azure cache-vel azonos régióban található végpontok, valamint **más** Azure-régiókban található tárolási végpontok. Az Azure Storage-végpontok a következő DNS-tartományok alatt oldhatók fel: *table.Core.Windows.net*, *blob.Core.Windows.net*, *Queue.Core.Windows.net*és *file.Core.Windows.net*. 
* Kimenő hálózati kapcsolat a *OCSP.msocsp.com*, a *mscrl.microsoft.com*és a *CRL.microsoft.com*. Ez a kapcsolat az SSL-funkciók támogatásához szükséges.
* A virtuális hálózat DNS-konfigurációjának képesnek kell lennie a korábbi pontokban említett összes végpont és tartomány feloldására. Ezek a DNS-követelmények teljesíthetők az érvényes DNS-infrastruktúra konfigurálásának és karbantartásának biztosításával a virtuális hálózat számára.
* Kimenő hálózati kapcsolat a következő DNS-tartományokban megoldott Azure monitoring-végpontokkal: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net , shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hogyan tudom ellenőrizni, hogy a gyorsítótár működik-e egy VNET?

>[!IMPORTANT]
>Amikor egy VNET üzemeltetett Redis-példányhoz csatlakozik egy Azure-gyorsítótárhoz, a gyorsítótár-ügyfeleknek ugyanabban a VNET kell lenniük, vagy olyan VNET kell lenniük, amelynek VNET-társítása engedélyezve van ugyanazon az Azure-régión belül. A globális virtuális társhálózatok létesítése jelenleg nem támogatott. Ez magában foglalja az alkalmazások tesztelését vagy a diagnosztikai pingelési eszközöket. Függetlenül attól, hogy az ügyfélalkalmazás hol található, a hálózati biztonsági csoportokat úgy kell konfigurálni, hogy az ügyfél hálózati forgalma elérje a Redis-példányt.
>
>

Ha a portra vonatkozó követelmények az előző szakaszban leírtak szerint vannak konfigurálva, a következő lépések végrehajtásával ellenőrizheti, hogy a gyorsítótár működik-e.

- [Indítsa újra](cache-administration.md#reboot) az összes gyorsítótár-csomópontot. Ha az összes szükséges gyorsítótár-függőség nem érhető el (a [bejövő portokra vonatkozó követelmények](cache-how-to-premium-vnet.md#inbound-port-requirements) és a [kimenő portokra vonatkozó követelmények](cache-how-to-premium-vnet.md#outbound-port-requirements)dokumentálása alapján), a gyorsítótár nem fog tudni újraindulni.
- A gyorsítótár-csomópontok újraindítása után (ahogy a gyorsítótár állapota a Azure Portalban szerepel) a következő teszteket végezheti el:
  - Pingelje a gyorsítótár-végpontot (az 6380-as port használatával) egy olyan gépről, amely a gyorsítótárral azonos VNET található, a [tcping](https://www.elifulkerson.com/projects/tcping.php)használatával. Példa:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Ha a `tcping` eszköz azt jelenti, hogy a port meg van nyitva, a gyorsítótár elérhető lesz a VNET lévő ügyfelekkel való csatlakozáshoz.

  - A tesztelés egy másik módja, ha létrehoz egy teszt gyorsítótár-ügyfelet (ez lehet egy egyszerű, a StackExchange. Redis-t használó konzolos alkalmazás), amely csatlakozik a gyorsítótárhoz, és felveszi és lekéri néhány elemet a gyorsítótárból. Telepítse a minta ügyfélalkalmazás egy olyan virtuális gépre, amely ugyanabban a VNET van, mint a gyorsítótár, és futtassa azt a gyorsítótárhoz való kapcsolódás ellenőrzéséhez.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Amikor megpróbálok csatlakozni az Azure-gyorsítótárhoz a Redis egy VNET, miért kapok hibaüzenetet arról, hogy a távoli tanúsítvány érvénytelen?

Amikor egy VNET Redis egy Azure-gyorsítótárhoz próbál csatlakozni, egy tanúsítvány-ellenőrzési hiba jelenik meg, például a következő:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Az ok lehet, hogy az IP-cím alapján csatlakozik a gazdagéphez. Az állomásnév használatát javasoljuk. Más szóval használja a következőt:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Kerülje a következő kapcsolódási karakterlánchoz hasonló IP-cím használatát:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Ha nem tudja feloldani a DNS-nevet, egyes ügyféloldali kódtárak olyan konfigurációs beállításokat tartalmaznak, mint például a StackExchange. Redis-ügyfél által biztosított `sslHost`. Ez lehetővé teszi a tanúsítvány-ellenőrzéshez használt állomásnév felülbírálását. Példa:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Használhatom a virtuális hálózatok standard vagy alapszintű gyorsítótárral?
A virtuális hálózatok csak Premium-gyorsítótárak használatával használható.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Miért nem sikerül létrehozni egy Azure cache-t a Redis-hez egyes alhálózatokban, de másokat nem?
Ha Azure-gyorsítótárat telepít a Redis egy Resource Manager-VNet, a gyorsítótárnak olyan dedikált alhálózaton kell lennie, amely nem tartalmaz más erőforrástípust. Ha kísérlet történt egy Azure-gyorsítótár üzembe helyezésére a Redis egy olyan Resource Manager VNet-alhálózatra, amely más erőforrásokat is tartalmaz, a telepítés meghiúsul. Ahhoz, hogy új Azure cache-t hozna létre a Redis számára, törölnie kell a meglévő erőforrásokat az alhálózaton belül.

Több típusú erőforrást is üzembe helyezhet egy klasszikus VNet, ha elegendő IP-cím áll rendelkezésre.

### <a name="what-are-the-subnet-address-space-requirements"></a>Mik az alhálózati címtartomány követelményei?
Az Azure egyes alhálózatokon belül fenntart néhány IP-címet, és ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokoll-megfelelőség számára van fenntartva, valamint az Azure-szolgáltatásokhoz használt három további címet. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure VNET-infrastruktúra által használt IP-címeken kívül az alhálózat minden Redis-példánya két IP-címet használ, és egy további IP-címet a terheléselosztó számára. A nem fürtözött gyorsítótár egy szegmensnek tekintendő.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Működik az összes gyorsítótár-funkció a gyorsítótár VNET való üzemeltetése során?
Ha a gyorsítótár egy VNET része, akkor csak a VNET lévő ügyfelek férhetnek hozzá a gyorsítótárhoz. Ennek eredményeképpen a következő gyorsítótár-kezelési funkciók jelenleg nem működnek.

* Redis-konzol – mivel a Redis-konzol a helyi böngészőben fut, amely kívül esik a VNET, nem tud csatlakozni a gyorsítótárhoz.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>A ExpressRoute használata az Azure cache használatával a Redis

Az ügyfelek az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) áramkört a virtuális hálózati infrastruktúrához kapcsolódhatnak, ezzel kiterjesztve a helyszíni hálózatot az Azure-ba. 

Alapértelmezés szerint az újonnan létrehozott ExpressRoute-áramkör nem hajt végre kényszerített bújtatást (az alapértelmezett Route, 0.0.0.0/0) hirdetményt egy VNET. Ennek eredményeképpen a kimenő internetkapcsolat közvetlenül a VNET érhető el, és az ügyfélalkalmazások csatlakozni tudnak más Azure-végpontokhoz, beleértve az Azure cache-t a Redis.

Ugyanakkor a gyakori ügyfél-konfiguráció a kényszerített bújtatás használata (alapértelmezett útvonal meghirdetése), amely a helyi forgalom helyett a kimenő internetes forgalmat kényszeríti. Ez a forgalmi folyamat megszakítja a kapcsolatot az Azure cache-vel a Redis, ha a kimenő forgalom ezután le van tiltva a helyszínen, így az Azure cache for Redis-példány nem tud kommunikálni a függőségeivel.

A megoldás egy (vagy több) felhasználó által megadott útvonal (UDR) definiálása azon az alhálózaton, amely a Redis tartozó Azure cache-t tartalmazza. A UDR olyan alhálózat-specifikus útvonalakat határoznak meg, amelyeket az alapértelmezett útvonal helyett tiszteletben kell tartani.

Ha lehetséges, javasoljuk, hogy a következő konfigurációt használja:

* A ExpressRoute-konfiguráció a 0.0.0.0/0 értéket hirdeti meg, és alapértelmezés szerint kényszeríti az alagutakat a helyszíni kimenő forgalomra.
* A Redis készült Azure cache-t tartalmazó alhálózatra alkalmazott UDR a 0.0.0.0/0 értéket határozza meg a nyilvános internetre irányuló TCP/IP-forgalomra vonatkozó munkaútvonalon. például a következő ugrás típusának "Internet" értékre állításával.

Ezen lépések együttes hatása, hogy az alhálózati szint UDR elsőbbséget élvez a ExpressRoute kényszerített bújtatásával szemben, így biztosítva a kimenő internet-hozzáférést a Redis készült Azure cache-hez.

A ExpressRoute-t használó helyszíni alkalmazásokból származó Redis-példány Azure cache-hez való csatlakoztatása nem jellemző használati forgatókönyv (a Redis-ügyfelek esetében a legjobb teljesítményű Azure cache-hez a Redis Azure cache-hez hasonló régióban kell lennie) .

>[!IMPORTANT] 
>A UDR meghatározott útvonalaknak elég **egyedinek kell lenniük** ahhoz, hogy elsőbbséget élvezzenek a ExpressRoute-konfiguráció által meghirdetett útvonalakkal szemben. Az alábbi példa a széles 0.0.0.0/0 címtartományt használja, ezért a hirdetmények az útválasztási hirdetmények használatával véletlenül felülbírálják a több megadott címtartományt.

>[!WARNING]  
>Az Redis-hez készült Azure cache nem támogatott olyan ExpressRoute-konfigurációk esetén, amelyek nem tesznek közzé olyan **útvonalakat, amelyek a nyilvános társ-létrehozási útvonalról a privát társítási útvonalra**kerülnek. A nyilvános ExpressRoute-konfigurációval rendelkező konfigurációk a Microsofttól érkező útválasztási hirdetményeket kapnak Microsoft Azure IP-címtartományok nagy készlete számára. Ha ezek a címtartományok helytelenül vannak meghirdetve a privát társítási útvonalon, az eredmény az, hogy a Redis-példány alhálózatának Azure cache-ről érkező összes kimenő hálózati csomag helytelenül van kényszerítve az ügyfél helyszíni hálózatára. infrastruktúra. Ez a hálózati folyamat megszakítja az Azure cache-t a Redis. A probléma megoldásának célja, hogy leállítsa az útvonalakat a nyilvános társítási útvonalról a privát társ-létrehozási útvonalra.


A felhasználó által megadott útvonalakon található háttér-információk ebben az [áttekintésben](../virtual-network/virtual-networks-udr-overview.md)érhetők el.

További információ a ExpressRoute: a [ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Következő lépések
További információ a prémium szintű gyorsítótár-funkciók használatáról.

* [A prémium szintű Redis készült Azure cache bemutatása](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
