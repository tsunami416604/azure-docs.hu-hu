---
title: "Azure Redis gyorsítótár Azure PowerShell kezelése |} Microsoft Docs"
description: "Útmutató az Azure PowerShell Azure Redis Cache felügyeleti feladatokat hajthat végre."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: sdanie
ms.openlocfilehash: 0a5c95eab3fd01f611fc049e80c5c506857e0b81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Azure Redis gyorsítótár Azure PowerShell kezelése
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

Ez a témakör bemutatja, hogyan végrehajtásához gyakori feladatokat, mint létrehozása, frissítése, és a méret az Azure Redis Cache példányt, a tárelérési kulcsok újragenerálása, és tekintse meg a gyorsítótárak. Azure Redis Cache PowerShell-parancsmagok teljes listáját lásd: [Azure Redis Cache parancsmagok](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

A klasszikus üzembe helyezési modellel kapcsolatos további információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési: üzembe helyezési modellek és az erőforrások állapotát](../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Előfeltételek
Ha már telepítette az Azure PowerShell, rendelkeznie kell Azure PowerShell 1.0.0 verzió vagy újabb. Ellenőrizheti az Azure PowerShell ezzel a paranccsal, az Azure PowerShell-parancssorba telepített verzióját.

    Get-Module azure | format-table version


Először be kell jelentkezni Azure ezzel a paranccsal.

    Login-AzureRmAccount

A Microsoft Azure bejelentkezési párbeszédpanelen adja meg az e-mail cím, az Azure-fiókjával, és a hozzá tartozó jelszó.

Ezután ha több Azure-előfizetéssel rendelkezik, be kell az Azure-előfizetéshez. Az aktuális előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Adja meg az előfizetést, futtassa a következő parancsot. A következő példában az előfizetés neve: `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

A Windows PowerShell használhatja az Azure Resource Manager, a következők szükségesek:

* Windows PowerShell 3.0 vagy 4.0-s verzióját. A Windows PowerShell verziója található, írja be a következőt:`$PSVersionTable` , és ellenőrizze a értékének `PSVersion` 3.0 vagy 4.0-s verzióját. Telepítsen egy kompatibilis verziót, lásd: [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) vagy [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Ebben az oktatóanyagban látja parancsmagokhoz részletes segítséget kérhet, használja a Get-Help parancsmagot.

    Get-Help <cmdlet-name> -Detailed

Segítség a példában a `New-AzureRmRedisCache` parancsmag, típus:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Más felhők csatlakoztatása
Az Azure alapértelmezés szerint a környezete `AzureCloud`, amely globális Azure felhőben példányt jelenti. Szeretne csatlakozni egy másik példányt, használja a `Add-AzureRmAccount` parancsot a `-Environment` vagy -`EnvironmentName` parancssori kapcsolóval a kívánt környezetre vagy a környezet neve.

Rendelkezésre álló környezeteket listájának megtekintéséhez futtassa a `Get-AzureRmEnvironment` parancsmag.

### <a name="to-connect-to-the-azure-government-cloud"></a>Az Azure Government felhőbe való kapcsolódáshoz
Az Azure Government felhő csatlakozni, használja a következő parancsok egyikét.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

vagy

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

A gyorsítótár Azure Government felhőalapú létrehozásához használja az alábbi helyek egyikét.

* USGov Virginia
* USGov Iowa

Az Azure Government felhő kapcsolatos további információkért lásd: [a Microsoft Azure Government](https://azure.microsoft.com/features/gov/) és [Microsoft Azure Government – útmutató fejlesztőknek](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Az Azure Kína felhőbe való kapcsolódáshoz
Az Azure Kína felhő csatlakozni, használja a következő parancsok egyikét.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

vagy

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

A gyorsítótár Azure Kína felhőalapú létrehozásához használja az alábbi helyek egyikét.

* Kelet-Kína
* Észak-Kína

Az Azure Kína felhő kapcsolatos további információkért lásd: [AzureChinaCloud Azure Kínában a 21Vianet által működtetett](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Csatlakozni a Microsoft Azure-Németország
A Microsoft Azure Németország csatlakozni, használja a következő parancsok egyikét.

    Add-AzureRMAccount -EnvironmentName AzureGermanCloud


vagy

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

A Microsoft Azure Németországban a gyorsítótár létrehozásához használja az alábbi helyek egyikét.

* Közép-Németország
* Északkelet-Németország

A Microsoft Azure Németország kapcsolatos további információkért lásd: [Microsoft Azure Németország](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Azure Redis Cache PowerShell használt tulajdonságok
A következő táblázat a tulajdonságok és a gyakran használt paraméterek létrehozása és kezelése az Azure Redis Cache példányt az Azure PowerShell leírását tartalmazza.

| Paraméter | Leírás | Alapértelmezett |
| --- | --- | --- |
| Név |A gyorsítótár neve | |
| Hely |A gyorsítótár helye | |
| erőforráscsoport-név |Az erőforráscsoport neve, amelyben a gyorsítótár létrehozásához | |
| Méret |A gyorsítótár méretét. Érvényes értékek a következők: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1 GB-os, 2.5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount |A szilánkok létrehozása, ha egy prémium szintű gyorsítótár létrehozása fürtözési engedélyezett száma. Érvényes értékek a következők: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Megadja a gyorsítótár a Termékváltozat. Érvényes értékek a következők: Basic, Standard, Premium |Standard |
| RedisConfiguration |Határozza meg a Redis-konfigurációs beállításokat. Az egyes beállítások, lásd a következő [RedisConfiguration tulajdonságok](#redisconfiguration-properties) tábla. | |
| enableNonSslPort |Azt jelzi, hogy engedélyezve van-e a nem SSL port. |False (Hamis) |
| MaxMemoryPolicy |Ez a paraméter elavult – használja inkább a RedisConfiguration. | |
| StaticIP |Esetén a VNETEN belül a gyorsítótárhoz, adja meg egy egyedi IP-cím az alhálózat, a gyorsítótár. Ha nem ad meg, egy választja meg az alhálózatból. | |
| Alhálózat |Esetén a VNETEN belül a gyorsítótár, a neve, amelyben a gyorsítótár telepítendő alhálózat. | |
| VirtualNetwork |Esetén a VNETEN belül a gyorsítótár, a virtuális Hálózatot, amelyben a gyorsítótár telepítendő erőforrás Azonosítóját adja meg. | |
| keyType |Megadja, mely újragenerálni a hozzáférési kulcsok megújításakor elérési kulcsot. Érvényes értékek a következők: elsődleges, másodlagos | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration tulajdonságai
| Tulajdonság | Leírás | Árképzési szintek |
| --- | --- | --- |
| rekordadatbázis biztonsági mentés engedélyezve |E [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) engedélyezve van |Csak a prémium |
| rekordadatbázis-storage-kapcsolat-karakterlánc |A kapcsolati karakterláncot a tárfiók [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |Csak a prémium |
| biztonsági mentés-gyakori rekordadatbázis |A biztonsági mentési gyakorisága [Redis-adatmegőrzés](cache-how-to-premium-persistence.md) |Csak a prémium |
| maxmemory fenntartott |Konfigurálja a [fenntartott memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) nem gyorsítótár folyamatok |Standard és Premium |
| maxmemory-házirend |Konfigurálja a [kiürítés házirend](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) a gyorsítótár |Minden tarifacsomagok |
| értesítés-kulcstérértesítések használatával-események |Konfigurálja az [kulcstérértesítések használatával értesítések](cache-configure.md#keyspace-notifications-advanced-settings) |Standard és Premium |
| kivonat-max-ziplist-bejegyzések |Konfigurálja az [memóriaoptimalizálási](http://redis.io/topics/memory-optimization) kis összesített adatok esetében |Standard és Premium |
| kivonat-max-ziplist-értéke |Konfigurálja az [memóriaoptimalizálási](http://redis.io/topics/memory-optimization) kis összesített adatok esetében |Standard és Premium |
| set-maximális-intset-bejegyzések |Konfigurálja az [memóriaoptimalizálási](http://redis.io/topics/memory-optimization) kis összesített adatok esetében |Standard és Premium |
| zset-maximális-ziplist-bejegyzések |Konfigurálja az [memóriaoptimalizálási](http://redis.io/topics/memory-optimization) kis összesített adatok esetében |Standard és Premium |
| zset-maximális-ziplist-érték |Konfigurálja az [memóriaoptimalizálási](http://redis.io/topics/memory-optimization) kis összesített adatok esetében |Standard és Premium |
| adatbázisok |Konfigurálja az adatbázisok számát. Ez a tulajdonság csak a gyorsítótár létrehozásakor konfigurálható. |Standard és Premium |

## <a name="to-create-a-redis-cache"></a>A Redis Cache létrehozása
Új Azure Redis Cache példány használatával hozhatók létre a [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) parancsmag.

> [!IMPORTANT]
> A Redis gyorsítótár hoz létre egy előfizetést az Azure-portált használja, először a portál regisztrálja a `Microsoft.Cache` adott előfizetéshez tartozó névtér. Ha az első Redis gyorsítótár létrehozása a PowerShell használatával előfizetés, először regisztrálnia kell, hogy a következő paranccsal; névtér például más parancsmagok `New-AzureRmRedisCache` és `Get-AzureRmRedisCache` sikertelen.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

A használható paramétereket, és azok leírásait tartalmazza a lista `New-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

A gyorsítótár létrehozásához alapértelmezett paraméterekkel rendelkező, a következő parancsot.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, és `Location` kötelező paraméterek tartoznak, de a többi opcionálisak, az alapértelmezett értékek szerint vannak. Az előző parancs futtatása hoz létre egy Standard Termékváltozat Azure Redis Cache példány a megadott név, hely és erőforráscsoport, amely 1 GB méretű és a nem SSL port le van tiltva.

A prémium gyorsítótár létrehozásához adjon meg egy mérete P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB), vagy P4 (53 GB - 530 GB). Fürtszolgáltatás engedélyezéséhez használatával megad egy shard száma a `ShardCount` paraméter. A következő példa egy premium P1 gyorsítótár 3 szilánkok hoz létre. Premium P1 gyorsítótár 6 GB-nál, és mivel azt a három szilánkok adott teljes mérete 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Az értékek megadása a `RedisConfiguration` paraméter, tegye az értékek belül `{}` kulcs/érték párok, például `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Az alábbi példakód létrehozza a szabványos 1 GB-os gyorsítótár `allkeys-random` maxmemory a beállított házirend- és kulcstérértesítések használatával értesítések `KEA`. További információkért lásd: [kulcstérértesítések használatával értesítések (Speciális beállítások)](cache-configure.md#keyspace-notifications-advanced-settings) és [memória házirendek](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>A gyorsítótár létrehozása során beállítás-adatbázisok konfigurálása
A `databases` beállítást csak a gyorsítótár létrehozása közben lehet megadni. Az alábbi példakód létrehozza a prémium P3 (26 GB-os) gyorsítótárat használ 48 adatbázisok a [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) parancsmag.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

További információ a `databases` tulajdonság, lásd: [Azure Redis Cache alapértelmezett kiszolgálókonfiguráció](cache-configure.md#default-redis-server-configuration). További létrehozásával kapcsolatos információkat a gyorsítótár használata a [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) parancsmag, tekintse meg az előző [Redis gyorsítótár létrehozásához](#to-create-a-redis-cache) szakasz.

## <a name="to-update-a-redis-cache"></a>A Redis gyorsítótár frissítése
Azure Redis Cache példány frissítése használatával a [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) parancsmag.

A használható paramétereket, és azok leírásait tartalmazza a lista `Set-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

A `Set-AzureRmRedisCache` parancsmag segítségével, mint tulajdonságainak frissítése `Size`, `Sku`, `EnableNonSslPort`, és a `RedisConfiguration` értékeket. 

A következő parancsot a maxmemory-házirend a Redis gyorsítótár myCache nevű frissíti.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>A Redis gyorsítótár méretezése
`Set-AzureRmRedisCache`az Azure Redis Cache-gyorsítótár méretezési használható példány, ha a `Size`, `Sku`, vagy `ShardCount` tulajdonság módosítását mutatjuk be. 

> [!NOTE]
> Skálázás a PowerShell használatával gyorsítótár az azonos korlátok és útmutatók méretezés a gyorsítótár Azure-portálról. A következő korlátozásokkal egy másik tarifacsomagra méretezheti.
> 
> * Egy alacsonyabb tarifacsomagra, méretezhető nem a magasabb szintű tarifacsomagban használható.
> * Nem lehet méretezni a egy **prémium** le a gyorsítótár egy **szabványos** vagy egy **alapvető** gyorsítótár.
> * Nem lehet méretezni a egy **szabványos** le a gyorsítótár egy **alapvető** gyorsítótár.
> * A méretezheti a **alapvető** gyorsítótárba egy **szabványos** gyorsítótár, de nem módosíthatja a méretét egyszerre. Ha különböző méretű van szüksége, végezhet egy későbbi skálázási műveletet, hogy a kívánt méretet.
> * Nem lehet méretezni a egy **alapvető** gyorsítótár közvetlenül egy **prémium** gyorsítótár. Kell méretezni a **alapvető** való **szabványos** egy skálázási műveletet, majd a **szabványos** való **prémium** a későbbi skálázás a műveletet.
> * A nagyobb méretű le nem lehet méretezni a **C0 csomag (250 MB)** méretét.
> 
> További információkért lásd: [Scale Azure Redis Cache hogyan](cache-how-to-scale.md).
> 
> 

A következő példa bemutatja, hogyan nevű gyorsítótár méretezési `myCache` 2,5 GB gyorsítótárhoz. Vegye figyelembe, hogy ez a parancs az alapszintű vagy Standard gyorsítótár is működik-e.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Ez a parancs kiadása után a gyorsítótár állapotának ad vissza (hívása hasonló `Get-AzureRmRedisCache`). Vegye figyelembe, hogy a `ProvisioningState` van `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


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

Ha a méretezési művelet befejeződött, a `ProvisioningState` vált `Succeeded`. Ha meg kell győződnie egy későbbi skálázási művelet, például a Standard módosítása az alapszintű és a méretet, majd módosítja meg kell várnia, amíg az előző művelet befejeződik, vagy az alábbihoz hasonló hibaüzenetet kap.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>A Redis gyorsítótár kapcsolatos adatok
Információ a gyorsítótár kérheti le a [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) parancsmag.

A használható paramétereket, és azok leírásait tartalmazza a lista `Get-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

A jelenlegi előfizetés az összes gyorsítótárak vonatkozó adatokat ad vissza, futtassa a `Get-AzureRmRedisCache` paraméter nélkül.

    Get-AzureRmRedisCache

Egy adott erőforráscsoportban található összes gyorsítótárak vonatkozó adatokat ad vissza, futtassa a `Get-AzureRmRedisCache` rendelkező a `ResourceGroupName` paraméter.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Egy adott gyorsítótár vonatkozó adatokat ad vissza, futtassa a `Get-AzureRmRedisCache` rendelkező a `Name` a nevét, a gyorsítótár tartalmazó paraméter és a `ResourceGroupName` , hogy a gyorsítótár tartalmazó erőforráscsoportot paraméterrel.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

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

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>A Redis gyorsítótár elérési kulcsainak beolvasása
A gyorsítótár elérési kulcsainak lekéréséhez használja a [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) parancsmag.

A használható paramétereket, és azok leírásait tartalmazza a lista `Get-AzureRmRedisCacheKey`, a következő parancsot.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

A gyorsítótár kulcsok lekéréséhez hívja meg a `Get-AzureRmRedisCacheKey` parancsmag, és adja át a gyorsítótár nevében, amely tartalmazza a gyorsítótár az erőforráscsoport nevét.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>A Redis gyorsítótár elérési kulcsainak újbóli
A gyorsítótár elérési kulcsainak újragenerálása, használhatja a [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) parancsmag.

A használható paramétereket, és azok leírásait tartalmazza a lista `New-AzureRmRedisCacheKey`, a következő parancsot.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

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
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Újragenerálja az elsődleges vagy másodlagos kulcsot a gyorsítótárhoz, hívja meg a `New-AzureRmRedisCacheKey` parancsmag és a neve, az erőforráscsoport, adjon át, és adja meg `Primary` vagy `Secondary` a a `KeyType` paraméter. A következő példában a másodlagos hozzáférési kulcsot a gyorsítótár újbóli létrehozása.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>A Redis gyorsítótár törlése
A Redis gyorsítótár törléséhez használja a [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) parancsmag.

A használható paramétereket, és azok leírásait tartalmazza a lista `Remove-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

A következő példában a gyorsítótár nevű `myCache` törlődnek.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>A Redis gyorsítótár importálása
Adatok importálása az Azure Redis Cache példány használatával a `Import-AzureRmRedisCache` parancsmag.

> [!IMPORTANT]
> Importálási/exportálási lehetőség csak a [prémium csomagban](cache-premium-tier-intro.md) gyorsítótárazza. Importálási/exportálási kapcsolatos további információkért lásd: [importálhat és exportálhat adatokat az Azure Redis Cache](cache-how-to-import-export-data.md).
> 
> 

A használható paramétereket, és azok leírásait tartalmazza a lista `Import-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


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
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


A következő parancsot a blobból az Azure Redis Cache SAS URI-azonosítóval megadott importálja az adatokat.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>A Redis gyorsítótár exportálása
Adatok exportálása az Azure Redis Cache példány használatával a `Export-AzureRmRedisCache` parancsmag.

> [!IMPORTANT]
> Importálási/exportálási lehetőség csak a [prémium csomagban](cache-premium-tier-intro.md) gyorsítótárazza. Importálási/exportálási kapcsolatos további információkért lásd: [importálhat és exportálhat adatokat az Azure Redis Cache](cache-how-to-import-export-data.md).
> 
> 

A használható paramétereket, és azok leírásait tartalmazza a lista `Export-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


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
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


A következő parancsot a tárolóba, a SAS URI azonosítója által megadott Azure Redis Cache-példányról exportálja az adatokat.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Újraindítja a Redis gyorsítótár
Az Azure Redis Cache példány használt újraindíthatja a `Reset-AzureRmRedisCache` parancsmag.

> [!IMPORTANT]
> Újraindítás lehetőség csak a [prémium csomagban](cache-premium-tier-intro.md) gyorsítótárazza. A gyorsítótár újraindítás kapcsolatos további információkért lásd: [felügyeleti gyorsítótár - újraindítás](cache-administration.md#reboot).
> 
> 

A használható paramétereket, és azok leírásait tartalmazza a lista `Reset-AzureRmRedisCache`, a következő parancsot.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


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
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


A következő parancsot a megadott gyorsítótár mindkét csomópont újraindul.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Következő lépések
Windows PowerShell használatával az Azure-ral kapcsolatos további tudnivalókért lásd a következőket:

* [Az Azure Redis Cache parancsmag dokumentációja az MSDN webhelyen](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Az Azure Resource Manager parancsmagjainak](http://go.microsoft.com/fwlink/?LinkID=394765): bemutatják, hogyan használhatja a parancsmagok az Azure erőforrás-kezelő modulban.
* [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-template-deploy-portal.md): megtudhatja, hogyan hozhatja létre és kezelheti az erőforráscsoportok az Azure portálon.
* [Az Azure blog](http://blogs.msdn.com/windowsazure): az Azure-ban új szolgáltatásainak megismerése.
* [A Windows PowerShell blog](http://blogs.msdn.com/powershell): a Windows PowerShell új szolgáltatásainak megismerése.
* ["Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): a Windows PowerShell-Közösség valós tippek és trükkök az beszerzése.

