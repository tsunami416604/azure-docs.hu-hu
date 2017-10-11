---
title: "Azure Redis Cache méretezése |} Microsoft Docs"
description: "További tudnivalók az Azure Redis Cache példány méretezése"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: sdanie
ms.openlocfilehash: 91b3580491a1e3504a3891b66606a9bd18c0638f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-scale-azure-redis-cache"></a>Azure Redis Cache méretezése
Azure Redis Cache rendelkezik másik gyorsítótármappa ajánlatokat, amelyek gyorsítótár mérete és a szolgáltatások rugalmasságot biztosítanak. A gyorsítótár létrehozása után méretezheti méretét és a gyorsítótár az árképzési szint Ha megváltoztatja az alkalmazás követelményeinek. Ez a cikk bemutatja, hogyan méretezése a gyorsítótár az Azure-portálon és Azure PowerShell és az Azure parancssori felület.

## <a name="when-to-scale"></a>Mikor érdemes méretezni
Használhatja a [figyelési](cache-how-to-monitor.md) állapotának és a gyorsítótár teljesítményének figyelésére, és segíthet meghatározni, mikor érdemes méretezni a gyorsítótár Azure Redis Cache-funkcióit. 

A következő metrikákat annak meghatározásához, ha skálázni szeretne figyelheti.

* A kiszolgálóterhelés redis
* Memóriahasználat
* Hálózati sávszélesség
* CPU-használat

Ha azt állapítja meg, hogy a gyorsítótár már nem teljesíti-e az alkalmazás követelményeinek, IP-címek számára az alkalmazás megfelelő kisebb vagy nagyobb gyorsítótárhoz méretezheti. További információ melyik gyorsítótár IP-címek használatára, lásd: [milyen Redis Cache-ajánlatot és méretet használjam](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>A gyorsítótár méretezése
A gyorsítótár méretezési [keresse meg a gyorsítótár](cache-configure.md#configure-redis-cache-settings) a a [Azure-portálon](https://portal.azure.com) kattintson **méretezési** a a **erőforrás menü**.

![Méretezés](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Válassza ki a kívánt tarifacsomagot a **válassza ki az IP-címek** panel megnyitásához, és kattintson **válasszon**.

![Tarifacsomag][redis-cache-pricing-tier-blade]


Méretezhető egy másik tarifacsomagra a következő korlátozásokkal:

* Egy alacsonyabb tarifacsomagra, méretezhető nem a magasabb szintű tarifacsomagban használható.
  * Nem lehet méretezni a egy **prémium** le a gyorsítótár egy **szabványos** vagy egy **alapvető** gyorsítótár.
  * Nem lehet méretezni a egy **szabványos** le a gyorsítótár egy **alapvető** gyorsítótár.
* A méretezheti a **alapvető** gyorsítótárba egy **szabványos** gyorsítótár, de nem módosíthatja a méretét egyszerre. Ha különböző méretű van szüksége, végezhet egy későbbi skálázási műveletet, hogy a kívánt méretet.
* Nem lehet méretezni a egy **alapvető** gyorsítótár közvetlenül egy **prémium** gyorsítótár. Kell méretezni a **alapvető** való **szabványos** egy skálázási műveletet, majd a **szabványos** való **prémium** a későbbi skálázás a műveletet.
* A nagyobb méretű le nem lehet méretezni a **C0 csomag (250 MB)** méretét.
 
Amíg a gyorsítótár az új tarifacsomagra méretezése folyik a **méretezés** állapota megjelenik a **Redis Cache** panelen.

![Méretezés][redis-cache-scaling]

Skálázás befejeződése után állapota a **méretezés** való **futtató**.

## <a name="how-to-automate-a-scaling-operation"></a>A méretezési művelet automatizálása
A gyorsítótár-példányok méretezési az Azure-portálon, mellett méretezheti PowerShell-parancsmagok, az Azure parancssori felület használatával és a Microsoft Azure felügyeleti függvénytárak (MAML) használatával. 

* [A skála PowerShell használatával](#scale-using-powershell)
* [Scale Azure parancssori felület használatával](#scale-using-azure-cli)
* [A skála MAML használatával](#scale-using-maml)

### <a name="scale-using-powershell"></a>A skála PowerShell használatával
Az Azure Redis Cache példány a PowerShell használatával méretezheti a [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) parancsmag amikor a `Size`, `Sku`, vagy `ShardCount` tulajdonság módosítását mutatjuk be. A következő példa bemutatja, hogyan nevű gyorsítótár méretezési `myCache` 2,5 GB gyorsítótárhoz. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

A skálázás a PowerShell használatával további információkért lásd: [méretezése a Powershell használatával Redis gyorsítótár](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Scale Azure parancssori felület használatával
Az Azure parancssori felület használatával Azure Redis Cache példány méretezése, hívja meg a `azure rediscache set` parancsot, és adja át a kívánt konfigurációs módosításokat, amely egy új méretét, a termékváltozat, illetve a fürt méretét, attól függően, hogy a méretezési műveletet.

Az Azure parancssori felülettel skálázás további információkért lásd: [egy meglévő Redis gyorsítótár beállításainak módosítása](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>A skála MAML használatával
Méretezési az Azure Redis Cache-példányok használata a [Microsoft Azure felügyeleti függvénytárak (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), hívja az `IRedisOperations.CreateOrUpdate` metódus, és új mérete adjon át a `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

További információkért lásd: a [kezelése Redis Cache segítségével MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) minta.

## <a name="scaling-faq"></a>Méretezési – gyakori kérdések
Az alábbi lista tartalmazza az Azure Redis Cache skálázás gyakran feltett kérdésekre adott válaszok.

* [A, vagy a prémium szintű gyorsítótár is méretezhető?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Skálázás, után kell módosítani a gyorsítótár neve vagy a hozzáférési kulcsainak?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Skálázás működése](#how-does-scaling-work)
* [I adat elvész a gyorsítótárból skálázás során?](#will-i-lose-data-from-my-cache-during-scaling)
* [Az egyéni adatbázisok állít érintett skálázás során?](#is-my-custom-databases-setting-affected-during-scaling)
* [A gyorsítótár elérhető lesz skálázás során?](#will-my-cache-be-available-during-scaling)
* [Nem támogatott műveletek](#operations-that-are-not-supported)
* [Mennyi időt skálázás igénybe?](#how-long-does-scaling-take)
* [Hogyan állapítható meg, hogy ha skálázás befejeződött?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>A, vagy a prémium szintű gyorsítótár is méretezhető?
* Nem lehet méretezni a egy **prémium** le a gyorsítótár egy **alapvető** vagy **szabványos** tarifacsomagra vált.
* Az egyik méretezheti **prémium** egy másik tarifacsomagban gyorsítótár.
* Nem lehet méretezni a egy **alapvető** gyorsítótár közvetlenül egy **prémium** gyorsítótár. Először át kell méretezni **alapvető** való **szabványos** egy skálázási műveletet, majd a **szabványos** való **prémium** egy későbbi skálázási művelet.
* Ha engedélyezte a fürtszolgáltatás létrehozásakor a **prémium** gyorsítótárában, akkor [a fürt méretének módosítása](cache-how-to-premium-clustering.md#cluster-size). Ha a gyorsítótár fürtszolgáltatás engedélyezése nélkül hozták létre, akkor nem konfigurálhatja a fürtözést egy későbbi időpontban.
  
  További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md) (Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Skálázás, után kell módosítani a gyorsítótár neve vagy a hozzáférési kulcsainak?
Nem, a gyorsítótár neve, valamint a kulcsok nem változik a méretezési művelet során.

### <a name="how-does-scaling-work"></a>Skálázás működése
* Ha egy **alapvető** különböző méretű gyorsítótár van méretezhető, le van állítva, és új gyorsítótárat ki van építve, új méret használatával. Ebben az időszakban a gyorsítótár nem érhető el, és a gyorsítótárban lévő összes adatot elvész.
* Ha egy **alapvető** gyorsítótár méretezve, hogy egy **szabványos** gyorsítótár, a replika gyorsítótár ki van építve, és az adatok másolja az elsődleges gyorsítótár a replika gyorsítótárba. A gyorsítótár a méretezés közben elérhető marad.
* Ha egy **szabványos** gyorsítótár méretezett egy másik értékre, vagy egy **prémium** gyorsítótár, a replikák közül leáll, és újra létrehozni, hogy az új méretet és az átvitt adatokat, majd a másik replika a feladatátvétel hajt végre, ahhoz, hogy újra kiépített, a folyamat során. a gyorsítótár-csomópontok közül az egyik hibát, hasonló.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>I adat elvész a gyorsítótárból skálázás során?
* Ha egy **alapvető** gyorsítótár van méretezve, hogy az új méretét, az összes adat elvész, és a gyorsítótár nem érhető el, a méretezési művelet során.
* Ha egy **alapvető** gyorsítótár méretezve, hogy egy **szabványos** gyorsítótár, a gyorsítótárban lévő adatok általában megőrződik.
* Ha egy **szabványos** gyorsítótár van méretezve, hogy egy nagyobb méretű vagy a réteg, vagy egy **prémium** gyorsítótár méretezett nagyobb méretűre, általában megmaradjon az összes adatot. Amikor skálázás egy **szabványos** vagy **prémium** gyorsítótár le egy kisebb méretet, az adatok elveszhetnek attól függően, hogy mennyi adatot a gyorsítótárban során azt méretezett kapcsolatos új méretét. Adat elvész, amikor a skálázás, ha a kulcsok kizárt vannak-e használata a [allkeys-lru](http://redis.io/topics/lru-cache) kiürítés házirend. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Az egyéni adatbázisok állít érintett skálázás során?
Különböző tartalmaznak az egyes tarifacsomagok [korlátok adatbázisok](cache-configure.md#databases), úgy, hogy nincs szempontokat konfigurálásakor skálázás Ha, egyéni értéket a `databases` beállítása a gyorsítótár létrehozása közben.

* Ha a tarifacsomag alsó méretezhetők `databases` a jelenlegi rétegtől határa:
  * Alapértelmezett számának használata `databases` ez 16 az összes árképzési szinteket, adatok nem vesztek el.
  * Ha egyéni számos használ `databases` , amely esik a határokon belül a réteget, amelyhez van folyamatban, ez a `databases` beállítás akkor is megmarad, és az adatok nem vesztek el.
  * Ha egyéni számos használ `databases` , amely meghaladja a új réteg a `databases` beállítás van az új réteget határain szintűre csökkent, és az eltávolított adatbázisokat az összes adat elvész.
* Ha a tarifacsomagot az azonos vagy újabb méretezhetők `databases` korlátot, mint a jelenlegi rétegtől a `databases` beállítás akkor is megmarad, és az adatok nem vesztek el.

Vegye figyelembe, hogy míg a Standard és Premium gyorsítótárak egy 99,9 %-os SLA-t a rendelkezésre állás érdekében, az adatvesztés nélküli SLA.

### <a name="will-my-cache-be-available-during-scaling"></a>A gyorsítótár elérhető lesz skálázás során?
* **Standard** és **prémium** gyorsítótárak elérhetők maradnak a méretezési művelet során.
* **Alapszintű** gyorsítótárak skálázás különböző méretű műveletek során offline módban, de elérhetők maradnak, ha a méretezés **alapvető** való **szabványos**.

### <a name="operations-that-are-not-supported"></a>Nem támogatott műveletek
* Egy alacsonyabb tarifacsomagra, méretezhető nem a magasabb szintű tarifacsomagban használható.
  * Nem lehet méretezni a egy **prémium** le a gyorsítótár egy **szabványos** vagy egy **alapvető** gyorsítótár.
  * Nem lehet méretezni a egy **szabványos** le a gyorsítótár egy **alapvető** gyorsítótár.
* A méretezheti a **alapvető** gyorsítótárba egy **szabványos** gyorsítótár, de nem módosíthatja a méretét egyszerre. Ha különböző méretű van szüksége, végezhet egy későbbi skálázási műveletet, hogy a kívánt méretet.
* Nem lehet méretezni a egy **alapvető** gyorsítótár közvetlenül egy **prémium** gyorsítótár. Először át kell méretezni **alapvető** való **szabványos** egy skálázási műveletet, majd a **szabványos** való **prémium** egy későbbi skálázási művelet.
* A nagyobb méretű le nem lehet méretezni a **C0 csomag (250 MB)** méretét.

A méretezési művelet sikertelen lesz, ha a szolgáltatás megpróbál visszaállítani a műveletet, és a gyorsítótár visszaáll az eredeti méret.

### <a name="how-long-does-scaling-take"></a>Mennyi időt skálázás igénybe?
Skálázás vesz körülbelül 20 percet, attól függően, hogy mennyi adatot a gyorsítótárban.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hogyan állapítható meg, hogy ha skálázás befejeződött?
Az Azure-portálon tekintheti meg a skálázási művelet folyamatban van. Ha skálázás befejeződött, a gyorsítótár állapota a **futtató**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



