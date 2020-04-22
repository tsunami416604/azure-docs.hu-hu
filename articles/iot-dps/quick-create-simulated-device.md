---
title: 'Rövid útmutató: Szimulált TPM-eszköz kiépítése az Azure IoT Hubba a C használatával'
description: Ez a rövid útmutató egyéni regisztrációkat használ. Ebben a rövid útmutatóban egy szimulált TPM-eszközt hoz létre és épít ki c-eszköz SDK-val az Azure IoT Hub-eszközkiépítési szolgáltatáshoz (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom:
- mvc
- amqp
- mqtt
ms.openlocfilehash: 5d52cd134c8c0f1702f57bff1f60bffa12ef6f4d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687231"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Rövid útmutató: Szimulált TPM-eszköz kiépítése az Azure IoT C SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat platformmegbízhatósági modul (TPM) alapú eszközszimulátort Windows rendszerű fejlesztői gépeken. A szimulált eszközt egy IoT Hubhoz csatlakoztatja egy Device Provisioning Service-példánnyal. Az eszköz egy Device Provisioning Service-példánnyal való regisztrálásához és az eszköz rendszerindításának szimulálásához az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) egy mintakódját használjuk majd.

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át az [automatikus kiépítés fogalmait.](concepts-auto-provisioning.md) A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztői környezetben. Linux vagy macOS esetén tekintse meg a megfelelő szakaszt [a Fejlesztői környezet előkészítése az](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) SDK dokumentációban című témakörben.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 az ["Asztali fejlesztés C++"-os munkaterheléssel.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Fejlesztői környezet előkészítése az Azure IoT C SDK-hoz

Ebben a szakaszban egy fejlesztői környezetet készítünk elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) és a [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) eszközszimulátor mintájának létrehozásához.

1. Töltse le a [CMake build rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben található címkét `-b` a paraméter értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a `azure-iot-sdk-c` következő parancsokat a könyvtárból:

    ```cmd/sh
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

2. A Visual Studio menüben válassza a Build Solution **összeállítása** > **Build Solution** lehetőséget az összes projekt létrehozásához a megoldásban.

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Tools** (Kiépítés > Eszközök) mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. 

4. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. Az alkalmazás beolvassa és megjeleníti a **_regisztrációs azonosítót_** és **_az ellenőrzőkulcsot._** Jegyezze fel vagy másolja ezeket az értékeket. Ezeket a következő szakaszban, az eszközregisztráció során fogjuk használni. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Jelentkezzen be az Azure Portalon, válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatást.

1. Válassza a **Regisztrációk kezelése** lapot, majd a lista tetején az **Egyéni beléptetés hozzáadása** gombot. 

1. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Adja meg a TPM-eszköz *regisztrációs azonosítóját* és *ellenőrzőkulcsát* a korábban megadott értékekből.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Adjon meg egy egyedi *eszközazonosítót* (használhatja a javasolt **teszt-docs-eszközt,** vagy megadhatja a sajátját). Ne használjon bizalmas adatokat az eszköz elnevezésekor. Ha úgy dönt, hogy nem ad meg egyet, a regisztrációs azonosítót fogja használni az eszköz azonosítására.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Miután elkészült, nyomja meg a **Mentés** gombot. 

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

7. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére irányuló kérdésben válassza az **Igen**lehetőséget a projekt futás előtti újraépítéséhez.

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

8. Miután a szimulált eszköz ki van építve az IoT hub a létesítési szolgáltatás, az eszköz azonosítója jelenik meg a hub **IoT-eszközök.** 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. Zárja be a TPM szimulátor ablakát a gépen.
3. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa **meg a szolgáltatás regisztrációinak kezelése** lehetőséget, majd válassza az Egyéni *REGISTRATION ID* **Delete** **regisztrációk** lapot. 
4. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg a hub **IoT-eszközeit,** jelölje be a rövid útmutatóban regisztrált eszköz *ESZKÖZazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy TPM szimulált eszközt a gépen, és kiépítette azt az IoT Hub-eszközkiépítési szolgáltatás használatával az IoT Hub-eszközkiépítési szolgáltatás használatával. A TPM-eszköz programozott regisztrálásáról további információ a TPM-eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsindítás – TPM-eszköz regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-tpm-java.md)
