---
title: Azure Service Fabric CLI- sfctl mesh hálózat
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A Service Fabric Mesh hálózati erőforrások be- és törlésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9aa0b6795508cb94200c486df092b867c1086316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905993"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh hálózata
Hálóhálózati erőforrások be- és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a Hálózati erőforrást. |
| lista | Felsorolja az összes hálózati erőforrást. |
| megjelenítés | Leveszi a hálózati erőforrást a megadott névvel. |

## <a name="sfctl-mesh-network-delete"></a>sfctl háló hálózat törlése
Törli a Hálózati erőforrást.

Törli a név által azonosított hálózati erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | A hálózat neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-network-list"></a>sfctl hálóhálózati lista
Felsorolja az összes hálózati erőforrást.

Egy adott erőforráscsoport összes hálózati erőforrására vonatkozó információ leése. Az információ tartalmazza a hálózat leírását és egyéb tulajdonságait.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-network-show"></a>sfctl hálóhálózati show
Leveszi a hálózati erőforrást a megadott névvel.

A hálózati erőforrásra vonatkozó információk bekésése a megadott névvel. Az információ tartalmazza a hálózat leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | A hálózat neve. |

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