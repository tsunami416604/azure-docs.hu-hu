---
title: Rövid útmutató – Szimulált TPM-eszköz kiépítése az Azure IoT Hubba a Python használatával
description: Gyorsútmutató – Szimulált TPM-eszköz létrehozása és kiépítése Java-eszköz SDK-val az IoT Hub-eszközlétesítési szolgáltatás (DPS) használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 67206f36d5c9f08a2110b02f1d3681684cda8a66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605414"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: Szimulált TPM-eszköz létrehozása és kiépítése python-eszköz SDK használatával az IoT Hub-eszközlétesítési szolgáltatáshoz

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ebben a rövid útmutatóban egy szimulált IoT-eszközt hoz létre egy Windows-számítógépen. A szimulált eszköz hardverbiztonsági modulként (HSM) tartalmaz egy TPM-szimulátort. Az eszközminta Python-kódot használja a szimulált eszköz és az IoT hub összekapcsolásához az eszközkiépítési szolgáltatás (DPS) egyéni regisztrációjával.

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

1. Töltse le és telepítse a [CMake buildelési rendszert](https://cmake.org/download/).

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. Nyisson meg egy parancssort vagy a Git Basht. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Hozzon létre egy mappát a GitHub-adattár helyi másolatában a CMake buildelési folyamathoz. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. A kódminta Windows TPM szimulátort használ. Futtassa az alábbi parancsot a SAS jogkivonat-hitelesítés engedélyezéséhez. Ez egy Visual Studio megoldást is létrehoz a szimulált eszközhöz.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. Egy külön parancssorban keresse meg a TPM-szimulátor mappáját, és futtassa a [TPM-szimulátort](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) úgy, hogy az legyen a szimulált eszköz [HSM-je.](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) Kattintson a **Hozzáférés engedélyezése** elemre. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot; a rövid útmutató végéig meg kell tartania ezt a szimulátort. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![TPM-szimulátor](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk:](concepts-service.md#individual-enrollment)Egyetlen eszköz regisztrálására használható.

Ez a cikk bemutatja az egyes regisztrációk.

1. Nyissa meg a *cmake* mappában létrehozott `azure_iot_sdks.sln` nevű megoldást, és építse fel azt a Visual Studióban.

1. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. Futtassa a megoldást. A kimeneti ablakban megjelenik az **_ellenőrző kulcs_** és az eszközregisztrációhoz szükséges **_regisztrációs azonosító._** Jegyezze fel ezeket az értékeket. 

    ![A TPM beállítása](./media/python-quick-create-simulated-device/tpm-setup.png)

1. Jelentkezzen be az Azure Portalon, válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatást.

1. Az Eszközkiépítési szolgáltatás menüben válassza **a Regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** lapot, és felül válassza az **Egyéni beléptetés hozzáadása** gombot. 

1. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Adja meg a TPM-eszköz *regisztrációs azonosítóját* és *ellenőrzőkulcsát* a korábban megadott értékekből.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Adjon meg egy egyedi *eszközazonosítót*. Ne használjon bizalmas adatokat az eszköz elnevezésekor. Ha úgy dönt, hogy nem ad meg egyet, a regisztrációs azonosítót fogja használni az eszköz azonosítására.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Miután elkészült, nyomja meg a **Mentés** gombot. 

     ![Írja be az eszköz beléptetési információit a portál panelén](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni beléptetések* lapon lévő listában. 


## <a name="simulate-the-device"></a>Az eszköz szimulálása

1. Töltse le és telepítse a [Python 2.x-es vagy 3.x-es verzióját](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változókhoz.
    - Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) van szükség a Python natív DLL-jei használatához.

1. A Python-csomagok létrehozásához kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md).

   > [!NOTE]
   > A `build_client.cmd` futtatása esetén mindenképp használja a `--use-tpm-simulator` jelzőt.
   > 
   > [!NOTE]
   > A `pip` használata esetén mindenképp telepítse az `azure-iot-provisioning-device-client` csomagot is. Vegye figyelembe, hogy a kiadott PIP-csomagok az igazi TPM-et használják, nem a szimulátort. A szimulátor használatához a fordítást a `--use-tpm-simulator` jelzőt használva a forrásból kell elvégeznie.

1. Lépjen a mintákat tartalmazó mappára.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. A Python IDE használatával módosítsa a **provisioning\_device\_client\_sample.py** nevű Python-szkriptet. Módosítsa a *\_GLOBAL\_PROV URI* és *ID\_SCOPE* változókat a korábban említett értékekre. Arról is győződjön meg, hogy a *SECURITY\_DEVICE\_TYPE* változó `ProvisioningSecurityDeviceType.TPM` értékre van állítva.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Szolgáltatás adatai](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Futtassa a mintát. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. 

    ![Sikeres regisztráció](./media/python-quick-create-simulated-device/registration-success.png)

1. A szimulált eszköz sikeres kiépítése a létesítési szolgáltatáshoz kapcsolódó IoT hub, az eszköz azonosítója jelenik meg a hub **IoT-eszközök** panel.

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/python-quick-create-simulated-device/hubregistration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Az eszközök ikreinek megértése és használata az IoT Hubban című témakörben talál.](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Nyissa meg a **szolgáltatás Regisztrációk kezelése** panelt, majd válassza az Egyéni **regisztrációk** lapot. *REGISTRATION ID* **Delete** 
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Nyissa meg az **IoT-eszközök** panelt a hubhoz, jelölje be a rövid útmutatóban regisztrált eszköz *eszközazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy TPM szimulált eszközt a gépen, és kiépítette azt az IoT Hub-eszközkiépítési szolgáltatás használatával az IoT Hub-eszközkiépítési szolgáltatás használatával. A TPM-eszköz programozott regisztrálásáról további információ a TPM-eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsindítás – TPM-eszköz regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-tpm-python.md)
