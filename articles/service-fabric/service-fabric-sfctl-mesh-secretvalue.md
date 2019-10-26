---
title: Azure Service Fabric CLI – sfctl Mesh secretvalue | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl Mesh secretvalue parancsait.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901180"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh titkos kódjának értéke
Háló secretvalue erőforrásainak beolvasása és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a névvel ellátott titkos erőforrás megadott értékét. |
| lista | A megadott titkos erőforrás összes értékének listázása. |
| megjelenítése | A titkos erőforrás megadott értékét listázza. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl Mesh secretvalue törlése
Törli a névvel ellátott titkos erőforrás megadott értékét.

Törli a név alapján azonosított titkos érték erőforrást. Az erőforrás neve általában az adott értékhez társított verzió. A törlés sikertelen lesz, ha a megadott érték használatban van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Secret-Name-n [kötelező] | A titkos erőforrás neve. |
| --Version-v [kötelező] | A titkos verzió neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl Mesh secretvalue listája
A megadott titkos erőforrás összes értékének listázása.

Információk beolvasása a megadott titkos erőforrás minden titkos érték erőforrásáról. Az információ tartalmazza a titkos érték erőforrásainak nevét, de nem a tényleges értékeket.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Secret-Name-n [kötelező] | A titkos erőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl Mesh secretvalue show
A titkos erőforrás megadott értékét listázza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Secret-Name-n [kötelező] | A titkos erőforrás neve. |
| --Version-v [kötelező] | A titkos verzió neve. |
| --show-Value | A titkos verzió tényleges értékének megjelenítése. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.