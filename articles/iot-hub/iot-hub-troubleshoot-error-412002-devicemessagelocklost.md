---
title: Az Azure IoT Hub 412002 DeviceMessageLockLost hibával kapcsolatos hibaelhárítása
description: A 412002 DeviceMessageLockLost hiba javítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960762"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Ez a cikk a **412002 DeviceMessageLockLost** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Amikor felhőből eszközre irányuló üzenetet próbál küldeni, a kérés a **412002 DeviceMessageLockLost**hibával sikertelen.

## <a name="cause"></a>Ok

Amikor egy eszköz felhőből az eszközre irányuló üzenetet kap [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)a várólistából (például használatával), az üzenetet az IoT Hub zárolja egy perces zárolási időout-időtartammal. Ha az eszköz megpróbálja befejezni az üzenetet a zárolási időkitöltés lejárta után, az IoT Hub eldobja ezt a kivételt.

## <a name="solution"></a>Megoldás

Ha az IoT Hub nem kapja meg az értesítést az egyperces zárolási időtúllépés időtartamán belül, az üzenet *visszaállítja a várólistán lévő* állapotba. Az eszköz megpróbálhatja újra fogadni az üzenetet. A hiba jövőbeni előfordulásának elkerülése érdekében valósítsa meg az eszközoldali logikát az üzenet fogadásától számított egy percen belül. Ezt az egyperces időt nem lehet megváltoztatni.