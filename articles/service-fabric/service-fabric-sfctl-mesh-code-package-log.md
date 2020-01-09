---
title: Azure Service Fabric CLI-sfctl Mesh Code-Package-log
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A megadott programkódhoz tartozó naplók beszerzésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 78edc9bb36b711f72300942bc9900b0fde7c51d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646143"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh kódcsomagnaplója
Szerezze be a naplófájlokat az adott szolgáltatás replikájának megadott csomagjának tárolójában.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| elérés | Lekéri a tárolóból a naplókat. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl Mesh Code-Package-log Get
Lekéri a tárolóból a naplókat.

Beolvassa a szolgáltatás replikájának megadott kódjának tárolóját tartalmazó naplókat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| --Code-Package-Name [kötelező] | A szolgáltatás kódjának neve. |
| --replika-név [kötelező] | Service Fabric replika neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |

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