---
title: Szimulált TPM-eszköz kiépítése Azure IoT Hubra C használatával | Microsoft Docs
description: Azure rövid útmutató – Szimulált TPM-eszköz létrehozása és kiépítése az Azure IoT Hub Device Provisioning Service-hez készült C eszközoldali SDK-val
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/16/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1d642bec50d8de2f1f9a44c33c99ba8edda040e7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Szimulált TPM-eszköz létrehozása és kiépítése az IoT Hub Device Provisioning Service-hez készült C eszközoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ezek a lépések bemutatják, hogyan hozhat létre szimulált eszközt egy Windows operációs rendszert futtató fejlesztői gépen, hogyan futtathatja a Windows TPM szimulátort az eszköz [hardveres biztonsági moduljaként (HSM-jeként)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/), és hogyan használhatja a kódmintát, hogy ezt a szimulált eszközt összekösse az eszközkiépítési szolgáltatással és az IoT Hubbal. 

Amennyiben nem ismeri az automatikus kiépítés folyamatát, olvassa el [az automatikus kiépítés alapfogalmait](concepts-auto-provisioning.md) ismertető cikket is. A folytatás előtt mindenképpen végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) szakasz lépéseit. 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a Visual Studio 2015 vagy a [Visual Studio 2017](https://www.visualstudio.com/vs/) telepítve van a gépen. A Visual Studio telepítésekor engedélyezni kell az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladatot.

2. Töltse le és telepítse a [CMake buildelési rendszert](https://cmake.org/download/). Fontos, hogy a Visual Studio az „Asztali fejlesztés C++ használatával” számítási feladattal együtt telepítve legyen a gépen a `cmake` telepítése **előtt**.

3. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

4. Nyisson meg egy parancssort vagy a Git Basht. Klónozza a GitHub-adattárat az eszközszimuláció kódmintájához:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. Hozzon létre egy mappát a GitHub-adattár helyi másolatában a CMake buildelési folyamathoz. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. A kódminta Windows TPM-szimulátort használ az SAS-azonosítón alapuló hitelesítéssel történő igazoláshoz. Futtassa az alábbi parancsot, hogy egy olyan SDK-verziót hozzon létre, amely igazodik az Ön által használt fejlesztésiügyfél-platformhoz és [igazolási eljáráshoz](concepts-security.md#attestation-mechanism) (TPM-szimulátor). Ez egy Visual Studio megoldást is létrehoz a szimulált eszközhöz.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

7. Egy különálló parancssorban keresse meg a GitHub gyökérmappáját, és futtassa a [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) szimulátort. A 2321-es és a 2322-es portokon lévő szoftvercsatornán keresztül figyel. Ne zárja be ezt a parancsablakot; a rövid útmutató végéig futnia kell ennek a szimulátornak. 

   A *cmake* mappában futtassa a következő parancsokat:

    ```cmd/sh
    cd..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

<a id="simulatetpm"></a>

## <a name="simulate-tpm-device"></a>TPM-eszköz szimulálása

1. Nyissa meg a *cmake* mappában létrehozott `azure_iot_sdks.sln` nevű megoldást, és építse fel azt a Visual Studióban.

2. A Visual Studio *Solution Explorer* (Megoldáskezelő) panelén lépjen a **Provision\_Tools** mappára. Kattintson a jobb gombbal a **tpm_device_provision** projektre, és válassza a **Set as Startup Project** (Beállítás kezdőprojektként) lehetőséget. 

3. Futtassa a megoldást. A kimeneti ablak megjeleníti az eszközök regisztrációjához szükséges **_regisztrációs azonosítót_** és **_ellenőrzőkulcsot_**. Jegyezze fel ezeket az értékeket. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Eszközregisztrációs bejegyzés létrehozása a portálon

1. Jelentkezzen be az Azure Portalra, a bal oldali menüben kattintson az **Összes erőforrás** gombra, és nyissa meg az eszközkiépítési szolgáltatást.

2. Az eszközkiépítési szolgáltatás összefoglalás panelén válassza a **Beléptetések kezelése** lehetőséget. Válassza az **Egyéni beléptetések** fület, és kattintson a felül lévő **Hozzáadás** gombra. 

3. A **Beléptetési listabejegyzés hozzáadása** területen adja meg a következő információkat:
    - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
    - Írja be a TPM-eszköz *Regisztrációs azonosítóját* és *Ellenőrzőkulcsát*.
    - Ha kívánja, megadhatja az alábbi információkat is:
        - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
        - Adjon meg egy egyedi eszközazonosítót. Ne használjon bizalmas adatokat az eszköz elnevezésekor.
        - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
    - Ha végzett, kattintson a **Mentés** gombra. 

    ![Írja be az eszköz beléptetési információit a portál panelén](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni beléptetések* lapon lévő listában. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Első rendszerindítás szimulálása az eszközhöz

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** panelét, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![DPS végpontinformációk kinyerése a portál paneljéről](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. A számítógépén a Visual Studio *Solution Explorer* (Megoldáskezelő) panelén lépjen a **Provision\_Samples** mappára. Válassza ki a **prov\_dev\_client\_sample** nevű mintaprojektet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű fájlt.

3. Rendelje az _ID Scope_ (Azonosító hatóköre) értéket a(z) `id_scope` változóhoz. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. Ugyanebben a fájlban, a **main()** függvényben ellenőrizze, hogy a **SECURE_DEVICE_TYPE** értéke TPM-e.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    ```

   Tegye megjegyzésbe vagy törölje az alapértelmezés szerint itt szereplő `hsm_type = SECURE_DEVICE_TYPE_X509;` utasítást. 

5. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. Futtassa a megoldást. 

6. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. Ha sikeresen kiépíti a szimulált eszközt a kiépítési szolgáltatáshoz csatolt IoT Hubon, az eszköz azonosítója megjelenik a hub **IoT-eszközök** panelén. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device/hub-registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az eszközügyfél minta használatát és megismerését, akkor ne törölje a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutatóhoz létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az eszközkiépítési szolgáltatást. Nyissa meg a szolgáltatás **Regisztrációk kezelése** paneljét, majd kattintson az **Egyéni regisztrációk** lapra. Válassza ki a rövid útmutatóban regisztrált eszköz *REGISZTRÁCIÓS AZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra. 
1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Nyissa meg a hub **IoT-eszközök** paneljét, válassza ki a rövid útmutatóban regisztrált eszköz *ESZKÖZAZONOSÍTÓJÁT*, majd kattintson a felül található **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy szimulált TPM-eszközt hozott létre a gépen, majd kiépítette azt az IoT Hubon az IoT Hub Device Provisioning Service használatával. Ha szeretné megismerni a TPM-eszköz programozott regisztrációjának folyamatát, lépjen tovább a TPM-eszközök programozott regisztrációjának rövid útmutatójára. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – TPM-eszköz regisztrációja az Azure IoT Hub Device Provisioning Service-be](quick-enroll-device-tpm-java.md)

