---
title: Egyéni foglalási szabályzatok használata az Azure IoT Hub Device Provisioning Service |} A Microsoft Docs
description: Egyéni foglalási szabályzatok használata az Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 89cb44366d4752052d990a1506482c9108cde103
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161706"
---
# <a name="how-to-use-custom-allocation-policies"></a>Egyéni foglalási szabályzatok használata


Egy egyéni foglalási szabályzat jobban szabályozhatja, hogyan eszközök vannak rendelve egy IoT-központ biztosítja. Ez az egyéni kód használatával történik egy [Azure-függvény](../azure-functions/functions-overview.md) hozzárendelni az eszközöket egy IoT hubra. A device provisioning szolgáltatás meghívja az Azure-függvény kódjának minden szükséges információt az eszköz és a regisztráció. A függvénykód hajtja végre, és az IoT hub információk segítségével a device provisioning adja vissza.

Egyéni foglalási szabályzatok segítségével meghatározhatja a saját foglalási szabályzatok Ha a házirendek a Device Provisioning Service által biztosított nem felelnek meg a forgatókönyv követelményeinek.

Például lehetséges, meg szeretne vizsgálni, a tanúsítvány eszköz kiépítése során használ, és hozzárendelheti az eszköz egy IoT hubot, egy tanúsítvány tulajdonsága alapján. Talán előfordulhat, hogy rendelkezik az eszközök egy adatbázisban tárolt adatok, és lekérdezheti az adatbázist, mely hozzá kell rendelni egy eszközt az IoT hub meghatározásához.


Ez a cikk bemutatja egy egyéni foglalási szabályzat egy C# nyelven írt Azure-függvény használatával. Két új IoT-központok jönnek létre jelölő egy *Contoso kenyérpirítók osztás* és a egy *Contoso megadott Hőtérképrészlet szivattyújának osztás*. Kiépítés biztosíthat telepítéshez el kell fogadni a következő utótag egyikével regisztrációs Azonosítót kell rendelkeznie:

- **– contoso-tstrsd-007**: Contoso kenyérpirítók körzet
- **– contoso-hpsd-088**: Contoso megadott Hőtérképrészlet szivattyúk körzet

Az eszközök megkapják ezeket a regisztrációs azonosítót. a szükséges utótagokat egyike alapján Ezek az eszközök fog szimulált üzembe helyezési mintájával szerepel a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

Ebben a cikkben végre fogja hajtani a következő lépéseket:

* Az Azure CLI használatával hozzon létre két Contoso részleg IoT hubok (**Contoso kenyérpirítók körzet** és **Contoso megadott Hőtérképrészlet szivattyújának osztás**)
* Hozzon létre egy új, Azure-függvény használatával az egyéni elosztási házirend csoportos regisztráció
* Hozzon létre két eszköz szimulációkhoz eszközkulcsok.
* Az Azure IoT C SDK a fejlesztési környezet beállítása
* Az eszközökre, hogy a példakód az egyéni kiosztási szabályzat szerint kiépítésüket szimulálása


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Befejezése után a [IoT Hub Device Provisioning Service beállítása az Azure Portallal](./quick-setup-auto-provision.md) rövid.
* Visual Studio 2015 vagy [Visual Studio 2017](https://www.visualstudio.com/vs/), amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladat.
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Hozzon létre két részlegszintű IoT-központok

Ebben a szakaszban jelölő két új IoT hub létrehozása az Azure Cloud Shell fogja használni a **Contoso kenyérpirítók osztás** és a **Contoso megadott Hőtérképrészlet szivattyújának osztás**.

1. Az Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

    A következő példában létrehozunk egy erőforráscsoportot, nevű *contoso-us-resource-group* a a *eastus* régióban. Javasoljuk, hogy a jelen cikkben létrehozott összes erőforrást használjon ehhez a csoporthoz. Ez a megközelítés megkönnyíti karbantartás befejezése után.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Az Azure Cloud Shell használatával hozzon létre a **Contoso kenyérpirítók osztás** IoT hub és az a [az iot hub létrehozása](/cli/azure/iot/hub#az-iot-hub-create) parancsot. Az IoT hub hozzáadandó *contoso-us-resource-group*.

    A következő példában létrehozunk egy nevű IoT hubot *contoso-kenyérpirítók-hub-1098* a a *eastus* helyét. A saját egyedi hub nevet kell használnia. A központ neve helyére a saját utótagját alkotó **1098**. Példakód az egyéni elosztási házirend van szükség `-toasters-` a hub nevét.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs eltarthat néhány percig.

3. Az Azure Cloud Shell használatával hozzon létre a **Contoso megadott Hőtérképrészlet szivattyújának körzet** IoT hub és az a [az iot hub létrehozása](/cli/azure/iot/hub#az-iot-hub-create) parancsot. Az IoT-központ is bekerülnek a *contoso-us-resource-group*.

    A következő példában létrehozunk egy nevű IoT hubot *contoso-heatpumps-hub-1098* a a *eastus* helyét. A saját egyedi hub nevet kell használnia. A központ neve helyére a saját utótagját alkotó **1098**. Példakód az egyéni elosztási házirend van szükség `-heatpumps-` a hub nevét.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    A parancs is eltarthat néhány percig.




## <a name="create-the-enrollment"></a>A regisztráció létrehozása

Ebben a szakaszban létrehozhat egy új regisztrációs csoportot, amely az egyéni foglalási szabályzat használja. Ez a cikk az egyszerűség kedvéért használja [szimmetrikus kulcsát a kulcsigazoláshoz](concepts-symmetric-key-attestation.md) a regisztrációval. A biztonságosabb megoldás, fontolja meg [tanúsítvány X.509-igazoláshoz](concepts-security.md#x509-certificates) és a egy megbízhatósági láncot.

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), és nyissa meg a Device Provisioning Service-példány.

2. Válassza ki a **beléptetések kezelése** fülre, majd a **regisztrációs csoport hozzáadása** gombra a lap tetején. 

3. A **regisztrációs csoport hozzáadásához**, adja meg a következőket, majd kattintson a **mentése** gombra.

    **Csoport neve**: Adja meg **contoso-egyéni lefoglalt – eszközök**.

    **Tanúsítvány típusa**: válasszon **szimmetrikus kulcs**.

    **Kulcsok automatikus létrehozása**: már ellenőrizni kell a ezt a jelölőnégyzetet.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket hubs**: válasszon **egyéni (használata Azure-függvény)**.

    ![Egyéni hozzárendelés regisztrációs csoportot szimmetrikus kulcsát a kulcsigazoláshoz hozzáadása](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. A **regisztrációs csoport hozzáadásához**, kattintson a **egy új IoT hub csatolása** mutató hivatkozást is az új részlegszintű IoT-központokat. 

    Ezt a lépést mindkét a részlegszintű IoT-központok esetében végre kell hajtani.

    **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki az előfizetést, ahol létrehozta a részlegszintű IoT-központok.

    **Az IoT hub**: Válasszon egyet a részlegszintű hubs hozott létre.

    **Hozzáférési szabályzat**: válasszon **iothubowner**.

    ![A részlegszintű IoT hubok az eszközkiépítési szolgáltatás csatolása](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. A **regisztrációs csoport hozzáadásához**, miután mindkét részlegszintű IoT-központok vannak kapcsolva, jelöljön ki azokat az IoT Hub-csoport a regisztrációs csoport a lent látható módon:

    ![A beléptetéshez részlegszintű eseményközpont-csoport létrehozása](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. A **regisztrációs csoport hozzáadásához**, görgessen le a **válassza ki Azure-függvény** szakaszt, és kattintson a **hozzon létre egy új függvényalkalmazást**.

7. A **Függvényalkalmazás** , hogy megnyitja, adja meg a következő értékeket a új funkció, és kattintson a lap létrehozása **létrehozás**:

    **Alkalmazásnév**: Adjon meg egy egyedi függvényalkalmazás-nevét. **Contoso-függvény-alkalmazás-1098** példaként jelenik meg.

    **Erőforráscsoport**: válasszon **meglévő használata** és a **contoso-us-resource-group** tartani egymáshoz a jelen cikkben létrehozott összes erőforrást.

    **Az Application Insights**: ehhez a gyakorlathoz kikapcsolhatja ez.

    ![A függvényalkalmazás létrehozása](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Lépjen vissza a **regisztrációs csoport hozzáadásához** lapon, győződjön meg arról, hogy az új függvényalkalmazást van kiválasztva. Jelölje ki az előfizetést, a függvény alkalmazás frissítéséhez újra lehet.

    Miután az új függvényalkalmazást be van jelölve, kattintson a **hozzon létre egy új függvényt**.

    ![A függvényalkalmazás létrehozása](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    az új függvényalkalmazást nyílnak meg.

9. A függvényalkalmazásban kattintson az új függvény létrehozása

    ![A függvényalkalmazás létrehozása](./media/how-to-use-custom-allocation-policies/new-function.png)

    Az új függvény az alapértelmezett beállítások használatával hozzon létre egy új **Webhook + API** használatával a **CSharp** nyelv. Kattintson a **függvény létrehozása**.

    Ez létrehoz egy új C#-függvény nevű **HttpTriggerCSharp1**.

10. Cserélje le a következő kódot, majd kattintson az új C#-függvény kódja **mentése**:    

    ```C#
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


11. Lépjen vissza a **regisztrációs csoport hozzáadásához** lapon, és győződjön meg arról, hogy az új funkció ki van jelölve. Jelölje ki a függvényalkalmazás a függvények frissítéséhez újra lehet.

    A kijelölt az új függvényben kattintson **mentése** a regisztrációs csoport mentéséhez.

    ![Végül mentse a regisztrációs csoport](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. A regisztrációt a mentés után nyissa meg újra, és jegyezze fel a **elsődleges kulcs**. A regisztrációs kulcsai jön létre, először mentenie kell. Ezt a kulcsot később a szimulált eszközök az eszköz egyedi kulcsok használható.


## <a name="derive-unique-device-keys"></a>Származtasson eszköz egyedi kulcsok

Ebben a szakaszban két eszköz egyedi kulcsot hoz létre. Egy kulcsot a toaster szimulált eszköz használható. A másik hívóbetűt egy szimulált megadott hőtérképrészlet szivattyú eszköz használható.

Az eszköz kulcs létrehozásához használhatja a **elsődleges kulcs** számítási korábban feljegyzett a [HMAC-SHA256 algoritmust](https://wikipedia.org/wiki/HMAC) , az eszköz regisztrációs azonosító minden eszközhöz, és az eredmény Base64 formátumra alakítható. Származtatott eszközkulcsok való regisztrációs csoportok létrehozását további információkért lásd: a csoport regisztrációk szakaszában [szimmetrikus kulcsát a kulcsigazoláshoz](concepts-symmetric-key-attestation.md).

Ez a cikk például használja a következő két eszköz regisztrációs azonosítóval, és számítási mindkét eszköz eszköz kulcsát. Mindkét regisztrációs azonosítók egy érvényes utótagot működéséhez példakód az egyéni elosztási házirend van:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Linux-munkaállomásokon

Egy Linux munkaállomáson használja, ha openssl használatával hozzon létre a származtatott eszköz kulcsokat, a következő példában látható módon.

1. Értékét cserélje **kulcs** együtt a **elsődleges kulcs** korábban feljegyzett.

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

Ha egy Windows-alapú munkaállomást használ, a PowerShell használatával hozhatja létre a származtatott eszköz kulcsot a következő példában látható módon.

1. Értékét cserélje **kulcs** együtt a **elsődleges kulcs** korábban feljegyzett.

    ```PowerShell
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

    ```PowerShell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


A szimulált eszközök fogja használni a származtatott eszköz kulcsok minden egyes regisztrációs azonosító szimmetrikus kulcsát a kulcsigazoláshoz végrehajtásához.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Az Azure IoT C SDK-t fejlesztési környezet előkészítése

Ebben a szakaszban egy segítségével hozhatók létre a fejlesztési környezetet előkészíti az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Az SDK-t magában foglalja a mintakód a szimulált eszközhöz. Ezt a szimulált eszközt megkísérli a kiépítés során az eszköz rendszerindítási során.

Ebben a szakaszban egy Windows-alapú munkaállomás felé objektumorientált. A Linuxos példa: a telepítési található virtuális gépek [több-bérlős módhoz kiépítéséről](how-to-provision-multitenant.md).



1. Töltse le a verzióját 3.11.4 a [CMake buildelési rendszert](https://cmake.org/download/). Ellenőrizze a letöltött bináris fájlt a megfelelő titkosítási kivonat értékének használatával. Az alábbi példa a Windows PowerShell használatával ellenőrizte az x64 MSI disztribúció 3.11.4-es verziójának titkosítási kivonatát:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    A következő kivonatértékeket verzió 3.11.4 írásának időpontjában a CMake webhelyen felsorolt:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. Hajtsa végre a következő parancsot az Azure IoT C SDK GitHub-adattár klónozása:
    
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

4. Futtassa a következő parancsot, amely létrehozza egy adott a fejlesztési fejlesztésiügyfél-platformhoz SDK verziója. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

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




## <a name="simulate-the-devices"></a>Az eszköz szimulálása

Ebben a szakaszban egy nevű eszközkiépítési minta frissíteni **prov\_fejlesztési\_ügyfél\_minta** korábban állítsa be az Azure IoT C SDK-ban található. 

A mintakód a kiépítési kérést küld a Device Provisioning Service-példány eszköz rendszerindítási sorrend szimulálja. A rendszerindítási sorrend miatt az toaster eszköz elfogadandó és az IoT hubon az egyéni foglalási szabályzat hozzárendelve.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a **azure_iot_sdks.sln** megoldásfájlt a CMake futó korábban létrehozott. A fájl a következő helyen kell lennie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. A Visual Studio *Solution Explorer* (Megoldáskezelő) ablakában lépjen a **Provision\_Samples** (Kiépítés > Minták) mappára. Bontsa ki a **prov\_dev\_client\_sample** nevű mintaprojektet. Bontsa ki a **Source Files** (Forrásfájlok) elemet, és nyissa meg a **prov\_dev\_client\_sample.c** nevű forrásfájlt.

4. Keresse meg az `id_scope` állandót, és cserélje le az értékét a korábban kimásolt **Azonosító hatóköre** értékre. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Keresse meg a `main()` függvény definícióját ugyanebben a fájlban. Győződjön meg arról, hogy a `hsm_type` változó értéke `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` alább látható módon:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Kattintson a jobb gombbal a **prov\_dev\_client\_sample** projektre, és válassza a **Beállítás kezdőprojektként** lehetőséget. 

#### <a name="simulate-the-contoso-toaster-device"></a>A Contoso toaster eszköz szimulálása

1. A Visual Studióban *Megoldáskezelőben* ablakban keresse meg a **hsm\_biztonsági\_ügyfél** projektre, és bontsa ki azt. Bontsa ki a **forrásfájlok**, és nyissa meg a **hsm\_ügyfél\_key.c**. 

    Keresse meg a nyilatkozat az `REGISTRATION_NAME` és `SYMMETRIC_KEY_VALUE` konstansok. A következő módosításokat a fájlt, és mentse a fájlt.

    Frissítse az értéket, a `REGISTRATION_NAME` a toaster eszköz regisztrációs Azonosítót az állandó **breakroom499-contoso-tstrsd-007**.
    
    Frissítse az értéket, a `SYMMETRIC_KEY_VALUE` állandó eszközkulcs számára létrehozott a toaster eszköz. Az érték **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** van csak példaként.

    ```c
    static const char* const REGISTRATION_NAME = "breakroom499-contoso-tstrsd-007";
    static const char* const SYMMETRIC_KEY_VALUE = "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=";
    ```

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    A következő kimenet sikeresen másolatából, és hogyan csatlakozhat a kiépítési szolgáltatás példánya kenyérpirítók IoT hub által az egyéni foglalási szabályzat hozzárendelését a toaster szimulált eszköz példája:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>A Contoso megadott hőtérképrészlet szivattyú eszköz szimulálása

1. Vissza a Visual Studióban *Megoldáskezelőben* ablakban keresse meg a **hsm\_biztonsági\_ügyfél** projektre, és bontsa ki azt. Bontsa ki a **forrásfájlok**, és nyissa meg a **hsm\_ügyfél\_key.c**. 

    Keresse meg a nyilatkozat az `REGISTRATION_NAME` és `SYMMETRIC_KEY_VALUE` konstansok. A következő módosításokat a fájlt, és mentse a fájlt.

    Frissítse az értéket, a `REGISTRATION_NAME` állandó, a regisztrációs azonosítót a megadott hőtérképrészlet szivattyú eszköz **mainbuilding167-contoso-hpsd-088**.
    
    Frissítse az értéket, a `SYMMETRIC_KEY_VALUE` állandó eszközkulcs számára létrehozott a toaster eszköz. Az érték **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** van csak példaként.

    ```c
    static const char* const REGISTRATION_NAME = "mainbuilding167-contoso-hpsd-088";
    static const char* const SYMMETRIC_KEY_VALUE = "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=";
    ```

7. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésére vonatkozó parancsablakban kattintson az **Igen** gombra a projekt újraépítéséhez a futtatás előtt.

    A következő kimenet sikeresen másolatából, és hogyan csatlakozhat a kiépítési szolgáltatás példánya a Contoso megadott hőtérképrészlet pedig előre felkészülhet az IoT hub által az egyéni foglalási szabályzat hozzárendelését a szimulált megadott hőtérképrészlet szivattyú eszköz példája:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```




## <a name="troubleshooting-custom-allocation-policies"></a>Egyéni elosztási házirendek hibaelhárítása

Az alábbi táblázat bemutatja a várt forgatókönyvek és az eredmények hibakódok találkozhat. Ez a táblázat segítségével az Azure Functions az egyéni házirend hibák elhárításához.


| Forgatókönyv | Eszközkiépítési szolgáltatás regisztrációs eredménye | Kiépítési SDK eredmények |
| -------- | --------------------------------------------- | ------------------------ |
| A webhook ad vissza 200 OK értékre egy érvényes IoT hub-állomásnévvel iotHubHostName | Állapot eredmény: hozzárendelve  | SDK-t PROV_DEVICE_RESULT_OK hub információk mellett adja vissza. |
| A webhook ad vissza 200 OK "iotHubHostName" az a válaszban található, de egy NULL értékű vagy üres karakterlánc beállítása | Állapot eredmény: nem sikerült<br><br> Kód chyby: CustomAllocationIotHubNotSpecified (400208) | SDK-t PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED adja vissza. |
| A webhook adja vissza a 401-es nem engedélyezett | Állapot eredmény: nem sikerült<br><br>Kód chyby: CustomAllocationUnauthorizedAccess (400209) | SDK-t PROV_DEVICE_RESULT_UNAUTHORIZED adja vissza. |
| Egyéni regisztrációt létrejött, az eszköz letiltása | Eredmény állapotának: letiltott | SDK-t PROV_DEVICE_RESULT_DISABLED adja vissza. |
| A webhook függvény hibakód: > = 429-es | DPS' vezénylési számos alkalommal próbálkozik újra. Az újrapróbálkozási szabályzat jelenleg:<br><br>&nbsp;&nbsp;-Újrapróbálkozások száma: 10<br>&nbsp;&nbsp;-Kezdeti időköz: 1s<br>&nbsp;&nbsp;-Növelje: 9-es | SDK-t fog hiba figyelmen kívül, és a egy másik get állapotüzenet küldése a megadott időtartam alatt |
| A webhook bármely más állapotkódot adja vissza. | Állapot eredmény: nem sikerült<br><br>Kód chyby: CustomAllocationFailed (400207) | SDK-t PROV_DEVICE_RESULT_DEV_AUTH_ERROR adja vissza. |


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a jelen cikkben létrehozott erőforrásokat, hagyhatja őket. Ha nem tervezi, hogy továbbra is használhassa az erőforrás, használja az alábbi lépéseket a felesleges költségek elkerülése érdekében ebben a cikkben létrehozott összes erőforrást törli.

Itt a lépések feltételezik, hogy létrehozott összes erőforrást Ez a cikk utasításai nevű ugyanabban az erőforráscsoportban lévő **contoso-us-resource-group**.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport törlése a név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

2. Az a **Szűrés név alapján...**  szövegmezőbe írja be az erőforrás nevének csoportba az erőforrásokat tartalmazó **contoso-us-resource-group**. 

3. Az eredménylistában kattintson az erőforráscsoporttól jobbra lévő **…** ikonra, majd kattintson az **Erőforráscsoport törlése** elemre.

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be újra az erőforráscsoport nevét, majd kattintson a **Törlés** elemre. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

- További Reprovisioning kapcsolatban lásd: [IoT Hub Device reprovisoning fogalmak](concepts-device-reprovision.md) 
- Megszüntetés további tudnivalókért lásd: [hogyan eszközöket, amelyek korábban automatikus – kiépített megszüntetése ](how-to-unprovision-devices.md) 











