---
title: 'Rövid útmutató – Szimulált TPM-eszköz kiépítése az Azure IoT Hubba c használatával #'
description: Rövid útmutató – hozzon létre és hozzon létre egy szimulált TPM-eszközt c# eszköz SDK használatával az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 82bd284ede23e8880f79c614f4a6e2f588a4293c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976995"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: Szimulált TPM-eszköz létrehozása és kiépítése C# eszköz SDK használatával az IoT Hub-eszközlétesítési szolgáltatáshoz

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Ezek a lépések bemutatják, hogyan használhatja az [Azure IoT-minták C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) egy TPM-eszköz szimulálására a Windows operációs rendszer fejlesztői gépen. A minta is csatlakozik a szimulált eszköz egy IoT Hub az eszközkiépítési szolgáltatás használatával. 

A mintakód a Windows TPM-szimulátort használja az eszköz [Hardveres biztonsági moduljaként (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). 

Ha nem ismeri az automatikus kiépítés folyamatát, mindenképpen tekintse át az [automatikus kiépítés fogalmait](concepts-auto-provisioning.md)is. Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:

- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a [.NET Core 2.1 SDK vagy újabb](https://www.microsoft.com/net/download/windows) rendszer telepítve van a számítógépen. 

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. Nyisson meg egy parancssort vagy a Git Basht. Az Azure IoT-minták klónozása C# GitHub-tártárhoz:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>A szimulált eszköz kiépítése

1. Jelentkezzen be az Azure portálra. Válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg az Eszközkiépítési szolgáltatást. Az **Áttekintés** panelen vegye figyelembe az **_Azonosító hatókör értékét._**

    ![Másolja ki a kiépítési szolgáltatás azonosítójának hatókörét a portál paneljéről](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. Egy parancssorban módosítsa a könyvtárakat a TPM-eszközkiépítési minta projektkönyvtárára.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Írja be az alábbi parancsot, és futtassa a TPM-eszközkiépítési mintát. Az `<IDScope>` értéket cserélje le a kiépítési szolgáltatás Azonosító hatóköre értékére. 

    ```cmd
    dotnet run <IDScope>
    ```

    Ez a parancs egy külön parancssorban indítja el a TPM chip szimulátort. A Windows rendszerben előfordulhat, hogy egy Windows biztonsági riasztás jelenik meg, amely megkérdezi, hogy engedélyezi-e a Simulator.exe számára a nyilvános hálózatokon való kommunikációt. Ennek a mintának az alkalmazásában visszavonhatja a kérelmet.

1. Az eredeti parancsablakban megjelenik az **_ellenőrző kulcs_**, a **_regisztrációs azonosító_** és az eszközregisztrációhoz szükséges javasolt **_eszközazonosító._** Vegye figyelembe ezeket az értékeket. Ezeket az értékeket fogja használni, hogy hozzon létre egy egyéni regisztráció az eszközkiépítési szolgáltatás példányában. 
   > [!NOTE]
   > Ne keverje össze a parancskimenetet tartalmazó ablakot a TPM-szimulátor kimenetét tartalmazó ablakkal. Előfordulhat, hogy az előtérbe hozáshoz ki kell jelölnie az eredeti parancsablakot.

    ![A parancsablak kimenete](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. Az Azure Portalon az Eszközkiépítési szolgáltatás menüben válassza **a Regisztrációk kezelése lehetőséget.** Válassza az **Egyéni regisztrációk** lapot, és a lista tetején válassza az **Egyéni beléptetés hozzáadása** gombot. 

1. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat:
   - Válassza a **TPM** elemet az identitás igazolási *Mechanizmusaként*.
   - Adja meg a TPM-eszköz *regisztrációs azonosítóját* és *ellenőrzőkulcsát* a korábban megadott értékekből.
   - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Adjon meg egy egyedi *eszközazonosítót* (használhatja a javasoltat, vagy megadhatja a sajátját). Ne használjon bizalmas adatokat az eszköz elnevezésekor. Ha úgy dönt, hogy nem ad meg egyet, a regisztrációs azonosítót fogja használni az eszköz azonosítására.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Miután elkészült, nyomja meg a **Mentés** gombot. 

     ![Írja be az eszköz beléptetési információit a portál panelén](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Sikeres beléptetés esetén az eszköz *Regisztrációs azonosítója* megjelenik az *Egyéni beléptetések* lapon lévő listában. 

1. Nyomja le az *Enter billentyűt* a parancsablakban (az **_ellenőrző kulcsot_**, **_regisztrációs azonosítót_** és javasolt **_eszközazonosítót)_** a szimulált eszköz regisztrálásához. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. 

1. Ellenőrizze, hogy a rendszer kiépítette-e az eszközt. A szimulált eszköz sikeres kiépítése a létesítési szolgáltatáshoz kapcsolódó IoT hub, az eszköz azonosítója jelenik meg a hub **IoT-eszközök** panel. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Az **Áttekintő** panel tetején nyomja le a **Delete** billentyűt az ablaktábla tetején.  
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Az **Áttekintő** panel tetején nyomja le a **Delete** billentyűt az ablaktábla tetején.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy TPM szimulált eszközt a gépen, és kiépítette azt az IoT Hub-eszközkiépítési szolgáltatás használatával az IoT Hub-eszközkiépítési szolgáltatás használatával. A TPM-eszköz programozott regisztrálásáról további információ a TPM-eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsindítás – TPM-eszköz regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-tpm-csharp.md)
