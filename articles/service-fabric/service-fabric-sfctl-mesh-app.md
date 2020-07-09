---
title: Azure Service Fabric CLI – sfctl Mesh alkalmazás
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Service Fabric mesh alkalmazás-erőforrások kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7c571136e6bef64c2024c81db85ef736f64a4e01
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906070"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh alkalmazás
Alkalmazás erőforrásainak beolvasása és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Description|
| --- | --- |
| delete | Törli az alkalmazás erőforrását. |
| lista | Felsorolja az összes alkalmazás-erőforrást. |
| megjelenítés | Lekéri az alkalmazás-erőforrást a megadott névvel. |

## <a name="sfctl-mesh-app-delete"></a>sfctl Mesh-alkalmazás törlése
Törli az alkalmazás erőforrását.

Törli a névvel azonosított alkalmazás-erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | Az alkalmazás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-app-list"></a>sfctl Mesh-alkalmazások listája
Felsorolja az összes alkalmazás-erőforrást.

Egy adott erőforráscsoport összes alkalmazás-erőforrásával kapcsolatos információk beolvasása. Az adatok tartalmazzák az alkalmazás leírását és egyéb tulajdonságait.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-app-show"></a>sfctl Mesh-alkalmazás megjelenítése
Lekéri az alkalmazás-erőforrást a megadott névvel.

Az alkalmazás-erőforrással kapcsolatos információk beolvasása a megadott névvel. Az adatok tartalmazzák az alkalmazás leírását és egyéb tulajdonságait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | Az alkalmazás neve. |

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