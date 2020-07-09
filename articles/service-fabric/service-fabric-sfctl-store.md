---
title: Azure Service Fabric CLI – sfctl-tároló
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Tartalmazza a fürt rendszerkép-tárolójában a fájl szintű műveletek végrehajtásához szükséges parancsok listáját.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905623"
---
# <a name="sfctl-store"></a>sfctl-tároló
Hajtsa végre az alapszintű fájl szintű műveleteket a fürt rendszerkép-tárolóján.

## <a name="commands"></a>Parancsok

|Parancs|Description|
| --- | --- |
| delete | Törli a meglévő rendszerkép-tároló tartalmát. |
| gyökér – információ | Lekéri a tartalom adatait a rendszerkép-tároló gyökerében. |
| stat | A rendszerkép-tároló tartalmának adatainak beolvasása. |

## <a name="sfctl-store-delete"></a>sfctl-tároló törlése
Törli a meglévő rendszerkép-tároló tartalmát.

Törli az adott rendszerkép-tároló relatív elérési útjában található meglévő rendszerkép-tároló tartalmát. Ezzel a paranccsal törölhetők a feltöltött alkalmazáscsomag az üzembe helyezésük után.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Content-Path [kötelező] | A fájl vagy mappa relatív elérési útja a rendszerkép-tárolóban a gyökeréből. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-root-info"></a>sfctl-tároló gyökerének adatai
Lekéri a tartalom adatait a rendszerkép-tároló gyökerében.

A rendszerkép-tároló gyökerénél lévő rendszerkép-tároló tartalmának adatait adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-stat"></a>sfctl Store-stat
A rendszerkép-tároló tartalmának adatainak beolvasása.

A megadott contentPath lévő rendszerkép-tároló tartalmának adatait adja vissza. A contentPath a rendszerkép-tároló gyökeréhez viszonyítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Content-Path [kötelező] | A fájl vagy mappa relatív elérési útja a rendszerkép-tárolóban a gyökeréből. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.