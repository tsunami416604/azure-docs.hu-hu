---
title: Az Azure Service Fabric parancssori felület - sfctl beállítások telemetriai adatok |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl beállítások telemetriai parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e0e229f0edb078fe9ce289d0089f34d7cbf9dbf8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285395"
---
# <a name="sfctl-settings-telemetry"></a>sfctl-beállítások telemetria
Az sfctl-példányra helyi telemetriai beállításainak konfigurálása.

Sfctl telemetriai adatokat gyűjt a megadott paraméterek nélkül a parancs neve és azok értékei, sfctl verziója, az operációs rendszer típusa, python-verzió, sikeres vagy sikertelen, a parancs a hibaüzenetet ad vissza.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| set-telemetria | Kapcsolja be- és kikapcsolhatja a telemetriát. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl beállítások telemetriai set-telemetria
Kapcsolja be- és kikapcsolhatja a telemetriát.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – kikapcsolva | Telemetria kikapcsolása. |
| – a | A telemetria bekapcsolásával. Ez az alapértelmezett érték. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

### <a name="examples"></a>Példák

Telemetria kikapcsolása.

```
sfctl settings telemetry set_telemetry --off
```

A telemetria bekapcsolásával.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).