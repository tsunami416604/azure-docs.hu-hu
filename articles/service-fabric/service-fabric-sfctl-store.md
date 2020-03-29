---
title: Az Azure Service Fabric CLI- sfctl áruház
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A fürtképtárban a fájlszintű műveletek végrehajtásához szükséges parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905623"
---
# <a name="sfctl-store"></a>sfctl-tároló
Alapvető fájlszintű műveletek végrehajtása a fürtképtárolón.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a meglévő képtár tartalmát. |
| gyökér-info | Leveszi a tartalominformációt a képtároló gyökerében. |
| Stat | Leveszi a képtároló tartalomadatait. |

## <a name="sfctl-store-delete"></a>sfctl áruház törlése
Törli a meglévő képtár tartalmát.

Törli az adott képtár relatív elérési útján található meglévő képtár-tartalmat. Ez a parancs a feltöltött alkalmazáscsomagok törlésére használható, miután kivannak építve.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --content-path [Kötelező] | A képtárolóban lévő fájl vagy mappa relatív elérési útja a gyökérből. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-root-info"></a>sfctl áruház gyökér-info
Leveszi a tartalominformációt a képtároló gyökerében.

A képtároló tartalmával kapcsolatos információkat adja vissza a képtároló gyökerében.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-stat"></a>sfctl áruház stat
Leveszi a képtároló tartalomadatait.

A megadott contentPath-on lévő képtároló tartalomadatait adja eredményül. A contentPath a képtároló gyökeréhez viszonyítva érhető el.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --content-path [Kötelező] | A képtárolóban lévő fájl vagy mappa relatív elérési útja a gyökérből. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.