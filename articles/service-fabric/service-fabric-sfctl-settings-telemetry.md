---
title: Azure Service Fabric CLI – sfctl beállítások telemetria
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A sfctl-telemetria konfigurálására szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76903028"
---
# <a name="sfctl-settings-telemetry"></a>sfctl-beállítások telemetriája
Konfigurálja a helyi telemetria-beállításokat a sfctl ezen példányára.

A Sfctl telemetria a megadott paraméterek nélkül gyűjti a parancs nevét, illetve azok értékeit, a Sfctl verzióját, az operációs rendszer típusát, a Python verzióját, a parancs sikerességét vagy hibáját. a visszaadott hibaüzenet.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| set-telemetria | Kapcsolja be vagy ki a telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-beállítások telemetria set-telemetria
Kapcsolja be vagy ki a telemetria.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – ki | Kapcsolja ki a telemetria. |
| – bekapcsolva | Kapcsolja be a telemetria. Ez az alapértelmezett érték. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Kapcsolja ki a telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Kapcsolja be a telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.