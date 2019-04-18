---
title: Az Azure IoT-Eszközfelügyelet az IoT-bővítmény az Azure CLI-vel |} A Microsoft Docs
description: Az IoT-bővítmény használata az Azure CLI-vel eszközök Azure IoT Hub eszközfelügyeleti közvetlen módszerek és lehetőségek az Ikereszköz kívánt tulajdonságait.
author: chrissie926
manager: ''
keywords: az Azure iot-Eszközfelügyelet, az azure iot hub-Eszközfelügyelet, eszközök kezelése iot, iot hub-Eszközfelügyelet
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 6b1029c5532e106c269b47e6e184b9c93faf8d09
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681420"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Azure CLI az Azure IoT Hub-Eszközfelügyelet az IoT-bővítmény használata

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az Azure CLI-vel az IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) egy új nyílt forráskódú IoT-bővítmény, amely hozzáadja a képességeit a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Az Azure CLI Azure resource manager és a felügyeleti végpontok folytatott interakcióra szolgáló parancsokat is tartalmaz. Ha például az Azure CLI használatával hozzon létre egy Azure virtuális Gépen vagy egy IoT hubot. A CLI-bővítmény lehetővé teszi, hogy az Azure-szolgáltatások révén az Azure CLI-vel így további szolgáltatásspecifikus funkciókat elérhetővé. Az IoT-bővítmény hozzáférést IoT fejlesztői parancssor minden IoT Hub, IoT Edge és IoT Hub Device Provisioning Service-képességeket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Lehetőséget          | Tevékenység  |
|----------------------------|-----------|
| Közvetlen metódusok             | Hogy egy eszköz, például indítása vagy leállítása üzeneteket küldjenek vagy az eszköz újraindítása jár el.                                        |
| Ikereszköz kívánt tulajdonságait    | Egy eszköz egyes állapotok, például a LED zöld vagy a telemetriai adatok küldési időköz – 30 percet kell helyezni.         |
| Ikereszköz jelentett tulajdonságait   | Az eszköz a jelentett állapot beolvasása. Például az eszköz jelenti a LED most villogó-e.                                    |
| Ikereszköz – címkék                  | Store eszközspecifikus metaadatokat a felhőben. Például a központi telepítési helye a Eladóautomata.                         |
| Ikereszköz-lekérdezések        | A lekérdezés összes ikereszközök beolvasni a tetszőleges feltételek, például a használható eszközök azonosítása. |

A különbségek ismertetése és útmutató az ezek a beállítások használatával, lásd: [eszközről a felhőbe való kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) és [felhőből az eszközre irányuló kommunikáció útmutatást](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT Hub továbbra is fennáll, az eszközök ikereszköze a minden eszközön, amelyhez kapcsolódik hozzá. Ikereszközök kapcsolatos további információkért lásd: [ikereszközök – első lépések](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti az Azure CLI használata az IoT-bővítmény különböző lehetőségek a fejlesztői gépen.

## <a name="what-you-do"></a>TEENDŐ

Azure CLI és az IoT-bővítmény futtatása Azure CLI-hez a különböző felügyeleti lehetőségek.

## <a name="what-you-need"></a>Mi szükséges

* Végezze el a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyag vagy az eszköz oktatóanyagokban; például [Raspberry Pi és node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ez érvényes a következő követelményeknek:

  - Aktív Azure-előfizetés.
  - Az Azure IoT hub az előfizetéséhez.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.

* Győződjön meg arról, hogy az eszköz fut az ügyfélalkalmazásban, ez az oktatóanyag során.

* [Python 2.7x vagy Python 3.x](https://www.python.org/downloads/)

* Az Azure parancssori felület. Ha szeretné telepíteni, tekintse meg [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Legalább az Azure CLI 2.0.24-es verzióját kell vagy újabb. A verziószámot az `az –version` paranccsal ellenőrizheti. 

* Az IoT-bővítmény telepítése. A legegyszerűbb módszer az `az extension add --name azure-cli-iot-ext` futtatása. [Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) több módszert is ismertet a bővítmény telepítésére.

## <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába.

Jelentkezzen be az Azure-fiókjába a következő parancs futtatásával:

```bash
az login
```

## <a name="direct-methods"></a>Közvetlen metódusok

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Ikereszköz kívánt tulajdonságait

Állítsa be a kívánt tulajdonság időköz = 3000 a következő parancs futtatásával:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Ez a tulajdonság elolvashatja az eszközről.

## <a name="device-twin-reported-properties"></a>Ikereszköz jelentett tulajdonságait

Az eszköz a jelentett tulajdonságok beolvasása a következő parancs futtatásával:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Az egyik az ikereszköz jelentett tulajdonságok $metadata. amely megjeleníti a legutóbbi alkalommal az eszközalkalmazás $lastUpdated frissítve a jelentett tulajdonságok beállítása.

## <a name="device-twin-tags"></a>Device twin címkék

Megjeleníti a címkék és tulajdonságok az eszköz a következő parancs futtatásával:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adjon hozzá egy mezőt szerepkör = hőmérsékleti és páratartalom az eszközön a következő parancs futtatásával:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Ikereszköz-lekérdezések

Eszközök a szerepkör címkével ellátott lekérdezése = 'hőmérsékleti és páratartalom' a következő parancs futtatásával:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Minden eszköz, kivéve azokat, egy szerepkör lekérdezése = 'hőmérsékleti és páratartalom' a következő parancs futtatásával:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>További lépések

Bemutattuk, hogyan lehet az eszköz a felhőbe irányuló üzenetek figyeléséhez és a felhőből az eszközre irányuló üzenetküldés az IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
