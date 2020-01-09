---
title: Azure Service Fabric CLI – sfctl rpm
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Tartalmazza a Repair Manager szolgáltatáshoz tartozó parancsok listáját.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 674970276046034d13801db7c1bb4ab5175385fb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639088"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Parancsok lekérdezése és küldése a Repair Manager szolgáltatásnak.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| jóváhagyás – kényszerít | Kényszeríti a megadott javítási feladat jóváhagyását. |
| delete | Töröl egy befejezett javítási feladatot. |
| lista | Lekéri a megadott szűrőknek megfelelő javítási feladatok listáját. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm jóváhagyása-Force
Kényszeríti a megadott javítási feladat jóváhagyását.

Ez az API támogatja a Service Fabric platformot; nem közvetlenül a kódból való használatra készült.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Task-ID [kötelező] | A javítási feladat azonosítója. |
| --Version | A javítási feladat aktuális verziószáma. Ha nullától eltérő értéket ad meg, a kérelem csak akkor lesz sikeres, ha ez az érték megegyezik a javítási feladat aktuális verziójával. Ha nulla, akkor a rendszer nem hajtja végre a verzió-ellenőrzéseket. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm törlése
Töröl egy befejezett javítási feladatot.

Ez az API támogatja a Service Fabric platformot; nem közvetlenül a kódból való használatra készült.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Task-ID [kötelező] | A törlendő befejezett javítási feladat azonosítója. |
| --Version | A javítási feladat aktuális verziószáma. Ha nullától eltérő értéket ad meg, a kérelem csak akkor lesz sikeres, ha ez az érték megegyezik a javítási feladat aktuális verziójával. Ha nulla, akkor a rendszer nem hajtja végre a verzió-ellenőrzéseket. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lista
Lekéri a megadott szűrőknek megfelelő javítási feladatok listáját.

Ez az API támogatja a Service Fabric platformot; nem közvetlenül a kódból való használatra készült.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --végrehajtó-szűrő | Annak a javítási végrehajtónak a neve, amelynek az igényelt feladatait fel kell venni a listára. |
| --állapot-szűrő | Egy bitenkénti – vagy a következő értékek közül, amelyek meghatározzák, hogy mely feladatok szerepeljenek az eredmények listájában. <br> 1 – létrehozva <br>2 – igényelt  <br>4 – felkészülés  <br>8 – jóváhagyott  <br>16 – végrehajtás  <br>32 – visszaállítás  <br>64 – befejezett |
| --Task-ID-Filter | A kijavítási feladat AZONOSÍTÓjának előtagja. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.