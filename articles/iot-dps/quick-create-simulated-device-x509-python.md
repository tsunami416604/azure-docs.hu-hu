---
title: Szimulált X. 509 eszköz kiépítése az Azure IoT Hub a Python használatával
description: Rövid útmutató – szimulált X. 509 eszköz létrehozása és kiépítése a IoT Hub Device Provisioning Service Python Device SDK-val. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 0bd47de8862be4e66914b9748a00ce907acda526
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555500"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: szimulált X. 509 eszköz létrehozása és kiépítése a IoT Hub Device Provisioning Service Python Device SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések bemutatják, hogyan hozhat létre szimulált X.509-eszközt egy Windows operációs rendszert futtató fejlesztői gépen, és hogyan kötheti össze ezt a szimulált eszközt a Device Provisioning Service-szel és az IoT Hubbal egy Python kódminta segítségével. 

> [!IMPORTANT]
> Ez a cikk csak az elavult v1 Python SDK-ra vonatkozik. Az IOT hub Device kiépítési szolgáltatáshoz tartozó eszközök és szolgáltatások ügyfelei még nem érhetők el a v2-ben. A csapat jelenleg nem működik, hogy a v2-et a szolgáltatás paritására hozza.

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket is. Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy telepítette a [Visual studio](https://visualstudio.microsoft.com/vs/) 2015-es C++vagy újabb verzióját, és a Visual Studio telepítéséhez engedélyezve van az "asztali fejlesztés" munkaterhelése.

2. Töltse le és telepítse a [CMake buildelési rendszert](https://cmake.org/download/).

3. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

4. Nyisson meg egy parancssort vagy a Git Basht. Klónozza a GitHub-tárházat az eszközszimuláció kódmintájának beszerzéséhez.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

5. Hozzon létre egy mappát a GitHub-adattár helyi másolatában a CMake buildelési folyamathoz. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

6. Futtassa az alábbi parancsot az üzembe helyezési ügyfél Visual Studio-megoldásának létrehozásához.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fog használni. Fontos szem előtt tartani az alábbi szempontokat:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

Az Azure IoT C SDK mintakódját használja majd a szimulált eszköz egyéni regisztrációs bejegyzéséhez használt tanúsítvány létrehozásához.

1. Nyissa meg a *cmake* mappában létrehozott `azure_iot_sdks.sln` nevű megoldást, és építse fel azt a Visual Studióban.

2. Kattintson a jobb gombbal a **dice\_device\_enrollment** projektre a **Provision\_Tools** mappában, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. Futtassa a megoldást. 

3. A kimeneti ablakba írja be a következőt az egyéni beléptetéshez: `i`. A kimeneti ablakban megjelenik egy helyileg létrehozott X.509-tanúsítvány a szimulált eszközhöz. 
    
    Másolja az első tanúsítványt a vágólapra. Kezdje a következő első előfordulásával:
    
        -----BEGIN CERTIFICATE----- 
        
    Fejezze be a másolást a következő első előfordulása után:
    
        -----END CERTIFICATE-----
        
    Győződjön meg róla, hogy azt a két sort is belefoglalta. 

    ![Dice eszközregisztrációs alkalmazás](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Hozzon létre egy **_X509testcertificate.pem_** nevű fájlt a Windows rendszerű gépén, nyissa meg egy tetszőleges szövegszerkesztővel, és másolja bele a vágólapra kimásolt szöveget. Mentse a fájlt. 

5. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg a kiépítési szolgáltatást.

6. Az eszközök kiépítési szolgáltatásának menüjében válassza a **regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** fület, és válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

7. A **beléptetés hozzáadása** panelen adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az *elsődleges tanúsítvány. PEM vagy. cer fájlban*válassza a *fájl kiválasztása* lehetőséget az előző lépésekben létrehozott **x509testcertificate tanúsítványfájlt. PEM** fájl kiválasztásához.
   - Ha kívánja, megadhatja az alábbi információkat is:
     - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
     - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
     - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Ha elkészült, kattintson a **Save (Mentés** ) gombra. 

     [![Egyéni regisztráció hozzáadása X.509-igazoláshoz a portálon](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Sikeres beléptetés esetén az X.509-eszköz **riot-device-cert** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban az *Egyéni beléptetések* lapon. 

## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az eszközök kiépítési szolgáltatásának menüjében válassza az **Áttekintés**lehetőséget. Jegyezze fel az _Azonosító hatóköre_ és a _Globális szolgáltatásvégpont_ értékeit.

    ![Szolgáltatás adatai](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Töltse le és telepítse a [Python 2.x-es vagy 3.x-es verzióját](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változókhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Ha Windows rendszert használ, telepítse a [Visual Studio 2015 szoftverhez készült Microsoft Visual C++ terjeszthető változatát](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). A PIP-csomagoknak szüksége van erre az összetevőre a C nyelvű DLL-ek betöltéséhez és végrehajtásához.

3. A Python-csomagok létrehozásához kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md).

   > [!NOTE]
   > A `pip` használata esetén mindenképp telepítse az `azure-iot-provisioning-device-client` csomagot is.

4. Lépjen a mintákat tartalmazó mappára.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

5. A Python IDE használatával módosítsa a **provisioning\_device\_client\_sample.py** nevű Python-szkriptet. Módosítsa a _GLOBAL\_PROV\_URI_ és az _ID\_SCOPE_ változót a korábban feljegyzett értékekre.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

6. Futtassa a mintát. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

7. Az alkalmazás csatlakozik, regisztrálja az eszközt, és megjelenít egy üzenetet a sikeres regisztrációról.

    ![sikeres regisztráció](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

8. A portálon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépíti a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz. Előfordulhat, hogy a felső **frissítés** gombra kell kattintania, ha már megnyitotta a panelt a minta eszköz futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Nyissa meg a szolgáltatás **regisztrációk kezelése** paneljét, majd válassza az **Egyéni regisztrációk** fület. jelölje be az ebben a rövid útmutatóban regisztrált eszköz *regisztrációs azonosítója* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra. 
3. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Nyissa meg a **IoT-eszközök** panelt, jelölje be az ebben a rövid útmutatóban regisztrált eszköz *azonosítója* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a szimulált X. 509 eszközt a Windows rendszerű gépen, és kiépíti azt az IoT hubhoz a portálon elérhető Azure IoT Hub Device Provisioning Service használatával. Az X. 509 eszköz programozott módon történő regisztrálásának megismeréséhez folytassa az X. 509 eszközök programozott regisztrálására szolgáló rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök regisztrálása az Azure-ba IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)
