---
title: Az Azure Cache for Redis kezelése az Azure PowerShell segítségével
description: Ismerje meg, hogyan hajthat végre felügyeleti feladatokat az Azure Cache for Redis számára az Azure PowerShell használatával.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: a385d3ed7ef46389f96de72c98ffc29cebf60ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278531"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Az Azure Cache for Redis kezelése az Azure PowerShell segítségével
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a témakör bemutatja, hogyan hajthatja végre a gyakori feladatokat, például az Azure-gyorsítótár létrehozása, frissítése és méretezése a Redis-példányok, hogyan lehet újragenerálni a hozzáférési kulcsokat, és hogyan tekintheti meg a gyorsítótárakkal kapcsolatos információkat. A Redis PowerShell-parancsmagok Azure Cache gyorsítótárának teljes listáját az [Azure Cache for Redis parancsmagok című témakörben található.](https://docs.microsoft.com/powershell/module/az.rediscache)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

A klasszikus üzembe helyezési modellről az [Azure Resource Manager és a klasszikus üzembe helyezés: Az üzembe helyezési modellek és az erőforrások állapotának megismerése](../azure-resource-manager/management/deployment-models.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek
Ha már telepítette az Azure PowerShellt, az Azure PowerShell 1.0.0-s vagy újabb verziójával kell rendelkeznie. Az Azure PowerShell parancssorában ellenőrizheti az Azure PowerShell ezzel a paranccsal telepített verzióját.

    Get-Module Az | format-table version


Először ezzel a paranccsal kell bejelentkeznie az Azure-ba.

    Connect-AzAccount

Adja meg az Azure-fiók e-mail címét és jelszavát a Microsoft Azure bejelentkezési párbeszédpanelén.

Ezután ha több Azure-előfizetéssel rendelkezik, be kell állítania az Azure-előfizetést. Az aktuális előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Az előfizetés megadásához futtassa a következő parancsot. A következő példában az `ContosoSubscription`előfizetés neve .

    Select-AzSubscription -SubscriptionName ContosoSubscription

A Windows PowerShell azure Resource Managerrel való használata előtt a következőkre van szüksége:

* Windows PowerShell, 3.0-s vagy 4.0-s verzió. A Windows PowerShell verziójának megkereséséhez írja be a következőt,`$PSVersionTable` és ellenőrizze a 3.0 vagy 4.0 értékét. `PSVersion` Kompatibilis verzió telepítéséről a [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) vagy [a Windows Management Framework 4.0 című](https://www.microsoft.com/download/details.aspx?id=40855)témakörben van.

Az oktatóanyagban látható parancsmagok részletes használatához használja a Súgó parancsmagát.

    Get-Help <cmdlet-name> -Detailed

A parancsmaghoz például `New-AzRedisCache` írja be a következőt:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Hogyan lehet csatlakozni más felhők
Alapértelmezés szerint az `AzureCloud`Azure-környezet , amely a globális Azure-felhőpéldányt jelöli. Ha másik példányhoz szeretne `Connect-AzAccount` csatlakozni, `-Environment` használja`EnvironmentName` a parancsot a vagy - parancssori kapcsolóval a kívánt környezet vagy környezet nevével.

Az elérhető környezetek listájának megtekintéséhez futtassa a `Get-AzEnvironment` parancsmalistát.

### <a name="to-connect-to-the-azure-government-cloud"></a>Csatlakozás az Azure Government Felhőhöz
Az Azure Government Cloudhoz való csatlakozáshoz használja az alábbi parancsok egyikét.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

vagy

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Gyorsítótár létrehozásához az Azure Government Cloud, használja az alábbi helyeken.

* USGov Virginia
* USGov Iowa

Az Azure Government Cloud szolgáltatásról a [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) és a Microsoft Azure Government [fejlesztői útmutatójában talál](../azure-government-developer-guide.md)további információt.

### <a name="to-connect-to-the-azure-china-cloud"></a>Csatlakozás az Azure China Cloud szolgáltatáshoz
Az Azure China Cloud hoz való csatlakozáshoz használja az alábbi parancsok egyikét.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

vagy

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Gyorsítótár létrehozásához az Azure China Cloud, használja az alábbi helyeken.

* Kelet-Kína
* Észak-Kína

Az Azure China Cloud szolgáltatásról a [21Vianet által kínában üzemeltetett Azure-beli Azure](https://www.windowsazure.cn/)című témakörben talál további információt.

### <a name="to-connect-to-microsoft-azure-germany"></a>Csatlakozás a Microsoft Azure Németországhoz
A Microsoft Azure Germany szolgáltatáshoz való csatlakozáshoz használja az alábbi parancsok egyikét.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


vagy

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Ha gyorsítótárat szeretne létrehozni a Microsoft Azure Németországban, használja az alábbi helyek egyikét.

* Közép-Németország
* Északkelet-Németország

A Microsoft Azure Germany szolgáltatásról további információt a [Microsoft Azure Germany című témakörben talál.](https://azure.microsoft.com/overview/clouds/germany/)

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>A Redis PowerShell azure-gyorsítótárához használt tulajdonságok
Az alábbi táblázat az Azure-gyorsítótár a Redis-példányok hozásaés és kezelése során az Azure PowerShell használatával az általánosan használt paraméterek tulajdonságait és leírását tartalmazza.

| Paraméter | Leírás | Alapértelmezett |
| --- | --- | --- |
| Név |A gyorsítótár neve | |
| Hely |A gyorsítótár helye | |
| ResourceGroupName |Erőforráscsoport neve, amelyben a gyorsítótár at létre szeretné hozni | |
| Méret |A gyorsítótár mérete. Érvényes értékek: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount között |A fürtözés engedélyezésével létrehozott szegmensek száma. Érvényes értékek: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |A gyorsítótár termékváltozatát adja meg. Érvényes értékek: Alap, Standard, Premium |Standard |
| RedisConfiguration |A Redis konfigurációs beállításait adja meg. Az egyes beállításokkal kapcsolatos részletekért tekintse meg a következő [RedisConfiguration tulajdonságtáblát.](#redisconfiguration-properties) | |
| EnableNonSslPort |Azt jelzi, hogy a nem SSL-port engedélyezve van-e. |False (Hamis) |
| MaxMemoryPolicy |Ez a paraméter elavult - használja a RedisConfiguration helyett. | |
| Statikus IP |Ha a gyorsítótárat egy virtuális hálózatban üzemelteti, egy egyedi IP-címet ad meg a gyorsítótár alhálózatában. Ha nincs megadva, az alhálózatból kiválasztanak egyet. | |
| Alhálózat |Amikor a gyorsítótárat egy virtuális hálózatban üzemelteti, megadja annak az alhálózatnak a nevét, amelyben a gyorsítótárat telepíteni szeretné. | |
| VirtualNetwork |Amikor a gyorsítótárat egy virtuális hálózatban üzemelteti, megadja a virtuális hálózat erőforrás-azonosítóját, amelyben a gyorsítótárat telepíteni. | |
| Kulcstípus |Itt adható meg, hogy a hozzáférési kulcsok megújításakor melyik hozzáférési kulcsot kell újragenerálni. Érvényes értékek: Elsődleges, Másodlagos | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration tulajdonságai
| Tulajdonság | Leírás | Árképzési szintek |
| --- | --- | --- |
| rdb-backup-kompatibilis |A [Redis adatmegőrzés](cache-how-to-premium-persistence.md) engedélyezve van-e |Csak prémium |
| rdb-storage-connection-string |A [Redis-adatmegőrzési](cache-how-to-premium-persistence.md) tárfiókhoz való kapcsolati karakterlánc |Csak prémium |
| rdb-backup-frekvencia |A [Redis-adatok megőrzési gyakorisága](cache-how-to-premium-persistence.md) |Csak prémium |
| maxmemory-fenntartott |A nem gyorsítótárazási folyamatok számára [fenntartott memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) konfigurálása |Standard és Premium |
| maxmemory-politika |A gyorsítótár [kilakoltatási házirendjének](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) konfigurálása |Minden tarifacsomag |
| notify-keyspace-események |[Kulcstér-értesítések](cache-configure.md#keyspace-notifications-advanced-settings) konfigurálása |Standard és Premium |
| kivonat-max-ziplist-bejegyzések |Kis összesítő adattípusok [memóriaoptimalizálásának](https://redis.io/topics/memory-optimization) konfigurálása |Standard és Premium |
| kivonat-max-ziplist-érték |Kis összesítő adattípusok [memóriaoptimalizálásának](https://redis.io/topics/memory-optimization) konfigurálása |Standard és Premium |
| set-max-intset-bejegyzések |Kis összesítő adattípusok [memóriaoptimalizálásának](https://redis.io/topics/memory-optimization) konfigurálása |Standard és Premium |
| zset-max-ziplist-bejegyzések |Kis összesítő adattípusok [memóriaoptimalizálásának](https://redis.io/topics/memory-optimization) konfigurálása |Standard és Premium |
| zset-max-ziplist-érték |Kis összesítő adattípusok [memóriaoptimalizálásának](https://redis.io/topics/memory-optimization) konfigurálása |Standard és Premium |
| adatbázisok |Az adatbázisok számának konfigurálása. Ez a tulajdonság csak a gyorsítótár létrehozásakor konfigurálható. |Standard és Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Azure-gyorsítótár létrehozása a Redis számára
A Redis-példányok új Azure-gyorsítótárai a [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) parancsmag használatával jönnek létre.

> [!IMPORTANT]
> Az első alkalommal, amikor létrehoz egy Azure-cache redis egy előfizetésben `Microsoft.Cache` az Azure Portalon, a portál regisztrálja a névteret az adott előfizetéshez. Ha megpróbálja létrehozni az első Azure-gyorsítótárat a Redis egy előfizetésben a PowerShell használatával, először regisztrálnia kell, hogy a névtér a következő paranccsal; egyébként parancsmagok, `New-AzRedisCache` `Get-AzRedisCache` mint például, és nem sikerül.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Az elérhető paraméterek és azok leírásának `New-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Ha alapértelmezett paraméterekkel rendelkező gyorsítótárat szeretne létrehozni, futtassa a következő parancsot.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, `Location` és kötelező paraméterek, de a többi nem kötelező, és alapértelmezett értékekkel rendelkezik. Az előző parancs futtatása létrehoz egy standard SKU Azure-gyorsítótár redis példány a megadott névvel, helyés erőforráscsoport, amely 1 GB méretű, a nem SSL-port le van tiltva.

Prémium szintű gyorsítótár létrehozásához adjon meg P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) vagy P4 (53 GB – 530 GB) méretet. Fürtözés engedélyezéséhez adja meg a `ShardCount` shard count a paraméter használatával. A következő példa létrehoz egy P1 prémium szintű gyorsítótárat 3 szegmenssel. A P1 prémium szintű gyorsítótár mérete 6 GB, és mivel három szilánkot adtunk meg, a teljes méret 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

A paraméter értékeinek `RedisConfiguration` megadásához a `{}` kulcs-/értékpárokhoz `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`hasonló kulcs-/értékpárokként csatolja a belső értékeket. A következő példa egy szabványos `allkeys-random` 1 GB-os gyorsítótárat `KEA`hoz létre a maxmemory házirenddel és a keyspace értesítésekkel konfigurálva. További információt a [Keyspace-értesítések (speciális beállítások)](cache-configure.md#keyspace-notifications-advanced-settings) és [a Memóriaházirendek című témakörben talál.](cache-configure.md#memory-policies)

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Az adatbázisok beállításának konfigurálása a gyorsítótár létrehozása során
A `databases` beállítás csak a gyorsítótár létrehozása során konfigurálható. A következő példa létrehoz egy prémium P3 (26 GB) gyorsítótárat 48 adatbázissal a [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) parancsmag használatával.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

A tulajdonságról `databases` további információt a [Redis-kiszolgáló alapértelmezett Azure-gyorsítótára című](cache-configure.md#default-redis-server-configuration)témakörben talál. A [gyorsítótár New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) parancsmag használatával történő létrehozásáról további információt az előző Az Azure-gyorsítótár létrehozása a Redis-hez című szakaszban talál.

## <a name="to-update-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárának frissítése
A Redis-példányok Azure-gyorsítótáraak a [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) parancsmag használatával frissülnek.

Az elérhető paraméterek és azok leírásának `Set-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

A `Set-AzRedisCache` parancsmag használható a tulajdonságok , `Size` `Sku`például a , , `EnableNonSslPort`és az `RedisConfiguration` értékek frissítésére. 

A következő parancs frissíti a redis nevű Azure Cache maxmemory-házirendet.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárának méretezése
`Set-AzRedisCache`A , `Size` `Sku`vagy `ShardCount` tulajdonságok módosításakor azure-gyorsítótár a Redis-példány méretezésére használható. 

> [!NOTE]
> A PowerShell használatával a gyorsítótár méretezése ugyanazokat a korlátokat és irányelveket, mint a gyorsítótár méretezése az Azure Portalon. A következő korlátozásokkal egy másik tarifacsomagra méretezhető.
> 
> * Nem skálázható magasabb tarifacsomagról alacsonyabb tarifacsomagra.
> * Prémium **szintű** gyorsítótárról nem skálázható **standard** vagy **alapszintű** gyorsítótárra.
> * **A standard** gyorsítótárról nem skálázható le **egy alapszintű** gyorsítótárra.
> * Az **alapszintű** gyorsítótárról **a szabványos** gyorsítótárra skálázható, de a méretet nem módosíthatja egyszerre. Ha más méretre van szüksége, elvégezhet egy további méretezési műveletet a kívánt méretre.
> * **Az alapszintű** gyorsítótárból nem skálázható közvetlenül egy **prémium szintű** gyorsítótárba. Egy skálázási műveletben **alapszintűről** **standardra** kell skáláznia, majd **standardról prémiumra** egy későbbi skálázási műveletben. **Premium**
> * A nagyobb méretről a **C0 (250 MB)** méretre nem lehet skálázni.
> 
> További információ: [Az Azure-gyorsítótár méretezése a Redis számára](cache-how-to-scale.md)című témakörben talál.
> 
> 

A következő példa bemutatja, `myCache` hogyan skálázható egy 2,5 GB-os gyorsítótárba nevezett gyorsítótár. Ne feledje, hogy ez a parancs alapszintű vagy szabványos gyorsítótárban is működik.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

A parancs kiadása után a rendszer visszaadja a `Get-AzRedisCache`gyorsítótár állapotát (hasonlóan a híváshoz). Ne feledje, hogy az `ProvisioningState` a `Scaling`.

    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Amikor a skálázási `ProvisioningState` művelet `Succeeded`befejeződött, a változások a. Ha egy későbbi skálázási műveletet kell végrehajtania, például alapszintűről normálra kell váltanod, majd módosítania kell a méretet, meg kell várnia, amíg az előző művelet befejeződik, vagy az alábbihoz hasonló hibaüzenetjelenik meg.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Információk beolvasása a Redis Azure-gyorsítótáráról
A gyorsítótárral kapcsolatos információkat a [Get-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) parancsmag segítségével kérheti le.

Az elérhető paraméterek és azok leírásának `Get-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Az aktuális előfizetés összes gyorsítótárával kapcsolatos `Get-AzRedisCache` információk visszaadásához futtassa a paraméterek nélkül.

    Get-AzRedisCache

Egy adott erőforráscsoport összes gyorsítótárával kapcsolatos `Get-AzRedisCache` információ `ResourceGroupName` visszaadásához futtassa a paraméterrel.

    Get-AzRedisCache -ResourceGroupName myGroup

Egy adott gyorsítótárra vonatkozó `Get-AzRedisCache` információk `Name` visszaadásához futtassa a gyorsítótár `ResourceGroupName` nevét tartalmazó paraméterrel és az adott gyorsítótárat tartalmazó erőforráscsoporttal rendelkező paraméterrel.

    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárhozzáférési kulcsainak beolvasása
A gyorsítótár hozzáférési kulcsainak beolvasásához használhatja a [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) parancsmagját.

Az elérhető paraméterek és azok leírásának `Get-AzRedisCacheKey`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

A gyorsítótár kulcsainak beolvasásához `Get-AzRedisCacheKey` hívja meg a parancsmamot, és adja át a gyorsítótár nevében a gyorsítótárat tartalmazó erőforráscsoport nevét.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Hozzáférési kulcsok újragenerálása az Azure Cache for Redis számára
A gyorsítótár hozzáférési kulcsainak újragenerálásához használja a [New-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) parancsmagot.

Az elérhető paraméterek és azok leírásának `New-AzRedisCacheKey`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

A `New-AzRedisCacheKey` gyorsítótár elsődleges vagy másodlagos kulcsának újragenerálásához hívja meg a parancsmamot, és adja át a nevet, az erőforráscsoportot, és adja meg a paramétert `Primary` vagy `Secondary` a `KeyType` paramétert. A következő példában a gyorsítótár másodlagos hozzáférési kulcsa újragenerálódik.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárának törlése
A Redis Azure-gyorsítótárának törléséhez használja az [Eltávolítás-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) parancsmag.

Az elérhető paraméterek és azok leírásának `Remove-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

A következő példában a `myCache` megnevezett gyorsítótár törlődik.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Azure-gyorsítótár importálása a Redis számára
A parancsmag használatával adatokat importálhat egy `Import-AzRedisCache` Azure-gyorsítótárba a Redis-példányhoz.

> [!IMPORTANT]
> Az importálás/exportálás csak [a prémium szintű](cache-premium-tier-intro.md) gyorsítótárak esetében érhető el. Az importálási/exportálási témakörben további információt az [Adatok importálása és exportálása az Azure Cache for Redis alkalmazásban](cache-how-to-import-export-data.md)című témakörben talál.
> 
> 

Az elérhető paraméterek és azok leírásának `Import-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


A következő parancs adatokat importál a Blob által megadott SAS uri az Azure Cache a Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Azure-gyorsítótár exportálása a Redis számára
A parancsmag használatával exportálhatja az `Export-AzRedisCache` adatokat egy Azure-gyorsítótárból a Redis-példányhoz.

> [!IMPORTANT]
> Az importálás/exportálás csak [a prémium szintű](cache-premium-tier-intro.md) gyorsítótárak esetében érhető el. Az importálási/exportálási témakörben további információt az [Adatok importálása és exportálása az Azure Cache for Redis alkalmazásban](cache-how-to-import-export-data.md)című témakörben talál.
> 
> 

Az elérhető paraméterek és azok leírásának `Export-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


A következő parancs adatokat exportál egy Azure-gyorsítótárból a Redis-példány a SAS uri által megadott tárolóba.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárának újraindítása
Újraindíthatja az Azure Cache for `Reset-AzRedisCache` Redis-példány a parancsmag használatával.

> [!IMPORTANT]
> Újraindítás csak [a prémium réteg gyorsítótárai.](cache-premium-tier-intro.md) A gyorsítótár újraindításáról további információt a Gyorsítótár felügyelete – újraindítás című témakörben [talál.](cache-administration.md#reboot)
> 
> 

Az elérhető paraméterek és azok leírásának `Reset-AzRedisCache`megtekintéséhez futtassa a következő parancsot.

    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


A következő parancs újraindítja a megadott gyorsítótár mindkét csomópontját.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>További lépések
Ha többet szeretne tudni a Windows PowerShell Azure-ral való használatáról, olvassa el az alábbi forrásokat:

* [Azure Cache for Redis parancsmag dokumentáció az MSDN-en](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Azure Resource Manager parancsmagok:](https://go.microsoft.com/fwlink/?LinkID=394765)Ismerje meg az Azure Resource Manager modul parancsmagjainak használatát.
* [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez:](../azure-resource-manager/templates/deploy-portal.md)Ismerje meg, hogyan hozhat létre és kezelhet erőforráscsoportokat az Azure Portalon.
* [Azure-blog:](https://azure.microsoft.com/blog/)Ismerje meg az Azure új funkcióit.
* [Windows PowerShell-blog:](https://blogs.msdn.com/powershell)Ismerje meg a Windows PowerShell új szolgáltatásait.
* ["Hé, Forgatókönyvíró Srác!" Blog:](https://blogs.technet.com/b/heyscriptingguy/)Valós tippeket és trükköket kaphat a Windows PowerShell-közösségtől.

