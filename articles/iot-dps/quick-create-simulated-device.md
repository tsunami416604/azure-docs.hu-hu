---
title: 'Quickstart: Provision a simulated TPM device to Azure IoT Hub using C'
description: Ez a rövid útmutató egyéni regisztrációkat használ. Ebben a rövid útmutatóban egy szimulált TPM-eszközt hoz létre és épít ki az Azure IoT Hub Device Provisioning Service-hez készült C eszközoldali SDK-val.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 0330476650af205854b6d0d4be098c28b46e78a1
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423176"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Rövid útmutató: Szimulált TPM-eszköz kiépítése az Azure IoT C SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat platformmegbízhatósági modul (TPM) alapú eszközszimulátort Windows rendszerű fejlesztői gépeken. A szimulált eszközt egy IoT Hubhoz csatlakoztatja egy Device Provisioning Service-példánnyal. Az eszköz egy Device Provisioning Service-példánnyal való regisztrálásához és az eszköz rendszerindításának szimulálásához az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) egy mintakódját használjuk majd.

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket. A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Fejlesztői környezet előkészítése az Azure IoT C SDK-hoz

Ebben a szakaszban egy fejlesztői környezetet készítünk elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) és a [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) eszközszimulátor mintájának létrehozásához.

1. Download the [CMake build system](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Ez a művelet várhatóan több percig is eltarthat.


3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Az SDK létrehozása és a TPM-eszközszimulátor futtatása

Ebben a szakaszban létrehozzuk az Azure IoT SDK-t, amely tartalmazza a TPM-eszközszimulátor mintakódját. Ez a minta TPM [igazolási mechanizmust](concepts-security.md#attestation-mechanism) nyújt közös hozzáférésű jogosultságkód- (SAS-) jogkivonat hitelesítésével.

1. Az azure-iot-sdk-c Git-adattárban létrehozott `cmake` alkönyvtárból futtassa a következő parancsot a minta létrehozásához. Ez a buildelési parancs egy Visual Studio-megoldást is létrehoz a szimulált eszközhöz.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Keresse meg a klónozott Git-adattár gyökérmappájához, és futtassa a [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-szimulátort az alább látható elérési úttal. Ez a szimulátor a 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot; a rövid útmutató végéig futnia kell ennek a szimulátornak. 

   A *cmake* mappában futtassa a következő parancsokat:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Nem jelenik meg kimenet a szimulátorból. Hagyja tovább futni a TPM-eszköz szimulálását.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Titkosítási kulcsok olvasása a TPM-eszközből

Ebben a szakaszban olyan mintát hoz létre és futtat, amely beolvassa a futó TPM-szimulátor ellenőrzőkulcsát és regisztrációs azonosítóját, továbbá a 2321-es és a 2322-es portokon figyel. A rendszer ezeket az értékeket használja az eszköz regisztrációjához a Device Provisioning Service-példány esetében.

1. Indítsa el a Visual Studiót, és nyissa meg az `azure_iot_sdks.sln` nevű új megoldásfájlt. A megoldásfájl az azure-iot-sdk-c Git-adattár gyökérkönyvtárában korábban létrehozott `cmake` mappában található.

2. A Visual Studio menüjében válassza a **Build** > **Build Solution** (Létrehozás > Megoldás létrehozása) menüpontot a megoldásban lévő összes projekt létrehozásához.

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Tools** (Kiépítés > Eszközök) mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. 

4. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. The app reads and displays a **_Registration ID_** and an **_Endorsement key_** . Note or copy these values. Ezeket a következő szakaszban, az eszközregisztráció során fogjuk használni. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service.

1. Select the **Manage enrollments** tab, and then select the **Add individual enrollment** button at the top. 

1. In the **Add Enrollment** panel, enter the following information:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Enter the *Registration ID* and *Endorsement key* for your TPM device from the values you noted previously.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Enter a unique *Device ID* (you can use the suggested **test-docs-device** or provide your own). Ne használjon bizalmas adatokat az eszköz elnevezésekor. If you choose not to provide one, the registration ID will be used to identify the device instead.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Once complete, press the **Save** button. 

      ![Írja be az eszköz beléptetési információit a portálon](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni beléptetések* lapon lévő listában. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

Ebben a szakaszban konfigurálja a mintakódot, hogy az [Advanced Message Queueing Protocol (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) használatával küldje el az eszköz rendszerindítási szekvenciáját a Device Provisioning Service-példánynak. A rendszerindítási szekvenciának köszönhetően a rendszer felismeri majd az eszközt, és hozzárendeli egy, a Device Provisioning Service-példányhoz társított IoT Hubhoz.

1. Az Azure Portalon válassza ki az eszközregisztrációs szolgáltatás **Áttekintés** lapját, és másolja ki az **_Azonosító hatóköre_** értéket.

    ![A Device Provisioning Service végpont-információinak kinyerése a portálról](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

3. A fájl elején keresse meg az eszközprotokollok `#define` utasításait az alábbi módon. Győződjön meg arról, hogy csak a `SAMPLE_AMQP` ne legyen ellátva megjegyzéssel.

    Jelenleg az [MQTT protokoll nem támogatott az egyéni TPM-regisztrációhoz](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Az alább látható módon győződjön meg róla, hogy az `hsm_type` változó értéke `SECURE_DEVICE_TYPE_TPM`, és nem `SECURE_DEVICE_TYPE_X509`.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

7. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. In the prompt to rebuild the project, select **Yes**, to rebuild the project before running.

    Az alábbi példakimeneten az látható, hogy az eszközregisztrációs mintaügyfél sikeresen elindul, és csatalakozik a Device Provisioning Service példányához az IoT Hub adatainak lekérése és a regisztráció céljából:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Once the simulated device is provisioned to the IoT hub by your provisioning service, the device ID appears with the hub's **IoT devices**. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

If you plan to continue working on and exploring the device client sample, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. Zárja be a TPM szimulátor ablakát a gépen.
3. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning service. Open **Manage Enrollments** for your service, and then select the **Individual Enrollments** tab. Select the check box next to the *REGISTRATION ID* of the device you enrolled in this quickstart, and press the **Delete** button at the top of the pane. 
4. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open **IoT devices** for your hub, select the check box next to the *DEVICE ID* of the device you registered in this quickstart, and then press the **Delete** button at the top of the pane.

## <a name="next-steps"></a>Következő lépések

In this quickstart, you’ve created a TPM simulated device on your machine and provisioned it to your IoT hub using the IoT Hub Device Provisioning Service. To learn how to enroll your TPM device programmatically, continue to the quickstart for programmatic enrollment of a TPM device. 

> [!div class="nextstepaction"]
> [Azure quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-java.md)
