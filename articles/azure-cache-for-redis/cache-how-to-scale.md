---
title: A Redis az Azure Cache méretezése |} A Microsoft Docs
description: Ismerje meg, hogyan skálázható az Azure Cache a Redis-példány
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: wesmc
ms.openlocfilehash: 009fbd3b86518758e9654fab547bab99bec369de
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105202"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>A Redis az Azure Cache méretezése
Az Azure Cache redis rendelkezik másik Cache gyorsítótárazási szolgáltatások, ami rugalmasságot biztosít a gyorsítótár méretét és a szolgáltatások a kiválasztott biztosítanak. Gyorsítótár létrehozása után skálázhatja mérete és a gyorsítótár a tarifacsomagot, ha az alkalmazás a követelmények változnak. Ez a cikk bemutatja, hogyan méretezzünk át a gyorsítótár az Azure portal és az eszközök, például az Azure PowerShell-lel és az Azure CLI használatával.

## <a name="when-to-scale"></a>Mikor érdemes méretezni
Használhatja a [figyelési](cache-how-to-monitor.md) Azure Cache redis állapotának és a gyorsítótár teljesítményének figyeléséhez, és segít meghatározni, hogy mikor érdemes méretezni a gyorsítótár funkcióját. 

A következő metrikák annak meghatározásához, ha méretezni kell követheti nyomon.

* Redis-kiszolgáló terhelése
* Memóriahasználat
* Hálózati sávszélesség
* CPU-használat

Ha azt állapítja meg, hogy a gyorsítótár már nem teljesíti az alkalmazáskövetelményeket, az alkalmazás megfelelő tarifacsomag kisebb vagy nagyobb gyorsítótárhoz skálázhatja. Amely meghatározza, hogy melyik gyorsítótár tarifacsomag használatára vonatkozó további információkért lásd: [milyen az Azure Cache a Redis-ajánlatot és -méretet használjam](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="scale-a-cache"></a>Gyorsítótár méretezése
A gyorsítótár méretezése [keresse meg azt](cache-configure.md#configure-azure-cache-for-redis-settings) a a [az Azure portal](https://portal.azure.com) kattintson **méretezési** a a **erőforrás menüben**.

![Méretezés](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Válassza ki a kívánt tarifacsomagot a **válassza ki a tarifacsomagot** panelre, és kattintson **kiválasztása**.

![Tarifacsomag][redis-cache-pricing-tier-blade]


Méretezhet egy másik tarifacsomagra, a következő korlátozásokkal:

* Nem skálázhatja árképzési szint alacsonyabb tarifacsomagra.
  * A nem skálázhatja egy **prémium szintű** le a gyorsítótár egy **Standard** vagy egy **alapszintű** gyorsítótár.
  * A nem skálázhatja egy **Standard** le a gyorsítótár egy **alapszintű** gyorsítótár.
* Méretezheti a egy **alapszintű** gyorsítótárba egy **Standard** gyorsítótár, de nem módosíthatja a méretét egyszerre. Ha más méretre van szüksége, érdemes egy későbbi skálázási műveletet, hogy a kívánt méretet.
* A nem skálázhatja egy **alapszintű** közvetlenül a gyorsítótár egy **prémium** gyorsítótár. Először virtuális gépből **alapszintű** való **Standard** egy skálázási műveletet, majd a **Standard** való **prémium szintű** az ezt követő méretezése a művelet.
* A nagyobb méretű le a nem skálázhatja a **C0 csomag (250 MB)** méretét.
 
Amíg a gyorsítótár van méretezési lehetőségek érhetők el az új tarifacsomag egy **méretezés** állapota megjelenik a **Azure Cache redis** panelen.

![Méretezés][redis-cache-scaling]

Skálázás befejeződése után az állapot változik a **méretezés** való **futó**.

## <a name="how-to-automate-a-scaling-operation"></a>Hogyan automatizálható egy skálázási művelet
Mellett a cache-példányok skálázása az Azure Portalon, skálázhatja PowerShell-parancsmagokkal, Azure CLI-vel, és a Microsoft Azure kezelési kódtárak (MAML) használatával. 

* [Méretezési csoport PowerShell-lel](#scale-using-powershell)
* [Méretezési csoport Azure CLI használatával](#scale-using-azure-cli)
* [Méretezési csoport MAML használatával](#scale-using-maml)

### <a name="scale-using-powershell"></a>Méretezési csoport PowerShell-lel
Az Azure Cache a Redis-példány a PowerShell használatával méretezheti a [Set-azurermrediscache parancsmag esetében](https://docs.microsoft.com/powershell/module/azurerm.rediscache/set-azurermrediscache?view=azurermps-6.6.0) parancsmag során a `Size`, `Sku`, vagy `ShardCount` tulajdonság módosítását mutatjuk be. Az alábbi példa bemutatja, hogyan nevű gyorsítótár méretezése `myCache` 2,5 GB-gyorsítótárhoz. 

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

A PowerShell-lel méretezéssel kapcsolatos további információkért lásd: [Redis Powershell-lel az Azure Cache méretezését](cache-howto-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Méretezési csoport Azure CLI használatával
A Redis-példány az Azure CLI-vel az Azure gyorsítótár méretezése, hívja meg a `azure rediscache set` parancsot, és adja meg a kívánt konfigurációs módosításokat, amely egy új méretét, a termékváltozat, illetve a fürt méretét, attól függően, a kívánt méretezési műveletet.

Az Azure CLI-vel méretezéssel kapcsolatos további információkért lásd: [Redis egy meglévő Azure-gyorsítótár beállításainak módosítása](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Méretezési csoport MAML használatával
Az Azure Cache a Redis-példány használata méretezését a [a Microsoft Azure kezelési kódtárak (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), hívja a `IRedisOperations.CreateOrUpdate` metódust, és adja meg az új mérete a `RedisProperties.SKU.Capacity`.

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

További információkért lásd: a [redis MAML használata kezelheti Azure Cache](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) minta.

## <a name="scaling-faq"></a>Skálázás – gyakori kérdések
Az alábbi lista Redis méretezése az Azure Cache kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Skálázhatom-e a, a vagy a prémium szintű gyorsítótár belül?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Méretezés, után kell módosítani a gyorsítótár neve vagy a hozzáférési kulcsokat?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Skálázás működése](#how-does-scaling-work)
* [Elveszítek adatokat a gyorsítótárból való méretezése során?](#will-i-lose-data-from-my-cache-during-scaling)
* [Az egyéni adatbázisok érintett beállítás méretezése során?](#is-my-custom-databases-setting-affected-during-scaling)
* [A gyorsítótár lesz elérhető méretezése során?](#will-my-cache-be-available-during-scaling)
* [A georeplikációval konfigurált, miért nem tudok méretezheti a gyorsítótár, vagy módosítsa a szegmensek fürtben?](#scaling-limitations-with-geo-relication)
* [Nem támogatott műveletek](#operations-that-are-not-supported)
* [Mennyi skálázás tart?](#how-long-does-scaling-take)
* [Hogyan állapítható meg, amikor skálázás elkészült?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Skálázhatom-e a, a vagy a prémium szintű gyorsítótár belül?
* A nem skálázhatja egy **prémium szintű** le a gyorsítótár egy **alapszintű** vagy **Standard** tarifacsomag.
* Egy méretezhető **prémium** gyorsítótár tarifacsomag egy másikba.
* A nem skálázhatja egy **alapszintű** közvetlenül a gyorsítótár egy **prémium** gyorsítótár. Először virtuális gépből **alapszintű** való **Standard** egy skálázási műveletet, majd a **Standard** való **prémium szintű** az ezt követő méretezése a művelet.
* Ha engedélyezte a fürtszolgáltatás létrehozásakor a **prémium** gyorsítótár, is [a fürt méretének módosítása](cache-how-to-premium-clustering.md#cluster-size). Ha a gyorsítótár fürtszolgáltatás engedélyezése nélkül hozták létre, konfigurálhatja a fürtszolgáltatás egy későbbi időpontban.
  
  További információkért lásd: [egy prémium szintű Azure Cache redis fürtözés konfigurálása](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Méretezés, után kell módosítani a gyorsítótár neve vagy a hozzáférési kulcsokat?
Nem, a gyorsítótár nevének és a kulcsok nem változik a skálázási művelet során.

### <a name="how-does-scaling-work"></a>Skálázás működése
* Ha egy **alapszintű** gyorsítótár van méretezve, hogy egy másik méretet, le van állítva, és új gyorsítótárat ki van építve az új mérettel. Ebben az időszakban a gyorsítótár nem érhető el, és a gyorsítótárban lévő összes adat elveszik.
* Ha egy **alapszintű** gyorsítótár van méretezve, hogy egy **Standard** gyorsítótár, replika gyorsítótár ki van építve, és az adatok másolja az elsődleges gyorsítótár a replika gyorsítótárhoz. A gyorsítótár a méretezés során elérhető marad.
* Ha egy **Standard** gyorsítótár más méretre és a méretezett egy **prémium** gyorsítótár, a replikák leáll és egy új mérete és az átvitt adatokat, majd a másik replika építeni hajt végre feladatátvételt, még mielőtt kiépíteni, hasonlóan ahhoz, hogy a folyamat, amely hiba esetén az egyik a gyorsítótár-csomópontok.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Elveszítek adatokat a gyorsítótárból való méretezése során?
* Ha egy **alapszintű** gyorsítótár van méretezve, hogy az új méretét, az összes adat elvesztését, és a gyorsítótár nem érhető el, a skálázási művelet során.
* Ha egy **alapszintű** gyorsítótár van méretezve, hogy egy **Standard** gyorsítótár, a gyorsítótárban lévő adatok általában a rendszer megőrzi.
* Ha egy **Standard** gyorsítótár van méretezve, hogy egy nagyobb méretű vagy szint, vagy egy **prémium** gyorsítótár van méretezve, hogy nagyobb méretű, az összes adat általában a rendszer megőrzi. Vertikális egy **Standard** vagy **prémium** gyorsítótár le egy kisebb méretet, az adatok elveszhetnek során az erőforrások méretezése pedig ettől kapcsolatos új mérete a gyorsítótárban van adatok mennyiségétől függően. Adatvesztés esetén vertikális, kulcsok kizárt használatával vannak a [allkeys-lru](https://redis.io/topics/lru-cache) kiürítési szabályzatot. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Az egyéni adatbázisok érintett beállítás méretezése során?
Ha konfigurálta az egyéni értéket a `databases` beállítás gyorsítótár létrehozása során, ne feledje, hogy néhány árképzési szintek rendelkezik másik [adatbázisok korlátai](cache-configure.md#databases). Ebben a forgatókönyvben vertikális az alábbiakban néhány szempontot:

* Az ennél alacsonyabb tarifacsomagra vertikális `databases` , mint a jelenlegi réteg korlát:
  * Ha használja az alapértelmezett száma `databases`, amely minden árképzési szint esetében 16, nem történik adatvesztés.
  * Ha egyéni számos használ `databases` , amely esik a határokon belül, amelyhez Ön rendszer méretezés, ez a szint `databases` beállítás akkor is megmarad, és nem történik adatvesztés.
  * Egyéni számos használatakor `databases` , amely az új csomag korlátozásait meghaladja a `databases` beállítás van csökkenteni annak az új csomag és az eltávolított adatbázisokat az összes adat elveszik.
* Az azonos vagy magasabb szintű tarifacsomagra vertikális `databases` korlátot, az aktuális szinthez, mint a `databases` beállítás akkor is megmarad, és nem történik adatvesztés.

Míg a Standard és prémium gyorsítótárak a szerződésben vállalt 99,9 százalékos rendelkezésre állás érdekében, nincs adatvesztés nem tartozik vállalt SZOLGÁLTATÁSSZINT.

### <a name="will-my-cache-be-available-during-scaling"></a>A gyorsítótár lesz elérhető méretezése során?
* **Standard szintű** és **prémium** gyorsítótárak elérhetők maradnak a skálázási művelet során. Kapcsolat jelekből azonban csak akkor fordulhat elő, miközben a Standard és prémium gyorsítótárak skálázása és az alapszintű, Standard gyorsítótárak skálázása közben. E kapcsolat jelekből várhatóan alacsony, és a redis-ügyfelek kapcsolatot létesíteni a saját azonnal képesnek kell lennie.
* **Alapszintű** alatt méretezési műveletek egy eltérő méretű gyorsítótárak offline is. Alapszintű gyorsítótárak esetében is elérhetőek maradnak a skálázás **alapszintű** való **Standard** , de a egy kis kapcsolat blip tapasztalhat. Ha egy kapcsolat blip történik, a redis-ügyfelek azonnal helyreállítani a kapcsolatot kell lennie.


### <a name="scaling-limitations-with-geo-replication"></a>A georeplikáció skálázási korlátozások

Miután hozzáadta a georeplikációs hivatkozás két gyorsítótárak között, már nem tud indítsa el egy skálázási műveletet, vagy a fürt szegmensei számának módosításához. Ezek a parancsok kiadása a gyorsítótár kell leválasztása. További információkért lásd: [konfigurálása georeplikációs](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Nem támogatott műveletek
* Nem skálázhatja árképzési szint alacsonyabb tarifacsomagra.
  * A nem skálázhatja egy **prémium szintű** le a gyorsítótár egy **Standard** vagy egy **alapszintű** gyorsítótár.
  * A nem skálázhatja egy **Standard** le a gyorsítótár egy **alapszintű** gyorsítótár.
* Méretezheti a egy **alapszintű** gyorsítótárba egy **Standard** gyorsítótár, de nem módosíthatja a méretét egyszerre. Ha más méretre van szüksége, érdemes egy későbbi skálázási műveletet, hogy a kívánt méretet.
* A nem skálázhatja egy **alapszintű** közvetlenül a gyorsítótár egy **prémium** gyorsítótár. Első virtuális gépből **alapszintű** való **Standard** egy skálázási műveletet, és a méretezési csoport **Standard** való **prémium szintű** az egy későbbi a művelet.
* A nagyobb méretű le a nem skálázhatja a **C0 csomag (250 MB)** méretét.

Ha egy skálázási művelet meghiúsul, a szolgáltatás megkísérli a művelet visszaállítja, és a gyorsítótár visszaáll az eredeti méret.


### <a name="how-long-does-scaling-take"></a>Mennyi skálázás tart?
Skálázás veszi körülbelül 20 percig, attól függően, hogy mennyi adatot a gyorsítótárban van.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hogyan állapítható meg, amikor skálázás elkészült?
Az Azure Portalon megtekintheti a skálázási művelet folyamatban van. Skálázás befejeződése után a gyorsítótár állapotra vált **futó**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



