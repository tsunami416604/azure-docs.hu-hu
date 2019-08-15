---
title: Azure Service Fabric CLI – sfctl beállítások telemetria | Microsoft Docs
description: Ismerteti a Service Fabric CLI-sfctl beállítások telemetria parancsait.
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
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035201"
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