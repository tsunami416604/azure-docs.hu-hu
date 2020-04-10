---
title: Virtuális hálózat konfigurálása – Prémium szintű Azure-gyorsítótár a Redis számára
description: Ismerje meg, hogyan hozhat létre és kezelhet virtuális hálózati támogatást a prémium szintű Azure-gyorsítótárhoz a Redis-példányok hozása és kezelése
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 2821ee637b2562b5287dd3d59cf943b3dcb7ef97
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010885"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>A virtuális hálózati támogatás beállítása prémium szintű Azure-gyorsítótárhoz a Redis számára
A Redis-gyorsítótár különböző gyorsítótár-ajánlatokkal rendelkezik, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és szolgáltatásainak kiválasztásában, beleértve a prémium szintű funkciókat, például a fürtözést, az adatmegőrzést és a virtuális hálózati támogatást. A virtuális hálózat egy magánhálózat a felhőben. Ha egy Azure-gyorsítótár redis-példány van konfigurálva egy virtuális hálózat, nem nyilvánosan címezhető, és csak a virtuális gépek és alkalmazások a virtuális hálózaton belül érhető el. Ez a cikk ismerteti, hogyan konfigurálhatja a virtuális hálózati támogatás egy prémium szintű Azure-gyorsítótár redis-példány.

> [!NOTE]
> A Redis Azure Cache támogatja a klasszikus és az erőforrás-kezelő virtuális hálózatok.
> 
> 

Az egyéb prémium szintű gyorsítótár-funkciókról [a Bevezetés az Azure Cache for Redis Premium tier című témakörbe.](cache-premium-tier-intro.md)

## <a name="why-vnet"></a>Miért a Virtuális hálózat?
[Az Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) központi telepítése fokozott biztonságot és elkülönítést biztosít az Azure-gyorsítótár redishez, valamint az alhálózatok, a hozzáférés-vezérlési szabályzatok és egyéb funkciók számára a hozzáférés további korlátozása érdekében.

## <a name="virtual-network-support"></a>Virtuális hálózatok támogatása
Virtuális hálózat (VNet) támogatása van konfigurálva az **új Azure-gyorsítótár Redis** panel gyorsítótár létrehozása során. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Miután kiválasztottegy prémium szintű tarifacsomagot, konfigurálhatja a Redis virtuális hálózat integrációját egy olyan virtuális hálózat kiválasztásával, amely ugyanabban az előfizetésben és helyen található, mint a gyorsítótár. Új virtuális hálózat használatához hozza létre először az [Azure Portal használatával hozzon létre egy virtuális hálózatlétrehozása](../virtual-network/manage-virtual-network.md#create-a-virtual-network) vagy [egy virtuális hálózat létrehozása (klasszikus) az Azure Portalon,](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) majd térjen vissza az **új Azure-gyorsítótár redis** panel létrehozása és konfigurálása érdekében.

Konfigurálása a virtuális hálózat az új gyorsítótár, kattintson a **Virtuális hálózat** az **új Azure-gyorsítótár redis** panelen, és válassza ki a kívánt virtuális hálózat a legördülő listából.

![Virtuális hálózat][redis-cache-vnet]

Válassza ki a kívánt alhálózatot az **Alhálózat** legördülő listából.  Ha szükséges, adjon meg **egy statikus IP-címet**. A **Statikus IP-cím** mező nem kötelező, és ha nincs megadva, a kiválasztott alhálózatból választ ki egyet.

> [!IMPORTANT]
> Amikor egy Azure-cache redis egy Erőforrás-kezelő virtuális hálózat, a gyorsítótár kell lennie egy dedikált alhálózat, amely nem tartalmaz más erőforrásokat, kivéve az Azure Cache redis példányok. Ha egy kísérlet történik egy Azure-cache redis egy erőforrás-kezelő virtuális hálózat egy alhálózat, amely más erőforrásokat tartalmaz, a központi telepítés sikertelen lesz.
> 
> 

![Virtuális hálózat][redis-cache-vnet-ip]

> [!IMPORTANT]
> Az Azure lefoglal néhány IP-címet az egyes alhálózatokon belül, és ezek a címek nem használhatók. Az alhálózatok első és utolsó IP-címei a protokollmegfelelőség hez vannak fenntartva, valamint további három cím az Azure-szolgáltatásokhoz. További információ: [Vannak-e korlátozások az IP-címek használatára ezeken az alhálózatokon belül?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Az Azure VNET-infrastruktúra által használt IP-címek mellett az alhálózat minden Egyes Redis-példánya két IP-címet használ szegmensenként és egy további IP-címet a terheléselosztóhoz. A nem fürtözött gyorsítótár egy szegmenssel rendelkezik.
> 
> 

A gyorsítótár létrehozása után az **Erőforrás menü** **Virtuális hálózat** parancsára kattintva tekintheti meg a virtuális hálózat konfigurációját.

![Virtuális hálózat][redis-cache-vnet-info]

Ha virtuális hálózat használata esetén szeretne csatlakozni az Azure-gyorsítótárhoz a Redis-példányhoz, adja meg a gyorsítótár állomásnevét a kapcsolati karakterláncban az alábbi példában látható módon:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Azure-gyorsítótár a Redis virtuális hálózathoz – gyakori kérdések
Az alábbi lista az Azure-gyorsítótár redis méretezésével kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* Milyen gyakori helytelen konfigurációs problémák vannak az Azure Cache for Redis és a VNets használatával?
* [Hogyan tudom ellenőrizni, hogy működik-e a gyorsítótáram egy virtuális hálózaton?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Amikor megpróbál csatlakozni az Azure Cache for Redis egy virtuális hálózat, miért kapok hibaüzenetet atávoli tanúsítvány érvénytelen?
* [Használhatom a virtuális hálózatokat szabványos vagy alapszintű gyorsítótárral?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Miért működik az Azure-gyorsítótár létrehozása a Redis egyes alhálózatok, de mások nem?
* [Mik az alhálózati címtér követelményei?](#what-are-the-subnet-address-space-requirements)
* [Minden gyorsítótár-szolgáltatás működik, ha a virtuális hálózatban gyorsítótárat tárol?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Milyen gyakori helytelen konfigurációs problémák vannak az Azure Cache for Redis és a VNets használatával?
Ha az Azure Cache for Redis egy virtuális hálózatban található, a következő táblák portjai használatosak. 

>[!IMPORTANT]
>Ha a következő táblázatokportjai le vannak tiltva, előfordulhat, hogy a gyorsítótár nem működik megfelelően. Egy vagy több ilyen port blokkolása a leggyakoribb helytelen konfigurációs probléma az Azure Cache for Redis használata kor egy virtuális hálózatban.
> 
> 

- [Kimenő portokkal kapcsolatos követelmények](#outbound-port-requirements)
- [Bejövő portokkal kapcsolatos követelmények](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Kimenő portokkal kapcsolatos követelmények

Kilenc kimenő portra van szükség. Ezekben a tartományokban a kimenő kérelmek vagy kimenő más szolgáltatások működéséhez szükséges a gyorsítótár működéséhez, vagy a Redis alhálózat belső internode kommunikáció. A georeplikációhoz további kimenő követelmények vonatkoznak az elsődleges és a másodlagos gyorsítótár alhálózatai közötti kommunikációra.

| Port(ok) | Irány | Szállítási protokoll | Cél | Helyi IP | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Kimenő |TCP |Az Azure Storage/PKI (Internet) redis-függőségei | (Redis alhálózat) |* |
| 443 | Kimenő | TCP | Redis függőség az Azure Key Vault | (Redis alhálózat) | AzureKeyVault <sup>1</sup> |
| 53 |Kimenő |TCP/UDP |Redis dns-függőségek (Internet/VNet) | (Redis alhálózat) | 168.63.129.16 és 169.254.169.254 <sup>2</sup> és az alhálózat bármely egyéni DNS-kiszolgálója <sup>3</sup> |
| 8443 |Kimenő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) | (Redis alhálózat) |
| 10221-10231 |Kimenő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) | (Redis alhálózat) |
| 20226 |Kimenő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) |(Redis alhálózat) |
| 13000-13999 |Kimenő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) |(Redis alhálózat) |
| 15000-15999 |Kimenő |TCP |Belső kommunikáció a Redis és a Geo-replikáció számára | (Redis alhálózat) |(Redis alhálózat) (Geo-replika társalhálózat) |
| 6379-6380 |Kimenő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) |(Redis alhálózat) |

<sup>1</sup> Használhatja az "AzureKeyVault" szolgáltatáscímkét a Resource Manager hálózati biztonsági csoportokkal.

<sup>2</sup> Ezek az IP-címek a Microsoft tulajdonában vannak az Azure DNS-t kiszolgáló gazdagép címzésére.

<sup>3</sup> Nem szükséges az egyéni DNS-kiszolgálónélküli alhálózatok, illetve az egyéni DNS-t figyelmen kívül hagyó újabb redis-gyorsítótárak esetében.

#### <a name="geo-replication-peer-port-requirements"></a>Georeplikációs társportok követelményei

Ha az Azure virtuális hálózatok gyorsítótárai között georeplikációt használ, vegye figyelembe, hogy az ajánlott konfiguráció az 15000-15999-es portok blokkolásának feloldása a teljes alhálózat számára mindkét gyorsítótárba bejövő és kimenő irányban, hogy az alhálózat összes replika-összetevője közvetlenül kommunikáljon egymással egy jövőbeli geofeladat-átvétel esetén is.

#### <a name="inbound-port-requirements"></a>Bejövő portokkal kapcsolatos követelmények

Nyolc bejövő porttartományra vonatkozó követelmény van. Az ezekben a tartományokban lévő bejövő kérelmek vagy bejövőek az azonos virtuális hálózaton tárolt más szolgáltatásokból, vagy a Redis alhálózati kommunikációba.

| Port(ok) | Irány | Szállítási protokoll | Cél | Helyi IP | Távoli IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Bejövő |TCP |Ügyfélkommunikáció a Redis-nek, Az Azure terheléselosztása | (Redis alhálózat) | (Redis alhálózat), virtuális hálózat, Azure Load Balancer <sup>1</sup> |
| 8443 |Bejövő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) |(Redis alhálózat) |
| 8500 |Bejövő |TCP/UDP |Azure terheléselosztás | (Redis alhálózat) |Azure Load Balancer |
| 10221-10231 |Bejövő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) |(Redis alhálózat), Azure-terheléselosztó |
| 13000-13999 |Bejövő |TCP |Ügyfélkommunikáció a Redis-fürtökkel, az Azure terheléselosztása | (Redis alhálózat) |Virtuális hálózat, Azure-terheléselosztó |
| 15000-15999 |Bejövő |TCP |Ügyfélkommunikáció a Redis-fürtökkel, az Azure terheléselosztással és a georeplikációval | (Redis alhálózat) |Virtuális hálózat, Azure terheléselosztó , (geo-replika társalhálózat) |
| 16001 |Bejövő |TCP/UDP |Azure terheléselosztás | (Redis alhálózat) |Azure Load Balancer |
| 20226 |Bejövő |TCP |Belső kommunikáció a Redis számára | (Redis alhálózat) |(Redis alhálózat) |

<sup>1</sup> Használhatja a Service Tag "AzureLoadBalancer" (Resource Manager) (vagy a "AZURE_LOADBALANCER" a klasszikus) az NSG-szabályok szerzői.

#### <a name="additional-vnet-network-connectivity-requirements"></a>További virtuális hálózat hálózati kapcsolati követelményei

Vannak hálózati kapcsolati követelmények az Azure Cache for Redis, amely kezdetben nem teljesül a virtuális hálózatban. A Redis Azure Cache a következő elemeket igényel megfelelően, ha egy virtuális hálózaton belül használják.

* Kimenő hálózati kapcsolat az Azure Storage-végpontokhoz világszerte. Ez magában foglalja a redis-példány azure-gyorsítótárával azonos régióban található végpontokat, valamint **a más** Azure-régiókban található tárolási végpontokat. Az Azure Storage-végpontok feloldása a következő DNS-tartományok ban: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*és *file.core.windows.net*. 
* Kimenő hálózati kapcsolat a *ocsp.msocsp.com,* *mscrl.microsoft.com*és *crl.microsoft.com.* Ez a kapcsolat a TLS/SSL funkció támogatásához szükséges.
* A virtuális hálózat DNS-konfigurációjának képesnek kell lennie a korábbi pontokban említett összes végpont és tartomány feloldására. Ezek a DNS-követelmények úgy teljesíthetők, hogy biztosítják, hogy a virtuális hálózathoz érvényes DNS-infrastruktúra legyen konfigurálva és karbantartva.
* Kimenő hálózati kapcsolat a következő Azure Monitoring-végpontok, amelyek a következő DNS-tartományok: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Hogyan tudom ellenőrizni, hogy működik-e a gyorsítótáram egy virtuális hálózaton?

>[!IMPORTANT]
>Amikor egy virtuális hálózatban üzemeltetett Redis-példány Azure-gyorsítótárához csatlakozik, a gyorsítótár-ügyfeleknek ugyanabban a virtuális hálózatban vagy egy virtuális hálózatban kell lenniük, ahol a virtuális hálózat társviszony-létesítése engedélyezve van ugyanabban az Azure-régióban. Globális vnet-társviszony-létesítés jelenleg nem támogatott. Ez magában foglalja a tesztalkalmazásokat vagy a diagnosztikai pingelő eszközöket. Függetlenül attól, hogy az ügyfélalkalmazás hová van tárolva, a hálózati biztonsági csoportokat úgy kell konfigurálni, hogy az ügyfél hálózati forgalma elérhető legyen a Redis-példányhoz.
>
>

Miután a portkövetelmények az előző szakaszban leírtak szerint vannak konfigurálva, a következő lépések végrehajtásával ellenőrizheti, hogy a gyorsítótár működik-e.

- [Indítsa újra](cache-administration.md#reboot) az összes gyorsítótár-csomópontot. Ha az összes szükséges gyorsítótár-függőség nem érhető el (a [bejövő portra és](cache-how-to-premium-vnet.md#inbound-port-requirements) a kimenő [portra vonatkozó követelményekben](cache-how-to-premium-vnet.md#outbound-port-requirements)dokumentált módon), a gyorsítótár nem fog sikeresen újraindulni.
- Miután a gyorsítótár-csomópontok újraindultak (az Azure Portalon a gyorsítótár állapota szerint), a következő teszteket hajthatja végre:
  - ping elpingelje a gyorsítótár végpontját (a 6380-as portot használva) egy olyan gépről, amely a gyorsítótáral azonos virtuális hálózaton belül van, [tcping](https://www.elifulkerson.com/projects/tcping.php)használatával. Például:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Ha `tcping` az eszköz jelenti, hogy a port meg van nyitva, a gyorsítótár elérhető a virtuális hálózatban lévő ügyfelek kapcsolatához.

  - A tesztelés másik módja egy tesztgyorsítótár-ügyfél létrehozása (amely a StackExchange.Redis használatával egy egyszerű konzolalkalmazás is lehet), amely csatlakozik a gyorsítótárhoz, és hozzáad és lekér néhány elemet a gyorsítótárból. Telepítse a mintaügyfél-alkalmazást egy virtuális gépre, amely ugyanabban a virtuális hálózatban van, mint a gyorsítótár, és futtassa a gyorsítótárhoz való kapcsolódás ellenőrzéséhez.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Amikor megpróbál csatlakozni az Azure Cache for Redis egy virtuális hálózat, miért kapok hibaüzenetet atávoli tanúsítvány érvénytelen?

Amikor egy virtuális hálózatban próbál csatlakozni egy Azure-gyorsítótárhoz a Redis számára, egy tanúsítvány-érvényesítési hiba jelenik meg, például a következő:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Ennek oka az lehet, hogy az IP-cím által csatlakozik az állomáshoz. Javasoljuk, hogy használja a hostname. Más szóval használja a következőket:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Ne használja az ALÁBBI kapcsolati karakterlánchoz hasonló IP-címet:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Ha nem tudja feloldani a DNS-nevet, egyes `sslHost` ügyfélkódtárak olyan konfigurációs beállításokat tartalmaznak, mint a StackExchange.Redis ügyfél. Ez lehetővé teszi a tanúsítvány-ellenőrzéshez használt állomásnév felülbírálását. Például:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Használhatom a virtuális hálózatokat szabványos vagy alapszintű gyorsítótárral?
Virtuális hálózatok csak prémium szintű gyorsítótárak használata.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Miért működik az Azure-gyorsítótár létrehozása a Redis egyes alhálózatok, de mások nem?
Ha egy Azure-cache redis egy Erőforrás-kezelő virtuális hálózat, a gyorsítótár kell lennie egy dedikált alhálózat, amely nem tartalmaz más erőforrástípust. Ha egy kísérlet történik egy Azure-cache redis üzembe helyezésére egy erőforrás-kezelő virtuális hálózat, amely más erőforrásokat tartalmaz, a központi telepítés sikertelen lesz. Új Azure-gyorsítótár a Redis-hez való létrehozása előtt törölnie kell a meglévő erőforrásokat az alhálózaton belül.

Több típusú erőforrásokat is üzembe helyezhet egy klasszikus virtuális hálózatra, feltéve, hogy elegendő IP-cím áll rendelkezésre.

### <a name="what-are-the-subnet-address-space-requirements"></a>Mik az alhálózati címtér követelményei?
Az Azure lefoglal néhány IP-címet az egyes alhálózatokon belül, és ezek a címek nem használhatók. Az alhálózatok első és utolsó IP-címei a protokollmegfelelőség hez vannak fenntartva, valamint további három cím az Azure-szolgáltatásokhoz. További információ: [Vannak-e korlátozások az IP-címek használatára ezeken az alhálózatokon belül?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Az Azure VNET-infrastruktúra által használt IP-címek mellett az alhálózat minden Egyes Redis-példánya két IP-címet használ szegmensenként és egy további IP-címet a terheléselosztóhoz. A nem fürtözött gyorsítótár egy szegmenssel rendelkezik.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Minden gyorsítótár-szolgáltatás működik, ha a virtuális hálózatban gyorsítótárat tárol?
Ha a gyorsítótár egy virtuális hálózat része, csak a virtuális hálózatban lévő ügyfelek férhetnek hozzá a gyorsítótárhoz. Ennek eredményeképpen a következő gyorsítótár-kezelési funkciók jelenleg nem működnek.

* Redis Console – Mivel a Redis konzol a helyi böngészőben fut, amely kívül esik a virtuális hálózaton, nem tud csatlakozni a gyorsítótárhoz.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Az ExpressRoute használata az Azure Cache for Redis-hez

Az ügyfelek csatlakoztathatnak egy [Azure ExpressRoute-áramkört](https://azure.microsoft.com/services/expressroute/) a virtuális hálózati infrastruktúrájukhoz, így kiterjesztve a helyszíni hálózatukat az Azure-ra. 

Alapértelmezés szerint egy újonnan létrehozott ExpressRoute-kapcsolatcsoport nem hajt végre kényszerített bújtatás (alapértelmezett útvonal hirdetése, 0.0.0/0) a virtuális hálózaton. Ennek eredményeképpen a kimenő internetkapcsolat közvetlenül a virtuális hálózatról engedélyezett, és az ügyfélalkalmazások más Azure-végpontokhoz, például a Redis Azure Cache-hez is csatlakozhatnak.

Azonban egy közös ügyfél konfiguráció használata kényszerített bújtatás (hirdessen egy alapértelmezett útvonal), amely arra kényszeríti a kimenő internetes forgalmat, hogy ehelyett a helyszíni áramlás. Ez a forgalmi folyamat megszakítja a kapcsolatot az Azure Cache for Redis, ha a kimenő forgalom majd blokkolva van a helyszínen, így az Azure Cache for Redis példány nem tud kommunikálni a függőségek.

A megoldás egy (vagy több) felhasználó által definiált útvonalak (UDRs) definiálása az alhálózaton, amely tartalmazza az Azure Cache for Redis. Az UDR azokat az alhálózati útvonalakat határozza meg, amelyek az alapértelmezett útvonal helyett a rendszer tiszteletben tartják őket.

Ha lehetséges, ajánlott a következő konfigurációt használni:

* Az ExpressRoute-konfiguráció 0.0.0.0/0-t hirdet, és alapértelmezés szerint az összes kimenő forgalmat a helyszínen tárolja.
* Az Azure Cache for Redis-t tartalmazó alhálózatra alkalmazott UDR 0.0.0.0/0-t határoz meg a nyilvános internetre irányuló TCP/IP-forgalom munkaútvonalával; például a következő ugrástípus "Internet" beállításával.

Ezek a lépések együttes hatása az, hogy az alhálózati szintű UDR elsőbbséget élvez az ExpressRoute kényszerített bújtatása, így biztosítva a kimenő internet-hozzáférést az Azure Cache for Redis.

A Redis-példány Azure-gyorsítótárhoz való csatlakozás egy helyszíni alkalmazásból ExpressRoute használatával nem tipikus használati forgatókönyv a teljesítmény miatt (a redis-ügyfelek számára a legjobb teljesítmény érdekében az Azure-gyorsítótárnak ugyanabban a régióban kell lennie, mint az Azure Cache for Redis).

>[!IMPORTANT] 
>Az UDR-ben definiált útvonalaknak elég konkrétnak **kell** lenniük ahhoz, hogy elsőbbséget élvezzenek az ExpressRoute-konfiguráció által meghirdetett útvonalakkal szemben. A következő példa a széles 0.0.0.0/0 címtartományt használja, és mint ilyen, véletlenül felülbírálhatók az útvonalhirdetések konkrétabb címtartományokhasználatával.

>[!WARNING]  
>A Redis Azure Cache for Redis nem támogatott olyan ExpressRoute-konfigurációkkal, amelyek **helytelenül keresztbe lépnek a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra vezető útvonalakon.** A nyilvános társviszony-létesítést konfigurált ExpressRoute-konfigurációk útvonalhirdetéseket kapnak a Microsofttól a Microsoft Azure IP-címtartományok nagy készletéhez. Ha ezek a címtartományok helytelenül kereszthirdetve vannak a privát társviszony-létesítési útvonalon, az eredmény az, hogy a Redis-példány alhálózatának Azure Cache-ből származó összes kimenő hálózati csomag helytelenül kényszeríti az ügyfél helyszíni hálózati infrastruktúráját. Ez a hálózati folyamat megszakítja a Redis Azure-gyorsítótárát. A probléma megoldása az, hogy állítsa le a kereszthirdetési útvonalakat a nyilvános társviszony-létesítési útvonalról a privát társviszony-létesítési útvonalra.


A felhasználó által definiált útvonalakkal kapcsolatos háttér-információk ebben az [áttekintésben](../virtual-network/virtual-networks-udr-overview.md)találhatók.

Az ExpressRoute-ról további információt az [ExpressRoute technikai áttekintése című témakörben talál.](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>További lépések
További információ a további prémium szintű gyorsítótár-funkciók használatáról.

* [Bevezetés az Azure Cache for Redis premium szintű rétegbe](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
