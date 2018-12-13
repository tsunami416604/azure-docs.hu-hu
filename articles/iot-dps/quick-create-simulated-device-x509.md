---
title: Ez a rövid útmutató egy szimulált X.509-eszköz kiépítését mutatja be az Azure IoT Hubra C használatával | Microsoft Docs
description: Ez a rövid útmutató egyéni regisztrációkat használ. Ebben a rövid útmutatóban egy szimulált X.509-eszközt hoz létre és épít ki az Azure IoT Hub Device Provisioning Service-hez készült C eszközoldali SDK-val.
author: wesmc7777
ms.author: wesmc
ms.date: 07/16/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 16942e183152720dc958b6c0ccecde1dee2e5cde
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185171"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Gyors útmutató: Szimulált X.509-eszköz használata az Azure IoT C SDK kiépítése

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és futtathat X.509 eszközszimulátort Windows rendszerű fejlesztői gépeken. A szimulált eszközt arra konfiguráljuk, hogy hozzárendeljük egy IoT-központhoz egy regisztráció használatával egy Device Provisioning Service-példányban. Az eszköz rendszerindításának szimulálásához az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) egy mintakódját használjuk majd. A rendszer a regisztrációs szolgáltatásban való regisztrációja alapján ismeri fel és társítja az eszközt az IoT-központhoz.

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket. A rövid útmutató folytatása előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Segítségével több kapcsolódó eszközöket regisztrálni.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egy adott eszköz regisztrálásához használja.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2015 vagy [Visual Studio 2017](https://www.visualstudio.com/vs/), amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladat.
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Fejlesztői környezet előkészítése az Azure IoT C SDK-hoz

Ebben a szakaszban egy fejlesztői környezetet készítünk elő az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) buildeléséhez, amely tartalmazza az X.509 rendszerindítási mintakódot.

1. Töltse le a [CMake buildelési rendszer](https://cmake.org/download/) 3.11.4-es verzióját. Ellenőrizze a letöltött bináris fájlt a megfelelő titkosítási kivonat értékének használatával. Az alábbi példa a Windows PowerShell használatával ellenőrizte az x64 MSI disztribúció 3.11.4-es verziójának titkosítási kivonatát:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    A CMake webhelyén az írás időpontjában a következő kivonatolási értékek szerepelnek a 3.11.4-es verzióhoz:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Az adattár mérete jelenleg körülbelül 220 MB. Ez a művelet várhatóan több percig is eltarthat.


3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. A kódminta X.509-tanúsítványt használ az X.509-hitelesítéssel történő igazoláshoz. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```



<a id="portalenroll"></a>

## <a name="create-a-self-signed-x509-device-certificate"></a>Önaláírt X.509-eszköztanúsítvány létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fog használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

Az Azure IoT C SDK mintakódját használja majd a szimulált eszköz egyéni regisztrációs bejegyzéséhez használt tanúsítvány létrehozásához.

1. Indítsa el a Visual Studiót, és nyissa meg az `azure_iot_sdks.sln` nevű új megoldásfájlt. A megoldásfájl az azure-iot-sdk-c Git-adattár gyökérkönyvtárában korábban létrehozott `cmake` mappában található.

2. A Visual Studio menüjében válassza a **Build** > **Build Solution** (Létrehozás > Megoldás létrehozása) menüpontot a megoldásban lévő összes projekt létrehozásához.

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Tools** (Kiépítés > Eszközök) mappára. Kattintson a jobb gombbal a **dice\_device\_enrollment** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

4. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A kimeneti ablakba írja be a következőt az egyéni regisztrációhoz, amikor a rendszer erre kéri: **i**. 

    A kimeneti ablakban megjelenik egy helyileg létrehozott önaláírt X.509-tanúsítvány a szimulált eszközhöz. Másolja a vágólapra a kimenetet a **-----BEGIN CERTIFICATE-----** sortól az első **-----END CERTIFICATE-----** sorig, és ügyeljen arra, hogy a kijelölésben ez a két sor is benne legyen. Csak az első tanúsítványra van szüksége a kimeneti ablakból.
 
5. Egy szövegszerkesztővel mentse a tanúsítványt egy új fájlba **_X509testcert.pem_** néven. 


## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson a **Minden erőforrás** gombra, és nyissa meg a Device Provisioning Service-t.

2. Válassza a **Regisztrációk kezelése** fület, és kattintson a felül lévő **Egyéni regisztráció hozzáadása** gombra. 

3. A **Regisztráció hozzáadása** lapon adja meg az alábbi adatokat, majd kattintson a **Mentés** gombra.

    - **Mechanizmus:** Válassza az X.509 elemet az identitás igazolási *Mechanizmusaként*.
    - **Elsődleges tanúsítvány .pem or .cer fájlja:** Kattintson a **válasszon ki egy fájlt** a tanúsítványfájlt, X509testcert.pem, válassza ki a korábban létrehozott.
    - **IoT Hub-Eszközazonosító:** Adja meg **test-docs-cert-device** biztosíthat az eszköz azonosítója.

      [![Egyéni regisztráció hozzáadása X.509-igazoláshoz a portálon](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Sikeres beléptetés esetén az X.509-eszköz **riot-device-cert** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban az *Egyéni beléptetések* lapon. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

Ebben a szakaszban frissítjük a mintakódot, hogy leküldje az eszköz rendszerindítási szekvenciáját a Device Provisioning Service-példányba. A rendszerindítási szekvenciának köszönhetően a rendszer felismeri majd az eszközt, és hozzárendeli egy, a Device Provisioning Service-példányhoz társított IoT Hubhoz.



1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

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

6. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

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

7. A portálon lépjen a kiépítési szolgáltatáshoz csatolt IoT-központhoz, és kattintson az **IoT-eszközök** fülre. Ha sikeresen kiépíti a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik az **IoT-eszközök** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz. Lehet, hogy rá kell kattintania fent a **Frissítés** gombra. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **Regisztrációk kezelése** lapját, majd kattintson az **Egyéni regisztrációk** lapra. Válassza ki a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra. 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** lapját, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy szimulált X.509-eszközt hozott létre a Windows rendszerű gépén, amelyet aztán kiépített az IoT Hubon a portál Azure IoT Hub Device Provisioning Service szolgáltatásával. Ha szeretné megismerni az X.509-eszköz programozott regisztrációjának folyamatát, lépjen tovább az X.509-eszközök programozott regisztrációjának rövid útmutatójára. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X.509-eszközök regisztrációja az Azure IoT Hub Device Provisioning Service-be](quick-enroll-device-x509-java.md)
