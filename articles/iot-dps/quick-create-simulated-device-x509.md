---
title: Szimulált X.509-es eszköz kiépítése az Azure IoT Hubba c használatával
description: Ez a rövid útmutató egyéni regisztrációkat használ. Ebben a rövid útmutatóban egy szimulált X.509-es eszközt hoz létre és épít ki c-eszköz SDK-val az Azure IoT Hub-eszközkiépítési szolgáltatáshoz (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f0c95e495e222cc72f0a6fc432404fcbaa47df65
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241163"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Rövid útmutató: Szimulált X.509-eszköz kiépítése az Azure IoT C SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat X.509 eszközszimulátort Windows rendszerű fejlesztői gépeken. A szimulált eszközt arra konfiguráljuk, hogy hozzárendeljük egy IoT-központhoz egy regisztráció használatával egy Device Provisioning Service-példányban. Az eszköz rendszerindításának szimulálásához az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) egy mintakódját használjuk majd. A rendszer a regisztrációs szolgáltatásban való regisztrációja alapján ismeri fel és társítja az eszközt az IoT-központhoz.

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át az [automatikus kiépítés fogalmait.](concepts-auto-provisioning.md) A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](quick-setup-auto-provision.md) szakasz lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

* [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
* [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztői környezetben. Linux vagy macOS esetén tekintse meg a megfelelő szakaszt [a Fejlesztői környezet előkészítése az](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) SDK dokumentációban című témakörben.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 az ["Asztali fejlesztés C++"-os munkaterheléssel.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Fejlesztői környezet előkészítése az Azure IoT C SDK-hoz

Ebben a szakaszban egy fejlesztői környezetet készít az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)létrehozásához, amely tartalmazza az X.509 rendszerindítási sorozat mintakódját.

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

5. A kódminta X.509-tanúsítványt használ az X.509-hitelesítéssel történő igazoláshoz. Futtassa a következő parancsot a fejlesztési platformra jellemző SDK-verzió létrehozásához, amely tartalmazza az eszközkiépítési ügyfelet. A szimulált eszközhöz visual studio-megoldás `cmake` jön létre a címtárban.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Miután a build sikeres, az utolsó néhány kimeneti sor a következő kimenethez hasonlóan néz ki:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Önaláírt X.509-eszköztanúsítvány létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fog használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

Az Azure IoT C SDK mintakódját használja majd a szimulált eszköz egyéni regisztrációs bejegyzéséhez használt tanúsítvány létrehozásához.

1. Indítsa el a Visual Studiót, és nyissa meg az `azure_iot_sdks.sln` nevű új megoldásfájlt. A megoldásfájl az azure-iot-sdk-c Git-adattár gyökérkönyvtárában korábban létrehozott `cmake` mappában található.

2. A Visual Studio menüben válassza a Build Solution **összeállítása** > **Build Solution** lehetőséget az összes projekt létrehozásához a megoldásban.

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Tools** (Kiépítés > Eszközök) mappára. Kattintson a jobb gombbal a **dice\_device\_enrollment** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

4. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A kimeneti ablakba írja be a következőt az egyéni regisztrációhoz, amikor a rendszer erre kéri: **i**.

    A kimeneti ablakban megjelenik egy helyileg létrehozott önaláírt X.509-tanúsítvány a szimulált eszközhöz. Másolja a vágólapra a kimenetet a **-----BEGIN CERTIFICATE-----** sortól az első **-----END CERTIFICATE-----** sorig, és ügyeljen arra, hogy a kijelölésben ez a két sor is benne legyen. Csak az első tanúsítványra van szüksége a kimeneti ablakból.

5. Egy szövegszerkesztővel mentse a tanúsítványt egy új fájlba **_X509testcert.pem_** néven.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Jelentkezzen be az Azure Portalon, válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatást.

2. Válassza a **Regisztrációk kezelése** lapot, majd a lista tetején az **Egyéni beléptetés hozzáadása** gombot.

3. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat, majd nyomja le a **Mentés** gombot.

    * **Mechanizmus:** Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
    * **Elsődleges tanúsítvány .pem vagy .cer fájl:** Válassza **a Fájl kijelölése** lehetőséget a korábban létrehozott X509testcert.pem tanúsítványfájl kijelöléséhez.
    * **IoT Hub-eszközazonosító:** Adja meg a **test-docs-cert-device** azonosítót az eszköz azonosítójaként.

      [![Egyéni regisztráció hozzáadása az X.509-es tanúsítványhoz a portálon](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Sikeres beléptetés esetén az X.509-eszköz **riot-device-cert** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban az *Egyéni beléptetések* lapon. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

Ebben a szakaszban frissítjük a mintakódot, hogy leküldje az eszköz rendszerindítási szekvenciáját a Device Provisioning Service-példányba. A rendszerindítási szekvenciának köszönhetően a rendszer felismeri majd az eszközt, és hozzárendeli egy, a Device Provisioning Service-példányhoz társított IoT Hubhoz.

1. Az Azure Portalon válassza **az Áttekintés** lapot az eszközkiépítési szolgáltatáshoz, és jegyezze fel az **_ID scope_** értékét.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

3. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Az alább látható módon győződjön meg róla, hogy az `hsm_type` változó értéke `SECURE_DEVICE_TYPE_X509`, és nem `SECURE_DEVICE_TYPE_TPM`.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget.

6. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére irányuló kérdésben válassza az **Igen** lehetőséget a projekt újraépítéséhez futás előtt.

    Az alábbi példakimeneten az látható, hogy az eszközregisztrációs mintaügyfél sikeresen elindul, és csatalakozik a regisztrációs rendszer példányához az IoT-központ adatainak lekéréséhez és a regisztráláshoz:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. A portálon keresse meg a létesítési szolgáltatáshoz kapcsolódó IoT-központot, és válassza az **IoT-eszközök** lapot. A szimulált X.509-es eszköz hubra történő sikeres kiépítésekor az eszközazonosítója megjelenik az **IoT-eszközök** paneljén, *a STATUS* pedig **engedélyezve**van. Előfordulhat, hogy meg kell **nyomnia** a frissítés gombot a tetején. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa **meg a szolgáltatás regisztrációinak kezelése** lehetőséget, majd válassza az Egyéni *REGISTRATION ID* **Delete** **regisztrációk** lapot. 
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg a hub **IoT-eszközeit,** jelölje be a rövid útmutatóban regisztrált eszköz *ESZKÖZazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szimulált X.509-es eszközt a Windows-gépen, és kiépítette azt az IoT hubba az Azure IoT Hub-eszközkiépítési szolgáltatás használatával a portálon. Az X.509-es eszköz programozott regisztrálásához folytassa az X.509-es eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsútmutató – X.509-es eszközök regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-x509-java.md)
