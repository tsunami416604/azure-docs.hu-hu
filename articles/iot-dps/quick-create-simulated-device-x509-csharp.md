---
title: 'Szimulált X. 509 eszköz kiépítése az Azure-IoT Hub C használatával #'
description: Rövid útmutató – szimulált X. 509 eszköz létrehozása és kiépítése az Azure-hoz készült C# eszközoldali SDK-IoT Hub Device Provisioning Service (DPS). Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 639ab07113a0e62cac43af5b79f052da1efd93ab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74976519"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: szimulált X. 509 eszköz létrehozása és kiépítése a IoT Hub Device Provisioning Service C# eszközoldali SDK-val

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések bemutatják, hogyan használhatja a C#-hoz készült [Azure IoT-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp) egy X. 509 eszköz szimulálására egy Windows operációs rendszert futtató fejlesztői gépen. A minta a szimulált eszközt is összekapcsolja egy IoT Hub a Device kiépítési szolgáltatás használatával.

Ha nem ismeri az automatikus kiépítés folyamatát, tekintse át az [automatikus kiépítési fogalmakat](concepts-auto-provisioning.md)is. Emellett a folytatás előtt végezze el az [IoT Hub eszközkiépítési szolgáltatás beállítása az Azure Portallal](./quick-setup-auto-provision.md) című cikk lépéseit. 

Az Azure IoT Device Provisioning Service kétféle típusú regisztrációt támogat:
- [Regisztrációs csoportok](concepts-service.md#enrollment-group): Több kapcsolódó eszköz regisztrálásához.
- [Egyéni regisztrációk](concepts-service.md#individual-enrollment): Egyetlen eszköz regisztrálásához.

Ez a cikk az egyéni regisztrációkat ismerteti.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése 

1. Győződjön meg arról, hogy a [.net Core 2,1 SDK vagy újabb verziója](https://www.microsoft.com/net/download/windows) telepítve van a gépen. 

1. Győződjön meg arról, hogy a(z) `git` telepítve van a gépen, és a parancsablakból elérhető környezeti változókhoz van adva. A [Software Freedom Conservancy's Git ügyfél eszközeiben](https://git-scm.com/download/) találja a telepíteni kívánt `git` eszközök legújabb verzióját, amely tartalmazza a **Git Bash** eszközt, azt a parancssori alkalmazást, amellyel kommunikálhat a helyi Git-adattárral. 

1. Nyisson meg egy parancssort vagy a Git Basht. Az Azure IoT-minták klónozása a C# GitHub-tárházban:
    
    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fogunk használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

A [kiépítési eszköz Client Sample-X. 509 tanúsítványának](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) mintakód használatával hozza létre a szimulált eszköz egyéni beléptetési bejegyzésével használandó tanúsítványt.


1. Egy parancssorban módosítsa a könyvtárakat az X.509-eszközkiépítési minta projektkönyvtárára.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. A mintakód úgy lett beállítva, hogy egy jelszóval védett PKCS12 formázott fájlban (certificate.pfx) tárolt X.509-tanúsítványokat használja. Emellett egy nyilvános kulcsú tanúsítványfájl (Certificate. cer) szükséges ahhoz, hogy a rövid útmutatóban később egyéni regisztrációt hozzon létre. Önaláírt tanúsítvány és a hozzátartozó .cer és .pfx fájlok létrehozásához futtassa az alábbi parancsot:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. A szkript kér egy PFX-jelszót. Jegyezze meg ezt a jelszót, mert a minta futtatásakor használnia kell.

    ![ A PFX-jelszó megadása](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Jelentkezzen be a Azure Portalba, majd a bal oldali menüben kattintson a **minden erőforrás** gombra, és nyissa meg a kiépítési szolgáltatást.

5. Az eszközök kiépítési szolgáltatásának menüjében válassza a **regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** fület, és válassza az **Egyéni regisztráció hozzáadása** gombot a felső részen. 

6. A **beléptetés hozzáadása** panelen adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az *elsődleges tanúsítvány. PEM vagy. cer fájlban*válassza a *fájl kiválasztása* lehetőséget, majd válassza ki az előző lépésekben létrehozott tanúsítványfájl- **tanúsítványt. cer** fájlt.
   - Az **Eszközazonosító** mezőt hagyja üresen. Az eszköz kiépítésekor a rendszer az eszközazonosítót az X.509-tanúsítványban lévő **iothubx509device1** köznapi névre állítja be. A rendszer ezt a nevet használja az egyéni regisztrációs bejegyzés regisztrációs azonosítójaként s. 
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Ha elkészült, kattintson a **Save (Mentés** ) gombra. 

     [![Egyéni regisztráció hozzáadása X. 509 tanúsítványhoz a portálon](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Sikeres regisztráció esetén az X.509 regisztrációs bejegyzés **iothubx509device1** azonosítóval jelenik meg a *Regisztrációs azonosító* oszlopban az *Egyéni regisztrációk* lapon. 

## <a name="provision-the-simulated-device"></a>A szimulált eszköz kiépítése

1. A kiépítési szolgáltatás **Áttekintés** paneljén jegyezze fel az **_azonosító hatókör_** értékét.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Írja be az alábbi parancsot az X.509-eszköz kiépítési mintájának összeállításához és futtatásához. Az `<IDScope>` értéket cserélje le a kiépítési szolgáltatás Azonosító hatóköre értékére. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Amikor a rendszer kéri, adja meg a korábban létrehozott PFX-fájl jelszavát. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. 

    ![Példa az eszközkimenetre](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Ellenőrizze, hogy a rendszer kiépítette-e az eszközt. Ha sikeresen kiépíti a szimulált eszközt a kiépítési szolgáltatáshoz kapcsolódó IoT hub-ra, az eszköz azonosítója megjelenik a hub **IoT-eszközök** paneljén. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [az eszközök ikrek megismerése és használata IoT hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és megkeresi az eszköz ügyféloldali mintáját, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az eszköz kiépítési szolgáltatását. Az **Áttekintés** panel felső részén kattintson a **Törlés** gombra a ablaktábla tetején.  
1. A Azure Portal bal oldali menüjében válassza a **minden erőforrás** lehetőséget, majd válassza ki az IoT hubot. Az **Áttekintés** panel felső részén kattintson a **Törlés** gombra a ablaktábla tetején.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a szimulált X. 509 eszközt a Windows rendszerű gépen, és kiépíti azt az IoT hubhoz a portálon elérhető Azure IoT Hub Device Provisioning Service használatával. Az X. 509 eszköz programozott módon történő regisztrálásának megismeréséhez folytassa az X. 509 eszközök programozott regisztrálására szolgáló rövid útmutatóval. 

> [!div class="nextstepaction"]
> [Azure rövid útmutató – X. 509 eszközök regisztrálása az Azure-ba IoT Hub Device Provisioning Service](quick-enroll-device-x509-csharp.md)
