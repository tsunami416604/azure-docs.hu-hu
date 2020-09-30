---
title: Használja az Azure IoT-bővítményt az Azure CLI-hez a IoT Plug and Play-eszközökkel való kommunikációhoz | Microsoft Docs
description: Telepítse az Azure IoT-bővítményt az Azure CLI-hez, és használja az IoT hub-hoz csatlakoztatott IoT Plug and Play eszközök használatához.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577458"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése és használata az Azure CLI-hez

[Az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT hub kezelésére. Az Azure CLI Windows, Linux és macOS rendszeren érhető el. Az Azure CLI-vel a bővítmények telepítése nélkül kezelheti az Azure IoT Hub erőforrásait, az eszközök kiépítési szolgáltatásának példányait és a társított hubokat.

Az Azure CLI-hez készült Azure IoT-bővítmény parancssori eszköz a IoT Plug and Play-eszközökhöz való interakcióhoz és teszteléshez. A bővítmény a következőre használható:

- Kapcsolódjon egy eszközhöz.
- Az eszköz által küldött telemetria megtekintése.
- Az eszköz tulajdonságainak használata.
- Hívja meg az eszköz parancsait.

Ez a cikk a következőkhöz nyújt útmutatást:

- Az Azure IoT bővítmény telepítése és konfigurálása az Azure CLI-hez.
- Használja a bővítményt az eszközök használatához és teszteléséhez.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése az Azure CLI-hez

### <a name="step-1---install-the-azure-cli"></a>1. lépés – az Azure CLI telepítése

Kövesse a [telepítési utasításokat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) , és állítsa be az Azure CLI-t a környezetében. A legjobb megoldás, ha az Azure CLI-verziójának 2.9.1 vagy újabb verziójúnak kell lennie. A verziószámot az `az -–version` paranccsal ellenőrizheti.

### <a name="step-2---install-iot-extension"></a>2. lépés – a IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-iot` paranccsal pedig megtekintheti az IoT-bővítmény adatait. Az írás időpontjában a bővítmény verziószáma `0.10.0` .

A bővítményt az `az extension remove --name azure-iot` paranccsal távolíthatja el.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény használata az Azure CLI-hez

### <a name="prerequisites"></a>Előfeltételek

Ha be szeretné jelentkezni az Azure-előfizetésbe, futtassa a következő parancsot:

```azurecli
az login
```

Az Azure CLI-hez készült Azure IoT-bővítmény használatához a következők szükségesek:

- Egy Azure IoT hub. Az Azure-előfizetéshez többféleképpen is hozzáadhat egy IoT hubot, például [létrehozhat egy IoT hubot az Azure CLI használatával](../iot-hub/iot-hub-create-using-cli.md). Az Azure IoT-bővítmény parancsainak futtatásához szüksége lesz az IoT hub kapcsolódási karakterláncára. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

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

### <a name="manage-models-in-the-model-repository"></a>Modellek kezelése a modell adattárában

Az Azure CLI Model repository parancsaival kezelheti az adattár modelljeit.

#### <a name="create-model-repository"></a>Modell adattárának létrehozása

Hozzon létre egy új IoT Plug and Play vállalati tárházat a bérlőhöz, ha Ön a bérlő első felhasználója:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Modell-adattár bérlői szerepköreinek kezelése

Hozzon létre egy szerepkör-hozzárendelést egy felhasználó vagy egy egyszerű szolgáltatásnév számára egy adott erőforráshoz.

Adja meg például a user@consoso.com bérlő **ModelsCreator** szerepkörét:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Vagy adja user@consoso.com meg a **ModelAdministrator** szerepkörét egy adott modellhez:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Modell létrehozása

Hozzon létre egy új modellt a vállalati adattárban:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Keresés a modellben

Adott kulcsszónak megfelelő modellek listázása:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Modell közzététele

Tegye közzé a vállalati tárházban található eszköz modelljét a nyilvános tárházban.

Tegyük fel például, hogy a modellt a következő AZONOSÍTÓval hozza nyilvánosságra `dtmi:com:example:ClimateSensor;1` :

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Modell közzétételéhez a következő követelményeknek kell teljesülniük:

- A vállalat vagy szervezet bérlője csak Microsoft-partner lehet. 
- A felhasználónak vagy egyszerű szolgáltatásnak az adattár bérlője **közzétevő** szerepkörének tagjának kell lennie.

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan telepítheti és használhatja az Azure CLI-hez készült Azure IoT-bővítményt, hogy együttműködjön a IoT Plug and Play eszközeivel. A következő lépés azt ismerteti, hogyan használható az [Azure IoT Explorer az eszközeivel](./howto-use-iot-explorer.md).
