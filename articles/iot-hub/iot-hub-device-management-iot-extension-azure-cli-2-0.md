---
title: Azure IoT-eszközkezelés IoT-bővítménylel az Azure CLI-hez | Microsoft dokumentumok
description: Használja az IoT-bővítményt az Azure IoT Hub eszközkezeléséhez, amely a közvetlen módszereket és a Twin kívánt tulajdonságkezelési beállításait kínálja.
author: chrissie926
manager: ''
keywords: azure iot eszközkezelés, azure iot hub eszközkezelés, eszközkezelés iot, iot hub eszközkezelés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239645"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Az Azure CLI IoT-bővítményhasználata az Azure IoT Hub eszközkezeléséhez

![Végpontok között diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az Azure CLI IoT-bővítménye](https://github.com/Azure/azure-iot-cli-extension) egy nyílt forráskódú IoT-bővítmény, amely növeli az [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)képességeit. Az Azure CLI az Azure Resource Manager rel és a felügyeleti végpontokkal való interakcióra vonatkozó parancsokat tartalmaz. Például az Azure CLI segítségével hozzon létre egy Azure virtuális gép vagy egy IoT hub. A CLI-bővítmény lehetővé teszi, hogy egy Azure-szolgáltatás bővítse az Azure CLI-t, amely további szolgáltatásspecifikus képességekhez biztosít hozzáférést. Az IoT-bővítmény az IoT-fejlesztők számára parancssori hozzáférést biztosít az összes IoT Hub-, IoT Edge- és IoT Hub-eszközkiépítési szolgáltatáshoz.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Felügyeleti beállítás          | Tevékenység  |
|----------------------------|-----------|
| Közvetlen metódusok             | Az eszköz megfelelően működjön, például indítsa el vagy állítsa le az üzenetek küldését, illetve indítsa újra az eszközt.                                        |
| Iker kívánt tulajdonságok    | Helyezzen egy eszközt bizonyos állapotokba, például egy LED-et zöldre, vagy a telemetriai küldési időközt 30 percre.         |
| Ikerjelentett tulajdonságok   | Az eszköz jelentett állapotának lerendőrsége. Például a készülék jelenti, hogy a LED villog.                                    |
| Ikercímkék                  | Eszközspecifikus metaadatok tárolása a felhőben. Például egy automaták üzembe helyezésének helye.                         |
| Ikereszköz-lekérdezések        | Az összes ikereszköz lekérése az ikrek tetszőleges feltételekkel, például a használható eszközök azonosításához. |

A különbségek részletesebb magyarázatát és a beállítások használatával kapcsolatos útmutatást az [Eszközről a felhőbe irányuló kommunikációra vonatkozó útmutatásban](iot-hub-devguide-d2c-guidance.md) és [a felhőből az eszközre irányuló kommunikációra vonatkozó útmutatásban talál.](iot-hub-devguide-c2d-guidance.md)

Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit (metaadatokat, konfigurációkat és állapotokat) tárolják. Az IoT Hub minden olyan eszközhöz, amely csatlakozik, egy ikereszköz marad. Az ikereszközökről az Első lépések az [ikereszközökkel című témakörben](iot-hub-node-node-twin-getstarted.md)talál további információt.

## <a name="what-you-learn"></a>Ismertetett témák

Megtanulhatja az IoT-bővítmény használatát az Azure CLI-hez a fejlesztői gépen különböző felügyeleti lehetőségekkel.

## <a name="what-you-do"></a>Mit csinálsz

Futtassa az Azure CLI-t és az Azure CLI IoT-bővítményét különböző felügyeleti lehetőségekkel.

## <a name="what-you-need"></a>Mi szükséges

* Töltse ki a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz oktatóanyagait; például [a Raspberry Pi a node.js-szel.](iot-hub-raspberry-pi-kit-node-get-started.md) Ezek a tételek a következő követelményekre vonatkoznak:

  - Aktív Azure-előfizetés.
  - Egy Azure IoT hub az előfizetés alatt.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.

* Győződjön meg arról, hogy az eszköz fut az ügyfélalkalmazással az oktatóanyag során.

* [Python 2.7x vagy Python 3.x](https://www.python.org/downloads/)

* Az Azure CLI. Ha telepítenie kell, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)című témakört. Az Azure CLI-verziónak legalább 2.0.70-nek kell lennie. A verziószámot az `az –version` paranccsal ellenőrizheti.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Telepítse az IoT-bővítményt. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. [Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) több módszert is ismertet a bővítmény telepítésére.

## <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Jelentkezzen be Azure-fiókjába a következő parancs futtatásával:

```azurecli
az login
```

## <a name="direct-methods"></a>Közvetlen metódusok

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Az ikereszköz kívánt tulajdonságai

Állítsa be a kívánt tulajdonságintervallumot = 3000 a következő parancs futtatásával:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Ez a tulajdonság az eszközről olvasható.

## <a name="device-twin-reported-properties"></a>Az ikereszköz jelentett tulajdonságai

Az eszköz jelentett tulajdonságainak beszerezése a következő parancs futtatásával:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Az ikerjelentett tulajdonságok egyike a $metadata.$lastUpdated, amely azt mutatja, hogy az eszközalkalmazás mikor frissítette utoljára a jelentett tulajdonságkészletét.

## <a name="device-twin-tags"></a>Ikereszköz-címkék

Jelenítse meg az eszköz címkéit és tulajdonságait a következő parancs futtatásával:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

A következő parancs futtatásával adjon hozzá egy mezőszerepet = hőmérséklet&páratartalom az eszközhöz:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Ikereszköz-lekérdezések

Lekérdező eszközök szerepkörrel = "hőmérséklet&páratartalom" a következő parancs futtatásával:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Az összes eszköz lekérdezése, kivéve azokat, amelyek szerepkörrel = "hőmérséklet&páratartalom" címkével rendelkezők kivételével a következő parancs futtatásával:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan figyelheti az eszközről a felhőbe irányuló üzeneteket, és küldhet felhőből az eszközre üzeneteket az IoT-eszköz és az Azure IoT Hub között.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
