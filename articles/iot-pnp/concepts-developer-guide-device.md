---
title: Eszköz fejlesztői útmutató (C) – IoT Plug and Play | Microsoft Docs
description: Az IoT Plug and Play leírása C-eszközök fejlesztői számára
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 3aa236570e518b142adb8382387a8cdea4fc08a0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453269"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT Plug and Play-eszköz fejlesztői útmutatója

A IoT Plug and Play lehetővé teszi, hogy intelligens eszközöket hozzon létre, amelyek az Azure IoT alkalmazásaiban hirdetik ki képességeiket. A IoT Plug and Play-eszközökhöz nincs szükség manuális konfigurálásra, ha az ügyfél a Plug and Play-kompatibilis alkalmazások IoT csatlakoztatja őket.

Egy intelligens eszköz közvetlenül is megvalósítható, [modulokat](../iot-hub/iot-hub-devguide-module-twins.md)használhat, vagy [IoT Edge modulokat](../iot-edge/about-iot-edge.md)használhat.

Ez az útmutató ismerteti az eszközök, modulok vagy IoT Edge modul létrehozásához szükséges alapvető lépéseket, amelyek a [IoT Plug and Play konvencióit](../iot-pnp/concepts-convention.md)követik.

IoT Plug and Play eszköz, modul vagy IoT Edge modul létrehozásához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az eszköz vagy a MQTT vagy a MQTT over WebSockets protokoll használatával csatlakozik az Azure IoT Hubhoz.
1. Hozzon létre egy [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modellt az eszköz leírásához. További információ: [az összetevők megértése a IoT Plug and Play-modellekben](concepts-components.md).
1. Frissítse eszközét vagy modulját, hogy bejelentse az `model-id` eszköz kapcsolatának részét.
1. Telemetria, tulajdonságok és parancsok implementálása a [IoT Plug and Play konvenciók](concepts-convention.md) használatával

Miután az eszköz vagy a modul implementációja elkészült, az [Azure IoT Explorer](howto-use-iot-explorer.md) segítségével ellenőrizheti, hogy az eszköz követi-e a IoT Plug and Play konvenciókat.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a IoT Plug and Play-eszköz fejlesztését, néhány további erőforrást is talál:

- [Digitális Twins-definíciós nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C eszköz SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [Modell összetevői](concepts-components.md)
- [A DTDL authoring Tools telepítése és használata](howto-use-dtdl-authoring-tools.md)
- [IoT Plug and Play Service – fejlesztői útmutató](concepts-developer-guide-service.md)