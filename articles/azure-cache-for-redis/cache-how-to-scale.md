---
title: Az Azure-gyorsítótár méretezése a Redis-hez
description: Megtudhatja, hogyan skálázhatja az Azure Cache for Redis-példányok az Azure Portalon, és az eszközök, például az Azure PowerShell és az Azure CLI.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277998"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Az Azure-gyorsítótár méretezése a Redis-hez
A Redis-gyorsítótár különböző gyorsítótár-ajánlatokkal rendelkezik, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és szolgáltatásainak kiválasztásában. A gyorsítótár létrehozása után méretezheti a gyorsítótár méretét és tarifacsomagját, ha az alkalmazás követelményei megváltoznak. Ez a cikk bemutatja, hogyan skálázhatja a gyorsítótárat az Azure Portalon, és olyan eszközök, például az Azure PowerShell és az Azure CLI használatával.

## <a name="when-to-scale"></a>Mikor érdemes méretezni
Az Azure Cache for Redis [figyelési](cache-how-to-monitor.md) funkcióival figyelheti a gyorsítótár állapotát és teljesítményét, és meghatározhatja, hogy mikor kell méretezni a gyorsítótárat. 

A következő metrikák figyelése, hogy segítsen meghatározni, ha kell a méretezés.

* Redis kiszolgáló betöltése
* Memóriahasználat
* Hálózati sávszélesség
* Processzorhasználat

Ha úgy találja, hogy a gyorsítótár már nem felel meg az alkalmazás követelményeinek, skálázhatja egy nagyobb vagy kisebb gyorsítótár-tarifaszint, amely megfelelő az alkalmazás számára. A használni használandó gyorsítótár-tarifacsomag meghatározásáról a [Mi az Azure-gyorsítótár a Redis-ajánlathoz és a mérethez.](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="scale-a-cache"></a>Gyorsítótár méretezése
A gyorsítótár méretezéséhez [keresse meg a gyorsítótárat](cache-configure.md#configure-azure-cache-for-redis-settings) az [Azure Portalon,](https://portal.azure.com) és kattintson az Erőforrás menü **Méretezés parancsára.** **Resource menu**

![Méretezés](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Válassza ki a kívánt tarifacsomagot a **Csomagbeállítási szint** panelen, és kattintson a **Kijelölés gombra.**

![Tarifacsomag][redis-cache-pricing-tier-blade]


A következő korlátozásokkal skálázhat egy másik tarifacsomagot:

* Nem skálázható magasabb tarifacsomagról alacsonyabb tarifacsomagra.
  * Prémium **szintű** gyorsítótárról nem skálázható **standard** vagy **alapszintű** gyorsítótárra.
  * **A standard** gyorsítótárról nem skálázható le **egy alapszintű** gyorsítótárra.
* Az **alapszintű** gyorsítótárról **a szabványos** gyorsítótárra skálázható, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, elvégezhet egy további méretezési műveletet a kívánt méretre.
* **Az alapszintű** gyorsítótárból nem skálázható közvetlenül egy **prémium szintű** gyorsítótárba. Először egy skálázási műveletben, majd **standardról** **prémium** ra skálázható egy későbbi skálázási műveletben az **alapszintűről** **a standardra** skálázási műveletben.
* A nagyobb méretről a **C0 (250 MB)** méretre nem lehet skálázni.
 
Amíg a gyorsítótár az új tarifacsomagra skáláz, a **Redis-panel azure-gyorsítótárában** megjelenik egy **skálázási** állapot.

![Méretezés][redis-cache-scaling]

Ha a méretezés befejeződött, az állapot **méretezésről** **futásra változik.**

## <a name="how-to-automate-a-scaling-operation"></a>Méretezési művelet automatizálása
A gyorsítótár-példányok Azure Portalon való méretezése mellett a PowerShell-parancsmagokkal, az Azure CLI-vel és a Microsoft Azure Management Libraries (MAML) használatával is skálázható. 

* [Méretezés a PowerShell használatával](#scale-using-powershell)
* [Méretezés az Azure CLI használatával](#scale-using-azure-cli)
* [Méretezés MAML-rel](#scale-using-maml)

### <a name="scale-using-powershell"></a>Méretezés a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A , vagy `ShardCount` a tulajdonságok módosításakor a , [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) az Azure Cache for Redis-példányok powershell használatával skálázhatja az `Size` `Sku`Azure Cache for PowerShell használatával. A következő példa bemutatja, `myCache` hogyan skálázható egy 2,5 GB-os gyorsítótárba nevezett gyorsítótár. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

A PowerShell használatával történő méretezésről a PowerShell használatával a [Redis-hez való Azure-gyorsítótár méretezése](cache-how-to-manage-redis-cache-powershell.md#scale)című témakörben talál további információt.

### <a name="scale-using-azure-cli"></a>Méretezés az Azure CLI használatával
Az Azure Cache for Redis-példányok méretezéséhez `azure rediscache set` az Azure CLI használatával, hívja meg a parancsot, és adja át a kívánt konfigurációs módosításokat, amelyek tartalmazzák az új méret, termékváltozat vagy fürt mérete, a kívánt skálázási művelettől függően.

Az Azure CLI használatával való skálázásról a [Redis meglévő Azure-gyorsítótárbeállításainak módosítása](cache-manage-cli.md#scale)című témakörben olvashat bővebben.

### <a name="scale-using-maml"></a>Méretezés MAML-rel
A Redis-példányok Azure-gyorsítótárának méretezéséhez a [Microsoft Azure Management Libraries (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)használatával hívja meg a `IRedisOperations.CreateOrUpdate` metódust, és adja át a rendszer új `RedisProperties.SKU.Capacity`méretét.

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

További információkért tekintse meg az [Azure-gyorsítótár kezelése a Redis maML-minta használatával.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>Méretezés – gyakori kérdések
Az alábbi lista az Azure-gyorsítótár redis méretezéssel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Skálázható prémium szintű gyorsítótárba, a b., vagy a prémium szintű gyorsítótáron belülre?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Méretezés után meg kell változtatnom a gyorsítótár nevét vagy a hozzáférési kulcsokat?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hogyan működik a méretezés?](#how-does-scaling-work)
* [Elvesznek az adatok a gyorsítótárból a méretezés során?](#will-i-lose-data-from-my-cache-during-scaling)
* [Az egyéni adatbázisok beállítása hatással van a méretezés során?](#is-my-custom-databases-setting-affected-during-scaling)
* [Elérhető lesz a gyorsítótár a méretezés során?](#will-my-cache-be-available-during-scaling)
* A georeplikáció konfigurálva, miért nem vagyok képes a gyorsítótár méretezése vagy a szegmensek módosítása a fürtben?
* [Nem támogatott műveletek](#operations-that-are-not-supported)
* [Mennyi ideig tart a méretezés?](#how-long-does-scaling-take)
* [Hogyan állapítható meg, hogy a méretezés befejeződött?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Skálázható prémium szintű gyorsítótárba, a b., vagy a prémium szintű gyorsítótáron belülre?
* Prémium **szintű** gyorsítótárról nem skálázható **alapszintű** vagy **standard** szintű tarifacsomagra.
* Skálázható egyik **prémium szintű** gyorsítótár tarifacsomagról a másikra.
* **Az alapszintű** gyorsítótárból nem skálázható közvetlenül egy **prémium szintű** gyorsítótárba. Először egy skálázási műveletben, majd **standardról** **prémium** ra skálázható egy későbbi skálázási műveletben az **alapszintűről** **a standardra** skálázási műveletben.
* Ha a **prémium szintű** gyorsítótár létrehozásakor engedélyezte a fürtözést, [módosíthatja a fürt méretét.](cache-how-to-premium-clustering.md#cluster-size) Ha a gyorsítótár fürtözés engedélyezése nélkül lett létrehozva, később konfigurálhatja a fürtözést.
  
  További információ: [A fürtözés konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis hez című témakörben.](cache-how-to-premium-clustering.md)

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Méretezés után meg kell változtatnom a gyorsítótár nevét vagy a hozzáférési kulcsokat?
Nem, a gyorsítótár neve és a kulcsok változatlanok maradnak a méretezési művelet során.

### <a name="how-does-scaling-work"></a>Hogyan működik a méretezés?
* Ha egy **alapszintű** gyorsítótár más méretűre van méretezve, leáll, és egy új gyorsítótár van kiépítve az új méret használatával. Ez alatt az idő alatt a gyorsítótár nem érhető el, és a gyorsítótárban lévő összes adat elvész.
* Ha egy **alapszintű** gyorsítótárat **szabványos** gyorsítótárba skáláznak, a replika-gyorsítótár kivan építve, és az adatok az elsődleges gyorsítótárból a replika-gyorsítótárba másolódnak. A gyorsítótár továbbra is elérhető marad a skálázási folyamat során.
* Ha egy **standard** gyorsítótár más méretűre vagy **prémium szintű** gyorsítótárra van méretezve, az egyik replikát leállítja, és az új méretre és az átvitt adatokra állítja be, majd a másik replika feladatátvételt hajt végre, mielőtt újra kiépítené, hasonlóan ahhoz a folyamathoz, amely a gyorsítótár-csomópontok egyikének meghibásodása során következik be.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Elvesznek az adatok a gyorsítótárból a méretezés során?
* Ha egy **alapszintű** gyorsítótár új méretre van méretezve, az összes adat elvész, és a gyorsítótár nem érhető el a méretezési művelet során.
* Ha egy **alapszintű** **gyorsítótárat szabványos** gyorsítótárba skáláznak, a gyorsítótárban lévő adatok általában megőrződnek.
* Ha egy **standard** gyorsítótár nagyobb méretűre vagy rétegre van méretezve, vagy egy **prémium szintű** gyorsítótár nagyobb méretre van méretezve, az összes adat általában megmarad. **Standard** vagy **prémium szintű** gyorsítótár kisebb méretűre skálázásakor adatok elveszhetnek attól függően, hogy mennyi adat van a gyorsítótárban az új mérethez kapcsolódóan, amikor méretezi. Ha az adatok elvesznek a leskálázás során, a kulcsok az [allkeys-lru](https://redis.io/topics/lru-cache) kilakoltatási házirend használatával kerülnek kizárva. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Az egyéni adatbázisok beállítása hatással van a méretezés során?
Ha a gyorsítótár létrehozása `databases` során egyéni értéket állított be a beállításhoz, ne feledje, hogy egyes tarifacsomagok különböző [adatbázis-korlátokkal rendelkeznek.](cache-configure.md#databases) Íme néhány szempont a méretezéskor ebben a forgatókönyvben:

* Ha az aktuális szintnél alacsonyabb `databases` korláttal rendelkező tarifacsomagra skálázható:
  * Ha az alapértelmezett számú `databases`, amely 16 az összes tarifacsomag, nem vesznek el adatokat.
  * Ha olyan egyéni számot `databases` használ, amely a skálázási szint korlátai `databases` közé esik, ez a beállítás megmarad, és nem vesznek el adatok.
  * Ha olyan egyéni számot `databases` használ, amely meghaladja az új `databases` szint korlátait, a beállítás az új szint korlátaira csökken, és az eltávolított adatbázisokban lévő összes adat elvész.
* Ha az aktuális szinttel azonos vagy `databases` magasabb korláttal rendelkező `databases` tarifacsomagra skáláz, a beállítás megmarad, és nem vesznek el adatok.

Bár a Standard és premium gyorsítótárak 99,9%-os SLA-val rendelkeznek a rendelkezésre álláshoz, az adatvesztéshez nincs SLA.

### <a name="will-my-cache-be-available-during-scaling"></a>Elérhető lesz a gyorsítótár a méretezés során?
* **A standard** és **prémium szintű** gyorsítótárak továbbra is elérhetők maradnak a skálázási művelet során. A kapcsolati pont azonban előfordulhat standard és prémium szintű gyorsítótárak méretezése közben, valamint az alapszintű ről a standard gyorsítótárakra való méretezés re is. Ezek a kapcsolat blips várhatóan kicsi, és redis ügyfelek képesnek kell lennie arra, hogy azonnal létrehozza a kapcsolatot.
* **Az alapszintű** gyorsítótárak offline állapotban vannak a méretezési műveletek során más méretre. Az alapszintű gyorsítótárak továbbra is elérhetők maradnak, ha **alapszintűről** **normálra** skáláznak, de előfordulhat, hogy egy kis kapcsolati blip jelentkezik. Ha egy kapcsolat blip fordul elő, redis ügyfelek képesnek kell lennie arra, hogy azonnal létrehozza a kapcsolatot.


### <a name="scaling-limitations-with-geo-replication"></a>Méretezési korlátozások a georeplikációval

Miután hozzáadott egy georeplikációs kapcsolatot két gyorsítótár között, a továbbiakban nem kezdeményezhet skálázási műveletet, és nem módosíthatja a fürtszegmenseinek számát. A parancsok kiadásához le kell kapcsolnia a gyorsítótárat. További információt a [Georeplikáció konfigurálása](cache-how-to-geo-replication.md)című témakörben talál.


### <a name="operations-that-are-not-supported"></a>Nem támogatott műveletek
* Nem skálázható magasabb tarifacsomagról alacsonyabb tarifacsomagra.
  * Prémium **szintű** gyorsítótárról nem skálázható **standard** vagy **alapszintű** gyorsítótárra.
  * **A standard** gyorsítótárról nem skálázható le **egy alapszintű** gyorsítótárra.
* Az **alapszintű** gyorsítótárról **a szabványos** gyorsítótárra skálázható, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, elvégezhet egy további méretezési műveletet a kívánt méretre.
* **Az alapszintű** gyorsítótárból nem skálázható közvetlenül egy **prémium szintű** gyorsítótárba. Először **az alapszintű** **ről a standardra** skálázható egy skálázási műveletben, majd egy későbbi műveletben **standardról** **prémiumra** skálázható.
* A nagyobb méretről a **C0 (250 MB)** méretre nem lehet skálázni.

Ha egy skálázási művelet sikertelen, a szolgáltatás megpróbálja visszaállítani a műveletet, és a gyorsítótár visszaáll az eredeti méretre.


### <a name="how-long-does-scaling-take"></a>Mennyi ideig tart a méretezés?
A skálázási idő attól függ, hogy mennyi adat van a gyorsítótárban, és a nagyobb mennyiségű adat hosszabb időt vesz igénybe. A méretezés körülbelül 20 percet vesz igénybe. Fürtözött gyorsítótárak esetén a méretezés shardonként körülbelül 20 percet vesz igénybe.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hogyan állapítható meg, hogy a méretezés befejeződött?
Az Azure Portalon láthatja a skálázási művelet folyamatban van. Ha a méretezés befejeződött, a gyorsítótár állapota Futás állapotúra **változik.**

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
