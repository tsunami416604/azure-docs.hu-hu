---
title: Az Azure IoT Hub 403006-os devicemaximumActiveFileUploadLimitexceedexceeded hibaelhárítása
description: A 403006-os DeviceMaximumActiveFileUploadLimitExceedexceeded hiba javítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960840"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Ez a cikk a **403006 DeviceMaximumActiveFileUploadLimitExceedexceedexceed** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

A fájlfeltöltési kérelem a **403006-os** hibakóddal sikertelen, és a következő üzenet jelenik meg: "Az aktív fájlfeltöltési kérelmek száma nem haladhatja meg a 10-et".

## <a name="cause"></a>Ok

Minden eszközügyfél legfeljebb [10 egyidejű fájlfeltöltésre van korlátozva.](./iot-hub-devguide-quotas-throttling.md#other-limits) 

Könnyedén túllépheti a korlátot, ha az eszköz nem értesíti az IoT Hubot, ha a fájlfeltöltések befejeződtek. Ezt a problémát általában egy megbízhatatlan eszközoldali hálózat okozza.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy az eszköz azonnal értesíteni tudja az [IoT Hub fájlfeltöltésének befejezését.](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) Ezután próbálja meg [csökkenteni a SAS token TTL fájlfeltöltési konfigurációjához](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a fájlfeltöltésekről, olvassa [el a Fájlok feltöltése az IoT Hubsegítségével című](./iot-hub-devguide-file-upload.md) témakört, és [konfigurálja az IoT Hub-fájlok feltöltését az Azure Portalon keresztül.](./iot-hub-configure-file-upload.md)