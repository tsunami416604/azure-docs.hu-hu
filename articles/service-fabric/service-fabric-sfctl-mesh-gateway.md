---
title: Azure Service Fabric CLI - sfctl mesh átjáró
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A Service Fabric Mesh átjáró erőforrások beszerzésére és törlésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3e398ff8a1a0a28fd9d0650a7c9d18ba04817d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906015"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh átjárója
Hálós átjáró-erőforrások be- és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli az Átjáró erőforrást. |
| lista | Az átjáró összes erőforrásának listája. |
| megjelenítés | Leveszi az átjáró erőforrást a megadott névvel. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh átjáró törlése
Törli az Átjáró erőforrást.

Törli a név által azonosított átjáró erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | Az átjáróerőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh átjárólista
Az átjáró összes erőforrásának listája.

Egy adott erőforráscsoport összes átjáróerőforrására vonatkozó információ leése. Az információ tartalmazza az átjáró leírását és egyéb tulajdonságait.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh átjáró megjelenítése
Leveszi az átjáró erőforrást a megadott névvel.

Az átjáró erőforrás ra vonatkozó információk bekésése a megadott névvel. Az információ tartalmazza az átjáró leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | Az átjáróerőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.