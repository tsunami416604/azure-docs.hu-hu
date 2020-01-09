---
title: Rövid útmutató – szimmetrikus kulcs használata szimulált eszköz Azure-ba való kiépítéséhez a C használatával IoT Hub
description: Ebben a rövid útmutatóban a C Device SDK-t fogja használni egy szimulált eszköz létrehozásához, amely szimmetrikus kulcsot használ az Azure IoT Hub Device Provisioning Service (DPS) használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 0c0192ac2cafc724875c07de152bdb1d3f4e49ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434694"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat eszközszimulátort a Windows rendszerű fejlesztői gépeken. A szimulált eszközt arra konfiguráljuk, hogy egy szimmetrikus kulcs használatával hitelesítse magát egy Device Provisioning Service-példánynál és hozzárendelésre kerüljön egy IoT-központhoz. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) mintakódját használva fogjuk szimulálni az eszközindítási szekvenciát, amely a kiosztás folyamatát fogja elindítani. A rendszer az eszközt a regisztrációs szolgáltatáspéldányban való egyéni regisztrációja alapján ismeri fel, és ez alapján társítja egy IoT-központhoz.

Bár ez a cikk csak egy egyéni regisztráció kiépítését mutatja be, ugyanezt az eljárást alkalmazhatja regisztrációs csoportok esetében is. Az egyetlen különbség, hogy ebben az esetben egy származtatott eszközkulcsot kell használnia, eszközönkénti egyedi regisztrációs azonosítóval. Regisztrációs csoportok esetében a regisztráció szimmetrikus kulcsa nem kerül közvetlenül felhasználásra. Bár a szimmetrikus kulcsot használó regisztrációs csoportok nem csak örökölt eszközök esetében használhatóak, a [Szimmetrikus kulcsok használata örökölt eszközök kiépítéséhez](how-to-legacy-device-symm-key.md) című cikk példája jól szemlélteti a regisztrációs csoportok használatát. További információért lásd: [Csoportos beléptetés használata szimmetrikus kulcsú igazolásnál](concepts-symmetric-key-attestation.md#group-enrollments)

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztési környezetéhez szükségesek. Linux vagy macOS esetén tekintse meg a [fejlesztési környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) az SDK-ban című dokumentáció megfelelő szakaszát.

* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019-es verziójában engedélyezve van az [" C++asztali fejlesztés"](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) munkaterhelése. A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. 

Az SDK tartalmaz szimulált eszközök esetében használható mintakódokat is. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build-szolgáltatását](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásához](https://github.com/Azure/azure-iot-sdk-c/releases/latest) tartozó címke nevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa az alábbi parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben megtalált címkét a `-b` paraméter értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa az alábbi parancsokat a `azure-iot-sdk-c` könyvtárából:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg az eszköz kiépítési szolgáltatását.

2. Válassza a **regisztrációk kezelése** fület, majd válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

3. A **beléptetés hozzáadása** panelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Kulcsok automatikus generálása**: jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például: **Symm-Key-Device-007**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **device-007**

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Miután mentette a regisztrációt, a rendszer létrehozza az **elsődleges kulcsot** és a **másodlagos kulcsot** , és hozzáadja a beléptetési bejegyzéshez. A Szimmetrikus kulcsot használó eszközregisztráció **symm-key-device-007** néven jelenik meg az *Egyéni beléptetések* lap *Regisztrációs azonosító* oszlopában. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

Ebben a szakaszban frissítjük a mintakódot, hogy leküldje az eszköz rendszerindítási szekvenciáját a Device Provisioning Service-példányba. A rendszerindítási szekvenciának köszönhetően a rendszer felismeri majd az eszközt, és hozzárendeli egy, a Device Provisioning Service-példányhoz társított IoT Hubhoz.



1. A Azure Portal válassza az eszközök kiépítési szolgáltatásának **Áttekintés** lapját, és jegyezze fel az **_azonosító hatókörének_** értékét.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a CMake futtatásával létrehozott **azure_iot_sdks.sln** megoldásfájlt. A megoldásfájlnak a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg róla, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, mint az alább is látható:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Keresse meg azt a hívást, amelyet a **prov\_dev\_ügyfél\_sample. c** `prov_dev_set_symmetric_key_info()` a megjegyzésben talál.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Adja meg a függvény hívását, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket is) a regisztrációs AZONOSÍTÓval és az elsődleges kulcs értékével.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Mentse a fájlt.

7. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

8. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésének megadásához válassza az **Igen**lehetőséget, ha a Futtatás előtt szeretné újraépíteni a projektet.

    Az alábbi példakimeneten látható, hogy a szimulált eszköz sikeresen elindul és csatlakozik a regisztrációs szolgáltatáspéldányhoz, hogy az hozzárendelhesse egy IoT-központhoz:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. A portálon navigáljon az IoT hubhoz, amelyet szimulált eszközhöz rendelt, és válassza a **IoT-eszközök** lapot. Ha sikeresen kiépíti a szimulált eszközt a központba, az eszköz azonosítója megjelenik az **IoT-eszközök** panelen, amely **engedélyezve** *állapotú* . Előfordulhat, hogy a felül található **refresh (frissítés** ) gombra kell kattintania. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációinak kezelése** elemet, majd válassza az **Egyéni regisztrációk** fület. jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközöket** a központhoz, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a szimulált eszközt a Windows rendszerű gépen, és kiépíti azt az IoT hub-ba a portálon található Azure-IoT Hub Device Provisioning Service szimmetrikus kulcs használatával. Az eszköz programozott módon történő regisztrálásának megismeréséhez folytassa az X. 509 eszközök programozott regisztrálására szolgáló rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök regisztrálása az Azure-ba IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
