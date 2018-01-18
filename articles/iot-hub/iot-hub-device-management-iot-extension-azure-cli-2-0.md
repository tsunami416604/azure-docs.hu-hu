---
title: "Az Azure IoT Eszközfelügyelet IoT-bővítménnyel Azure CLI 2.0 |} Microsoft Docs"
description: "Az Azure CLI 2.0 eszközt az Azure IoT Hub eszköz kezeléséhez, ha kiemeli a közvetlen módszerek és a kettős kívánt tulajdonságokkal kezelési lehetőségek IoT bővítményt használja."
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: "az Azure iot kezelés, az azure iot hub – eszközkezelés, eszköz felügyeleti iot, iot hub – Eszközkezelés"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 760a6a30513308aa59c5e253e3b91e28cf9e3241
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Az Azure CLI 2.0 IoT bővítményt használja Azure IoT Hub eszközkezeléshez

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az IoT-bővítmény az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) IoT-bővítménnyel, amely a képességeit ad hozzá egy új nyílt forráskódú [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) Azure resource manager és a felügyeleti végpontok való interakció parancsokat tartalmaz. Az Azure CLI 2.0 és az Azure resource manager és a felügyeleti végpontok közötti kommunikáció során parancsokat tartalmaz. Például az Azure CLI 2.0 hozhat létre egy Azure virtuális gép vagy egy IoT-központot. A CLI-bővítmény lehetővé teszi, hogy az Azure-szolgáltatások révén a szolgáltatással kapcsolatos további képességeket hozzáférést az Azure parancssori felület próbálkozik. Az IoT-bővítmény parancssori hozzáférést biztosít a IoT fejlesztők minden IoT Hub, IoT széle és az IoT Hub eszköz kiépítése szolgáltatás képességeit.

| Lehetőséget          | Tevékenység                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Közvetlen metódusok             | Egy eszköz, például indítása és leállítása üzenetküldésre, vagy az eszköz újraindul működésre tétele.                                        |
| A két szükségeskonfiguráció-tulajdonságok    | Egy eszköz üzembe egyes állapotok, például egy LED zöld vagy a telemetriai adatok küldési időköze és 30 perc.         |
| A két jelentett tulajdonságai   | Egy eszköz jelentett állapotának beolvasására. Például az eszköz jelentés készít a LED most villogó-e.                                    |
| A két címkék                  | A felhőben tárolt eszközre vonatkozó metaadatok. Például a központi telepítési helye a Eladóautomata.                         |
| Eszköz iker lekérdezések        | A lekérdezés az összes eszköz twins rendelkező tetszőleges feltételek, például az eszközök, amelyek használható azonosító beolvasása. |

A különbségek a Magyarázat és útmutatást ezen beállítások használatával, lásd: [eszközről a felhőbe kommunikációs útmutatást](iot-hub-devguide-d2c-guidance.md) és [felhő eszközre kommunikációs útmutatást](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT-központ továbbra is fennáll, egy eszköz iker az egyes eszközök ahhoz csatlakozó ügyfélnél. Eszköz twins kapcsolatos további információkért lásd: [Ismerkedés az eszköz twins](iot-hub-node-node-twin-getstarted.md).

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

- Az Azure CLI 2.0 telepítése. Egy egyszerű telepítése Windows módja töltse le és telepítse a [MSI](https://aka.ms/InstallAzureCliWindows). A telepítési utasításokat hajtsa végre [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) beállítani az Azure CLI 2.0 a környezetben. Az Azure CLI 2.0-s verziója legalább a 2.0.24 kell lennie vagy újabb. Használjon `az –version` érvényesítéséhez. 

- Az IoT-bővítményének telepítése. A legegyszerűbb módszer `az extension add --name azure-cli-iot-ext`. [Az IoT-bővítmény fontos](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) többféle módon, a bővítmény telepítéséhez ismerteti.


## <a name="login-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjával

Bejelentkezés az Azure-fiókjával a következő parancs futtatásával:

```bash
az login
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-direct-methods"></a>Az Azure CLI 2.0 IoT bővítményt használja a közvetlen módszer

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-desired-properties"></a>Az IoT-bővítmény használja az Azure CLI 2.0 iker a kívánt tulajdonságokkal

Állítsa be a kívánt tulajdonság intervallumát 3000 = a következő parancs futtatásával:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Ez a tulajdonság elolvashatja az eszközről.

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-reported-properties"></a>Az IoT-bővítmény használja az Azure CLI 2.0 iker által jelentett tulajdonságokkal

Az eszköz jelentett tulajdonságait olvassa be a következő parancs futtatásával:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

A tulajdonságok egyike $metadata. az eszköz megjelenítheti az utolsó $lastUpdated küld vagy üzenetet kap.

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-tags"></a>Az IoT-bővítmény használja az Azure CLI 2.0 iker tartozó címkékkel

A címkéket és az eszköz Tulajdonságok megjelenítése a következő parancs futtatásával:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

A mező szerepkör hozzáadása = a hőmérséklet és a páratartalom az eszköz a következő parancs futtatásával:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-device-twins-queries"></a>Az IoT-bővítmény használja az Azure CLI 2.0 eszköz twins lekérdezések

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