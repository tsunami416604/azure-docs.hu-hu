---
title: Azure Service Fabric CLI- sfctl mesh secretvalue
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A Service Fabric Mesh titkos értékek beszerzésére és törlésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905965"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh titkos kódjának értéke
Háló titkos értékerőforrásainak be- és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a megnevezett titkos erőforrás megadott értékét. |
| lista | A megadott titkos erőforrás összes értékének felsorolása. |
| megjelenítés | A titkos erőforrás megadott értékének listázása. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue törlés
Törli a megnevezett titkos erőforrás megadott értékét.

Törli a név által azonosított titkos értékerőforrást. Az erőforrás neve általában az ehhez az értékhez társított verzió. A törlés sikertelen lesz, ha a megadott érték használatban van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --titkos név -n [Kötelező] | A titkos erőforrás neve. |
| --verzió -v [Kötelező] | A titkos verzió neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh titkos érték lista
A megadott titkos erőforrás összes értékének felsorolása.

A megadott titkos erőforrás összes titkos értékerőforrására vonatkozó információ bekésése. Az információ tartalmazza a titkos érték erőforrások nevét, de a tényleges értékeket nem.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --titkos név -n [Kötelező] | A titkos erőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl háló titkos érték megjelenítése
A titkos erőforrás megadott értékének listázása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --titkos név -n [Kötelező] | A titkos erőforrás neve. |
| --verzió -v [Kötelező] | A titkos verzió neve. |
| --show-érték | A titkos verzió tényleges értékének megjelenítése. |

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