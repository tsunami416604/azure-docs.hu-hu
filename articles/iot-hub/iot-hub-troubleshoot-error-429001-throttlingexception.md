---
title: Az Azure IoT Hub 429001-es throttlingException hibájának hibaelhárítása
description: A 429001-es throttlingException hiba javításának ismertetése
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960697"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Ez a cikk a **429001 ThrottlingException** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az IoT Hubra irányuló kérések sikertelenek a **429001-es throttlingException**hibával.

## <a name="cause"></a>Ok

Az IoT Hub [sávszélesség-szabályozási korlátjai](./iot-hub-devguide-quotas-throttling.md) túllépték a kért művelet.

## <a name="solution"></a>Megoldás

Ellenőrizze, hogy eléri-e a sávszélesség-szabályozási korlátot, ha összehasonlítja a *telemetriai üzenet küldési kísérletek* metrikáját a fent megadott korlátokkal. A *szabályozási hibák száma* metrika is ellenőrizheti. Ezekről és az IoT Hubhoz elérhető egyéb metrikákról az [IoT Hub-metrikák ban és azok használatának módjában](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)talál további információt.

Az IoT Hub 429 throttlingException értéket ad vissza, csak akkor, ha a korlát túl hosszú ideig meg lett sértve. Ez úgy történik, hogy az üzenetek ne kerüljön el, ha az IoT hub kap burst forgalmat. Eközben az IoT Hub a művelet sávszélesség-szabályozási értékén dolgozza fel az üzeneteket, ami lassú lehet, ha túl nagy a forgalom a várólistán. További tudnivalókért lásd [az IoT Hub adatforgalom-alakításával kapcsolatos részt](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>További lépések

Fontolja meg [az IoT Hub felskálázását,](./iot-hub-scaling.md) ha kvóta- vagy sávszélesség-szabályozási korlátokba ütközik.