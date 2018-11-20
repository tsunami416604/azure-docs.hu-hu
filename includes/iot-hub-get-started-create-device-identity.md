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
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "52156777"
---
## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban az Azure CLI eszközidentitás létrehozása ehhez az oktatóanyaghoz használja. Az Azure CLI telepítve van a [Azure Cloud Shell](~/articles/cloud-shell/overview.md), vagy beállíthatja a [telepítheti helyileg](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. Futtassa a következő parancsot a parancssori környezetben, ahol használ az Azure CLI-vel az IoT-bővítmény telepítéséhez:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Ha helyileg futtatja az Azure CLI, jelentkezzen be az Azure-fiókjával (Ha használja a Cloud Shellben, jelentkezett-e automatikusan, és nem kell az alábbi paranccsal) a következő parancs segítségével:

    ```cmd/sh
    az login
    ```

1. Végül hozzon létre egy új eszközidentitást nevű `myDeviceId` és az eszköz kapcsolati karakterlánc a következő parancsokkal:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Jegyezze fel az eszköz kapcsolati karakterláncát az eredményből. Az eszköz kapcsolati karakterláncát használják az eszközalkalmazás kapcsolódni az IoT hubhoz eszközként.

<!-- images and links -->
