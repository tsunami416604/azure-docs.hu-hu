---
title: Quickstart - Provision simulated TPM device to Azure IoT Hub using Node.js
description: Quickstart - Create and provision a simulated TPM device using Node.js device SDK for Azure IoT Hub Device Provisioning Service. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 942bc64153e1c35e3fb9c5bbb989607b7e443e9a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423460"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision a simulated TPM device using Node.js device SDK for IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ezek a lépések bemutatják, hogyan hozhat létre szimulált eszközt egy Windows operációs rendszert futtató fejlesztői gépen, hogyan futtathatja a Windows TPM szimulátort az eszköz [hardveres biztonsági moduljaként (HSM-jeként)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/), és hogyan használhatja a kódmintát, hogy ezt a szimulált eszközt összekösse az eszközkiépítési szolgáltatással és az IoT Hubbal. 

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket is. Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 


## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

1. Nyisson meg egy parancssort vagy a Git Basht. Klónozza a `azure-utpm-c` GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Keresse meg a GitHub gyökérmappáját, és futtassa a [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) szimulátort. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Do not close this command window; you need to keep this simulator running until the end of this quickstart guide: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Hozzon létre egy új, **registerdevice** nevű üres mappát. A **registerdevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Válaszolja meg az `npm` által feltett összes kérdést, vagy fogadja el az alapértelmezett értékeket, ha azok megfelelnek Önnek:
   
    ```cmd/sh
    npm init
    ```

1. Telepítse a következő előzetes csomagokat:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > A fenti csomagok telepítése során ismert problémák fordulhatnak elő. Ezen problémák elhárításához futtassa az `npm install --global --production windows-build-tools` parancsot a parancssorból **Futtatás rendszergazdaként** módban, majd futtassa a `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` parancsot, amelyben a telepített verziónak megfelelően cserélje le az útvonalat, végül pedig futtassa újra a fenti telepítési parancsokat.
    >

1. Telepítse a következő csomagokat, amelyek a regisztráció során használt összetevőket tartalmazzák:

   - TPM-mel együttműködő biztonsági ügyfél: `azure-iot-security-tpm`
   - átviteli összetevő, amellyel az eszköz csatlakozhat az eszközkiépítési szolgáltatáshoz: `azure-iot-provisioning-device-http` vagy `azure-iot-provisioning-device-amqp`
   - ügyfél az átvitel és a biztonsági ügyfél használatához: `azure-iot-provisioning-device`

     Miután megtörtént az eszköz regisztrálása, a szokásos IoT Hub eszközügyfél-csomagokkal csatlakoztathatja az eszközt a regisztráció során megadott hitelesítő adatok használatával. A következőkre lesz szükség:

   - az eszközügyfél: `azure-iot-device`
   - átviteli összetevő: `azure-iot-device-amqp`, `azure-iot-device-mqtt` vagy `azure-iot-device-http`
   - a már telepített biztonsági ügyfél: `azure-iot-security-tpm`

     > [!NOTE]
     > Az alábbi minták az `azure-iot-provisioning-device-http` és az `azure-iot-device-mqtt` átviteli összetevőket használják.
     > 

     A következő parancs parancssorbeli futtatásával egyszerre telepítheti ezen csomagok mindegyikét a **registerdevice** mappába:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Egy szövegszerkesztővel hozzon létre egy új **ExtractDevice.js** fájlt a **registerdevice** mappában.

1. Adja hozzá a következő `require` utasításokat az **ExtractDevice.js** fájl elejéhez:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Adja hozzá az alábbi függvényt a metódus megvalósításához:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Mentse és zárja be az **ExtractDevice.js** fájlt. Minta futtatása:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. The output window displays the **_Endorsement key_** and the **_Registration ID_** needed for device enrollment. Jegyezze fel ezeket az értékeket. 


## <a name="create-a-device-entry"></a>Eszközbejegyzés létrehozása

1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service.

1. From the Device Provisioning Service menu, select **Manage enrollments**. Select **Individual Enrollments** tab and select the **Add individual enrollment** button at the top. 

1. In the **Add Enrollment** panel, enter the following information:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Enter the *Registration ID* and *Endorsement key* for your TPM device from the values you noted previously.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Enter a unique *Device ID*. Ne használjon bizalmas adatokat az eszköz elnevezésekor. If you choose not to provide one, the registration ID will be used to identify the device instead.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Once complete, press the **Save** button. 

     ![Írja be az eszköz beléptetési információit a portál panelén](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni regisztrációk* lapon lévő listában. 


## <a name="register-the-device"></a>Eszköz regisztrálása

1. In the Azure portal, select the **Overview** blade for your Device Provisioning service and note the **_Global Device Endpoint_** and **_ID Scope_** values.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Egy szövegszerkesztővel hozzon létre egy új **RegisterDevice.js** fájlt a **registerdevice** mappában.

1. Adja hozzá a következő `require` utasításokat a **RegisterDevice.js** fájl elejéhez:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > Az **Azure IoT SDK for Node.js** támogatja az olyan további protokollokat, mint az _AMQP_, az _AMQP WS_ és az _MQTT WS_.  További példák: [Device Provisioning Service SDK for Node.js-minták](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples).
    > 

1. Adjon hozzá egy **globalDeviceEndpoint** és egy **idScope** változót, és ezek használatával hozzon létre egy **ProvisioningDeviceClient** példányt. A **{globalDeviceEndpoint}** és az **{idScope}** elemeket cserélje le az **1. lépésből** származó **_Globális eszközvégpont_** és **_Azonosító hatókörére_** elemekre:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Adja hozzá az alábbi függvényt a metódus megvalósításához az eszközön:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Mentse és zárja be a **RegisterDevice.js** fájlt. Minta futtatása:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. On successful provisioning of your simulated device to the IoT hub linked with your provisioning service, the device ID appears on the hub's **IoT devices** blade. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open the **Manage Enrollments** blade for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
1. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open the **IoT devices** blade for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.


## <a name="next-steps"></a>Következő lépések

In this quickstart, you’ve created a TPM simulated device on your machine and provisioned it to your IoT hub using the IoT Hub Device Provisioning Service. To learn how to enroll your TPM device programmatically, continue to the quickstart for programmatic enrollment of a TPM device. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)
