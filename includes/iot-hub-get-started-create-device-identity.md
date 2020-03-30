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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78892954"
---
Ebben a szakaszban az Azure CLI használatával hozzon létre egy eszköz identitást ebben a cikkben. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. Nyissa meg [az Azure Cloud Shell](https://shell.azure.com/)t.

1. Az Azure Cloud Shellben futtassa a következő parancsot a Microsoft Azure IoT Extension for Azure CLI telepítéséhez:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Hozzon létre egy `myDeviceId` új eszközidentitást, amelyet az alábbi parancsokkal hív meg, és olvassa be az eszköz kapcsolati karakterláncát:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Jegyezze fel az eszköz kapcsolati karakterláncát az eredményből. Ezt az eszközkapcsolati karakterláncot az eszközalkalmazás használja az IoT Hubhoz eszközként való csatlakozáshoz.

<!-- images and links -->
