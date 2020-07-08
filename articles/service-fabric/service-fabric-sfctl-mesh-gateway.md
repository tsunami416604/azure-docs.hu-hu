---
title: Azure Service Fabric CLI – sfctl Mesh átjáró
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Az Service Fabric Mesh-átjáró erőforrásainak beolvasására és törlésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906015"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh átjárója
Rácsvonal-átjáró erőforrásainak beolvasása és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Description|
| --- | --- |
| delete | Az átjáró erőforrásának törlése. |
| lista | Felsorolja az összes átjáró erőforrását. |
| megjelenítés | Az átjáró erőforrásának beolvasása a megadott névvel. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl rácsvonal-átjáró törlése
Az átjáró erőforrásának törlése.

Törli a névvel azonosított átjáró-erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | Az átjáró erőforrásának neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl Mesh-átjárók listája
Felsorolja az összes átjáró erőforrását.

Az adott erőforráscsoport összes átjáró-erőforrásával kapcsolatos információk beolvasása. Az információ tartalmazza az átjáró leírását és egyéb tulajdonságait.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl Mesh Gateway show
Az átjáró erőforrásának beolvasása a megadott névvel.

Az átjáró-erőforrással kapcsolatos információk beolvasása a megadott névvel. Az információ tartalmazza az átjáró leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | Az átjáró erőforrásának neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.