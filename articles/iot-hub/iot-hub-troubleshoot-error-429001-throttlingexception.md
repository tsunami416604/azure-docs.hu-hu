---
title: Az Azure IoT Hub hibáinak elhárítása 429001 ThrottlingException
description: Ismerje meg, hogyan javíthatja a 429001-es hibát a ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960697"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Ez a cikk a **429001 ThrottlingException** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

A IoT Hub-kérések sikertelenek lesznek a 429001-es **ThrottlingException**hiba esetén.

## <a name="cause"></a>Ok

Túllépte a kért művelet IoT Hub [szabályozási korlátait](./iot-hub-devguide-quotas-throttling.md) .

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a sávszélesség-szabályozási korlátot a *telemetria üzenet küldési kísérletek* metrikájának a fent megadott korlátokkal való összehasonlításával éri el. Azt is megtekintheti, hogy a *sávszélesség-szabályozás hány* mérőszámot tartalmaz. További információ ezekről és a IoT Hubhoz elérhető egyéb mérőszámokról: [IoT hub mérőszámok és azok használata](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

A IoT Hub a 429 ThrottlingException csak akkor adja vissza, ha a határérték túl hosszú ideig megsértette a korlátot. Erre azért van szükség, hogy az üzenetek ne legyenek elvetve, ha az IoT hub burst forgalmat kap. Eközben az IoT Hub a művelet sávszélesség-szabályozási értékén dolgozza fel az üzeneteket, ami lassú lehet, ha túl nagy a forgalom a várólistán. További tudnivalókért lásd [az IoT Hub adatforgalom-alakításával kapcsolatos részt](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>További lépések

Ha kvóta-vagy sávszélesség-korlátozást használ, vegye fontolóra [a IoT hub skálázását](./iot-hub-scaling.md) .