---
title: 'Szimulált X.509-es eszköz kiépítése az Azure IoT Hubba c használatával #'
description: Rövid útmutató – hozzon létre és hozzon létre egy szimulált X.509-es eszközt c# eszköz SDK-val az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 639ab07113a0e62cac43af5b79f052da1efd93ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976519"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Rövid útmutató: Szimulált X.509-es eszköz létrehozása és kiépítése C# eszköz SDK használatával az IoT Hub-eszközkiépítési szolgáltatáshoz

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ezek a lépések bemutatják, hogyan használhatja az [Azure IoT-minták C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) egy X.509-es eszköz szimulálására egy fejlesztői gépen a Windows operációs rendszer. A minta is csatlakozik a szimulált eszköz egy IoT Hub az eszközkiépítési szolgáltatás használatával.

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

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Önaláírt X.509-eszköztanúsítvány és egyéni regisztrációs bejegyzés létrehozása

Ebben a szakaszban egy önaláírt X.509-tanúsítványt fogunk használni. Fontos szem előtt tartani a következőket:

* Az önaláírt tanúsítványok csak tesztelési célokra alkalmasak, és nem javasolt őket éles környezetben alkalmazni.
* Az önaláírt tanúsítványok alapértelmezett lejárati ideje 1 év.

A [kiépítési eszközügyfél-minta – X.509 tanúsítvány](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) mintakódját fogja használni a szimulált eszköz egyéni beléptetési bejegyzéséhez használandó tanúsítvány létrehozásához.


1. Egy parancssorban módosítsa a könyvtárakat az X.509-eszközkiépítési minta projektkönyvtárára.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. A mintakód úgy lett beállítva, hogy egy jelszóval védett PKCS12 formázott fájlban (certificate.pfx) tárolt X.509-tanúsítványokat használja. Ezenkívül a rövid útmutató későbbi részében egy nyilvános kulcsú tanúsítványfájlra (certificate.cer) is szükség van az egyéni igénylés létrehozásához. Önaláírt tanúsítvány és a hozzátartozó .cer és .pfx fájlok létrehozásához futtassa az alábbi parancsot:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. A szkript kér egy PFX-jelszót. Jegyezze meg ezt a jelszót, mert a minta futtatásakor használnia kell.

    ![ A PFX-jelszó megadása](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Jelentkezzen be az Azure Portalon, válassza a bal oldali menü **Minden erőforrás** gombját, és nyissa meg a létesítési szolgáltatást.

5. Az Eszközkiépítési szolgáltatás menüben válassza **a Regisztrációk kezelése**lehetőséget. Válassza az **Egyéni regisztrációk** lapot, és felül válassza az **Egyéni beléptetés hozzáadása** gombot. 

6. A **Beiktatás hozzáadása** panelen adja meg a következő adatokat:
   - Válassza az **X.509** elemet az identitás igazolási *Mechanizmusaként*.
   - Az *Elsődleges tanúsítvány .pem vagy .cer fájl*ban válassza *a Fájl kijelölése lehetőséget* az előző lépésekben létrehozott **certificate.cer** tanúsítványfájl kijelöléséhez.
   - Az **Eszközazonosító** mezőt hagyja üresen. Az eszköz kiépítésekor a rendszer az eszközazonosítót az X.509-tanúsítványban lévő **iothubx509device1** köznapi névre állítja be. A rendszer ezt a nevet használja az egyéni regisztrációs bejegyzés regisztrációs azonosítójaként s. 
   - Ha kívánja, megadhatja az alábbi információkat is:
       - Válassza ki a kiépítési szolgáltatáshoz kapcsolódó egyik IoT hubot.
       - Frissítse az **Eszköz kezdeti ikerállapotát** az eszköz kívánt kezdeti konfigurációjával.
   - Miután elkészült, nyomja meg a **Mentés** gombot. 

     [![Egyéni regisztráció hozzáadása az X.509-es tanúsítványhoz a portálon](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Sikeres regisztráció esetén az X.509 regisztrációs bejegyzés **iothubx509device1** azonosítóval jelenik meg a *Regisztrációs azonosító* oszlopban az *Egyéni regisztrációk* lapon. 

## <a name="provision-the-simulated-device"></a>A szimulált eszköz kiépítése

1. A **kiépítési** szolgáltatás áttekintése panelen vegye figyelembe az **_id hatókör_** értékét.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Írja be az alábbi parancsot az X.509-eszköz kiépítési mintájának összeállításához és futtatásához. Az `<IDScope>` értéket cserélje le a kiépítési szolgáltatás Azonosító hatóköre értékére. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Amikor a rendszer kéri, adja meg a korábban létrehozott PFX-fájl jelszavát. Figyelje meg az eszköz rendszerindítását szimuláló és az eszközkiépítési szolgáltatáshoz az IoT Hub információk lekérése érdekében kapcsolódó üzeneteket. 

    ![Példa az eszközkimenetre](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Ellenőrizze, hogy a rendszer kiépítette-e az eszközt. A szimulált eszköz sikeres kiépítése a létesítési szolgáltatáshoz kapcsolódó IoT hub, az eszköz azonosítója jelenik meg a hub **IoT-eszközök** panel. 

    ![Az eszköz regisztrálva van az IoT Hubbal](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Ha módosította az *Eszköz kezdeti ikerállapota* alapértelmezett értékét az eszköz beléptetési bejegyzésében, az lekérheti és felhasználhatja a kívánt ikerállapotot a központból. További információ: [Az eszközök ikreinek megértése és használata az IoT Hubban című témakörben talál.](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát, és vizsgálja meg az eszköz ügyfél minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem tervezi a folytatást, az alábbi lépésekkel törölje az összes olyan erőforrást, amelyet ez a rövid útmutató hozott létre.

1. Zárja be az eszközügyfél minta kimeneti ablakát a gépen.
1. Zárja be a TPM szimulátor ablakát a gépen.
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** t, majd válassza ki az Eszközkiépítési szolgáltatást. Az **Áttekintő** panel tetején nyomja le a **Delete** billentyűt az ablaktábla tetején.  
1. Az Azure Portal bal oldali menüjében válassza az **Összes erőforrás** lehetőséget, majd válassza ki az IoT-központot. Az **Áttekintő** panel tetején nyomja le a **Delete** billentyűt az ablaktábla tetején.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy szimulált X.509-es eszközt a Windows-gépen, és kiépítette azt az IoT hubba az Azure IoT Hub-eszközkiépítési szolgáltatás használatával a portálon. Az X.509-es eszköz programozott regisztrálásához folytassa az X.509-es eszközök programozott regisztrációjának rövid útmutatójával. 

> [!div class="nextstepaction"]
> [Azure-gyorsútmutató – X.509-es eszközök regisztrálása az Azure IoT Hub-eszközkiépítési szolgáltatásra](quick-enroll-device-x509-csharp.md)
