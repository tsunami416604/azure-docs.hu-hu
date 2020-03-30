---
title: Gyorsútmutató – Szimmetrikus kulcs használata szimulált eszköz kiépítése az Azure IoT Hubba c használatával
description: Ebben a rövid útmutatóban a C-eszköz SDK-t fogja használni egy szimulált eszköz létrehozásához, amely szimmetrikus kulcsot használ az Azure IoT Hub-eszközkiépítési szolgáltatással (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/14/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6047051a36459d61bb5f02907dde9e73a70e86ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75945212"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Rövid útmutató: Szimmetrikus kulcs kiosztása szimulált eszköz számára

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat eszközszimulátort a Windows rendszerű fejlesztői gépeken. A szimulált eszközt arra konfiguráljuk, hogy egy szimmetrikus kulcs használatával hitelesítse magát egy Device Provisioning Service-példánynál és hozzárendelésre kerüljön egy IoT-központhoz. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) mintakódját használva fogjuk szimulálni az eszközindítási szekvenciát, amely a kiosztás folyamatát fogja elindítani. A rendszer az eszközt a regisztrációs szolgáltatáspéldányban való egyéni regisztrációja alapján ismeri fel, és ez alapján társítja egy IoT-központhoz.

Bár ez a cikk bemutatja az egyéni beléptetési kiépítést, használhatja a regisztrációs csoportokat. A regisztrációs csoportok használata kor van néhány különbség. Például egy származtatott eszközkulcsot kell használnia, amely egyedi regisztrációs azonosítóval van elhasználva az eszközhöz. Bár a szimmetrikus kulcsot használó regisztrációs csoportok nem csak örökölt eszközök esetében használhatóak, a [Szimmetrikus kulcsok használata örökölt eszközök kiépítéséhez](how-to-legacy-device-symm-key.md) című cikk példája jól szemlélteti a regisztrációs csoportok használatát. További információért lásd: [Csoportos beléptetés használata szimmetrikus kulcsú igazolásnál](concepts-symmetric-key-attestation.md#group-enrollments)

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket. 

A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. Ez a rövid útmutató feltételezi, hogy korábban már létrehozott egy Device Provisioning Service-példányt.

A cikk során egy Windows-alapú munkaállomást fogunk használni. Azonban az eljárások Linux esetében is alkalmazhatóak. Linux-munkaállomást használó példát a [Több bérlős regisztráció](how-to-provision-multitenant.md) című cikkben talál.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztői környezetben. Linux vagy macOS esetén tekintse meg a megfelelő szakaszt [a Fejlesztői környezet előkészítése az](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) SDK dokumentációban című témakörben.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 az ["Asztali fejlesztés C++"-os munkaterheléssel.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. 

Az SDK tartalmaz szimulált eszközök esetében használható mintakódokat is. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

1. Töltse le a [CMake build rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

    A CMake buildrendszer régebbi verziói nem hozhatják létre a cikkben használt megoldásfájlt. Győződjön meg arról, hogy a CMake újabb verzióját használja.

2. Kattintson a **Címkék** elemre, és keresse meg a legújabb kiadás címkenevét [az Azure IoT C SDK Kiadási oldalán.](https://github.com/Azure/azure-iot-sdk-c/releases/latest)

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

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatást.

2. Válassza a **Regisztrációk kezelése** lapot, majd a lista tetején az **Egyéni beléptetés hozzáadása** gombot. 

3. A **Beiktatás hozzáadása** panelen adja meg az alábbi adatokat, és nyomja meg a **Mentés** gombot.

   - **Eljárás**: Identitásazonosító *eljárásnak* válassza a **Szimmetrikus kulcs** lehetőséget.

   - **Billentyűk automatikus generálása**: Jelölje be ezt a jelölőnégyzetet.

   - **Regisztrációs azonosító**: Adja meg a regisztráció azonosításra szolgáló Regisztrációs azonosítót. Csak a kisbetűs alfanumerikus karaktereket és a kötőjel karaktert használhatja. Például **symm-key-device-007**.

   - **IoT Hub-eszközazonosító**: Adjon meg egy eszközazonosítót. Például: **device-007**

     ![Egyéni regisztráció hozzáadása szimmetrikus kulcsú igazoláshoz a Portalon](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Miután mentette a regisztrációt, az **elsődleges kulcs** és a **másodlagos kulcs** jön létre, és hozzáadódik a regisztrációs bejegyzéshez. A Szimmetrikus kulcsot használó eszközregisztráció **symm-key-device-007** néven jelenik meg az *Egyéni beléptetések* lap *Regisztrációs azonosító* oszlopában. 

    Nyissa meg a regisztrációt, és másolja ki a generált **Elsődleges kulcsot**.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

Ebben a szakaszban frissítjük a mintakódot, hogy leküldje az eszköz rendszerindítási szekvenciáját a Device Provisioning Service-példányba. A rendszerindítási szekvenciának köszönhetően a rendszer felismeri majd az eszközt, és hozzárendeli egy, a Device Provisioning Service-példányhoz társított IoT Hubhoz.



1. Az Azure Portalon válassza **az Áttekintés** lapot az eszközkiépítési szolgáltatáshoz, és jegyezze fel az **_ID scope_** értékét.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a CMake futtatásával létrehozott **azure_iot_sdks.sln** megoldásfájlt. A megoldásfájlnak a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

    Ha a fájl nem jött létre a cmake könyvtárban, győződjön meg arról, hogy a CMake buildrendszer legújabb verzióját használta.

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

6. Keresse meg `prov_dev_set_symmetric_key_info()` a hívást, hogy a **prov\_dev\_\_ügyfél sample.c,** amely kommentálta ki.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ne fűzzön megjegyzést a függvényhíváshoz, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket) a regisztrációs azonosítóval és az elsődleges kulcsértékekkel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Mentse a fájlt.

7. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

8. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére irányuló kérdésben válassza az **Igen**lehetőséget a projekt futás előtti újraépítéséhez.

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

9. A portálon keresse meg azt az IoT-központot, amelyhez a szimulált eszköz hozzá lett rendelve, és válassza ki az **IoT-eszközök** lapot. A szimulált hubra való sikeres kiépítésekor az eszközazonosítója megjelenik az **IoT-eszközök** panelen, és a *STATUS* **engedélyezve van.** Előfordulhat, hogy meg kell **nyomnia** a frissítés gombot a tetején. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa **meg a szolgáltatás regisztrációinak kezelése** lehetőséget, majd válassza az Egyéni *REGISTRATION ID* **Delete** **regisztrációk** lapot. 
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg a hub **IoT-eszközeit,** jelölje be a rövid útmutatóban regisztrált eszköz *ESZKÖZazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szimulált eszközt a Windows-gépen, és kiépítette azt az IoT hubra szimmetrikus kulccsal az Azure IoT Hub-eszközkiépítési szolgáltatással a portálon. Ha meg szeretné tudni, hogyan regisztrálhatja az eszközt programozott módon, folytassa az X.509-es eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsútmutató – X.509-es eszközök regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-x509-java.md)
