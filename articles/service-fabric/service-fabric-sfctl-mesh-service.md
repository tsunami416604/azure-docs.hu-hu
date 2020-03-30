---
title: Az Azure Service Fabric CLI- sfctl mesh szolgáltatás
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Az alkalmazás-erőforrások szolgáltatásrészleteinek beszerzésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905924"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh szolgáltatás
Egy alkalmazás-erőforrás szolgáltatásrészleteinek és listázásának beszereznie.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| lista | Felsorolja az összes szolgáltatási erőforrást. |
| megjelenítés | Letöltődik a szolgáltatás erőforrás a megadott nevet. |

## <a name="sfctl-mesh-service-list"></a>sfctl háló szolgáltatáslista
Felsorolja az összes szolgáltatási erőforrást.

Egy alkalmazás-erőforrás összes szolgáltatására vonatkozó információ lekért. Az információk tartalmazzák a Szolgáltatás leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --alkalmazás-név [Kötelező] | Az alkalmazás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-service-show"></a>sfctl háló szolgáltatás show
Letöltődik a szolgáltatás erőforrás a megadott nevet.

A megadott nevű Service erőforrásadatainak bekerülése. Az információk tartalmazzák a Szolgáltatás leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --alkalmazás-név [Kötelező] | Az alkalmazás neve. |
| --név -n [Kötelező] | A szolgáltatás neve. |

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