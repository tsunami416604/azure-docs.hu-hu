---
title: Az Azure IoT Eszközfelügyelet IoT-bővítménnyel Azure CLI 2.0 |} Microsoft Docs
description: Az Azure CLI 2.0 eszközt az Azure IoT Hub eszköz kezeléséhez, ha kiemeli a közvetlen módszerek és a kettős kívánt tulajdonságokkal kezelési lehetőségek IoT bővítményt használja.
author: chrissie926
manager: ''
keywords: az Azure iot kezelés, az azure iot hub – eszközkezelés, eszköz felügyeleti iot, iot hub – Eszközkezelés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: dc96e70a031d6080217e71b829ec5de3c64e4cf7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632815"
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Az Azure CLI 2.0 IoT bővítményt használja Azure IoT Hub eszközkezeléshez

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az IoT-bővítmény az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) IoT-bővítménnyel, amely a képességeit ad hozzá egy új nyílt forráskódú [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Az Azure CLI 2.0 és az Azure resource manager és a felügyeleti végpontok közötti kommunikáció során parancsokat tartalmaz. Például az Azure CLI 2.0 hozhat létre egy Azure virtuális gép vagy egy IoT-központot. A CLI-bővítmény lehetővé teszi, hogy az Azure-szolgáltatások révén a szolgáltatással kapcsolatos további képességeket hozzáférést az Azure parancssori felület próbálkozik. Az IoT-bővítmény parancssori hozzáférést biztosít a IoT fejlesztők minden IoT Hub, IoT széle és az IoT Hub eszköz kiépítése szolgáltatás képességeit.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Lehetőséget          | Tevékenység                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Közvetlen metódusok             | Egy eszköz, például indítása és leállítása üzenetküldésre, vagy az eszköz újraindul működésre tétele.                                        |
| A két szükségeskonfiguráció-tulajdonságok    | Egy eszköz üzembe egyes állapotok, például egy LED zöld vagy a telemetriai adatok küldési időköze és 30 perc.         |
| A két jelentett tulajdonságai   | Egy eszköz jelentett állapotának beolvasására. Például az eszköz jelentés készít a LED most villogó-e.                                    |
| A két címkék                  | A felhőben tárolt eszközre vonatkozó metaadatok. Például a központi telepítési helye a Eladóautomata.                         |
| Eszköz iker lekérdezések        | A lekérdezés az összes eszköz twins rendelkező tetszőleges feltételek, például az eszközök, amelyek használható azonosító beolvasása. |

A különbségek a Magyarázat és útmutatást ezen beállítások használatával, lásd: [eszközről a felhőbe kommunikációs útmutatást](iot-hub-devguide-d2c-guidance.md) és [felhő eszközre kommunikációs útmutatást](iot-hub-devguide-c2d-guidance.md).

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT-központ továbbra is fennáll, egy eszköz iker az egyes eszközök ahhoz csatlakozó ügyfélnél. Eszköz twins kapcsolatos további információkért lásd: [Ismerkedés az eszköz twins](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti az IoT-bővítmény a fejlesztői gépen különböző kezelési lehetőségek az Azure CLI 2.0 használata.

## <a name="what-you-do"></a>Mit

Azure CLI 2.0 és az IoT-bővítmény futtatása az Azure CLI 2.0 különböző felügyeleti beállításokkal.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [beállítani az eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely hozzá van rendelve az alábbi követelményeknek:
  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazást, amely üzeneteket küld az Azure IoT hub.

- Győződjön meg arról, hogy az eszköz az ügyfélalkalmazás az oktatóanyag során fut-e.

- [Python 2.7x vagy Python 3.x](https://www.python.org/downloads/)

- Az Azure CLI 2.0 telepítése. Windows rendszeren a legegyszerűbb megoldás a telepítésre az [MSI](https://aka.ms/InstallAzureCliWindows) letöltése és telepítése. A telepítési utasításokat hajtsa végre [Microsoft Docs](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) beállítani az Azure CLI 2.0 a környezetben. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –version` paranccsal ellenőrizheti. 

- Az IoT-bővítményének telepítése. A legegyszerűbb módszer az `az extension add --name azure-cli-iot-ext` futtatása. [Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) több módszert is ismertet a bővítmény telepítésére.


## <a name="log-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába.

Jelentkezzen be az Azure-fiókjával a következő parancs futtatásával:

```bash
az login
```

## <a name="direct-methods"></a>Közvetlen metódusok

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>A két szükséges tulajdonságai

Állítsa be a kívánt tulajdonság intervallumát 3000 = a következő parancs futtatásával:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Ez a tulajdonság elolvashatja az eszközről.

## <a name="device-twin-reported-properties"></a>A két eszköz jelentett tulajdonságai

Az eszköz jelentett tulajdonságait olvassa be a következő parancs futtatásával:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

A kettős egyik jelentette tulajdonságok $metadata. az eszköz alkalmazás megjelenítheti az utolsó $lastUpdated frissítése a jelentett tulajdonságkészlet.

## <a name="device-twin-tags"></a>Eszköz iker címkék

A címkéket és az eszköz Tulajdonságok megjelenítése a következő parancs futtatásával:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

A mező szerepkör hozzáadása = a hőmérséklet és a páratartalom az eszköz a következő parancs futtatásával:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Eszköz iker lekérdezések

Eszközök a szerepkör címkével ellátott lekérdezése = "a hőmérséklet és a páratartalom" a következő parancs futtatásával:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Szerepkör címkével ellátott kivételével minden eszköz lekérdezése = "a hőmérséklet és a páratartalom" a következő parancs futtatásával:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>További lépések

Hogy megismerte az eszköz a felhőbe küldött üzeneteket figyelése, és az IoT-eszközök és az Azure IoT-központ között felhő eszközre üzeneteket küldeni.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
