---
title: "Az Azure IoT-eszközök kezelése a IOT hubbal-explorer |} Microsoft Docs"
description: "Eszközzel az IOT hubbal-explorer CLI Azure IoT Hub kezeléséhez, ha kiemeli a közvetlen módszerek és a kettős kívánt tulajdonságok felügyeleti lehetőségeket."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "az Azure iot kezelés, az azure iot hub – eszközkezelés, eszköz felügyeleti iot, iot hub – Eszközkezelés"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.openlocfilehash: 5b7a5057bdfb5920fbb5759bed1f5561cfa1d7e0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Használja az IOT hubbal-explorer Azure IoT Hub – Eszközkezelés

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[IOT hubbal-explorer](https://github.com/azure/iothub-explorer) parancssori eszköz, amely a gazdagépen futtat az IoT hub beállításjegyzék eszköz identitásainak kezelése a számítógépet. Az ismét felügyeleti beállításokkal rendelkezik, amelyek segítségével különböző feladatok végrehajtására.

| Lehetőséget          | Tevékenység                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Közvetlen metódusok             | Egy eszköz, például indítása és leállítása üzenetküldésre, vagy az eszköz újraindul működésre tétele.                                        |
| A két szükségeskonfiguráció-tulajdonságok    | Egy eszköz üzembe egyes állapotok, például egy LED zöld vagy a telemetriai adatok küldési időköze és 30 perc.         |
| A két jelentett tulajdonságai   | Egy eszköz jelentett állapotának beolvasására. Például az eszköz jelentés készít a LED most villogó-e.                                    |
| A két címkék                  | A felhőben tárolt eszközre vonatkozó metaadatok. Például a központi telepítési helye a Eladóautomata.                         |
| Felhő-eszközre küldött üzenetek   | Értesítések küldése eszközre. Például "nagyon valószínű ma eső számára. Ne feledje az összevonó kapcsolja."              |
| Eszköz iker lekérdezések        | A lekérdezés az összes eszköz twins rendelkező tetszőleges feltételek, például az eszközök, amelyek használható azonosító beolvasása. |

A különbségek a Magyarázat és útmutatást ezen beállítások használatával, lásd: [eszközről a felhőbe kommunikációs útmutatást](iot-hub-devguide-d2c-guidance.md) és [felhő eszközre kommunikációs útmutatást](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT-központ továbbra is fennáll, egy eszköz iker az egyes eszközök ahhoz csatlakozó ügyfélnél. Eszköz twins kapcsolatos további információkért lásd: [Ismerkedés az eszköz twins](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti a fejlesztői gépen különböző kezelési lehetőségek az IOT hubbal-Intéző segítségével.

## <a name="what-you-do"></a>Mit

IOT hubbal-kezelő futtatása a különböző felügyeleti beállításokkal.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [beállítani az eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely hozzá van rendelve az alábbi követelményeknek:
  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazást, amely üzeneteket küld az Azure IoT hub.
- Győződjön meg arról, hogy az eszköz az ügyfélalkalmazás az oktatóanyag során fut-e.
- IOT hubbal-Explorerben [telepítse az IOT hubbal-explorer](https://github.com/azure/iothub-explorer) a fejlesztési számítógépén.

## <a name="connect-to-your-iot-hub"></a>Az IoT hub kapcsolódni

Az IoT hub csatlakoztatása a következő parancs futtatásával:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Használja az IOT hubbal-explorer közvetlen módszer

Hívása a `start` módszer használatát az eszköz alkalmazásának üzeneteket küldhet az IoT hub a következő parancs futtatásával:

```bash
iothub-explorer device-method <your device Id> start
```

Hívása a `stop` módszer az eszköz alkalmazásban való küldésének leállításához az IoT hub üzenetek a következő parancs futtatásával:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Használja az IOT hubbal-explorer iker a kívánt tulajdonságokkal

Állítsa be a kívánt tulajdonság intervallumát 3000 = a következő parancs futtatásával:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Ez a tulajdonság az eszköz által is olvasható.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Használja az IOT hubbal-explorer iker által jelentett tulajdonságokkal

Az eszköz jelentett tulajdonságait olvassa be a következő parancs futtatásával:

```bash
iothub-explorer get-twin <your device id>
```

A tulajdonságok egyike $metadata. az eszköz megjelenítheti az utolsó $lastUpdated küld vagy üzenetet kap.

## <a name="use-iothub-explorer-with-twins-tags"></a>-Kezelővel IOT hubbal-iker tartozó címkékkel

A címkéket és az eszköz Tulajdonságok megjelenítése a következő parancs futtatásával:

```bash
iothub-explorer get-twin <your device id>
```

A mező szerepkör hozzáadása = a hőmérséklet és a páratartalom az eszköz a következő parancs futtatásával:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"

```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>IOT hubbal-explorer használata a felhő-eszközre küldött üzenetek

"A Hello, World" üzenetet küldeni az eszköz a következő parancs futtatásával:

```bash
iothub-explorer send <device-id> "Hello World"
```

Lásd: [IOT hubbal-Intéző segítségével az eszköz és az IoT-központ között üzeneteket küldjön és fogadjon](iot-hub-explorer-cloud-device-messaging.md) valós forgatókönyv esetén ez a parancs használatával.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Használja az IOT hubbal-explorer eszköz twins lekérdezések

Eszközök a szerepkör címkével ellátott lekérdezése = "a hőmérséklet és a páratartalom" a következő parancs futtatásával:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Szerepkör címkével ellátott kivételével minden eszköz lekérdezése = "a hőmérséklet és a páratartalom" a következő parancs futtatásával:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Következő lépések

Hogy megismerte az IOT hubbal-explorer használata a különböző felügyeleti lehetőségek.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
