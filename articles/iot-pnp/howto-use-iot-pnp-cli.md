---
title: Használja az Azure IoT-bővítményt az Azure CLI-hez a IoT Plug and Play Preview-eszközökkel való kommunikációhoz | Microsoft Docs
description: Telepítse az Azure IoT-bővítményt az Azure CLI-hez, és használja az IoT hub-hoz csatlakoztatott IoT Plug and Play eszközök használatához.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352230"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése és használata az Azure CLI-hez

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT hub kezelésére. Az Azure CLI Windows, Linux és MacOS rendszeren érhető el. Az Azure CLI-t a [Azure Cloud Shell](https://shell.azure.com)is előre telepíti. Az Azure CLI-vel a bővítmények telepítése nélkül kezelheti az Azure IoT Hub erőforrásait, az eszközök kiépítési szolgáltatásának példányait és a társított hubokat.

Az Azure CLI-hez készült Azure IoT-bővítmény parancssori eszköz a IoT Plug and Play előnézeti eszközökhöz való interakcióhoz és teszteléshez. A bővítmény a következőre használható:

- Kapcsolódjon egy eszközhöz.
- Az eszköz által küldött telemetria megtekintése.
- Az eszköz tulajdonságainak használata.
- Hívja meg az eszköz parancsait.

Ez a cikk a következőkhöz nyújt útmutatást:

- Az Azure IoT bővítmény telepítése és konfigurálása az Azure CLI-hez.
- Használja a bővítményt az eszközök használatához és teszteléséhez.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése az Azure CLI-hez

### <a name="step-1---install-the-azure-cli"></a>1. lépés – az Azure CLI telepítése

Kövesse a [telepítési utasításokat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , és állítsa be az Azure CLI-t a környezetében. A legjobb megoldás, ha az Azure CLI-verziójának 2.9.1 vagy újabb verziójúnak kell lennie. A verziószámot az `az -–version` paranccsal ellenőrizheti.

### <a name="step-2---install-iot-extension"></a>2. lépés – a IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-iot` paranccsal pedig megtekintheti az IoT-bővítmény adatait. Az írás időpontjában a bővítmény verziószáma `0.9.7` .

A bővítményt az `az extension remove --name azure-iot` paranccsal távolíthatja el.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény használata az Azure CLI-hez

### <a name="prerequisites"></a>Előfeltételek

Ha be szeretné jelentkezni az Azure-előfizetésbe, futtassa a következő parancsot:

```azurecli
az login
```

> [!NOTE]
> Ha az Azure Cloud shellt használja, automatikusan bejelentkezik, és nem kell futtatnia az előző parancsot.

Az Azure CLI-hez készült Azure IoT-bővítmény használatához a következők szükségesek:

- Egy Azure IoT hub. Az Azure-előfizetéshez többféleképpen is hozzáadhat egy IoT hubot, például [létrehozhat egy IoT hubot az Azure CLI használatával](../iot-hub/iot-hub-create-using-cli.md). Az Azure IoT-bővítmény parancsainak futtatásához szüksége lesz az IoT hub kapcsolódási karakterláncára. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- Az IoT hub-ban regisztrált eszköz. Az alábbi Azure CLI-parancs használatával regisztrálhat egy eszközt, és a helyőrzőket cserélje le az `{YourIoTHubName}` `{YourDeviceID}` értékekre:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Eszköz használata

A bővítmény használatával megtekintheti és kezelheti a IoT Plug and Play az IoT hub-hoz csatlakoztatott eszközöket. A bővítmény a IoT Plug and Play eszközt jelképező digitális ikertel működik.

#### <a name="list-devices"></a>Eszközök listázása

IoT Hub összes eszközének listázása:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Digitális iker megtekintése

Egy eszköz digitális ikerének megtekintése:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Tulajdonságok

Egy írható-olvasható tulajdonság értékének beállítása:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Parancsok

Parancs meghívása:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Digitális kettős események

Az összes IoT figyelése egy adott eszközről és felületről Plug and Play digitális Twin-eseményekről az **$default** Event hub fogyasztói csoportba:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan telepítheti és használhatja az Azure CLI-hez készült Azure IoT-bővítményt az Plug and Play-eszközökkel való kommunikációhoz. A következő lépés azt ismerteti, hogyan használható az [Azure IoT Explorer az eszközeivel](./howto-use-iot-explorer.md).
