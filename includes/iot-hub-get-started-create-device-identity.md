---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 1e919f22d5dd7975f055f262ec9ba69230aebd17
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780848"
---
Ebben a szakaszban az Azure CLI használatával hozza létre az eszköz identitását ehhez a cikkhez. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. Nyissa meg az [Azure Cloud Shellt](https://shell.azure.com/).

1. Azure Cloud Shell futtassa a következő parancsot az Azure CLI-hez készült Microsoft Azure IoT-bővítmény telepítéséhez:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

2. Hozzon létre egy új eszköz `myDeviceId` identitását, és kérje le az eszköz-kapcsolatok karakterláncát a következő parancsokkal:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Jegyezze fel az eszköz csatlakoztatási karakterláncát az eredményből. Az eszköz kapcsolati karakterláncát a IoT Hub eszközként való kapcsolódáshoz használja a rendszer.

<!-- images and links -->
