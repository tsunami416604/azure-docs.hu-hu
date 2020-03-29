---
title: Azure Service Fabric CLI- sfctl hálókötet
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A köteterőforrások beszerzésére és törlésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0e5ba1a311bd0480c56ee40edf01fc13ec01664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905910"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh kötete
Köteterőforrások beésése és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a Kötet erőforrást. |
| lista | Felsorolja az összes köteterőforrást. |
| megjelenítés | Leveszi a kötet erőforrást a megadott névvel. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl háló térfogattörlése
Törli a Kötet erőforrást.

Törli a név által azonosított Kötet erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | A kötet neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-volume-list"></a>sfctl hálótérfogat-lista
Felsorolja az összes köteterőforrást.

Egy adott erőforráscsoport összes köteterőforrására vonatkozó információ leése. Az információ tartalmazza a kötet leírását és egyéb tulajdonságait.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-volume-show"></a>sfctl háló térfogata
Leveszi a kötet erőforrást a megadott névvel.

A megadott nevű Kötet erőforrással kapcsolatos információk bekéselése. Az információ tartalmazza a kötet leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | A kötet neve. |

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