---
title: Az Azure Service Fabric CLI-sfctl
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Az infrastruktúra kezelésére vonatkozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906080"
---
# <a name="sfctl-is"></a>sfctl értéke
Parancsok lekérdezése és küldése az infrastruktúra-szolgáltatásnak.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| command | Meghív egy felügyeleti parancsot az adott Infrastruktúraszolgáltatás-példányon. |
| lekérdezés | Írásvédett lekérdezésmeghívása az adott infrastruktúra-szolgáltatás példánya. |

## <a name="sfctl-is-command"></a>Az sfctl parancs
Meghív egy felügyeleti parancsot az adott Infrastruktúraszolgáltatás-példányon.

Az infrastruktúra-szolgáltatás egy vagy több példányát konfigurált fürtök esetében ez az API lehetővé teszi, hogy infrastruktúra-specifikus parancsokat küldjön az infrastruktúra-szolgáltatás egy adott példányának. Az elérhető parancsok és a hozzájuk tartozó válaszformátumok afürt által futtatott infrastruktúrától függően változnak. Ez az API támogatja a Service Fabric platformot; ez nem azt jelentette, hogy közvetlenül a kódot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --parancs [Kötelező] | A meghívni hívandó parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --szolgáltatás-id | Az infrastruktúra-szolgáltatás kiléte. <br><br> Ez az infrastruktúra-szolgáltatás teljes neve a\:"háló" URI-séma nélkül. Ez a paraméter csak ahhoz a fürthöz szükséges, amelynek egynél több infrastruktúra-szolgáltatása fut. |
| --idő-out -t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-is-query"></a>Sfctl lekérdezés
Írásvédett lekérdezésmeghívása az adott infrastruktúra-szolgáltatás példánya.

Az infrastruktúra-szolgáltatás egy vagy több példányával konfigurált fürtök esetében ez az API lehetővé teszi, hogy infrastruktúra-specifikus lekérdezéseket küldjön az infrastruktúra-szolgáltatás egy adott példányának. Az elérhető parancsok és a hozzájuk tartozó válaszformátumok afürt által futtatott infrastruktúrától függően változnak. Ez az API támogatja a Service Fabric platformot; ez nem azt jelentette, hogy közvetlenül a kódot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --parancs [Kötelező] | A meghívni hívandó parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --szolgáltatás-id | Az infrastruktúra-szolgáltatás kiléte. <br><br> Ez az infrastruktúra-szolgáltatás teljes neve a\:"háló" URI-séma nélkül. Ez a paraméter csak ahhoz a fürthöz szükséges, amelynek egynél több infrastruktúra-szolgáltatása fut. |
| --idő-out -t | Alapértelmezett\: 60. |

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
