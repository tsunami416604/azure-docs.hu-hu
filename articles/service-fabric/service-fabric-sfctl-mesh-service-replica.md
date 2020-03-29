---
title: Azure Service Fabric CLI- sfctl mesh szolgáltatás-replika
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Az alkalmazás-erőforrások replikarészleteinek beszerzésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905953"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh szolgáltatásreplika
A replika részleteinek és egy adott szolgáltatás listájának listája egy alkalmazás-erőforrásban.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| lista | Egy szolgáltatás összes replikájának listája. |
| megjelenítés | Letöltője az alkalmazás szolgáltatásának adott replikáját. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh szolgáltatás-replika lista
Egy szolgáltatás összes replikájának listája.

A szolgáltatás összes replikájának információi. Az információk tartalmazzák a szolgáltatásreplika leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --alkalmazás-név [Kötelező] | Az alkalmazás neve. |
| --szolgáltatás neve [Kötelező] | A szolgáltatás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh szolgáltatás-replika show
Letöltője az alkalmazás szolgáltatásának adott replikáját.

A szolgáltatás replikájával kapcsolatos információk bemásolása a megadott névvel. Az információk tartalmazzák a szolgáltatásreplika leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --alkalmazás-név [Kötelező] | Az alkalmazás neve. |
| --név -n [Kötelező] | A szolgáltatás kópiájának neve. |
| --szolgáltatás neve [Kötelező] | A szolgáltatás neve. |

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