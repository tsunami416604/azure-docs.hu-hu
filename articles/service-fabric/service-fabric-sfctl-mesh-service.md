---
title: Azure Service Fabric CLI – sfctl Mesh szolgáltatás
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Az alkalmazás-erőforrások szolgáltatás részleteinek beolvasására szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905924"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh szolgáltatás
Szolgáltatás részleteinek beolvasása és az alkalmazás-erőforrások listázása.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| lista | Felsorolja az összes szolgáltatás-erőforrást. |
| megjelenítése | Lekéri a szolgáltatás erőforrását a megadott névvel. |

## <a name="sfctl-mesh-service-list"></a>sfctl Mesh-szolgáltatás listája
Felsorolja az összes szolgáltatás-erőforrást.

Az alkalmazás-erőforrások összes szolgáltatásával kapcsolatos információk beolvasása. Az információ tartalmazza a szolgáltatás leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-service-show"></a>sfctl Mesh Service show
Lekéri a szolgáltatás erőforrását a megadott névvel.

Lekéri a szolgáltatás erőforrásának adatait a megadott névvel. Az információ tartalmazza a szolgáltatás leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| --Name-n [kötelező] | A szolgáltatás neve. |

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