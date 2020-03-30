---
title: Az Azure Cache for Redis kezelése a klasszikus Azure CLI használatával
description: Ismerje meg, hogyan telepítheti az Azure klasszikus CLI-t bármely platformon, hogyan használhatja az Azure-fiókhoz való csatlakozáshoz, és hogyan hozhat létre és kezelhet egy Azure-gyorsítótárat a Redis számára a klasszikus CLI-ből.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: e2b1ed693ea57e3414d465a57a5ba2b1203f67c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277985"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Azure Cache for Redis létrehozása és kezelése a klasszikus Azure CLI használatával
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure klasszikus parancssori felület](cache-manage-cli.md)
>

Az Azure klasszikus CLI egy nagyszerű módja annak, hogy kezelje az Azure-infrastruktúra bármilyen platformról. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az Azure Cache redis-példányok az Azure klasszikus CLI használatával.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> A legújabb Azure CLI-mintaparancsfájlokat az [Azure CLI Azure-gyorsítótár redis mintákhoz című témakörben található.](cli-samples.md)

## <a name="prerequisites"></a>Előfeltételek
Az Azure Cache for Redis-példányok létrehozása és kezelése az Azure classic CLI használatával, a következő lépéseket kell végrehajtania.

* Rendelkeznie kell egy Azure-fiókkal. Ha még nem rendelkezik ilyen, néhány pillanat alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)
* [Telepítse az Azure klasszikus CLI.](../cli-install-nodejs.md)
* Csatlakoztassa az Azure CLI-telepítést egy személyes Azure-fiókkal, vagy egy munkahelyi vagy iskolai `azure login` Azure-fiókkal, és jelentkezzen be a klasszikus CLI-ből a paranccsal.
* Az alábbi parancsok futtatása előtt a `azure config mode arm` parancs futtatásával váltson a klasszikus CLI-t Erőforrás-kezelő módba. További információ: [Az Azure klasszikus CLI használata az Azure-erőforrások és erőforráscsoportok kezeléséhez.](../xplat-cli-azure-resource-manager.md)

## <a name="azure-cache-for-redis-properties"></a>Azure-gyorsítótár a Redis-tulajdonságokhoz
A következő tulajdonságok at a Redis-példányok Azure Cache létrehozásakor és frissítésekor használatosak.

| Tulajdonság | Kapcsoló | Leírás |
| --- | --- | --- |
| név |-n, --név |A Redis Azure-gyorsítótárának neve. |
| erőforráscsoport |-g, --erőforrás-csoport |Az erőforráscsoport neve. |
| location |-l, --hely |Gyorsítótár létrehozásának helye. |
| size |-z, --méret |Az Azure-gyorsítótár a Redis mérete. Érvényes értékek: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| Sku |-x, --sku |Redis-termékváltozat. Az egyik nek: [Alap, Standard, Prémium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |A Redis Azure-gyorsítótár ának EnableNonSslPort tulajdonsága. Adja hozzá ezt a jelzőt, ha engedélyezni szeretné a nem SSL-portot a gyorsítótárhoz |
| Redis konfiguráció |-c, --redis-konfiguráció |Redis konfiguráció. Itt adhatja meg a konfigurációs kulcsok és értékek JSON-formátumú karakterláncát. Formátum:"{"":"","":"":"}" |
| Redis konfiguráció |-f, --redis-configuration-file |Redis konfiguráció. Itt adhatja meg a konfigurációs kulcsokat és értékeket tartalmazó fájl elérési útját. A fájlbejegyzés formátuma: {"":"","":"":"}} |
| Szilánkok száma |-r, --shard-count |A fürtözéssel rendelkező prémium szintű fürtgyorsítótárban létrehozandó szegmensek száma. |
| Virtual Network |-v, --virtuális hálózat |Amikor a gyorsítótárat egy virtuális hálózatban üzemelteti, megadja a virtuális hálózat pontos ARM erőforrás-azonosítóját az Azure-gyorsítótár redis üzembe helyezéséhez. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| kulcs típusa |-t, --kulcs-típus |A megújítandó kulcs típusa. Érvényes értékek: [Elsődleges, Másodlagos] |
| Statikus IP |-p, --static-ip \<statikus-ip\> |Ha a gyorsítótárat egy virtuális hálózatban üzemelteti, egy egyedi IP-címet ad meg a gyorsítótár alhálózatában. Ha nincs megadva, az alhálózatból kiválasztanak egyet. |
| Alhálózat |t, --alhálózat \<alhálózat\> |Amikor a gyorsítótárat egy virtuális hálózatban üzemelteti, megadja annak az alhálózatnak a nevét, amelyben a gyorsítótárat telepíteni szeretné. |
| VirtualNetwork |-v, --virtuális \<hálózati virtuális hálózat\> |Amikor a gyorsítótárat egy virtuális hálózatban üzemelteti, megadja a virtuális hálózat pontos ARM erőforrás-azonosítóját az Azure-gyorsítótár redis üzembe helyezéséhez. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Előfizetés |-s, --előfizetés |Az előfizetés azonosítója. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Az összes Azure Cache for Redis parancs megtekintése
Az összes Azure Cache redis parancsok és azok `azure rediscache -h` paramétereit, használja a parancsot.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Azure Cache for Redis
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Azure Cache for Redis
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-an-azure-cache-for-redis"></a>Az Azure Cache for Redis létrehozása
Azure-gyorsítótár létrehozásához a Redis számára, használja a következő parancsot:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

A parancsról további információt `azure rediscache create -h` a parancs futtatásához.

    C:\>azure rediscache create -h
    help:    Create an Azure Cache for Redis
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Meglévő Azure-gyorsítótár törlése a Redis-hez
A Redis Azure-gyorsítótárának törléséhez használja a következő parancsot:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

A parancsról további információt `azure rediscache delete -h` a parancs futtatásához.

    C:\>azure rediscache delete -h
    help:    Delete an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Az összes Redis-alapú Azure-gyorsítótár listázása az előfizetésen vagy erőforráscsoporton belül
Ha az előfizetésen vagy erőforráscsoporton belül szeretné felsorolni a Redis összes Azure-gyorsítótárát, használja a következő parancsot:

    azure rediscache list [options]

A parancsról további információt `azure rediscache list -h` a parancs futtatásához.

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Meglévő Azure-gyorsítótár tulajdonságainak megjelenítése a Redis számára
Egy meglévő Azure-gyorsítótár redis-i tulajdonságainak megjelenítéséhez használja a következő parancsot:

    azure rediscache show [--name <name> --resource-group <resource-group>]

A parancsról további információt `azure rediscache show -h` a parancs futtatásához.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Meglévő Azure-gyorsítótár beállításainak módosítása a Redis számára
Meglévő Azure-gyorsítótár redis-i beállításainak módosításához használja a következő parancsot:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

A parancsról további információt `azure rediscache set -h` a parancs futtatásához.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Meglévő Azure-gyorsítótár redis-i gyorsítótárának hitelesítési kulcsának megújítása
A Redis meglévő Azure-gyorsítótárának hitelesítési kulcsának megújításához használja a következő parancsot:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Adja `Primary` `Secondary` meg `key-type`vagy a.

A parancsról további információt `azure rediscache renew-key -h` a parancs futtatásához.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Meglévő Azure-gyorsítótár elsődleges és másodlagos kulcsainak listázása a Redis számára
Egy meglévő Azure-gyorsítótár Redis-gyorsítótár elsődleges és másodlagos kulcsainak listázásához használja a következő parancsot:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

A parancsról további információt `azure rediscache list-keys -h` a parancs futtatásához.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
