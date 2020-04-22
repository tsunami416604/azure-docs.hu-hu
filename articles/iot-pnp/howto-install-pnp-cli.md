---
title: Az Azure CLI Azure IoT-bővítményének használata az IoT Plug and Play előzetes eszközökkel való együttműködéshez | Microsoft dokumentumok
description: Telepítse az Azure CLI Azure IoT-bővítményét, és használja azt az IoT Plug and Play-eszközökio-központhoz csatlakoztatott eszközökkel való együttműködéshez.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770458"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT-bővítmény telepítése és használata az Azure CLI-hez

[Az Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) egy nyílt forráskódú, platformfüggetlen parancssori eszköz az Azure-erőforrások, például az IoT Hub kezelésére. Az Azure CLI Windows, Linux és MacOS rendszeren érhető el. Az Azure CLI előre telepítve van az [Azure Cloud Shellben](https://shell.azure.com)is. Az Azure CLI lehetővé teszi az Azure IoT Hub-erőforrások, eszközkiépítési szolgáltatás példányai és a kapcsolódó hubok kezelését bővítmények telepítése nélkül.

Az Azure CLI Azure IoT-bővítménye parancssori eszköz az IoT Plug and Play előzetes eszközökkel való interakcióhoz és teszteléshez. A bővítmény ta-

- Csatlakozzon egy eszközhöz.
- Tekintse meg az eszköz által küldött telemetriai adatokat.
- Az eszköz tulajdonságok használata.
- Eszközparancsok hívása.

Ez a cikk a következőkhöz nyújt útmutatást:

- Telepítse és konfigurálja az Azure IoT-bővítményt az Azure CLI-hez.
- A bővítmény segítségével kommunikálhat az eszközökkel, és tesztelheti azeszközöket.
- A bővítmény segítségével kezelheti a modelltárházban lévő felületeket.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Telepítse az Azure IoT-bővítményt az Azure CLI-hez

### <a name="step-1---install-the-azure-cli"></a>1. lépés - Az Azure CLI telepítése

Kövesse a [telepítési utasításokat](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure CLI beállításához a környezetben. Az alábbi parancsok használatához az Azure CLI-verziónak 2.0.73-as vagy újabb verziónak kell lennie. A verziószámot az `az -–version` paranccsal ellenőrizheti.

### <a name="step-2---install-iot-extension"></a>2. lépés - IoT-bővítmény telepítése

[Az IoT-bővítmény fontos (readme) fájlja](https://github.com/Azure/azure-iot-cli-extension) több módszert is ismertet a bővítmény telepítésére. A legegyszerűbb módszer az `az extension add --name azure-iot` futtatása. A telepítés után az `az extension list` paranccsal ellenőrizheti az aktuálisan telepített bővítményeket, az `az extension show --name azure-iot` paranccsal pedig megtekintheti az IoT-bővítmény adatait. A bővítményt az `az extension remove --name azure-iot` paranccsal távolíthatja el.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure IoT-bővítmény használata az Azure CLI-hez

### <a name="prerequisites"></a>Előfeltételek

Az Azure-előfizetésbe való bejelentkezéshez futtassa a következő parancsot:

```azurecli
az login
```

> [!NOTE]
> Ha az Azure felhőbeli rendszerhéjat használja, automatikusan bejelentkezik, és nem kell futtatnia az előző parancsot.

Az Azure IoT-bővítmény azure CLI-hez való használatához a következőkre van szüksége:

- Egy Azure IoT-központ. Az Azure CLI használatával számos módon adhat hozzá egy IoT-központot az Azure-előfizetéshez, például [létrehozhat egy IoT-központot.](../iot-hub/iot-hub-create-using-cli.md) Az Azure IoT-bővítmény parancsok futtatásához az IoT hub kapcsolati karakterláncára van szükség. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- Az IoT hubon regisztrált eszköz. A következő Azure CLI-paranccsal regisztrálhat egy eszközt, `{YourDeviceID}` és mindenképpen lecserélheti a helyőrzőket az `{YourIoTHubName}` értékekre:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Egyes parancsokhoz a vállalati modelltárház kapcsolati karakterláncára van szükség. A vállalat modelltárháza akkor jön létre, amikor először [az Azure Certified for IoT portalra kerül.](howto-onboard-portal.md) Előfordulhat, hogy egy harmadik fél megosztja Önnel a modelltár-kapcsolati karakterláncát, hogy hozzáférést biztosítson a felületeikhez és modelljeikhez.

### <a name="interact-with-a-device"></a>Eszköz használata

A bővítmény segítségével megtekintheti és kezelheti az IoT Plug and Play-eszközök, amelyek egy IoT hubhoz csatlakozik. A bővítmény együttműködik az IoT Plug and Play eszközt képviselő digitális ikertestvérrel.

#### <a name="list-devices-and-interfaces"></a>Eszközök és összeköttetések listázása

Az IoT Hub összes eszköze listázása:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Az IoT Plug and Play eszköz által regisztrált összes felület listázása:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Tulajdonságok

Az eszköz összes tulajdonságának és tulajdonságértékének listázása:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Állítsa be az írási és olvasási tulajdonság értékét:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Egy példa hasznos adatfájl, amely az eszköz **érzékelőfelületén** a **Contoso-ra** állítja be a **névtulajdonságot,** a következőképpen néz ki:

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

Az eszköz összes összeköttetésének összes parancsának listázása:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

A `--repo-login` paraméter nélkül ez a parancs a nyilvános modelltárházat használja.

Parancs meghívása:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Digitális ikeresemények

Az összes IoT Plug and Play digitális ikeresemény figyelése egy adott eszközről és felületről a **$Default** eseményközpont fogyasztói csoportjába:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Az összes IoT Plug and Play digitális ikeresemény figyelése egy adott eszközről és felületről egy adott fogyasztói csoportban:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Illesztőfelületek kezelése modelltárházban

A következő parancsok a nyilvános IoT Plug and Play modelltárházat használják. Vállalati modelltárház használatához adja `--login` hozzá az argumentumot a modelltár kapcsolati karakterláncával.

A nyilvános IoT Plug and Play modelltárban lévő felületek listázása:

```azurecli
az iot pnp interface list
```

Egy felület megjelenítése a nyilvános IoT Plug and Play modelltárházban:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Hozzon létre egy felületet az IoT Plug and Play vállalati modelltárban:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Közvetlenül nem hozhat létre felületet a nyilvános modelltárházban.

Az IoT Plug and Play vállalati modelltártárban egy felület frissítése:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

A nyilvános modelltárházban lévő felület közvetlenfrissítése nem frissíthető.

Az IoT Plug and Play vállalati modelltárból egy felületet közzétehet a nyilvános modelltárházban. Ez a művelet a kapcsolatot nem módosíthatóvá teszi:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Csak a Microsoft-partnerek tehetnek közzé felületeket a nyilvános modelltárházban.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Eszközképesség-modellek kezelése modelltárházban

A következő parancsok a nyilvános IoT Plug and Play modelltárházat használják. Vállalati modelltárház használatához adja `--login` hozzá az argumentumot a modelltár kapcsolati karakterláncával.

Az eszközképességi modellek listázása az IoT Plug and Play nyilvános modelltárházban:

```azurecli
az iot pnp capability-model list
```

Eszközképesség-modell megjelenítése az IoT Plug and Play nyilvános modelltárházban:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Hozzon létre egy eszközképességi modellt egy IoT Plug and Play vállalati modelltárházban:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Közvetlenül nem hozhat létre modellt a nyilvános modelltárházban.

Eszközképesség-modell frissítése az IoT Plug and Play vállalati modelltárban:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

A nyilvános modelltárházban nem frissíthet közvetlenül egy modellt.

Az IoT Plug and Play vállalati modelltárból közzétehet egy eszközképességi modellt a nyilvános modelltárházban. Ez a művelet a modellt megváltoztathatatlanvá teszi:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Csak a Microsoft-partnerek tehetnek közzé modelleket a nyilvános modelltárházban.

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben megtanulta, hogyan telepítheti és használhatja az Azure IOT-bővítményt az Azure CLI-hez a Plug and Play-eszközökkel való együttműködéshez. A javasolt következő lépés a [modellek kezelése](./howto-manage-models.md).
