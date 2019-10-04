---
title: Azure Service Fabric CLI – sfctl áruház | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl-tároló parancsait.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: ccd9e8323f6e0de7b81c7600e7828e4858c51201
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035861"
---
# <a name="sfctl-store"></a>sfctl-tároló
Hajtsa végre az alapszintű fájl szintű műveleteket a fürt rendszerkép-tárolóján.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| törlés | Törli a meglévő rendszerkép-tároló tartalmát. |
| root-info | Lekéri a tartalom adatait a rendszerkép-tároló gyökerében. |
| stat | A rendszerkép-tároló tartalmának adatainak beolvasása. |

## <a name="sfctl-store-delete"></a>sfctl-tároló törlése
Törli a meglévő rendszerkép-tároló tartalmát.

Törli az adott rendszerkép-tároló relatív elérési útjában található meglévő rendszerkép-tároló tartalmát. Ezzel a paranccsal törölhetők a feltöltött alkalmazáscsomag az üzembe helyezésük után.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Content-Path [kötelező] | A fájl vagy mappa relatív elérési útja a rendszerkép-tárolóban a gyökeréből. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-root-info"></a>sfctl-tároló gyökerének adatai
Lekéri a tartalom adatait a rendszerkép-tároló gyökerében.

A rendszerkép-tároló gyökerénél lévő rendszerkép-tároló tartalmának adatait adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-stat"></a>sfctl Store-stat
A rendszerkép-tároló tartalmának adatainak beolvasása.

A megadott contentPath lévő rendszerkép-tároló tartalmának adatait adja vissza. A contentPath a rendszerkép-tároló gyökeréhez viszonyítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Content-Path [kötelező] | A fájl vagy mappa relatív elérési útja a rendszerkép-tárolóban a gyökeréből. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.