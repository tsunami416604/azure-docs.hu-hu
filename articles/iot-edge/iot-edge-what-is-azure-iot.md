---
title: "Azure-megoldások az eszközök internetes hálózatához (IoT Edge) | Microsoft Docs"
description: "Egy példa IoT-megoldásarchitektúra áttekintése, és hogy miként kapcsolódik eszközökhöz, az Azure IoT Hub szolgáltatáshoz, az Azure IoT eszközoldali SDK-hoz, az Azure IoT szolgálatásoldali SDK-khoz és egyéb Azure-szolgáltatásokhoz."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Következő lépések

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
