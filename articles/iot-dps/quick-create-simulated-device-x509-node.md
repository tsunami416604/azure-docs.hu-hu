---
title: Provision simulated X.509 device to Azure IoT Hub using Node.js
description: Szimulált X.509-eszköz létrehozása és kiépítése az Azure IoT Hub Device Provisioning Service-hez készült Node.js eszközoldali SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 5ba18ae304ee1d72306f233a5b288c358efcd696
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423363"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Create and provision an X.509 simulated device using Node.js device SDK for IoT Hub Device Provisioning Service
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések bemutatják, hogyan hozhat létre regisztrációs bejegyzést a Device Provisioning Service-ben, hogyan szimulálhat egy X.509-eszközt a fejlesztői gépén, hogyan csatlakoztatja a szimulált eszközt a Device Provisioning Service-hez, és hogyan regisztrálhatja ezt az eszközt az IoT Hubon az [Azure IoT Hubhoz készült Node.js eszközoldali SDK segítségével](https://github.com/Azure/azure-iot-sdk-node).

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket is. Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. A folytatás előtt végezze el az [IoT Hub Device Provisioning Service Azure Portallal való beállítását](./quick-setup-auto-provision.md) ismertető cikk lépéseit.

2. Győződjön meg arról, hogy a [Node.js 4.0-s vagy újabb verziója](https://nodejs.org) telepítve van a gépén.

3. Győződjön meg arról, hogy a [Git](https://git-scm.com/download/) telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. 

4. Győződjön meg arról, hogy az [OpenSSL](https://www.openssl.org/) telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. Ez a kódtár létrehozható és telepíthető valamilyen forrásból, vagy letölthető és telepíthető [külső féltől](https://wiki.openssl.org/index.php/Binaries), például [innen](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Ha már létrehozta az X.509 _fő-_ , _köztes_, és/vagy _levéltanúsítványát_, kihagyhatja ezt a lépést és a tanúsítvány létrehozásával kapcsolatos további lépéseket.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fogunk használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

Az [Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node.git) mintakódját használja majd a szimulált eszköz egyéni regisztrációs bejegyzéséhez használandó tanúsítvány létrehozásához.


1. Nyisson meg egy parancssort. Klónozza a GitHub-adattárat a kódmintákhoz:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Lépjen a tanúsítványt létrehozó szkripthez, és állítsa össze a projektet. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. A saját _tanúsítványnevét_ használva hozzon létre egy X.509 _levéltanúsítványt_ a szkript futtatásával. A levéltanúsítvány köznapi neve lesz a [regisztrációs azonosító](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id), ezért csak kisbetűket, alfanumerikus karaktereket és kötőjeleket használjon.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Sign in to the [Azure portal](https://portal.azure.com), select the **All resources** button on the left-hand menu and open your Device Provisioning Service instance.

5. From the Device Provisioning Service menu, select **Manage enrollments**. Select **Individual Enrollments** tab and select the **Add individual enrollment** button at the top. 

6. In the **Add Enrollment** panel, enter the following information:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Under the *Primary certificate .pem or .cer file*, choose *Select a file* to select the certificate file **{certificate-name}_cert.pem** created in the previous steps.  
   - Ha kívánja, megadhatja az alábbi információkat is:
     - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
     - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
     - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
     - Once complete, press the **Save** button. 

     [![Egyéni regisztráció hozzáadása X.509-igazoláshoz a portálon](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     On successful enrollment, your X.509 device appears as **{certificatename}** under the *Registration ID* column in the *Individual Enrollments* tab. Note this value for later.

## <a name="simulate-the-device"></a>Az eszköz szimulálása

Az [Azure IoT Hub Node.js eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-node) segítségével könnyedén szimulálhatóak eszközök. További információkért tekintse meg az [eszközökkel kapcsolatos alapelveket ismertető](https://docs.microsoft.com/azure/iot-dps/concepts-device) témakört.

1. In the Azure portal, select the **Overview** blade for your Device Provisioning service and note the **_GLobal Device Endpoint_** and **_ID Scope_** values.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Másolja a _tanúsítványt_ és a _kulcsot_ a mintamappába.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Keresse meg az eszköz tesztszkriptjét, és állítsa össze a projektet. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Szerkessze a **register\_x509.js** fájlt. A következő módosítások elvégzése után mentse a fájlt.
    - A `provisioning host` elemet cserélje le a fenti **1. lépésben** feljegyzett **_globális eszközvégpontra_** .
    - Replace `id scope` with the **_ID Scope_** noted in **Step 1** above. 
    - Replace `registration id` with the **_Registration ID_** noted in the previous section.
    - A `cert filename` és a `key filename` elemet cserélje le a fenti **2. lépésben** másolt fájlokra. 

5. Futtassa a szkriptet, és ellenőrizze, hogy az eszköz sikeresen lett-e kiépítve.

    ```cmd/sh
    node register_x509.js
    ```   

6. In the portal, navigate to the IoT hub linked to your provisioning service and open the **IoT devices** blade. On successful provisioning of the simulated X.509 device to the hub, its device ID appears on the **IoT devices** blade, with *STATUS* as **enabled**. You might need to press the **Refresh** button at the top if you already opened the blade prior to running the sample device application. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open the **Manage Enrollments** blade for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
3. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open the **IoT devices** blade for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.


## <a name="next-steps"></a>Következő lépések

In this quickstart, you’ve created a simulated X.509 device and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service on the portal. To learn how to enroll your X.509 device programmatically, continue to the quickstart for programmatic enrollment of X.509 devices. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll X.509 devices to Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)
