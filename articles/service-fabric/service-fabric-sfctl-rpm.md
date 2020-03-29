---
title: Azure Service Fabric CLI- sfctl fordulatszám
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. A javításkezelő szolgáltatásparancsainak listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1787c0c8a8d5994ce1f1e9e4c55f155bbca6934c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904946"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Parancsok lekérdezése és küldése a javításkezelő szolgáltatásnak.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| jóváhagyó-erő | Kényszeríti az adott javítási feladat jóváhagyását. |
| delete | Törölt egy befejezett javítási feladatot. |
| lista | Beszerzi az adott szűrőknek megfelelő javítási feladatok listáját. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm jóváhagyó erő
Kényszeríti az adott javítási feladat jóváhagyását.

Ez az API támogatja a Service Fabric platformot; ez nem azt jelentette, hogy közvetlenül a kódot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --feladat-azonosító [Kötelező] | A javítási feladat azonosítója. |
| --verzió | A javítási feladat aktuális verziószáma. Ha nem nulla, akkor a kérelem csak akkor lesz sikeres, ha ez az érték megegyezik a javítási feladat aktuális verziójával. Ha nulla, akkor a verzióellenőrzés nem történik meg. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-rpm-delete"></a>sfctl fordulatszám törlése
Törölt egy befejezett javítási feladatot.

Ez az API támogatja a Service Fabric platformot; ez nem azt jelentette, hogy közvetlenül a kódot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --feladat-azonosító [Kötelező] | A törölendő befejezett javítási feladat azonosítója. |
| --verzió | A javítási feladat aktuális verziószáma. Ha nem nulla, akkor a kérelem csak akkor lesz sikeres, ha ez az érték megegyezik a javítási feladat aktuális verziójával. Ha nulla, akkor a verzióellenőrzés nem történik meg. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-rpm-list"></a>sfctl rpm lista
Beszerzi az adott szűrőknek megfelelő javítási feladatok listáját.

Ez az API támogatja a Service Fabric platformot; ez nem azt jelentette, hogy közvetlenül a kódot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --végrehajtó-szűrő | Annak a javítóvégrehajtónak a nevét, akinek az igényelt feladatait fel kell venni a listára. |
| --állapotszűrő | Az alábbi értékek bitenkénti VAGY értéke, amely meghatározza, hogy mely tevékenységállapotokat kell felvenni az eredménylistában. <ul><li>1 - Létrehozva</li><li>2 - Igényelt</li><li>4 - Felkészülés</li><li>8 - Jóváhagyva</li><li>16 - Végrehajtás</li><li>32 - Visszaállítás</li><li>64 - Kész</li></ul>
| --feladat-id-szűrő | Az egyeztetendő javítási feladatazonosító előtagja. |

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
