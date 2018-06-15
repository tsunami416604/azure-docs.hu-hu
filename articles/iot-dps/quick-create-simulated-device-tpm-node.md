---
title: Szimulált TPM-eszköz kiépítése Azure IoT Hubra Node.js használatával | Microsoft Docs
description: Azure rövid útmutató – Szimulált TPM-eszköz létrehozása és kiépítése az Azure IoT Hub Device Provisioning Service-hez készült Node.js eszközoldali SDK-val
author: bryanla
ms.author: bryanla
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98cee10b52a973ec0ecbad6586f939ae6982ea5d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629772"
---
# <a name="create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Szimulált TPM-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service-hez készült Node.js eszközoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ezek a lépések bemutatják, hogyan hozhat létre szimulált eszközt egy Windows operációs rendszert futtató fejlesztői gépen, hogyan futtathatja a Windows TPM szimulátort az eszköz [hardveres biztonsági moduljaként (HSM-jeként)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/), és hogyan használhatja a kódmintát, hogy ezt a szimulált eszközt összekösse az eszközkiépítési szolgáltatással és az IoT Hubbal. 

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket is. A folytatás előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 


## <a name="simulate-a-tpm-device"></a>TPM-eszköz szimulálása

1. Nyisson meg egy parancssort vagy a Git Basht. Klónozza a `azure-utpm-c` GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git
    ```

1. Keresse meg a GitHub gyökérmappáját, és futtassa a [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) szimulátort. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot; a rövid útmutató végéig futnia kell ennek a szimulátornak. 

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

1. A kimeneti ablak megjeleníti az eszközök beléptetéséhez szükséges **_ellenőrzőkulcsot_** és **_regisztrációs azonosítót_**. Jegyezze fel ezeket az értékeket. 


## <a name="create-a-device-entry"></a>Eszközbejegyzés létrehozása

1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatást.

1. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. 

1. A **Beléptetési listabejegyzés hozzáadása** területen adja meg a következő információkat:
    - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
    - Írja be a TPM-eszköz *Regisztrációs azonosítóját* és *Ellenőrzőkulcsát*.
    - Ha kívánja, megadhatja az alábbi információkat is:
        - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
        - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor.
        - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
    - Ha végzett, kattintson a **Mentés** gombra. 

    ![Írja be az eszköz beléptetési információit a portál panelén](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni regisztrációk* lapon lévő listában. 


## <a name="register-the-device"></a>Eszköz regisztrálása

1. Az Azure Portalon válassza ki a Device Provisioning Service **Áttekintés** panelét, és jegyezze fel a **_Globális eszközvégpont_** és az **_Azonosító hatóköre_** értékét.

    ![DPS végpontinformációk kinyerése a portál paneljéről](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

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

1. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. Ha sikeresen kiépíti a szimulált eszközt a kiépítési szolgáltatáshoz csatolt IoT Hubon, az eszköz azonosítója megjelenik a hub **IoT-eszközök** panelén. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **Regisztrációk kezelése** paneljét, majd kattintson az **Egyéni regisztrációk** lapra. Válassza ki a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra. 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy szimulált TPM-eszközt hozott létre a gépen, majd kiépítette azt az IoT Hubon az IoT Hub Device Provisioning Service használatával. Ha szeretné megismerni a TPM-eszköz programozott regisztrációjának folyamatát, lépjen tovább a TPM-eszközök programozott regisztrációjának rövid útmutatójára. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – TPM-eszköz regisztrációja az Azure IoT Hub Device Provisioning Service-be](quick-enroll-device-tpm-node.md)
