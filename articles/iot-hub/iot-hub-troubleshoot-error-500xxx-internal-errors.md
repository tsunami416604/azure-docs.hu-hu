---
title: Az Azure IoT Hub 500xxx belső hibáinak elhárítása
description: Ismerje meg, hogyan lehet kijavítani 500xxx Belső hibák
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271043"
---
# <a name="500xxx-internal-errors"></a>500xxx Belső hibák

Ez a cikk az **500xxx belső hibák**okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az IoT Hubhoz intézett kérés 500-mal és/vagy valamilyen "kiszolgálóhibával" kezdődő hibával sikertelen. Néhány lehetőség:

* **500001 ServerError**: Az IoT Hub kiszolgálóoldali problémába ütközött.

* **500008 GenericTimeout:** Az IoT Hub nem tudta teljesíteni a csatlakozási kérelmet az időtúllépés előtt.

* **ServiceUnavailable (nincs hibakód)**: Az IoT Hub belső hibát észlelt.

* **InternalServerError (nincs hibakód)**: Az IoT Hub belső hibát észlelt.

## <a name="cause"></a>Ok

Az 500xxx-es hibaválasznak számos oka lehet. Minden esetben a probléma valószínűleg átmeneti. Míg az IoT Hub csapata keményen dolgozik [az SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)karbantartásán, az IoT Hub-csomópontok kis részhalmazai esetenként átmeneti hibákat tapasztalhatnak. Amikor az eszköz megpróbál csatlakozni egy olyan csomóponthoz, amelynek problémái vannak, ez a hibaüzenet jelenik meg.

## <a name="solution"></a>Megoldás

Az 500xxx-es hibák csökkentése érdekében adjon ki újrapróbálkozást az eszközről. Az [újrapróbálkozások automatikus kezeléséhez](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)győződjön meg arról, hogy az Azure [IoT SDK-k](./iot-hub-devguide-sdks.md)legújabb verzióját használja. Az átmeneti hibakezelésés újrapróbálkozások ajánlott eljárását az [Átmeneti hibakezelés](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)című témakörben tésszet.  Ha a probléma továbbra is fennáll, ellenőrizze [az Erőforrás állapota](./iot-hub-monitor-resource-health.md#use-azure-resource-health) és az Azure [állapota,](https://status.azure.com/) hogy ha az IoT Hub egy ismert probléma. Használhatja a [manuális feladatátvételi szolgáltatást](./tutorial-manual-failover.md)is. Ha nincsenek ismert problémák, és a probléma továbbra is fennáll, további vizsgálat céljából [forduljon](https://azure.microsoft.com/support/options/) az ügyfélszolgálathoz.
