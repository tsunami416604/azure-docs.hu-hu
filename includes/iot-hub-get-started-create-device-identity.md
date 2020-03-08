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
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892954"
---
Ebben a szakaszban az Azure CLI használatával hozza létre az eszköz identitását ehhez a cikkhez. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. Nyissa meg az [Azure Cloud Shellt](https://shell.azure.com/).

1. Azure Cloud Shell futtassa a következő parancsot az Azure CLI-hez készült Microsoft Azure IoT-bővítmény telepítéséhez:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Hozzon létre egy `myDeviceId` nevű új eszközt, majd az alábbi parancsokkal olvassa be az eszközhöz tartozó kapcsolatok karakterláncát:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Jegyezze fel az eszköz csatlakoztatási karakterláncát az eredményből. Az eszköz kapcsolati karakterláncát a IoT Hub eszközként való kapcsolódáshoz használja a rendszer.

<!-- images and links -->
