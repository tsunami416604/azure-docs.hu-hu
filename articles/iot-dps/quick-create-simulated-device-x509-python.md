---
title: Szimulált X.509-es eszköz kiépítése az Azure IoT Hubba a Python használatával
description: Rövid útmutató – hozzon létre és hozzon létre egy szimulált X.509-es eszközt a Python-eszköz SDK-val az IoT Hub-eszközlétesítési szolgáltatás (DPS) használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 44f1a2cd3336eeae87878c333fb05d2e6b1f88e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605388"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: Szimulált X.509-es eszköz létrehozása és kiépítése Python-eszköz SDK-val az IoT Hub-eszközkiépítési szolgáltatáshoz

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ebben a rövid útmutatóban egy szimulált X.509-es eszközt hoz létre Windows rendszerű számítógépen. Az eszközminta Python-kódot használja a szimulált eszköz és az IoT hub összekapcsolásához az eszközkiépítési szolgáltatás (DPS) egyéni regisztrációjával.

## <a name="prerequisites"></a>Előfeltételek

- Az [automatikus kiépítés fogalmainak áttekintése](concepts-auto-provisioning.md).
- Az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portalon.](./quick-setup-auto-provision.md)
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/) asztali fejlesztéssel C++-al.
- [CMake build rendszer](https://cmake.org/download/).
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Ez a cikk csak az elavult V1 Python SDK vonatkozik. Az Iot Hub-eszközkiépítési szolgáltatás eszköz- és szolgáltatásügyfelei még nem érhetők el a V2-ben. A csapat jelenleg keményen dolgozik, hogy v2-funkció paritás.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>A környezet előkészítése 

1. Győződjön meg arról, hogy telepítette a [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015-ös vagy újabb verzióit, és engedélyezve van az "Asztali fejlesztés C++" munkaterheléssel a Visual Studio telepítéséhez.

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

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk:](concepts-service.md#individual-enrollment)Egyetlen eszköz regisztrálására használható.

Ez a cikk bemutatja az egyes regisztrációk.

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

5. Jelentkezzen be az Azure Portalon, válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg a létesítési szolgáltatást.

6. Az Eszközkiépítési szolgáltatás menüben válassza **a Regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** lapot, és felül válassza az **Egyéni beléptetés hozzáadása** gombot. 

7. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az *Elsődleges tanúsítvány .pem vagy .cer fájlban*válassza *a Fájl kijelölése lehetőséget* az előző lépésekben létrehozott **X509testcertificate.pem** tanúsítványfájl kijelöléséhez.
   - Ha kívánja, megadhatja az alábbi információkat is:
     - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
     - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor. 
     - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Miután elkészült, nyomja meg a **Mentés** gombot. 

     [![Egyéni regisztráció hozzáadása az X.509-es tanúsítványhoz a portálon](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Sikeres beléptetés esetén az X.509-eszköz **riot-device-cert** azonosítóval megjelenik a *Regisztrációs azonosító* oszlopban az *Egyéni beléptetések* lapon. 

## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Az Eszközkiépítési szolgáltatás menüben válassza **az Áttekintés parancsot.** Jegyezze fel az _azonosítóhatókört_ és _a globális szolgáltatásvégpontot._

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

8. A portálon lépjen a kiépítési szolgáltatáshoz csatolt IoT hubhoz, és nyissa meg a **Device Explorer** (Eszközkereső) panelt. Ha sikeresen kiépíti a szimulált X.509-eszközt a hubon, az eszköz azonosítója megjelenik a **Device Explorer** panelen, a hozzá tartozó *ÁLLAPOT* pedig **engedélyezett** lesz. Előfordulhat, hogy meg kell **nyomnia** a frissítés gombot a tetején, ha már kinyitotta a panelt a mintaeszköz-alkalmazás futtatása előtt. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
2. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa meg a **szolgáltatás Regisztrációk kezelése** panelt, majd válassza az Egyéni **regisztrációk** lapot. *REGISTRATION ID* **Delete** 
3. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg az **IoT-eszközök** panelt a hubhoz, jelölje be a rövid útmutatóban regisztrált eszköz *eszközazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szimulált X.509-es eszközt a Windows-gépen, és kiépítette azt az IoT hubba az Azure IoT Hub-eszközkiépítési szolgáltatás használatával a portálon. Az X.509-es eszköz programozott regisztrálásához folytassa az X.509-es eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsútmutató – X.509-es eszközök regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-x509-python.md)
