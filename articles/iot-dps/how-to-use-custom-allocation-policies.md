---
title: Egyéni kiosztási szabályzatok használata az Azure IoT Hub Device Provisioning Service használatával | Microsoft Docs
description: Egyéni kiosztási szabályzatok használata az Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 1e672e7bd43dcd05d048d22205939749c1d96579
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348064"
---
# <a name="how-to-use-custom-allocation-policies"></a>Egyéni foglalási szabályzatok használata


Az egyéni foglalási szabályzatok részletesebben szabályozzák, hogy az eszközök hogyan legyenek hozzárendelve az IoT hubhoz. Ezt egy [Azure-függvény](../azure-functions/functions-overview.md) egyéni kódjának használatával végezheti el az eszközök IoT-hubhoz való hozzárendeléséhez. Az eszköz kiépítési szolgáltatása meghívja az Azure-függvény kódját, amely az eszközre és a regisztrációra vonatkozó összes releváns információt megadja. A rendszer végrehajtja a függvény kódját, és az eszköz kiépítési IoT hub-információit adja vissza.

Egyéni kiosztási szabályzatok használatával saját kiosztási szabályzatokat határozhat meg, ha az eszköz kiépítési szolgáltatása által biztosított szabályzatok nem felelnek meg a forgatókönyv követelményeinek.

Előfordulhat például, hogy meg szeretné vizsgálni az eszköz által a kiépítés során használt tanúsítványt, és hozzárendeli az eszközt egy IoT-hubhoz a Certificate tulajdonság alapján. Előfordulhat, hogy az eszközök adatbázisában tárolt adatokkal rendelkezik, és le kell kérdezni az adatbázist annak meghatározásához, hogy melyik IoT hubot kell hozzárendelni az eszközhöz.


Ez a cikk egy egyéni kiosztási szabályzatot mutat be egy írt C#Azure-függvény használatával. Két új IoT hub jön létre, amely egy *contoso kenyérpirítói osztályt* és egy *contoso Heat pumps*-részleget jelöl. Az üzembe helyezést kérő eszközökön regisztrálni kell egy regisztrációs azonosítót a következő utótagok egyikével, hogy el lehessen fogadni az üzembe helyezéshez:

- **-contoso-tstrsd-007**: Contoso-kenyérpirítók osztása
- **-contoso-hpsd-088**: Contoso Heat szivattyúk divízió

Az eszközök a regisztrációs AZONOSÍTÓn alapuló kötelező utótagok egyikén lesznek kiépítve. Ezek az eszközök az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-ban foglalt kiépítési minta használatával lesznek szimulálva. 

A cikkben a következő lépéseket kell végrehajtania:

* Hozzon létre két contoso Division IoT-hubokat az Azure CLI használatával (**contoso kenyérpirító részleg** és **contoso Heat pumps Division**)
* Új csoportos regisztráció létrehozása Azure-függvény használatával az egyéni kiosztási szabályzathoz
* Hozzon létre két eszköz-szimulációs eszköz kulcsait.
* A fejlesztési környezet beállítása az Azure IoT C SDK-hoz
* Szimulálja az eszközöket, hogy azok az egyéni foglalási szabályzathoz tartozó mintakód alapján legyenek kiépítve.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [beállított IoT hub Device Provisioning Service befejezése a Azure Portal](./quick-setup-auto-provision.md) rövid útmutatóval.
* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015-as vagy újabb verziójának használata az ["asztali fejlesztés C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) a munkaterheléssel" beállítással.
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Két IoT hub létrehozása

Ebben a szakaszban a Azure Cloud Shell fogja használni két új IoT-központ létrehozásához, amely a **contoso kenyérpirítói osztályt** és a **contoso Heat szivattyúk osztályt**jelképezi.

1. A Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    A következő példában létrehozunk egy *contoso-US-Resource-Group* nevű erőforráscsoportot a *eastus* régióban. Ezt a csoportot az ebben a cikkben létrehozott összes erőforráshoz ajánlott használni. Ez a megközelítés megkönnyíti a tisztítást a befejezést követően.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. A Azure Cloud Shell használatával hozza létre a **contoso kenyérpirítók Division** IoT hubot az az [IoT hub Create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Az IoT hub hozzá lesz adva a *contoso-US-Resource-Group*-hoz.

    Az alábbi példa egy *contoso-kenyérpirítós-hub-1098* nevű IoT-hubot hoz létre a *eastus* helyen. A saját egyedi hub-nevét kell használnia. Hozzon létre saját utótagot a hub neveként a **1098**helyett. Az egyéni kiosztási szabályzathoz tartozó példa `-toasters-` programkódjának a hub nevében kell lennie.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs végrehajtása több percet is igénybe vehet.

3. A Azure Cloud Shell használatával hozza létre a **contoso Heat szivattyúk Division** IoT hubot az az [IoT hub Create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Ez az IoT hub a *contoso-US-Resource-Group*-hoz is hozzá lesz adva.

    A következő példa egy *contoso-heatpumps-hub-1098* nevű IoT-hubot hoz létre a *eastus* helyen. A saját egyedi hub-nevét kell használnia. Hozzon létre saját utótagot a hub neveként a **1098**helyett. Az egyéni kiosztási szabályzathoz tartozó példa `-heatpumps-` programkódjának a hub nevében kell lennie.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    Ez a parancs néhány percet is igénybe vehet.




## <a name="create-the-enrollment"></a>A regisztráció létrehozása

Ebben a szakaszban egy új beléptetési csoportot fog létrehozni, amely az egyéni kiosztási szabályzatot használja. Az egyszerűség kedvéért ez a cikk [szimmetrikus kulcsú tanúsítványokat](concepts-symmetric-key-attestation.md) használ a beléptetéshez. A biztonságosabb megoldás érdekében érdemes lehet az [X. 509 tanúsítvány](concepts-security.md#x509-certificates) -igazolást használni egy megbízhatósági lánc használatával.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg az eszköz kiépítési szolgáltatásának példányát.

2. Válassza a **regisztrációk kezelése** fület, majd kattintson a **regisztrációs csoport hozzáadása** gombra az oldal tetején. 

3. A **regisztrációs csoport hozzáadása**párbeszédpanelen adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

    **Csoport neve**: Adja meg a **contoso-Custom-lefoglalt-Devices**értéket.

    **Igazolás típusa**: Válassza a **szimmetrikus kulcs**lehetőséget.

    **Kulcsok automatikus generálása**: Ezt a jelölőnégyzetet már be kell jelölni.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a hubokhoz**: Válassza **az egyéni (Azure-függvény használata)** lehetőséget.

    ![Egyéni foglalási beléptetési csoport hozzáadása a szimmetrikus kulcs igazolásához](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. A **beléptetési csoport hozzáadása**párbeszédpanelen kattintson az **új IoT hub csatolása** lehetőségre az új kiosztott IoT-hubok összekapcsolásához. 

    Ezt a lépést mindkét kiosztott IoT-hubhoz végre kell hajtania.

    **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben létrehozta a kiosztott IoT hubokat.

    **IoT hub**: Válassza ki a létrehozott részlegi hubok egyikét.

    **Hozzáférési szabályzat**: Válassza a **iothubowner**lehetőséget.

    ![A IoT-hubok összekapcsolása a létesítési szolgáltatással](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. A **regisztrációs csoport hozzáadásakor**, ha a IoT hubok is össze lettek kapcsolva, ki kell választania azokat IoT hub csoportként a beléptetési csoport számára az alábbi ábrán látható módon:

    ![Az üzembe helyezési központ csoportjának létrehozása a regisztrációhoz](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. A **regisztrációs csoport hozzáadása**lapon görgessen le az **Azure-függvény kiválasztása** szakaszhoz, és kattintson az **új Function-alkalmazás létrehozása**lehetőségre.

7. A megnyíló **függvényalkalmazás** létrehozás oldalon adja meg az új függvény következő beállításait, majd kattintson a **Létrehozás**gombra:

    **Alkalmazás neve**: Adjon meg egy egyedi Function app-nevet. a **contoso-Function-app-1098** példaként jelenik meg.

    **Erőforráscsoport**: Válassza a **meglévő használata** és a **contoso-US-Resource-Group** lehetőséget, hogy megőrizze az ebben a cikkben létrehozott összes erőforrást.

    **Application Insights**: Ehhez a gyakorlathoz ki lehet kapcsolni ezt a feladatot.

    ![A függvényalkalmazás létrehozása](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. A **regisztrációs csoport hozzáadása** oldalon ellenőrizze, hogy az új Function alkalmazás van-e kiválasztva. Előfordulhat, hogy újra ki kell választania az előfizetést a Function App-lista frissítéséhez.

    Miután kiválasztotta az új Function alkalmazást, kattintson az **új függvény létrehozása**lehetőségre.

    ![A függvényalkalmazás létrehozása](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    Megnyílik az új Function alkalmazás.

9. A Function alkalmazásban kattintson az új függvény létrehozásához

    ![A függvényalkalmazás létrehozása](./media/how-to-use-custom-allocation-policies/new-function.png)

    Az új függvény esetében az alapértelmezett beállítások használatával hozzon létre egy új **webhook + API** -t a **csharp** nyelv használatával. Kattintson **a függvény létrehozása**elemre.

    Ez létrehoz egy C# **HttpTriggerCSharp1**nevű új függvényt.

10. Cserélje le az új C# függvény kódját a következő kódra, majd kattintson a **Save (Mentés**) gombra:    

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. Térjen vissza a **regisztrációs csoport hozzáadása** lapra, és ellenőrizze, hogy az új függvény van-e kiválasztva. Előfordulhat, hogy újra ki kell választania a Function alkalmazást a függvények listájának frissítéséhez.

    Miután kiválasztotta az új függvényt, kattintson a **Save (Mentés** ) gombra a beléptetési csoport mentéséhez.

    ![Végül mentse a beléptetési csoportot](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Miután mentette a beléptetést, nyissa meg újra, és jegyezze fel az **elsődleges kulcsot**. Először mentenie kell a beléptetést, hogy a kulcsok létrejöttek legyenek. A rendszer ezt a kulcsot használja a szimulált eszközökhöz tartozó egyedi eszközök kulcsának létrehozásához.


## <a name="derive-unique-device-keys"></a>Egyedi eszköz kulcsának származtatása

Ebben a szakaszban két egyedi eszköz kulcsát fogja létrehozni. Egy szimulált kenyérpirító eszközhöz egy kulcs lesz használatban. A rendszer egy szimulált hőszivattyús eszközhöz használja a másik kulcsot.

Az eszköz kulcsának létrehozásához a korábban feljegyzett **elsődleges kulcsot** kell használnia, hogy kiszámítsa az eszköz regisztrációs azonosítójának [HMAC a sha256](https://wikipedia.org/wiki/HMAC) , és az eredményt Base64 formátumra alakítsa át. A származtatott eszközök regisztrációs csoportokkal való létrehozásával kapcsolatos további információkért tekintse meg a [szimmetrikus kulcs igazolásának](concepts-symmetric-key-attestation.md)csoportos regisztrációk című szakaszát.

A jelen cikkben szereplő példához használja a következő két eszköz regisztrációs azonosítóját, és számítsa ki mindkét eszköz kulcsát. Mindkét regisztrációs azonosító érvényes utótaggal működik együtt az egyéni foglalási szabályzathoz tartozó mintakód használatával:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Linux-munkaállomások

Ha Linux-munkaállomást használ, az OpenSSL használatával hozhatja elő a származtatott eszköz kulcsait az alábbi példában látható módon.

1. Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

Ha Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja elő a származtatott eszköz kulcsát az alábbi példában látható módon.

1. Cserélje le a **kulcs** értékét a korábban feljegyzett **elsődleges kulcsra** .

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


A szimulált eszközök a származtatott eszközök kulcsait az egyes regisztrációs AZONOSÍTÓkkal fogják használni a szimmetrikus kulcs igazolásának elvégzéséhez.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészítjük az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) build készítésére szolgáló fejlesztőkörnyezetet. Az SDK tartalmazza a szimulált eszközhöz tartozó mintakód kódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

Ez a szakasz a Windows-alapú munkaállomás irányába mutat. Linux-példaként tekintse meg a virtuális gépek beállítása a [bérlős](how-to-provision-multitenant.md)való kiépítésével foglalkozó témakört.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build](https://cmake.org/download/)-szolgáltatását.

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az Azure IoT C SDK GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Ez a művelet várhatóan több percig is eltarthat.


3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

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




## <a name="simulate-the-devices"></a>Az eszközök szimulálása

Ebben a szakaszban a korábban beállított Azure IoT C SDK-ban található **prov\_dev\_Client\_minta** nevű kiépítési mintát fogja frissíteni. 

Ez a mintakód szimulál egy eszköz rendszerindítási sorozatot, amely elküldi a kiépítési kérést az eszköz kiépítési szolgáltatásának példányára. A rendszerindítási folyamat azt eredményezi, hogy a kenyérpirító eszköz fel lesz ismerve, és hozzá lesz rendelve az IoT hub-hoz az egyéni kiosztási házirend használatával.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a **azure_iot_sdks. SLN** megoldást, amelyet korábban a CMAK futtatása hozott létre. A megoldásfájlnak a következő helyen kell lennie:

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

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 


#### <a name="simulate-the-contoso-toaster-device"></a>A contoso kenyérpirító eszközének szimulálása

1. A kenyérpirító eszköz szimulálásához keresse meg a `prov_dev_set_symmetric_key_info()` " **prov\_dev\_Client\_sample. c** " hívását, amely kommentálva van.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Adja meg a függvény hívását, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket is) a kenyérpirító regisztrációs azonosítójával és a korábban létrehozott származtatott eszköz kulccsal. A **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** alább látható kulcs értéke csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    A következő kimenet egy példa a szimulált kenyérpirító eszköz sikeres rendszerindítására és a kiépítési szolgáltatási példányhoz való csatlakozásra, hogy az egyéni foglalási szabályzattal legyen hozzárendelve a kenyérpirítók IoT hubhoz:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>A contoso hőszivattyú eszköz szimulálása

1. A hőszivattyús eszköz szimulálásához frissítse `prov_dev_set_symmetric_key_info()` a **prov\_dev\_Client\_sample. c** -ben megjelenő hívást, és a hőszivattyú regisztrációs azonosítóját és a korábban létrehozott származtatott eszköz kulcsát. A **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** alább látható kulcs értéke szintén csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    A következő kimenet egy példa a szimulált hőszivattyús eszköz sikeres rendszerindítására és a kiépítési szolgáltatási példányhoz való csatlakozásra, amelyet a contoso Heat pumps IoT hub-hoz rendel hozzá az egyéni kiosztási szabályzat szerint:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Egyéni foglalási házirendek hibaelhárítása

Az alábbi táblázat a várt forgatókönyveket és az esetlegesen felmerülő hibakódokat mutatja. Ez a táblázat segítséget nyújt az egyéni kiosztási házirendekkel kapcsolatos hibák elhárításához a Azure Functions.


| Forgatókönyv | Regisztrációs eredmény a kiépítési szolgáltatástól | Az SDK-eredmények kiépítés |
| -------- | --------------------------------------------- | ------------------------ |
| A webhook a 200 OK értéket adja vissza a "iotHubHostName" értékkel egy érvényes IoT hub-állomásnévre. | Eredmény állapota: Hozzárendelt  | Az SDK visszaadja az PROV_DEVICE_RESULT_OK-t a hub információi mellett |
| A webhook 200 OK értéket ad vissza a válaszban szereplő "iotHubHostName" értékkel, de üres sztringre vagy NULL értékre van állítva. | Eredmény állapota: Meghiúsult<br><br> Hibakód: CustomAllocationIotHubNotSpecified (400208) | Az SDK visszaadja a PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| A webhook visszaadja a 401-et | Eredmény állapota: Meghiúsult<br><br>Hibakód: CustomAllocationUnauthorizedAccess (400209) | Az SDK visszaadja a PROV_DEVICE_RESULT_UNAUTHORIZED |
| Egyéni regisztráció lett létrehozva az eszköz letiltásához | Eredmény állapota: Letiltva | Az SDK visszaadja a PROV_DEVICE_RESULT_DISABLED |
| A webhook a következő hibakódot adja vissza: > = 429 | A DPS-előkészítés számos alkalommal próbálkozik újra. Az újrapróbálkozási szabályzat jelenleg a következőket hajtja végre:<br><br>&nbsp;&nbsp;-Újrapróbálkozások száma: 10<br>&nbsp;&nbsp;-Kezdeti időköz: 1s<br>&nbsp;&nbsp;Növekmény 9-es | Az SDK figyelmen kívül hagyja a hibát, és egy másik lekérési állapotjelző üzenetet küld a megadott időn belül |
| A webhook bármely más állapotkódot ad vissza | Eredmény állapota: Meghiúsult<br><br>Hibakód: CustomAllocationFailed (400207) | Az SDK visszaadja a PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a jelen cikkben létrehozott erőforrásokkal való munkát, meghagyhatja őket. Ha nem tervezi tovább használni az erőforrást, a következő lépésekkel törölheti a cikkben létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt leírt lépések azt feltételezik, hogy a cikkben szereplő összes erőforrást a **contoso-US-Resource-Group**nevű erőforráscsoport utasításai szerint hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport törlése név szerint:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. A **szűrés név szerint...** szövegmezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét, a **contoso-US-Resource-Group**nevet. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

- További információ: [IoT hub eszköz](concepts-device-reprovision.md) újraépítése 
- További részletekért lásd: [az előzőleg automatikusan kiépített eszközök](how-to-unprovision-devices.md) kiépítése. 











