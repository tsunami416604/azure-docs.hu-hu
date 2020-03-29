---
title: Azure Service Fabric CLI- sfctl-beállítások telemetria
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Az sfctl-telemetria konfigurálásához szükséges parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903028"
---
# <a name="sfctl-settings-telemetry"></a>sfctl-beállítások telemetriája
Konfigurálja az sfctl ezen példányához helyi telemetriai beállításokat.

Sfctl telemetriai adatok gyűjti parancs nevét paraméterek nélkül megadott vagy azok értékeit, sfctl verzió, operációs rendszer típusa, python verzió, a sikeres vagy sikertelen a parancs, a hibaüzenet et adott vissza.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| beállított telemetria | A telemetriai adatok be- és kikapcsolása. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-beállítások telemetriai beállítás-telemetriai adatai
A telemetriai adatok be- és kikapcsolása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --off | Kapcsolja ki a telemetriai adatokat. |
| --be | Kapcsolja be a telemetriai adatokat. Ez az alapértelmezett érték. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Kapcsolja ki a telemetriai adatokat.

```
sfctl settings telemetry set_telemetry --off
```

Kapcsolja be a telemetriai adatokat.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.