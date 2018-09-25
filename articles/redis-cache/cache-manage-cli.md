---
title: Kezelheti a klasszikus Azure CLI-vel az Azure Redis Cache |} A Microsoft Docs
description: Ismerje meg, a klasszikus Azure CLI telepítése, bármely platformra, hogyan használható az Azure-fiókjába való kapcsolódáshoz és hogyan hozhat létre, és a klasszikus parancssori felület a Redis gyorsítótár felügyelete.
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
ms.openlocfilehash: 0e8bbaad920f35028c51641779a3272f73f81f37
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978404"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-classic-cli"></a>Hogyan hozhat létre és kezelhet az Azure redis Cache gyorsítótárral a klasszikus Azure CLI használatával
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Az Azure klasszikus parancssori felület](cache-manage-cli.md)
>

A klasszikus Azure CLI kiválóan alkalmas az Azure-infrastruktúra kezelése bármilyen platformon. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az Azure Redis Cache-példány az Azure klasszikus parancssori felület használatával.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> A legfrissebb Azure CLI-mintaszkriptek, lásd: [Azure CLI Redis cache-minták](cli-samples.md).

## <a name="prerequisites"></a>Előfeltételek
Hozhat létre és kezelheti a klasszikus Azure CLI-vel az Azure Redis Cache-példány, a következő lépéseket kell elvégeznie.

* Rendelkeznie kell egy Azure-fiókkal. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc múlva.
* [A klasszikus Azure CLI telepítése](../cli-install-nodejs.md).
* Csatlakozás az Azure CLI telepítése, személyes Azure-fiókjával, vagy a munkahelyi vagy iskolai Azure-fiókkal, és jelentkezzen be a klasszikus parancssori felület használatával a `azure login` parancsot.
* Mielőtt a következő parancsok egyikét futtatja, váltson a klasszikus parancssori felület Resource Manager módba futtatásával a `azure config mode arm` parancsot. További információkért lásd: [Azure-erőforrások és -erőforráscsoportok kezelése az Azure klasszikus parancssori felület használatával](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Redis Cache-gyorsítótár tulajdonságai
A következő tulajdonságok létrehozása és Redis Cache-példány frissítése során használt.

| Tulajdonság | Kapcsoló | Leírás |
| --- | --- | --- |
| név |-n, a--neve |A Redis Cache neve. |
| erőforráscsoport |-g, a--resource-group |Az erőforráscsoport neve. |
| location |az l-,--helye |Hely a gyorsítótár létrehozásához. |
| méret |– z, a--mérete |A Redis gyorsítótár méretét. Érvényes értékek: [C0 csomag, C1, C2, C3, C4, C5, C6 csomag, P1, P2, P3, P4] |
| sku |-x-,--sku |A redis-Termékváltozat. Egyikének kell lennie: [alapszintű, Standard, prémium szintű] |
| EnableNonSslPort |-e,--engedélyezése – ssl-portszám nélküli |A Redis Cache EnableNonSslPort tulajdonság. Adja hozzá ezt a jelzőt, ha azt szeretné, a gyorsítótár a nem SSL Port engedélyezéséhez |
| A redis konfigurálása |-c, --redis-configuration |A redis konfigurálása. Adja meg a konfigurációs kulcsokat és értékeket itt formázott JSON-karakterláncot. Formátum: "{" ":""," ":" "}" |
| A redis konfigurálása |-f, --redis-configuration-file |A redis konfigurálása. Adja meg a fájl elérési útját tartalmazó konfigurációs kulcsokat és értékeket itt. A fájl bejegyzést formátum: {"": "","": ""} |
| Szegmensszám |-r,---szegmensszám |A fürtözési prémium fürt gyorsítótár létrehozása a szegmensek száma. |
| Virtual Network |-v, --virtual-network |Üzemelteti a gyorsítótár egy virtuális hálózaton, amikor megadja a pontos ARM erőforrás-Azonosítóját a virtuális hálózatban való üzembe helyezése a redis-gyorsítótárként. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Kulcs típusa |-t, a--key-típus |Kulcs típusa, hogy újítsa meg. Érvényes értékek: [elsődleges, másodlagos] |
| StaticIP |-p,--statikus ip-< ip-statikus > |Tárolásához a gyorsítótár egy virtuális hálózaton, adja meg egy egyedi IP-cím az alhálózat, a gyorsítótár. Ha nincs megadva, az egyik van kiválasztva, az alhálózatról. |
| Alhálózat |t, a--alhálózat <subnet> |Üzemelteti a gyorsítótár egy virtuális hálózaton, amikor megadja az alhálózaton, melyben szeretné üzembe helyezni a gyorsítótár nevére. |
| VirtualNetwork |-v,--virtuális hálózat < virtuális-hálózat > |Üzemelteti a gyorsítótár egy virtuális hálózaton, amikor megadja a pontos ARM erőforrás-Azonosítóját a virtuális hálózatban való üzembe helyezése a redis-gyorsítótárként. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Előfizetés |-s, – előfizetés |Az előfizetés-azonosító. |

## <a name="see-all-redis-cache-commands"></a>Tekintse meg a Redis Cache minden parancs
Redis Cache minden parancs és a paraméterek megjelenítéséhez használja a `azure rediscache -h` parancsot.

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
Redis gyorsítótár létrehozásához használja a következő parancsot:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache create -h` parancsot.

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

## <a name="delete-an-existing-redis-cache"></a>Meglévő Redis gyorsítótár törlése
Egy Redis Cache törléséhez használja a következő parancsot:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache delete -h` parancsot.

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

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Az összes redis Cache-gyorsítótárak az előfizetésen vagy erőforráscsoporton belüli listázása
Az előfizetésen vagy erőforráscsoporton belüli összes Redis gyorsítótárak listájában, használja a következő parancsot:

    azure rediscache list [options]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache list -h` parancsot.

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

## <a name="show-properties-of-an-existing-redis-cache"></a>Meglévő Redis Cache-tulajdonságainak megjelenítése
Meglévő Redis Cache-tulajdonságainak megjelenítéséhez használja a következő parancsot:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache show -h` parancsot.

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

## <a name="change-settings-of-an-existing-redis-cache"></a>Meglévő Redis gyorsítótár beállításainak módosítása
Meglévő Redis gyorsítótár beállításainak módosításához használja a következő parancsot:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache set -h` parancsot.

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

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>A hitelesítési kulcs megújítása egy meglévő Redis Cache-gyorsítótárhoz
A hitelesítési kulcs megújítása egy meglévő Redis Cache-hez, a következő paranccsal:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Adja meg `Primary` vagy `Secondary` a `key-type`.

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache renew-key -h` parancsot.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Meglévő Redis Cache lista elsődleges és másodlagos kulcsok
Elsődleges és másodlagos kulcsok listázása egy meglévő Redis Cache használja a következő parancsot:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache list-keys -h` parancsot.

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
