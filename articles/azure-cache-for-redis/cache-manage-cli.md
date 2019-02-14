---
title: Azure Cache kezelheti a klasszikus Azure CLI-vel Redis |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti az Azure klasszikus parancssori felület bármilyen platformon, hogyan csatlakozhat az Azure-fiók használatával és létrehozása és kezelése az Azure Cache Redis a klasszikus parancssori felület.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 99148332a8310428cb73287d861ef71c35d59a26
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233200"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Hogyan hozhat létre és kezelheti Azure Cache a Redis az Azure klasszikus parancssori felület használatával
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure klasszikus parancssori felület](cache-manage-cli.md)
>

A klasszikus Azure CLI kiválóan alkalmas az Azure-infrastruktúra kezelése bármilyen platformon. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az Azure Cache a Redis-példány az Azure klasszikus parancssori felület használatával.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> A legfrissebb Azure CLI-mintaszkriptek, lásd: [Azure CLI az Azure Cache Redis minták](cli-samples.md).

## <a name="prerequisites"></a>Előfeltételek
Hozhat létre és kezelheti Azure Cache a Redis-példány az Azure klasszikus parancssori felület használatával, akkor a következő lépéseket kell elvégeznie.

* Rendelkeznie kell egy Azure-fiókkal. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc múlva.
* [A klasszikus Azure CLI telepítése](../cli-install-nodejs.md).
* Csatlakozás az Azure CLI telepítése, személyes Azure-fiókjával, vagy a munkahelyi vagy iskolai Azure-fiókkal, és jelentkezzen be a klasszikus parancssori felület használatával a `azure login` parancsot.
* Mielőtt a következő parancsok egyikét futtatja, váltson a klasszikus parancssori felület Resource Manager módba futtatásával a `azure config mode arm` parancsot. További információkért lásd: [Azure-erőforrások és -erőforráscsoportok kezelése az Azure klasszikus parancssori felület használatával](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Az Azure Cache a Redis-tulajdonságok
A következő tulajdonságok létrehozása és frissítése az Azure Cache a Redis-példány során használt.

| Tulajdonság | Kapcsoló | Leírás |
| --- | --- | --- |
| név |-n, a--neve |A Redis az Azure gyorsítótár nevét. |
| erőforráscsoport |-g, --resource-group |Az erőforráscsoport neve. |
| location |az l-,--helye |Hely a gyorsítótár létrehozásához. |
| méret |-z, --size |Redis Cache számára az Azure gyorsítótár méretét. Érvényes értékek: [C0 csomag, C1, C2, C3, C4, C5, C6 csomag, P1, P2, P3, P4] |
| termékváltozat |-x, --sku |A redis-Termékváltozat. Egyikének kell lennie: [alapszintű, Standard, prémium szintű] |
| EnableNonSslPort |-e,--engedélyezése – ssl-portszám nélküli |Az Azure Cache redis EnableNonSslPort tulajdonság. Adja hozzá ezt a jelzőt, ha azt szeretné, a gyorsítótár a nem SSL Port engedélyezéséhez |
| A redis konfigurálása |-c, --redis-configuration |A redis konfigurálása. Adja meg a konfigurációs kulcsokat és értékeket itt formázott JSON-karakterláncot. Formátum: "{" ":""," ":" "}" |
| A redis konfigurálása |-f, --redis-configuration-file |A redis konfigurálása. Adja meg a fájl elérési útját tartalmazó konfigurációs kulcsokat és értékeket itt. A fájl bejegyzést formátum: {"": "","": ""} |
| Szegmensszám |-r,---szegmensszám |A fürtözési prémium fürt gyorsítótár létrehozása a szegmensek száma. |
| Virtual Network |-v, --virtual-network |Megadja a pontos ARM erőforrás-Azonosítóját a virtuális hálózat üzembe helyezéséhez az Azure gyorsítótár egy virtuális hálózaton, a gyorsítótár üzemeltetése esetén a Redis. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Kulcs típusa |-t, --key-type |Kulcs típusa, hogy újítsa meg. Érvényes értékek: [elsődleges, másodlagos] |
| StaticIP |-p, --static-ip <static-ip> |Tárolásához a gyorsítótár egy virtuális hálózaton, adja meg egy egyedi IP-cím az alhálózat, a gyorsítótár. Ha nincs megadva, az egyik van kiválasztva, az alhálózatról. |
| Alhálózat |t, --subnet <subnet> |Üzemelteti a gyorsítótár egy virtuális hálózaton, amikor megadja az alhálózaton, melyben szeretné üzembe helyezni a gyorsítótár nevére. |
| VirtualNetwork |-v,--virtuális hálózat < virtuális-hálózat > |Megadja a pontos ARM erőforrás-Azonosítóját a virtuális hálózat üzembe helyezéséhez az Azure gyorsítótár egy virtuális hálózaton, a gyorsítótár üzemeltetése esetén a Redis. Példa formátum: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Előfizetés |-s, – előfizetés |Az előfizetés-azonosító. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Tekintse meg az összes Azure Cache Redis parancsok
Az összes Azure Cache Redis parancsok és a paraméterek megjelenítéséhez használja a `azure rediscache -h` parancsot.

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

## <a name="create-an-azure-cache-for-redis"></a>A Redis Azure Cache létrehozása
Hozzon létre egy Azure Cache redis, használja a következő parancsot:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache create -h` parancsot.

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

## <a name="delete-an-existing-azure-cache-for-redis"></a>Egy meglévő Azure Cache Redis törlése
Az Azure Cache Redis-törléséhez használja a következő parancsot:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache delete -h` parancsot.

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

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>A Redis az előfizetésen vagy erőforráscsoporton belüli összes Azure Cache listázása
A listában az összes Azure Cache redis az előfizetésen vagy erőforráscsoporton belül, a következő paranccsal:

    azure rediscache list [options]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache list -h` parancsot.

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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>A Redis egy meglévő Azure Cache tulajdonságainak megjelenítése
A Redis egy meglévő Azure Cache tulajdonságainak megjelenítéséhez használja a következő parancsot:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache show -h` parancsot.

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

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>A Redis egy meglévő Azure-gyorsítótár beállításainak módosítása
A Redis egy meglévő Azure-gyorsítótár beállításainak módosításához használja a következő parancsot:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache set -h` parancsot.

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

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>A hitelesítési kulcs megújítása egy meglévő Azure Cache redis
A hitelesítési kulcs megújítása az egy meglévő Azure Cache redis, használja a következő parancsot:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Adja meg `Primary` vagy `Secondary` a `key-type`.

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache renew-key -h` parancsot.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>A Redis egy meglévő Azure Cache lista elsődleges és másodlagos kulcsok
Elsődleges és másodlagos kulcsok listázása egy meglévő Azure Cache redis használja a következő parancsot:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Ezzel a paranccsal kapcsolatos további információkért futtassa a `azure rediscache list-keys -h` parancsot.

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
