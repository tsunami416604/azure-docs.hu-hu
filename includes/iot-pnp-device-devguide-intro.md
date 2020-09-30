---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 09/24/2020
ms.openlocfilehash: 706e936590f5f47ee2989bf77b3caad67a8f28fc
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579792"
---
A IoT Plug and Play lehetővé teszi, hogy intelligens eszközöket hozzon létre, amelyek az Azure IoT alkalmazásaiban hirdetik ki képességeiket. A IoT Plug and Play-eszközökhöz nincs szükség manuális konfigurálásra, ha az ügyfél a Plug and Play-kompatibilis alkalmazások IoT csatlakoztatja őket.

Egy intelligens eszköz közvetlenül is megvalósítható, [modulokat](../articles/iot-hub/iot-hub-devguide-module-twins.md)használhat, vagy [IoT Edge modulokat](../articles/iot-edge/about-iot-edge.md)használhat.

Ez az útmutató ismerteti az eszközök, modulok vagy IoT Edge modul létrehozásához szükséges alapvető lépéseket, amelyek a [IoT Plug and Play konvencióit](../articles/iot-pnp/concepts-convention.md)követik.

IoT Plug and Play eszköz, modul vagy IoT Edge modul létrehozásához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az eszköz vagy a MQTT vagy a MQTT over WebSockets protokoll használatával csatlakozik az Azure IoT Hubhoz.
1. Hozzon létre egy [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modellt az eszköz leírásához. További információ: [az összetevők megértése a IoT Plug and Play-modellekben](../articles/iot-pnp/concepts-components.md).
1. Frissítse eszközét vagy modulját, hogy bejelentse az `model-id` eszköz kapcsolatának részét.
1. Telemetria, tulajdonságok és parancsok implementálása a [IoT Plug and Play konvenciók](../articles/iot-pnp/concepts-convention.md) használatával

Miután az eszköz vagy a modul implementációja elkészült, az [Azure IoT Explorer](../articles/iot-pnp/howto-use-iot-explorer.md) segítségével ellenőrizheti, hogy az eszköz követi-e a IoT Plug and Play konvenciókat.
