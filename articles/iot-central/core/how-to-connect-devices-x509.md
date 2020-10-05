---
title: X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása Azure IoT Central-alkalmazásokban
description: X. 509 tanúsítvánnyal rendelkező eszközök csatlakoztatása a IoT Central alkalmazáshoz készült Node.js eszközoldali SDK-val
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 22d86b96b7d9493ecc2f734be3f677a270a2739a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714265"
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

Ebben a szakaszban egy X. 509 tanúsítvány használatával csatlakozik egy olyan eszközhöz, amely a beléptetési csoport tanúsítványa alapján származtatott tanúsítvánnyal rendelkezik, amely csatlakozhat a IoT Central alkalmazáshoz.

> [!WARNING]
> Az X. 509 tanúsítványok létrehozásának így csak tesztelésre van lehetőség. Éles környezetben a tanúsítvány generálásához a hivatalos, biztonságos mechanizmust kell használnia.

1. Nyisson meg egy parancssort. A tanúsítvány-létrehozási parancsfájlok GitHub-tárházának klónozása:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navigáljon a Certificate Generator parancsfájlhoz, és telepítse a szükséges csomagokat:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Hozzon létre egy főtanúsítványt, majd származtatja az eszköz tanúsítványát a szkript futtatásával. Ügyeljen arra, hogy csak kisbetűs alfanumerikus karaktereket és kötőjeleket használjon a tanúsítvány neveként:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

Ezek a parancsok három fájlt hoznak létre a gyökérhez és az eszköz tanúsítványához.

fájlnév | tartalmát
-------- | --------
\<name\>_cert. PEM | Az X509-tanúsítvány nyilvános része
\<name\>_key. PEM | Az X509-tanúsítvány titkos kulcsa
\<name\>_fullchain. PEM | Az X509-tanúsítvány teljes kulcstartója.

## <a name="create-a-group-enrollment"></a>Csoportos regisztráció létrehozása

1. Nyissa meg IoT Central alkalmazást, és a bal oldali ablaktáblában navigáljon a **felügyelet**  elemre, és válassza az **eszköz-kapcsolódás**lehetőséget.

1. Válassza a **+ beléptetési csoport létrehozása**lehetőséget, és hozzon létre egy új, _MyX509Group_ nevű regisztrációs csoportot tanúsítványok igazolási típusával **(X. 509)**.

1. Nyissa meg a létrehozott beléptetési csoportot, és válassza az **elsődleges kezelése**lehetőséget.

1. Válassza a fájl lehetőséget, és töltse fel a korábban létrehozott _mytestrootcert_cert. PEM_ nevű főtanúsítvány-fájlt:

    ![Tanúsítvány feltöltése](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Az ellenőrzés befejezéséhez hozza létre az ellenőrző kódot, másolja, majd használja egy X. 509 ellenőrző tanúsítvány létrehozásához a parancssorban:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Töltse fel az aláírt ellenőrző tanúsítványt _verification_cert. PEM_ az ellenőrzés befejezéséhez:

    ![Ellenőrzött tanúsítvány](./media/how-to-connect-devices-x509/verified.png)

Mostantól az elsődleges főtanúsítványból származtatott X. 509 tanúsítvánnyal rendelkező eszközöket is összekapcsolhat. A beléptetési csoport mentése után jegyezze fel az azonosító hatókörét.

## <a name="run-sample-device-code"></a>Minta eszköz kódjának futtatása

1. Az Azure IoT Central alkalmazásban válassza az **eszközök**lehetőséget, és hozzon létre egy új eszközt a _mytestdevice_ , mint a **környezeti érzékelő** eszközének **azonosítóját** .

1. Másolja a _mytestdevice_key. PEM_ és a _mytestdevice_cert. pem_ fájlokat a _environmentalSensor.js_ alkalmazást tartalmazó mappába. Ezt az alkalmazást az [eszköz csatlakoztatása (Node.js) oktatóanyag](./tutorial-connect-device-nodejs.md)befejezése után hozta létre.

1. Navigáljon a environmentalSensor.js alkalmazást tartalmazó mappához, és futtassa a következő parancsot az X. 509 csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Szerkessze a **environmentalSensor.js** fájlt.
    - Cserélje le az `idScope` értéket az **azonosító hatókörre** , amelyet korábban jegyzett készített.
    - `registrationId`Az értéket cserélje le a értékre `mytestdevice` .

1. Szerkessze az `require` utasításokat a következőképpen:

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Szerkessze az ügyfelet létrehozó szakaszt a következőképpen:

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

1. A következő lépésekkel módosíthatja a kapcsolatokat megnyitó szakaszt:

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

1. Futtassa a szkriptet, és győződjön meg róla, hogy az eszközt sikeresen kiosztották:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Azt is ellenőrizheti, hogy a telemetria megjelenik-e az irányítópulton.

    ![Az eszköz telemetria ellenőrzése](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Egyéni regisztráció használata

Az eszköz és a megoldás teszteléséhez használjon X. 509 tanúsítványokat egyéni regisztrációval. Egyéni regisztráció esetén nincs root vagy Intermediate X. 509 tanúsítvány a IoT Central alkalmazásban. Az eszközök önaláírt X. 509 tanúsítványt használnak az alkalmazáshoz való kapcsolódáshoz.

## <a name="generate-self-signed-device-cert"></a>Önaláírt eszköz tanúsítványának előállítása

Ebben a szakaszban egy önaláírt X. 509 tanúsítványt használ az eszközök egyéni regisztrációhoz való csatlakoztatásához, amelyek egyetlen eszköz regisztrálására szolgálnak. Az önaláírt tanúsítványok csak tesztelésre szolgálnak.

Hozzon létre egy önaláírt X. 509 eszköz-tanúsítványt a szkript futtatásával. Ügyeljen arra, hogy csak kisbetűs alfanumerikus karaktereket és kötőjeleket használjon a tanúsítvány neveként:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Egyéni regisztráció létrehozása

1. Az Azure IoT Central alkalmazásban válassza az **eszközök**lehetőséget, majd hozzon létre egy **Device ID** új eszközt az _mytestselfcertprimary_ a környezeti érzékelő eszköz sablonjában. Jegyezze fel az **azonosító hatókörét**, amelyet később használni fog.

1. Nyissa meg a létrehozott eszközt, és válassza a **Csatlakoztatás**lehetőséget.

1. Válassza az **Egyéni regisztrációk** a **csatlakozási módszer** és a **tanúsítványok (X. 509)** lehetőséget a következő mechanizmusként:

    ![Egyéni regisztráció](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Válassza a fájl lehetőséget az elsődleges területen, és töltse fel a korábban létrehozott _mytestselfcertprimary_cert. PEM_ nevű tanúsítványfájl-fájlt.

1. Válassza a fájl lehetőséget a másodlagos tanúsítványhoz, és töltse fel a _mytestselfcertsecondary_cert. PEM_ nevű tanúsítványfájl-fájlt. Ezután válassza a **Mentés**lehetőséget:

    ![Egyéni beléptetési tanúsítvány feltöltése](./media/how-to-connect-devices-x509/individual-enrollment.png)

Az eszköz most már X. 509 tanúsítvánnyal lett kiépítve.

## <a name="run-a-sample-individual-enrollment-device"></a>Minta egyéni beléptetési eszköz futtatása

1. Másolja a _mytestselfcertprimary_key. PEM_ és a _mytestselfcertprimary_cert. pem_ fájlokat a environmentalSensor.js alkalmazást tartalmazó mappába. Ezt az alkalmazást az [eszköz csatlakoztatása (Node.js) oktatóanyag](./tutorial-connect-device-nodejs.md)befejezése után hozta létre.

1. Szerkessze a **environmentalSensor.js** fájlt az alábbiak szerint, és mentse.
    - Cserélje le az `idScope` értéket az **azonosító hatókörre** , amelyet korábban jegyzett készített.
    - `registrationId`Az értéket cserélje le a értékre `mytestselfcertprimary` .
    - A **var deviceCert** a következőképpen cserélje le:

        ```javascript
        var deviceCert = {
        cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
        key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
        };
        ```

1. Futtassa a szkriptet, és győződjön meg róla, hogy az eszközt sikeresen kiosztották:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Azt is ellenőrizheti, hogy a telemetria megjelenik-e az irányítópulton.

    ![Telemetria egyéni regisztráció](./media/how-to-connect-devices-x509/telemetry-primary.png)

A fenti lépéseket a _mytestselfcertsecondary_ -tanúsítványhoz is megismételheti.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan csatlakoztathatók az eszközök az X. 509 tanúsítványokkal, a javasolt következő lépés az [eszköz kapcsolatának figyelése az Azure CLI használatával](howto-monitor-devices-azure-cli.md)
