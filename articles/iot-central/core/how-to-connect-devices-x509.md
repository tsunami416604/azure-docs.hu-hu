---
title: X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása Azure IoT Central-alkalmazásokban
description: X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása a IoT Central alkalmazáshoz készült Node.js eszközoldali SDK-val
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6de711567e87bcdd1e58185f90264d0c9aecdfde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343872"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása a IoT Central alkalmazáshoz készült Node.js eszközoldali SDK-val

IoT Central támogatja a közös hozzáférésű aláírások (SAS) és az X. 509 tanúsítványok használatát az eszköz és az alkalmazás közötti kommunikáció biztonságossá tételéhez. Az [ügyfélalkalmazás létrehozása és összekapcsolása az Azure IoT Central Application](./tutorial-connect-device-nodejs.md) oktatóanyagban sas-t használ. Ebből a cikkből megtudhatja, hogyan módosíthatja a kód mintát az X. 509 használatára.  Az X. 509 tanúsítványokat éles környezetekben ajánlott használni. További információ: [Csatlakozás az Azure IoT Centralhoz](./concepts-get-connected.md).

Ez a cikk két módszert mutat be az X. 509 – [csoportos regisztrációk](how-to-connect-devices-x509.md#use-a-group-enrollment) jellemzően éles környezetben való használatát, valamint a teszteléshez hasznos [Egyéni regisztrációkat](how-to-connect-devices-x509.md#use-an-individual-enrollment) .

## <a name="prerequisites"></a>Előfeltételek

- [Ügyfélalkalmazás létrehozásának és összekapcsolásának befejezése az Azure IoT Central Application (Node.js)](./tutorial-connect-device-nodejs.md) oktatóanyagban.
- [Git](https://git-scm.com/download/).
- Töltse le és telepítse az [OpenSSL](https://www.openssl.org/)-t. Ha Windows rendszert használ, a fájlok a [SourceForge-on lévő OpenSSL lapról](https://sourceforge.net/projects/openssl/)is használhatók.

## <a name="use-a-group-enrollment"></a>Csoportos regisztráció használata

Használjon X. 509 tanúsítványokat egy csoportos regisztrációval éles környezetben. A csoportos regisztráció során adjon hozzá egy gyökérszintű vagy köztes X. 509 tanúsítványt a IoT Central alkalmazáshoz. A legfelső szintű vagy köztes tanúsítványból származtatott levél tanúsítvánnyal rendelkező eszközök csatlakozhatnak az alkalmazáshoz.


## <a name="generate-root-and-device-cert"></a>Gyökér-és eszköz-tanúsítvány előállítása

Ebben a szakaszban egy X. 509 tanúsítványt fog használni egy olyan eszköz csatlakoztatásához, amely a beléptetési csoport tanúsítványa alapján származtatott tanúsítvánnyal rendelkezik, amely képes csatlakozni a IoT Central alkalmazáshoz.

> [!WARNING]
> Az X. 509 tanúsítványok létrehozásának így csak tesztelésre van lehetőségük. Éles környezetben a tanúsítvány generálásához a hivatalos, biztonságos mechanizmust kell használnia.

1. Nyisson meg egy parancssort. A tanúsítvány-létrehozási parancsfájlok GitHub-tárházának klónozása:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

2. Navigáljon a Certificate Generator parancsfájlhoz, és telepítse a szükséges csomagokat:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Hozzon létre egy főtanúsítványt, majd származtatja az eszköz tanúsítványát a szkript futtatásával. Ügyeljen arra, hogy csak kisbetűs alfanumerikus karaktereket és kötőjeleket használjon a tanúsítvány neveként.

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

Ez három fájlt hoz létre a gyökérhez és az eszköz tanúsítványához.

fájlnév | tartalmát
-------- | --------
\<name\>_cert. PEM | Az X509-tanúsítvány nyilvános része
\<name\>_key. PEM | Az X509-tanúsítvány titkos kulcsa
\<name\>_fullchain. PEM | Az X509-tanúsítvány teljes kulcstartója.


## <a name="create-a-group-enrollment"></a>Csoportos regisztráció létrehozása


1. Most nyissa meg IoT Central alkalmazást, és a bal oldali ablaktáblán navigáljon a **felügyelet**  elemre, majd kattintson az **eszköz csatlakoztatása**elemre. 

2. Válassza a + **beléptetési csoport létrehozása**lehetőséget, és hozzon létre egy új, _MyX509Group_ nevű regisztrációs csoportot tanúsítványok igazolási típusával **(X. 509)**:


3. Nyissa meg a létrehozott beléptetési csoportot, és kattintson az **elsődleges kezelése**elemre. 

4. Válassza a fájl lehetőséget, és töltse fel a korábban létrehozott _mytestrootcert_cert. PEM_ nevű főtanúsítvány-fájlt:


    ![Tanúsítvány feltöltése](./media/how-to-connect-devices-x509/certificate-upload.png)



5. Az ellenőrzés elvégzéséhez másolja az ellenőrző kódot, és hozzon létre egy X. 509 ellenőrző tanúsítványt az adott kóddal a parancssorban.

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

6. Töltse fel az aláírt ellenőrző tanúsítványt _verification_cert. PEM_ -ra az ellenőrzés befejezéséhez.

    ![Ellenőrzött tanúsítvány](./media/how-to-connect-devices-x509/verified.png)


Mostantól az elsődleges főtanúsítványból származtatott X. 509 tanúsítvánnyal rendelkező eszközöket is összekapcsolhat. A beléptetési csoport mentése után jegyezze fel az azonosító hatókörét.


## <a name="run-sample-device-code"></a>Minta eszköz kódjának futtatása


1. Az Azure IoT Central alkalmazásban kattintson az **eszközök**elemre, és hozzon létre egy új eszközt a _mytestdevice_ , mint az **eszköz azonosítóját** a környezeti érzékelő eszköz sablonjában.


2. Másolja a _mytestdevice_key. PEM_ és a _mytestdevice_cert. PEM_ mappát arra a mappába, amely a _environmentalSensor.js_ alkalmazást tartalmazza az [eszköz csatlakoztatása (Node.js) oktatóanyag](./tutorial-connect-device-nodejs.md)befejezése után.

3. Navigáljon a environmentalSensor.js alkalmazást tartalmazó mappához, és futtassa a következő parancsot az X. 509 csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

4. Szerkessze a **environmentalSensor.js** fájlt.
    - Cserélje le az `idScope` értéket az **azonosító hatókörre** , amelyet korábban jegyzett készített 
    - `registrationId`Az értéket cserélje le a értékre `mytestdevice` .

5. Szerkessze az `require` utasításokat a következőképpen:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

6. Szerkessze az ügyfelet létrehozó szakaszt a következőképpen:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

7. A következő lépésekkel módosíthatja a kapcsolatokat megnyitó szakaszt:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

8. Futtassa a szkriptet, és ellenőrizze, hogy az eszköz sikeresen lett-e kiépítve.

    ```cmd/sh
    node environmentalSensor.js
    ```   

    Azt is ellenőrizheti, hogy a telemetria megjelenik-e az irányítópulton.

    ![Az eszköz telemetria ellenőrzése](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Egyéni regisztráció használata

Az eszköz és a megoldás teszteléséhez használjon X. 509 tanúsítványokat egyéni regisztrációval. Egyéni regisztráció esetén nincs root vagy Intermediate X. 509 tanúsítvány a IoT Central alkalmazásban. Az eszközök önaláírt X. 509 tanúsítványt használnak az alkalmazáshoz való kapcsolódáshoz.

## <a name="generate-self-signed-device-cert"></a>Önaláírt eszköz tanúsítványának előállítása


Ebben a szakaszban egy önaláírt X. 509 tanúsítványt fog használni az eszközök egyéni regisztrációhoz való csatlakoztatásához, amely egyetlen eszköz regisztrálására szolgál. Az önaláírt tanúsítványok csak tesztelésre szolgálnak.

Hozzon létre egy önaláírt X. 509 eszköz-tanúsítványt a szkript futtatásával. Ügyeljen arra, hogy csak kisbetűs alfanumerikus karaktereket és kötőjeleket használjon a tanúsítvány neveként.

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Egyéni regisztráció létrehozása

1. Az Azure IoT Central alkalmazásban válassza az **eszközök**lehetőséget, majd hozzon létre egy **Device ID** új eszközt az _mytestselfcertprimary_ a környezeti érzékelő eszköz sablonjában. Jegyezze fel az **azonosító hatókörét**

2. Nyissa meg a létrehozott eszközt, és válassza a **Csatlakoztatás** lehetőséget.

3. Válassza az **Egyéni regisztrációk** lehetőséget, mint a csatlakozási módszert és a **tanúsítványokat (X. 509)** mechanizmusként.

    ![Egyéni regisztráció](./media/how-to-connect-devices-x509/individual-device-connect.png)


4. Válassza a fájl lehetőséget az elsődleges területen, és töltse fel a korábban létrehozott _mytestselfcertprimary_cert. PEM_ nevű tanúsítványfájl-fájlt. 

5. Válassza a fájl lehetőséget a másodlagos tanúsítványhoz, és töltse fel a _mytestselfcertsecondary_cert. PEM_ nevű tanúsítványfájl-fájlt. Ezután válassza a **Mentés** lehetőséget.

    ![Egyéni beléptetési tanúsítvány feltöltése](./media/how-to-connect-devices-x509/individual-enrollment.png)

Az eszköz most már X. 509 tanúsítvánnyal lett kiépítve.



## <a name="run-a-sample-individual-enrollment-device"></a>Minta egyéni beléptetési eszköz futtatása

1. Másolja a _mytestselfcertprimary_key. PEM_ és a _mytestselfcertprimary_cert. PEM_mappát arra a mappára, amely a environmentalSensor.js alkalmazást tartalmazza, amikor befejezte az [eszköz csatlakoztatása (Node.js) oktatóanyagot](./tutorial-connect-device-nodejs.md).


2. Szerkessze a **environmentalSensor.js** fájlt az alábbiak szerint, és mentse.
    - Cserélje le az `idScope` értéket az **azonosító hatókörre** , amelyet korábban jegyzett készített.
    - `registrationId`Az értéket cserélje le a értékre `mytestselfcertprimary` .
    - A **var deviceCert** a következőképpen cserélje le:
    ```cmd\sh
    var deviceCert = {
    cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
    key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
    };
    ```

3. Futtassa a szkriptet, és ellenőrizze, hogy az eszköz sikeresen lett-e kiépítve.

    ```cmd/sh
    node environmentalSensor.js
    ```   

    Azt is ellenőrizheti, hogy a telemetria megjelenik-e az irányítópulton.

    ![Telemetria egyéni regisztráció](./media/how-to-connect-devices-x509/telemetry-primary.png)

A fenti lépéseket a _mytestselfcertsecondary_ -tanúsítványhoz is megismételheti.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan csatlakoztathatók az eszközök az X. 509 tanúsítványokkal, a javasolt következő lépés az [eszköz kapcsolatának figyelése az Azure CLI használatával](howto-monitor-devices-azure-cli.md)

