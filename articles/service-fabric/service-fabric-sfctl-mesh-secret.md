---
title: Azure Service Fabric CLI- sfctl háló titkos
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A Service Fabric Mesh titkos erőforrásainak beszerzésére és törlésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905985"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh titkos kódja
Hálótitkos erőforrások be- és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a titkos erőforrást. |
| lista | Felsorolja az összes titkos erőforrást. |
| megjelenítés | Leveszi a titkos erőforrást a megadott névvel. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl háló titkos törlése
Törli a titkos erőforrást.

Törli a megadott titkos erőforrást és annak összes elnevezett értékét.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | A titkos erőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secret-list"></a>sfctl háló titkos listája
Felsorolja az összes titkos erőforrást.

Egy adott erőforráscsoport összes titkos erőforrására vonatkozó információ leése. Az információ tartalmazza a titkos titok leírását és egyéb tulajdonságait.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secret-show"></a>sfctl háló titkos show
Leveszi a titkos erőforrást a megadott névvel.

A titkos erőforrás ra vonatkozó információk bekésése a megadott névvel. Az információ tartalmazza a titkos titok leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --név -n [Kötelező] | A titkos erőforrás neve. |

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