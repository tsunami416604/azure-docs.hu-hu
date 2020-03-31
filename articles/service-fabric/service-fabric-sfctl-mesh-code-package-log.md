---
title: Azure Service Fabric CLI - sfctl mesh kód-csomag-log
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A megadott kódcsomag naplóinak beszerzésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906043"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh kódcsomagnaplója
Az adott szolgáltatásreplika megadott kódcsomagtárolójának naplóinak beszereznie.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| get | Leveszi a naplókat a tárolóból. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh kód-csomag-log bekés
Leveszi a naplókat a tárolóból.

Lekéri a naplót a tároló a szolgáltatás replika megadott kódcsomag.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --app-name --alkalmazás-név [Kötelező] | Az alkalmazás neve. |
| --code-package-name [Kötelező] | A szolgáltatás kódcsomagjának neve. |
| --replikanév [Kötelező] | A Service Fabric replika neve. |
| --szolgáltatás neve [Kötelező] | A szolgáltatás neve. |
| --farok | A naplók végétől megjelenítandó sorok száma. Az alapértelmezett érték 100. "all", hogy megmutassa a teljes naplókat. |

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