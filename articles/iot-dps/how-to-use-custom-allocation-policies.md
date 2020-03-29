---
title: Egyéni foglalási szabályzatok az Azure IoT Hub eszközkiépítési szolgáltatásával
description: Egyéni foglalási szabályzatok használata az Azure IoT Hub-eszközkiépítési szolgáltatással (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453940"
---
# <a name="how-to-use-custom-allocation-policies"></a>Az egyéni foglalási házirendek használata

Az egyéni foglalási szabályzat lehetővé teszi, hogy az eszközök hogyan vannak hozzárendelve egy IoT-központhoz. Ez úgy érhető el, hogy egy [Azure-függvényben](../azure-functions/functions-overview.md) egyéni kódot használ az eszközök IoT hubhoz való hozzárendeléséhez. Az eszközkiépítési szolgáltatás meghívja az Azure-függvény kódját, amely az eszközről és a regisztrációról nyújt minden lényeges információt. A függvénykód végrehajtása és az eszköz kiépítéséhez használt IoT hub-adatokat adja vissza.

Egyéni foglalási szabályzatok használatával megadhatja a saját foglalási szabályzatokat, ha az eszközkiépítési szolgáltatás által biztosított szabályzatok nem felelnek meg a forgatókönyv követelményeinek.

Például érdemes megvizsgálni a tanúsítvány t használja az eszköz kiépítése során, és rendelje hozzá az eszközt egy IoT hub tanúsítvány tulajdonság alapján. Vagy lehet, hogy az eszközök adatbázisában tárolt információkat, és le kell kérdeznie az adatbázist, hogy melyik IoT hub egy eszköz kell hozzárendelni.

Ez a cikk bemutatja az egyéni foglalási szabályzat ot egy C#-ban írt Azure-függvény használatával. Két új IoT-központ jön létre, amelyek egy *Contoso Kenyérpirító divíziót* és egy *Contoso hőszivattyú-részleget képviselnek.* A kiépítést kérő eszközöknek rendelkezniük kell egy regisztrációs azonosítóval, amely a következő utótagok egyikével rendelkezik a kiépítéshez:

* **-contoso-tstrsd-007**: Contoso Kenyérpirítók Osztály
* **-contoso-HPSd-088**: Contoso hőszivattyúk osztály

Az eszközök a regisztrációs azonosítóban szükséges utótagok egyike alapján lesznek kiépítve. Ezek az eszközök az [Azure IoT C SDK-ban](https://github.com/Azure/azure-iot-sdk-c)található kiépítési minta használatával lesznek szimulálva.

A cikkben az alábbi lépéseket hajthatja végre:

* Az Azure CLI használatával hozzon létre két Contoso divízió IoT hubot (**Contoso Kenyérpirítók divízió** és **Contoso Hőszivattyúk részlege**)
* Új csoportregisztráció létrehozása egy Azure-funkció használatával az egyéni foglalási szabályzathoz
* Eszközkulcsok létrehozása két eszközszimulációhoz.
* Az Azure IoT C SDK fejlesztői környezetének beállítása
* Szimulálja az eszközöket, és ellenőrizze, hogy az egyéni foglalási házirendben a példakódnak megfelelően vannak-e kiépítve

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a Windows fejlesztői környezetben. Linux vagy macOS esetén tekintse meg a megfelelő szakaszt [a Fejlesztői környezet előkészítése az](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) SDK dokumentációban című témakörben.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 az ["Asztali fejlesztés C++"-os munkaterheléssel.](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) A Visual Studio 2015 és a Visual Studio 2017 is támogatott.

* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Hozza létre a létesítési szolgáltatást és két megosztott IoT-központot

Ebben a szakaszban az Azure Cloud Shell használatával hozzon létre egy kiépítési szolgáltatást és két IoT-központot, amelyek a **Contoso Kenyérpirítók részleget** és a **Contoso hőszivattyúk részleget képviselik.**

> [!TIP]
> Az ebben a cikkben használt parancsok létre hozzák a létesítési szolgáltatás és más erőforrások az USA nyugati részén helyen. Azt javasoljuk, hogy hozza létre az erőforrásokat a legközelebbi régióban, amely támogatja az eszközkiépítési szolgáltatás. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban a helyek egy vagy többszavas formátumban is megadhatók; például: westus, WEST USA, WEST USA stb. Az érték nem a kis- és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `-- location "West US"`.
>

1. Az Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

    A következő példa létrehoz egy *contoso-us-resource-group* nevű erőforráscsoportot a *Westus* régióban. Javasoljuk, hogy ezt a csoportot használja a cikkben létrehozott összes erőforráshoz. Ez a megközelítés megkönnyíti a tisztítást, miután befejezte.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Az Azure Cloud Shell használatával hozzon létre egy eszközkiépítési szolgáltatást az [aziot dps create](/cli/azure/iot/dps#az-iot-dps-create) paranccsal. A kiépítési szolgáltatás hozzá lesz adva *a contoso-us-resource-group csoporthoz.*

    A következő példa létrehoz egy *contoso-provisioning-service-1098* nevű kiépítési szolgáltatást a *Westus* helyen. Egyedi szolgáltatásnevet kell használnia. Töltsd fel a saját utótagot a szolgáltatás nevében **az 1098**helyett.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    A parancs végrehajtásához néhány perc is igénybe vehet.

3. Az Azure Cloud Shell használatával hozza létre a **Contoso Kenyérpirítók Osztály** IoT hub az [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) parancsot. Az IoT-központ hozzá lesz adva *a contoso-us-resource-group hoz.*

    A következő példa létrehoz egy *ConToso-toasters-hub-1098* nevű IoT hubot a *Westus* helyen. Egyedi hubnevet kell használnia. Töltsd fel a saját utótagot a hub nevében **az 1098**helyett. Az egyéni foglalási házirend `-toasters-` példakódja a hub nevében szükséges.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs végrehajtásához néhány perc is igénybe vehet.

4. Az Azure Cloud Shell használatával hozza létre a **Contoso Hőszivattyúk Osztály** IoT hub az [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) parancsot. Ez az IoT-központ is hozzá lesz adva a contoso-us-resource-group.This IoT hub will also be added to *contoso-us-resource-group.*

    A következő példa létrehoz egy *Contoso-heatpumps-hub-1098* nevű IoT hubot a *Westus* helyen. Egyedi hubnevet kell használnia. Töltsd fel a saját utótagot a hub nevében **az 1098**helyett. Az egyéni foglalási házirend `-heatpumps-` példakódja a hub nevében szükséges.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs végrehajtásához néhány perc is igénybe vehet.

## <a name="create-the-custom-allocation-function"></a>Az egyéni felosztási függvény létrehozása

Ebben a szakaszban hozzon létre egy Azure-függvényt, amely megvalósítja az egyéni foglalási szabályzatot. Ez a funkció dönti el, hogy melyik divíziós IoT-hubhoz kell regisztrálni egy eszközt annak alapján, hogy a regisztrációs azonosítója tartalmazza-e a **-contoso-tstrsd-007** vagy **-contoso-hpsd-088**karakterláncot. Azt is beállítja a kezdeti állapotát a készülék iker alapján, hogy a készülék egy kenyérpirító vagy hőszivattyú.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) A kezdőlapon válassza a **+ Erőforrás létrehozása**lehetőséget.

2. A *Keresés a Piactéren* keresőmezőbe írja be a "Function App" kifejezést. A legördülő listában válassza a **Függvényalkalmazás**lehetőséget, majd a **Létrehozás**lehetőséget.

3. A **Függvényalkalmazás** létrehozáslapján az **Alapok** lapon adja meg az új függvényalkalmazás következő beállításait, és válassza a **Véleményezés + létrehozás**lehetőséget:

    **Erőforráscsoport**: Válassza ki a **contoso-us-resource-group-t,** hogy a cikkben létrehozott összes erőforrás együtt maradjon.

    **Függvényalkalmazás neve**: Adjon meg egy egyedi függvényalkalmazás nevét. Ez a példa **contoso-function-app-1098-at**használ.

    **Közzététel**: Ellenőrizze, hogy a **Kód** ki van-e jelölve.

    **Futásidejű verem**: Válassza a **.NET Core elemet** a legördülő menüből.

    **Régió**: Válassza ki ugyanazt a régiót, mint az erőforráscsoport. Ez a példa **az USA nyugati nyugati részén**jelenik meg.

    > [!NOTE]
    > Alapértelmezés szerint az Application Insights engedélyezve van. Application Insights nem szükséges ebben a cikkben, de ez segíthet megérteni és kivizsgálni az egyéni foglalással tapasztalt problémákat. Ha szeretné, letilthatja az Application Insightsalkalmazást, ha a **Figyelés** fülre, majd az **Alkalmazáselemzési adatok engedélyezése** **elemre** választa.

    ![Hozzon létre egy Azure Függvényalkalmazást az egyéni foglalási függvény üzemeltetéséhez](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Az **Összegzés** lapon válassza a **Létrehozás** gombot a függvényalkalmazás létrehozásához. Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, válassza **az Ugrás az erőforrásra**lehetőséget.

5. A függvényalkalmazás **áttekintése** lap bal **+** oldali ablaktábláján válassza a **Funkciók** lehetőséget új függvény hozzáadásához.

    ![Függvény hozzáadása a Függvényalkalmazáshoz](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Az **Azure Functions for .NET – első lépések** lapon a SELECT A **DEPLOYMENT ENVIRONMENT (TELEPÍTÉSI KÖRNYEZET KIVÁLASZTÁSA)** lépéshez válassza a **Portálon** csempét, majd a **Continue (Folytatás) lehetőséget.**

    ![Válassza ki a portál fejlesztési környezetét](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. A következő lapon a **FÜGGVÉNY LÉTREHOZÁSA** lépésnél válassza a **Webhook + API** csempét, majd a **Létrehozás gombot.** Létrejön egy **HttpTrigger1** nevű függvény, és a portál megjeleníti a **run.csx** kódfájl tartalmát.

8. Hivatkozás szükséges Nuget csomagok. A kezdeti ikereszköz létrehozásához az egyéni foglalási függvény két Nuget-csomagban definiált osztályokat használ, amelyeket be kell tölteni a gazdakörnyezetbe. Az Azure Functions segítségével a Nuget-csomagokra egy *function.host* fájl hivatkozik. Ebben a lépésben *function.host* fájlt ment és tölt fel.

    1. Másolja a következő sorokat a kedvenc szerkesztőjébe, és mentse a fájlt a számítógépre *function.host*néven.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. A **HttpTrigger1** függvényben bontsa ki a **Fájlok megtekintése** lapot az ablak jobb oldalán.

        ![Nézetfájlok megnyitása](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Válassza **a Feltöltés**gombot, keresse meg a **function.proj** fájlt, és a fájl feltöltéséhez válassza a **Megnyitás** gombot.

        ![Töltse fel a fájlt](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Cserélje le a **HttpTrigger1** függvény kódját a következő kódra, és válassza a **Mentés**lehetőséget:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
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
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>A regisztráció létrehozása

Ebben a szakaszban egy új regisztrációs csoportot hoz létre, amely az egyéni felosztási házirendet használja. Az egyszerűség kedvéért ez a cikk [szimmetrikus kulcsigazolást](concepts-symmetric-key-attestation.md) használ a regisztrációval. A biztonságosabb megoldás érdekében fontolja meg [az X.509 tanúsítvány tanúsítvány](concepts-security.md#x509-certificates) használatát megbízhatósági lánccal.

1. Továbbra is az [Azure Portalon,](https://portal.azure.com)nyissa meg a kiépítési szolgáltatás.

2. Válassza az **Igénylések kezelése lehetőséget** a bal oldali ablaktáblán, majd a lap tetején kattintson a **Beléptetési csoport hozzáadása** gombra.

3. A **Beiktatási csoport hozzáadása mezőbe**írja be a következő adatokat, és kattintson a **Mentés** gombra.

    **Csoportnév**: Adja meg **a contoso-egyénileg lefoglalt eszközöket.**

    **Tanúsítvány típusa**: Válassza a **Szimmetrikus kulcs lehetőséget.**

    **Kulcsok automatikus létrehozása**: Ezt a jelölőnégyzetet már be kell pipani.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a hubokhoz:** Válassza **az Egyéni (Azure-függvény használata) lehetőséget.**

    ![Egyéni foglalási igénylési csoport hozzáadása a szimmetrikus kulcstanúsítványhoz](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. A **Enrollment Group hozzáadása**, válassza a Link egy új **IoT hub** összekapcsolására mindkét új megosztott IoT hubok.

    Hajtsa végre ezt a lépést mindkét megosztott IoT-hubon.

    **Előfizetés:** Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyhez létrehozta a megosztott IoT-központokat.

    **IoT hub:** Válassza ki a létrehozott megosztott elosztók egyikét.

    **Hozzáférési házirend**: Válassza az **iothubowner lehetőséget.**

    ![A megosztott IoT-központok összekapcsolása a létesítési szolgáltatással](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. A **Enrollment Group hozzáadása**, miután mindkét megosztott IoT-hub oka van csatolva, ki kell választania őket, mint az IoT Hub-csoport a regisztrációs csoport az alábbiak szerint:

    ![A divízióközpont-csoport létrehozása a regisztrációhoz](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. A **Beiktatási csoport hozzáadása**, görgessen le az **Azure-függvény kiválasztása** szakaszban válassza ki az előző szakaszban létrehozott Függvény alkalmazást. Ezután jelölje ki a létrehozott függvényt, és válassza a Mentés gombot a regisztrációs csoport mentéséhez.

    ![Válassza ki a funkciót, és mentse a regisztrációs csoportot](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. A regisztráció mentése után nyissa meg újra, és jegyezze fel az **elsődleges kulcsot**. A kulcsok létrehozásához először mentenie kell a regisztrációt. Ezzel a kulccsal lesz nek egyedi eszközkulcsok a szimulált eszközök később.

## <a name="derive-unique-device-keys"></a>Egyedi eszközkulcsok származtatása

Ebben a szakaszban két egyedi eszközkulcsot hoz létre. Egy kulcs lesz használva egy szimulált kenyérpirító eszközhöz. A másik kulcs egy szimulált hőszivattyú eszközhöz lesz használva.

Az eszközkulcs létrehozásához a korábban feljegyzett **elsődleges kulcs** segítségével számítsa ki az egyes eszközök hez tartozó eszközregisztrációs azonosító [HMAC-SHA256-ot,](https://wikipedia.org/wiki/HMAC) és konvertálja az eredményt Base64 formátumba. A származtatott eszközkulcsok regisztrációs csoportokkal való létrehozásáról a [Szimmetrikus kulcstanúsítvány](concepts-symmetric-key-attestation.md)csoportbeléptetési szakaszában talál további információt.

Ebben a cikkben a példában használja a következő két eszköz regisztrációs azonosítók és kiszámítja az eszköz kulcs mindkét eszközhöz. Mindkét regisztrációs azonosító rendelkezik érvényes utótaggal az egyéni foglalási házirend példakódjával:

* **breakroom499-contoso-tstrsd-007**
* **főépület167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux munkaállomások

Ha Linux munkaállomást használ, az openssl segítségével létrehozhatja a származtatott eszközkulcsokat, ahogy az a következő példában látható.

1. Cserélje le a **KEY** értékét a korábban feljegyzett **elsődleges kulcsra.**

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

### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

Ha Windows-alapú munkaállomást használ, a PowerShell segítségével létrehozhatja a származtatott eszközkulcsot, ahogy az a következő példában látható.

1. Cserélje le a **KEY** értékét a korábban feljegyzett **elsődleges kulcsra.**

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

A szimulált eszközök a származtatott eszközkulcsokat minden regisztrációs azonosítóval együtt szimmetrikus kulcsigazolás végrehajtására fogják használni.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK fejlesztői környezet előkészítése

Ebben a szakaszban előkészíti az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)létrehozásához használt fejlesztői környezetet. Az SDK tartalmazza a szimulált eszköz mintakódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

Ez a szakasz egy Windows-alapú munkaállomás felé irányul. Egy Linux-példában tekintse meg a virtuális gépek beállítását a [Több-bérlős](how-to-provision-multitenant.md)szolgáltatás kiépítése című témakörben.

1. Töltse le a [CMake build rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben található címkét `-b` a paraméter értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a `azure-iot-sdk-c` következő parancsokat a könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Ha `cmake` nem találja a C++ fordítót, a parancs futtatása közben buildhibákat kaphat. Ebben az esetben próbálja meg futtatni a parancsot a [Visual Studio parancssorában.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

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

Ebben a szakaszban frissíti a **prov\_\_\_dev-ügyfél minta** nevű kiépítési minta nevű üzembe helyező d. az Azure IoT C SDK korábban beállított.

Ez a mintakód egy eszköz indítási sorozatát szimulálja, amely elküldi a létesítési kérelmet az eszközkiépítési szolgáltatás példányának. A rendszerindítási sorrend hatására a kenyérpirító eszköz fellesz ismerve, és az egyéni foglalási szabályzat használatával hozzárendeli az IoT hubhoz.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg a **Azure_iot_sdks.sln** megoldásfájlt, amelyet a CMake korábbi futtatása hozott létre. A megoldásfájlnak a következő helyen kell lennie:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

### <a name="simulate-the-contoso-toaster-device"></a>A Contoso kenyérpirító eszköz szimulálása

1. A kenyérpirító eszköz szimulálása `prov_dev_set_symmetric_key_info()` érdekében keresse meg a hívást a **prov\_dev\_\_ügyfél sample.c,** amely kommentálta ki.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ne fűzzön megjegyzést a függvényhíváshoz, és cserélje le a helyőrző értékeket (beleértve a szögletes zárójeleket) a kenyérpirító regisztrációs azonosítójára és a korábban létrehozott származtatott eszközkulcsra. A **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** kulcsérték csak példaként szolgál.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére irányuló kérdésben válassza az **Igen**lehetőséget a projekt futás előtti újraépítéséhez.

    A következő kimenet egy példa a szimulált kenyérpirító eszköz sikeresen elindult, és csatlakozik a létesítési szolgáltatás példánya kell rendelni a kenyérpirítók IoT hub az egyéni foglalási szabályzat:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>A Contoso hőszivattyú-eszköz szimulálása

1. A hőszivattyú-eszköz szimulálása `prov_dev_set_symmetric_key_info()` érdekében frissítse a hívást a **prov\_dev\_\_client sample.c-ben** ismét a hőszivattyú regisztrációs azonosítójával és a korábban létrehozott származtatott eszközkulival. A kulcs érték **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** az alábbiakban látható is csak példaként.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Hibakeresés** > **indítása hibakeresés nélkül** lehetőséget a megoldás futtatásához. A projekt újraépítésére irányuló kérdésben válassza az **Igen** lehetőséget a projekt újraépítéséhez futás előtt.

    A következő kimenet egy példa a szimulált hőszivattyú-eszköz sikeresen elindult, és csatlakozik a létesítési szolgáltatás példánya hozzá kell rendelni a Contoso hőszivattyúk IoT hub az egyéni kiosztási szabályzat:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Egyéni foglalási házirendek – hibaelhárítás

Az alábbi táblázat a várt eseteket és a kapott hibakódokat mutatja be. Ebben a táblázatban az Azure Functions egyéni foglalási házirend-hibáinak hibaelhárításához.

| Forgatókönyv | A létesítési szolgáltatás regisztrációs eredménye | SDK-eredmények kiépítése |
| -------- | --------------------------------------------- | ------------------------ |
| A webhook 200 OK értéket ad vissza az "iotHubHostName" értékkel egy érvényes IoT hub állomásnévre állítva | Eredmény állapota: Hozzárendelve  | Az SDK PROV_DEVICE_RESULT_OK a központi adatokkal együtt ad vissza |
| A webhook 200 OK értéket ad vissza a válaszban jelen lévő "iotHubHostName" értékkel, de üres karakterláncra vagy null értékre van állítva | Eredmény állapota: Nem sikerült<br><br> Hibakód: CustomAllocationIotHubNotSpecified (400208) | Az SDK PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED ad vissza |
| A webhook 401 jogosulatlan értéket ad vissza | Eredmény állapota: Nem sikerült<br><br>Hibakód: CustomAllocationUnauthorizedAccess (400209) | Az SDK PROV_DEVICE_RESULT_UNAUTHORIZED ad vissza |
| Egyéni regisztráció jött létre az eszköz letiltásához | Eredmény állapota: Letiltva | Az SDK PROV_DEVICE_RESULT_DISABLED ad vissza |
| A webhook >= 429 hibakódot ad vissza | A DPS vezénylése többször is újra próbálkozik. Az újrapróbálkozási házirend jelenleg a következő:<br><br>&nbsp;&nbsp;- Újrapróbálkozások száma: 10<br>&nbsp;&nbsp;- Kezdeti intervallum: 1s<br>&nbsp;&nbsp;- Növekmény: 9s | Az SDK figyelmen kívül hagyja a hibát, és egy másik begetállapot-üzenetet küld a megadott időn |
| A webhook minden más állapotkódot ad vissza | Eredmény állapota: Nem sikerült<br><br>Hibakód: CustomAllocationFailed (400207) | Az SDK PROV_DEVICE_RESULT_DEV_AUTH_ERROR ad vissza |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát a cikkben létrehozott erőforrásokkal, hagyja őket. Ha nem tervezi az erőforrások további használatát, az alábbi lépésekkel törölje a cikkben létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt leírt lépések feltételezik, hogy a jelen cikkben szereplő összes erőforrást a **contoso-us-resource-group**nevű erőforráscsoport utasításai nak megfelelően hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport név szerint törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **Név szerint szűrés...** mezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét, **contoso-us-resource-group**. 

3. Az eredménylistában az erőforráscsoporttól jobbra válassza **a ...** majd **az Erőforráscsoport törlése**lehetőséget.

4. A rendszer megkéri, hogy erősítse meg az erőforráscsoport törlését. A megerősítéshez írja be ismét az erőforráscsoport nevét, majd kattintson a **Törlés gombra.** A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

* További újraépítés, lásd: [IoT Hub-eszköz újrakiépítése fogalmak](concepts-device-reprovision.md) 
* További információ a megszüntetésről: [A korábban automatikusan kiépített eszközök kiirtása](how-to-unprovision-devices.md) 
