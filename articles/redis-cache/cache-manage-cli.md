---
title: Azure parancssori felület használatával Azure Redis Cache kezelése |} Microsoft Docs
description: 'Útmutató: az Azure parancssori felület telepítése bármilyen platformon, az Azure-fiókjába való kapcsolódáshoz használandó, és létrehozását és kezelését a Redis gyorsítótár az Azure parancssori felületen.'
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: fdb0989af2215166b69f10474a0d22aab7b4d593
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911278"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Létrehozása és kezelése az Azure Redis Cache az Azure parancssori felület (CLI) használatával
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
>
>

Az Azure parancssori felület kiváló módja a kezelését az Azure-infrastruktúra bármely platformra. Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti az Azure Redis Cache példányt az Azure parancssori felület használatával.

> [!NOTE]
> Ez a cikk az Azure parancssori felület korábbi verziójára vonatkozik. Tekintse meg a legújabb Azure CLI 2.0 mintaparancsfájlok [Azure CLI Redis gyorsítótár minták](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Létrehozása és kezelése az Azure Redis Cache példány Azure CLI-vel, az alábbi lépéseket kell végrehajtania.

* Azure-fiókkal kell rendelkeznie. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) csak néhány perc múlva.
* [Az Azure parancssori felület telepítése](../cli-install-nodejs.md).
* Csatlakozás az Azure parancssori felület telepítése személyes Azure-fiókkal, vagy a munkahelyi vagy iskolai Azure-fiókra, és jelentkezzen be az Azure parancssori felület használatával a `azure login` parancsot. Különbségek megismeréséhez, és válassza ki, [csatlakozás Azure-előfizetéshez az Azure parancssori felület (CLI)](/cli/azure/authenticate-azure-cli).
* A következő parancsok futtatásához váltson az Azure parancssori felület Resource Manager módra futtatásával a `azure config mode arm` parancsot. További információkért lásd: [Azure-erőforrások és csoportok kezelése az Azure parancssori felület használatával](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Redis gyorsítótár tulajdonságai
Az alábbi tulajdonságokat használja a létrehozása és frissítése a Redis Cache példányt.

| Tulajdonság | Kapcsoló | Leírás |
| --- | --- | --- |
| név |-n,--neve |A Redis gyorsítótárt neve. |
| erőforráscsoport |-g, --resource-group |Az erőforráscsoport neve. |
| location |-l,--helye |Hely gyorsítótár létrehozásához. |
| méret |-z, --size |A Redis gyorsítótár méretét. Érvényes értékek: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x,--termékváltozat |Redis Termékváltozat. Egyike lehet: [alapszintű, Standard, Premium] |
| EnableNonSslPort |-e,--enable-nem-ssl-port |A Redis Cache EnableNonSslPort tulajdonsága. Adja hozzá ezt a jelzőt, ha azt szeretné, hogy a gyorsítótár a nem SSL Port engedélyezéséhez |
| Konfigurációs redis |-c, --redis-configuration |Konfigurációs redis. Konfigurációs kulcsok és értékek itt egy JSON formátumú karakterláncot adjon meg. Formátum: "{" ":""," ":" "}" |
| Konfigurációs redis |-f, --redis-configuration-file |Konfigurációs redis. Konfigurációs kulcsok és értékek itt tartalmazó fájl elérési útját adja meg. A következő fájl bejegyzés formátum: {"": "","": ""} |
| A shard száma |-r,--shard-száma |Hozzon létre egy prémium szintű fürt gyorsítótár fürtözési a szilánkok száma. |
| Virtual Network |-v, --virtual-network |Esetén a VNETEN belül a gyorsítótárhoz, Azonosítóját adja meg a pontos ARM erőforrás a virtuális hálózati redis gyorsítótár telepítéséhez. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| kulcs típusa |-t,--kulcs-típus |Kulcs megújításához típusa. Érvényes értékek: [elsődleges, másodlagos] |
| StaticIP |-p, – statikus ip-< ip-statikus > |Esetén a VNETEN belül a gyorsítótárhoz, adja meg egy egyedi IP-cím az alhálózat, a gyorsítótár. Ha nem ad meg, egy választja meg az alhálózatból. |
| Alhálózat |t,--alhálózati<subnet> |Esetén a VNETEN belül a gyorsítótár, a neve, amelyben a gyorsítótár telepítendő alhálózat. |
| VirtualNetwork |-v rendszerben--virtuális-< virtuális hálózatok > |Esetén a VNETEN belül a gyorsítótárhoz, Azonosítóját adja meg a pontos ARM erőforrás a virtuális hálózati redis gyorsítótár telepítéséhez. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Előfizetés |-s, --subscription |Az előfizetés-azonosító. |

## <a name="see-all-redis-cache-commands"></a>Tekintse meg az összes Redis Cache-parancsok
Minden Redis Cache parancsot és paramétereket, használja a `azure rediscache -h` parancsot.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Redis Cache létrehozása
Redis gyorsítótárat létrehozni, használja a következő parancsot:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache create -h` parancsot.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Egy meglévő Redis Cache törlése
Egy Redis gyorsítótár törléséhez használja a következő parancsot:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache delete -h` parancsot.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Minden Redis Cache-gyorsítótárak az előfizetéshez vagy erőforráscsoporthoz belül felsorolása
Összes Redis Cache-gyorsítótárak az előfizetéshez vagy erőforráscsoporthoz belül listájában, használja a következő parancsot:

    azure rediscache list [options]

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache list -h` parancsot.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Egy meglévő Redis Cache tulajdonságainak megjelenítése
Egy meglévő Redis Cache tulajdonságainak megjelenítéséhez használja a következő parancsot:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache show -h` parancsot.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Egy meglévő Redis gyorsítótár beállításainak módosítása
Egy meglévő Redis gyorsítótár beállításainak módosításához használja a következő parancsot:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache set -h` parancsot.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Újítsa meg a hitelesítési kulcs egy meglévő Redis gyorsítótár
A hitelesítési kulcs megújításához egy meglévő Redis gyorsítótár, a következő paranccsal:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Adja meg `Primary` vagy `Secondary` a `key-type`.

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache renew-key -h` parancsot.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Egy meglévő Redis gyorsítótár lista elsődleges és másodlagos kulcsok
Lista elsődleges és másodlagos kulcsok egy meglévő Redis gyorsítótár használja a következő parancsot:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Ez a parancs kapcsolatos további információkért futtassa a `azure rediscache list-keys -h` parancsot.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
