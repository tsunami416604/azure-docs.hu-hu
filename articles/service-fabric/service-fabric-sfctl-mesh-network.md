---
title: Azure Service Fabric CLI – sfctl Mesh hálózat
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Service Fabric Mesh hálózati erőforrások beolvasására és törlésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9aa0b6795508cb94200c486df092b867c1086316
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905993"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh hálózata
Háló hálózati erőforrásainak beolvasása és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Description|
| --- | --- |
| delete | A hálózati erőforrás törlése. |
| lista | Felsorolja az összes hálózati erőforrást. |
| megjelenítés | Lekéri a megadott nevű hálózati erőforrást. |

## <a name="sfctl-mesh-network-delete"></a>sfctl Mesh hálózat törlése
A hálózati erőforrás törlése.

Törli a névvel azonosított hálózati erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | A hálózat neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-network-list"></a>sfctl Mesh hálózat listája
Felsorolja az összes hálózati erőforrást.

Egy adott erőforráscsoport összes hálózati erőforrásával kapcsolatos információk beolvasása. Az információk közé tartozik a hálózat leírása és egyéb tulajdonságai.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-network-show"></a>sfctl Mesh Network show
Lekéri a megadott nevű hálózati erőforrást.

A megadott nevű hálózati erőforrással kapcsolatos információk beolvasása. Az információk közé tartozik a hálózat leírása és egyéb tulajdonságai.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | A hálózat neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.