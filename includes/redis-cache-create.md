Gyorsítótár létrehozásához jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Új** > **Adatbázisok** > **Redis Cache** lehetőségre.

> [!NOTE]
> Ha nincs Azure-fiókja, [ingyenesen nyithat egyet](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) mindössze pár perc alatt.
> 
> 

![Új gyorsítótár](media/redis-cache-create/redis-cache-new-cache-menu.png)

> [!NOTE]
> A gyorsítótárak létrehozására az Azure Portalon kívül a Resource Manager-sablonok, a PowerShell vagy az Azure parancssori felület is használható.
> 
> * A gyorsítótár Resource Manager-sablonokkal való létrehozásához lásd: [Create a Redis cache using a template](../articles/redis-cache/cache-redis-cache-arm-provision.md) (Redis Cache létrehozása sablon használatával).
> * A gyorsítótár Azure PowerShell használatával való létrehozásához lásd: [Manage Azure Redis Cache with Azure PowerShell](../articles/redis-cache/cache-howto-manage-redis-cache-powershell.md) (Az Azure Redis Cache kezelése az Azure PowerShellel).
> * A gyorsítótár Azure parancssori felület használatával való létrehozásához lásd: [How to create and manage Azure Redis Cache using the Azure Command-Line Interface (Azure CLI)](../articles/redis-cache/cache-manage-cli.md) (Az Azure Redis Cache létrehozása és kezelése az Azure parancssori felület (Azure CLI) használatával).
> 
> 

Az **Új Redis Cache-gyorsítótár** panelen adja meg a gyorsítótár kívánt beállításait.

![Gyorsítótár létrehozása](media/redis-cache-create/redis-cache-cache-create.png) 

* A **DNS-név** mezőben adja meg a gyorsítótár végpontjához használandó egyedi nevet. A gyorsítótár nevének 1 és 63 karakter közötti karakterláncnak kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A gyorsítótár neve nem kezdődhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.
* Az **Előfizetés** mezőben válassza ki a gyorsítótárhoz használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, ez lesz automatikusan kiválasztva, és az **Előfizetés** legördülő lista nem jelenik meg.
* Az **Erőforráscsoport** listából válasszon ki vagy hozzon létre egy erőforráscsoportot a gyorsítótárhoz. További információk: [Using Resource groups to manage your Azure resources](../articles/azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez). 
* A **Hely** beállítás segítségével megadhatja a földrajzi helyet, ahol a gyorsítótárat üzemeltetni kívánja. A legjobb teljesítmény érdekében a Microsoft határozottan javasolja, hogy a gyorsítótárat ugyanabban a régióban hozza létre, mint ahol ügyfélalkalmazás található.
* A **Tarifacsomag** listával válassza ki a kívánt gyorsítótár-méretet és -funkciókat.
* A **Redis-fürt** használatával 53 GB-nál nagyobb méretű gyorsítótárak hozhatók létre, és részekre bonthatók az adatok több Redis-csomóponton. További információk: [How to configure clustering for a Premium Azure Redis Cache](../articles/redis-cache/cache-how-to-premium-clustering.md) (Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* A **Redis-adatmegőrzés** lehetővé teszi a gyorsítótár adatainak megőrzését egy Azure Storage-fiókban. Útmutató az adatmegőrzés konfigurálásához: [How to configure clustering for a Premium Azure Redis Cache](../articles/redis-cache/cache-how-to-premium-persistence.md) (Adatmegőrzés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* A **virtuális hálózat** magasabb védelmet és elszigeteltséget biztosít, mivel a gyorsítótárhoz csak az adott Azure virtuális hálózatban található ügyfelek férnek hozzá. Használhatja a VNet összes funkcióját, köztük az alhálózatokat, a hozzáférés-vezérlési házirendeket, és a Redishez való hozzáférést korlátozó többi egyéb funkciókat. További információk: [How to configure clustering for a Premium Azure Redis Cache](../articles/redis-cache/cache-how-to-premium-vnet.md) (Virtuális hálózat támogatásának konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
* A nem SSL hozzáférés alapértelmezés szerint le van tiltva az új gyorsítótárakhoz. Nem SSL-port engedélyezéséhez tekintse meg a **6379 számú port zárolásának feloldása (nem SSL titkosított)** részt.

Az új gyorsítótár beállításainak konfigurálása után kattintson a **Létrehozás** lehetőségre. A gyorsítótár létrehozása eltarthat néhány percig. A folyamat állapotát a kezdőpult alján ellenőrizheti. Miután a rendszer létrehozta a gyorsítótárat, az új gyorsítótár **Futó** állapotú, és az [alapértelmezett beállításokkal](../articles/redis-cache/cache-configure.md#default-redis-server-configuration) használatra kész.

![A gyorsítótár létrejött](media/redis-cache-create/redis-cache-cache-created.png)

