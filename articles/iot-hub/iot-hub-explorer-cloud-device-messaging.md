---
title: Az IOT hubbal-explorer üzenetküldési Azure IoT Hub felhőalapú eszközök felügyelete |} Microsoft Docs
description: Megtudhatja, hogyan eszközzel az IOT hubbal-explorer CLI figyelő eszközre cloud (D2C) üzenetek és felhő küldeni az Azure IoT Hub eszköz (C2D) üzeneteket.
services: iot-hub
documentationcenter: ''
author: shizn
manager: timlt
tags: ''
keywords: IOT hubbal explorer, üzenetküldés, iot hub felhő eszközre, az eszköz üzenetkezelés felhő felhőalapú eszköz
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: 7e74c08849ae2f98d3189c0cd3d340874ea085ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Az eszköz és az IoT-központ között üzeneteket küldjön és fogadjon IOT hubbal-kezelő használatával

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[IOT hubbal-explorer](https://github.com/azure/iothub-explorer) rendelkezik, amely egyszerűbbé teszi az IoT-központ felügyeleti néhány olyan parancsok. Ez az oktatóanyag az IOT hubbal-kezelő használata az eszköz és az IoT hub között üzeneteket küldjön és fogadjon összpontosít.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Amiről tanulni fog

Megismerheti, hogyan kezelővel IOT hubbal-eszköz a felhőbe küldött üzeneteket figyelése és a felhő-eszközre küldött üzenetek küldéséhez. Eszköz a felhőbe küldött üzeneteket érzékelőadatait, amely az eszköz összegyűjti, és ezután elküldi az IoT hub lehet. Felhő-eszközre küldött üzenetek parancsokat, amelyek az IoT hub küld az eszközre, amely az eszköz csatlakozik LED villogni lehet.

## <a name="what-you-will-do"></a>Mit fog

- IOT hubbal-explorer segítségével figyelheti az eszköz a felhőbe küldött üzeneteket.
- IOT hubbal-explorer segítségével felhő eszközre üzenetek.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [beállítani az eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely hozzá van rendelve az alábbi követelményeknek:
  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazást, amely üzeneteket küld az Azure IoT hub.
- iothub-explorer. ([Telepítse az IOT hubbal-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Eszköz a felhőbe küldött üzeneteket figyelése

Az IoT hub az eszközről küldött üzenetek figyeléséhez, kövesse az alábbi lépéseket:

1. Nyissa meg a konzol ablakot.
1. Futtassa az alábbi parancsot:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Első `<device-id>` és `<IoTHubConnectionString>` az IoT hub a. Győződjön meg arról, hogy az előző oktatóanyag befejezése után. Vagy próbálja meg használni `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` Ha `HostName`, `SharedAccessKeyName` és `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Üzenetküldés a felhőből az eszközökre

Az IoT hub egy üzenetet küldeni az eszközre, kövesse az alábbi lépéseket:

1. Nyissa meg a konzol ablakot.
1. Az IoT hub a munkamenetet indítani a következő parancs futtatásával:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Üzenet küldése az eszköz a következő parancs futtatásával:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

A parancs villogjon, amely az eszköz csatlakozik, és az üzenetet küld az eszköz a LED-jét.

> [!Note]
> Nincs szükség az eszköz egy külön ack parancs küldése a az IoT hub, az üzenet fogadásakor.

## <a name="next-steps"></a>További lépések

Hogy megismerte az eszköz a felhőbe küldött üzeneteket figyelése, és az IoT-eszközök és az Azure IoT-központ között felhő eszközre üzeneteket küldeni.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
