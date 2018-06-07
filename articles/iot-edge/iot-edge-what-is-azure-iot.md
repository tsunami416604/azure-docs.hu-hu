---
title: Azure-megoldások az eszközök internetes hálózatához (IoT Edge) | Microsoft Docs
description: Egy példa IoT-megoldásarchitektúra áttekintése, és hogy miként kapcsolódik eszközökhöz, az Azure IoT Hub szolgáltatáshoz, az Azure IoT eszközoldali SDK-hoz, az Azure IoT szolgálatásoldali SDK-khoz és egyéb Azure-szolgáltatásokhoz.
author: dominicbetts
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: bd59e740803f8f0e6f5f542805d615772efba913
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630340"
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>További lépések

Az Azure IoT Edge egy olyan Azure-beli szolgáltatás, amely lehetővé teszi elemzések és adatfeldolgozási műveletek elvégzését a peremhálózaton. Az IoT Edge segítségével felruházhatja az eszközeit egy tárolóalapú kóddal, amely vagy a használt Azure-szolgáltatásokból közvetlenül lekért logikát, vagy az Ön saját megoldásspecifikus kódját tartalmazza. Ez lehetővé teszi, hogy az adott eszköz:

* Átjáróeszközként szolgáljon, amely több levéleszköz adatait gyűjti és dolgozza fel.
* Rendellenesség-észlelést végezzen és reagáljon a környezetben beálló változásokra anélkül, hogy utasításokra kellene várnia a felhőből.
* Az adatok előfeldolgozásával és az eredmények elküldésével minimalizálja a sávszélességgel és a tárterülettel kapcsolatos költségeket. 

Az IoT Edge egy felhőbeli felületet is tartalmaz, amely lehetővé teszi az eszközök távfelügyeletét. Így az eszközök fizikai hozzáférése nélkül telepíthet rájuk kódokat, monitorozhatja az állapotukat és frissítheti őket. Az eszközöket egyesével is kezelheti távolról, vagy létrehozhat olyan telepítéseket, amelyek nagy mennyiségű meghatározott eszközre terjednek ki. További információ: [Az egyetlen vagy nagy mennyiségű eszközből álló IoT Edge-telepítések ismertetése][lnk-deployment].

Az IoT Edge működését biztosító összetevőkről bővebben [Az Azure IoT Edge működése][lnk-overview] témakörben olvashat.

Ha még nem használta az IoT Hubot, először tekintse át [Az Azure IoT Hub szolgáltatás áttekintése][lnk-iot-hub] témakört.

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
