---
title: Használja az Azure IoT-bővítményt az Azure CLI-hez a IoT Plug and Play Preview-eszközökkel való kommunikációhoz | Microsoft Docs
description: Telepítse az Azure IoT-bővítményt az Azure CLI-hez, és használja az IoT hub-hoz csatlakoztatott IoT Plug and Play eszközök használatához.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eb4f607672c39d45b7791ccaeeb6f7cff9393cb9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571016"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése és használata az Azure CLI-hez

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, többplatformos parancssori eszköz az Azure-erőforrások, például a IoT hub kezelésére. Az Azure CLI Windows, Linux és MacOS rendszeren érhető el. Az Azure CLI-t a [Azure Cloud Shell](https://shell.azure.com)is előre telepíti. Az Azure CLI-vel a bővítmények telepítése nélkül kezelheti az Azure IoT Hub erőforrásait, az eszközök kiépítési szolgáltatásának példányait és a társított hubokat.

Az Azure CLI-hez készült Azure IoT-bővítmény parancssori eszköz a IoT Plug and Play előnézeti eszközökhöz való interakcióhoz és teszteléshez. A bővítmény a következőre használható:

- Kapcsolódjon egy eszközhöz.
- Az eszköz által küldött telemetria megtekintése.
- Az eszköz tulajdonságainak használata.
- Hívja meg az eszköz parancsait.

Ez a cikk bemutatja, hogyan végezheti el a következőket:

- Az Azure IoT bővítmény telepítése és konfigurálása az Azure CLI-hez.
- Használja a bővítményt az eszközök használatához és teszteléséhez.
- A bővítmény használatával kezelheti az illesztőfelületeket a modell adattárában.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése az Azure CLI-hez

### <a name="step-1---install-the-azure-cli"></a>1\. lépés – az Azure CLI telepítése

Kövesse a [telepítési utasításokat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , és állítsa be az Azure CLI-t a környezetében. Az alábbi parancsok használatához az Azure CLI-verziójának 2.0.73 vagy újabb verziójúnak kell lennie. A verziószámot az `az -–version` paranccsal ellenőrizheti.

### <a name="step-2---install-iot-extension"></a>2\. lépés – a IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-cli-iot-ext` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-cli-iot-ext` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-cli-iot-ext` paranccsal távolíthatja el.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény használata az Azure CLI-hez

### <a name="prerequisites"></a>Előfeltételek

Ha be szeretné jelentkezni az Azure-előfizetésbe, futtassa a következő parancsot:

```cmd/sh
az login
```

> [!NOTE]
> Ha az Azure Cloud shellt használja, automatikusan bejelentkezik, és nem kell futtatnia az előző parancsot.

Az Azure CLI-hez készült Azure IoT-bővítmény használatához a következők szükségesek:

- Egy Azure IoT hub. Az Azure-előfizetéshez többféleképpen is hozzáadhat egy IoT hubot, például [létrehozhat egy IoT hubot az Azure CLI használatával](../iot-hub/iot-hub-create-using-cli.md). Az Azure IoT-bővítmény parancsainak futtatásához szüksége lesz az IoT hub kapcsolódási karakterláncára. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

    > [!NOTE]
    > A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

- Az IoT hub-ban regisztrált eszköz. Az alábbi Azure CLI-paranccsal regisztrálhat egy eszközt, és a `{YourIoTHubName}` és `{YourDeviceID}` helyőrzőket cserélje le az értékekre:

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Egyes parancsokhoz szükség van a vállalati modell adattárának a kapcsolatok karakterláncára. A vállalati modell tárháza akkor jön létre, amikor először [bekerül az Azure Certified for IoT portálra](howto-onboard-portal.md). Előfordulhat, hogy egy harmadik fél megoszthatja a modell-adattár kapcsolati sztringjét, hogy hozzáférést biztosítson a kezelőfelületekhez és a modellekhez.

### <a name="interact-with-a-device"></a>Eszköz használata

A bővítmény használatával megtekintheti és kezelheti a IoT Plug and Play az IoT hub-hoz csatlakoztatott eszközöket. A bővítmény a IoT Plug and Play eszközt jelképező digitális ikertel működik.

#### <a name="list-devices-and-interfaces"></a>Eszközök és felületek listázása

IoT Hub összes eszközének listázása:

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

IoT Plug and Play-eszköz által regisztrált összes felület listázása:

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Tulajdonságok

Az eszközön található felület összes tulajdonságának és tulajdonságának listázása:

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Egy írható-olvasható tulajdonság értékének beállítása:

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Egy példa arra, hogy az eszköz és a **contoso** között a **Name (név** ) **tulajdonság az** alábbihoz hasonlóan néz ki:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Parancsok

Az eszközön található felület összes parancsának listázása:

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

A `--repo-login` paraméter nélkül ez a parancs a nyilvános modell tárházát használja.

Parancs meghívása:

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Digitális kettős események

Az összes IoT figyelése egy adott eszközről és felületről Plug and Play digitális Twin-eseményekről az **$default** Event hub fogyasztói csoportba:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Az összes IoT figyelése egy adott eszközről és egy adott felhasználói csoportba tartozó csatolón keresztüli digitális két esemény Plug and Play:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Felületek kezelése a modell adattárában

Az alábbi parancsok a nyilvános IoT Plug and Play Model repositoryt használják. A vállalati modell tárházának használatához adja hozzá a `--login` argumentumot a Model adattár-kapcsolódási karakterlánccal.

A nyilvános IoT Plug and Play Model adattárában található felületek listázása:

```cmd/sh
az iot pnp interface list
```

Interfész megjelenítése a nyilvános IoT Plug and Play Model adattár:

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

Hozzon létre egy felületet a IoT Plug and Play vállalati modell adattárában:

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Közvetlenül nem hozhat létre felületet a nyilvános modell adattárában.

Felület frissítése a IoT Plug and Play vállalati modell adattárában:

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

A nyilvános modell adattárában nem lehet közvetlenül frissíteni a felületet.

Hozzon nyilvánosságra egy felületet a IoT Plug and Play vállalati modell adattárból a nyilvános modell adattárba. Ezzel a művelettel a felületet nem változtathatják meg:

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Csak a Microsoft-partnerek tehetnek közzé felületet a nyilvános modell adattárában.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Eszköz-képesség modellek kezelése a modell-tárházban

Az alábbi parancsok a nyilvános IoT Plug and Play Model repositoryt használják. A vállalati modell tárházának használatához adja hozzá a `--login` argumentumot a Model adattár-kapcsolódási karakterlánccal.

Eszköz-képesség modellek listázása a IoT Plug and Play nyilvános modell tárházban:

```cmd/sh
az iot pnp capability-model list
```

Eszköz képesség modell megjelenítése a IoT Plug and Play nyilvános modell tárházban:

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

Eszköz-képesség modell létrehozása egy IoT Plug and Play vállalati modell adattárában:

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nem hozhat létre közvetlenül modellt a nyilvános modell adattárában.

Eszköz-képesség modell frissítése a IoT Plug and Play vállalati modell tárházban:

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

A modell nem frissíthető közvetlenül a nyilvános modell adattárában.

Tegye közzé az eszköz képességeinek modelljét a IoT Plug and Play vállalati modell adattárból a nyilvános modell adattárba. Ez a művelet a modell megváltoztathatatlan működését teszi lehetővé:

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Csak Microsoft-partnerek tehetnek közzé modelleket a nyilvános modell adattárában.

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan telepítheti és használhatja az Azure CLI-hez készült Azure IoT-bővítményt az Plug and Play-eszközökkel való kommunikációhoz. Egy javasolt következő lépés a [modellek kezelésének](./howto-manage-models.md)megismerése.
