---
title: Az Azure IoT Hub hibáinak elhárítása 412002 DeviceMessageLockLost
description: Ismerje meg, hogyan javíthatja a 412002-es hibát a DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960762"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Ez a cikk a **412002 DeviceMessageLockLost** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

A felhőből az eszközre irányuló üzenetek küldésére tett kísérlet során a kérelem meghiúsul a **412002**-es hiba DeviceMessageLockLost.

## <a name="cause"></a>Ok

Ha egy eszköz egy felhőből az eszközre irányuló üzenetet kap a sorból (például [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)használatával), az üzenet zárolási időtúllépési időtartamának egy percen belül IoT hub zárolva lesz. Ha az eszköz a zárolási időkorlát lejárta után megpróbálja befejezni az üzenetet, IoT Hub eldönti ezt a kivételt.

## <a name="solution"></a>Megoldás

Ha IoT Hub nem kap értesítést az egyperces Zárolási időkorlát időtartamán belül, az üzenetet visszaállítja *várólistán lévő* állapotba. Az eszköz megpróbálhatja újra fogadni az üzenetet. Ha meg szeretné akadályozni, hogy a hiba a jövőben is megtörténjen, hajtsa végre az eszköz oldalának logikáját az üzenet fogadása után egy percen belül. Ez az egyperces időtúllépés nem módosítható.