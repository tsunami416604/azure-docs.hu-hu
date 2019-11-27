---
title: Egyéni kiosztási szabályzatok az Azure IoT Hub Device Provisioning Service
description: Egyéni kiosztási szabályzatok használata az Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2a17cc6c9f2211de31d4551bd12e6c832d4eee38
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228734"
---
# <a name="how-to-use-custom-allocation-policies"></a>Egyéni foglalási szabályzatok használata

Az egyéni foglalási szabályzatok részletesebben szabályozzák, hogy az eszközök hogyan legyenek hozzárendelve az IoT hubhoz. Ezt egy [Azure-függvény](../azure-functions/functions-overview.md) egyéni kódjának használatával végezheti el az eszközök IoT-hubhoz való hozzárendeléséhez. Az eszköz kiépítési szolgáltatása meghívja az Azure-függvény kódját, amely az eszközre és a regisztrációra vonatkozó összes releváns információt megadja. A rendszer végrehajtja a függvény kódját, és az eszköz kiépítési IoT hub-információit adja vissza.

Egyéni kiosztási szabályzatok használatával saját kiosztási szabályzatokat határozhat meg, ha az eszköz kiépítési szolgáltatása által biztosított szabályzatok nem felelnek meg a forgatókönyv követelményeinek.

Tegyük fel például, hogy meg szeretné vizsgálni az eszköz által a kiépítés során használt tanúsítványt, és hozzárendeli az eszközt egy IoT-hubhoz a Certificate tulajdonság alapján. Vagy előfordulhat, hogy az eszközök adatbázisában tárolt adatokkal rendelkezik, és le kell kérdezni az adatbázist annak meghatározásához, hogy melyik IoT hub-eszközt kell hozzárendelni.

Ez a cikk egy egyéni kiosztási szabályzatot mutat be egy írt C#Azure-függvény használatával. Két új IoT hub jön létre, amely egy *contoso kenyérpirítói osztályt* és egy *contoso Heat pumps-részleget*jelöl. Az üzembe helyezést kérő eszközökön regisztrálni kell egy regisztrációs azonosítót a következő utótagok egyikével, hogy el lehessen fogadni az üzembe helyezéshez:

* **-contoso-tstrsd-007**: contoso kenyérpirítók osztása
* **-contoso-hpsd-088**: contoso Heat pumps Division

Az eszközök a regisztrációs AZONOSÍTÓn alapuló kötelező utótagok egyikén lesznek kiépítve. Ezek az eszközök az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-ban foglalt kiépítési minta használatával lesznek szimulálva.

A cikk a következő lépéseket hajtja végre:

* Hozzon létre két contoso Division IoT-hubokat az Azure CLI használatával (**contoso kenyérpirító részleg** és **contoso Heat pumps Division**)
* Új csoportos regisztráció létrehozása Azure-függvény használatával az egyéni kiosztási szabályzathoz
* Hozzon létre két eszköz-szimulációs eszköz kulcsait.
* A fejlesztési környezet beállítása az Azure IoT C SDK-hoz
* Szimulálja az eszközöket, és ellenőrizze, hogy az egyéni kiosztási szabályzatban szereplő mintakód alapján van-e kiépítve.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015-as vagy újabb verziójának használata az ["asztali fejlesztés C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) a munkaterheléssel" beállítással.
* A [Git](https://git-scm.com/download/) legújabb verziójának telepített példánya.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>A kiépítési szolgáltatás és két IoT hub létrehozása

Ebben a szakaszban a Azure Cloud Shell használatával hozzon létre egy üzembe helyezési szolgáltatást és két IoT, amely a **contoso kenyérpirítói osztályt** és a **contoso Heat szivattyúk osztályt**jelképezi.

> [!TIP]
> A cikkben használt parancsok létrehozzák a kiépítési szolgáltatást és más erőforrásokat az USA nyugati régiójában. Azt javasoljuk, hogy hozza létre az erőforrásokat a legközelebbi, a Device kiépítési szolgáltatást támogató régióban. Az elérhető helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs futtatásával vagy az [Azure állapotlapjának](https://azure.microsoft.com/status/) megnyitásával, majd a „Device Provisioning Service” kifejezésre való kereséssel tekintheti meg. A parancsokban a hely megadható egy vagy több szóból álló formátumban is. például: westus, USA nyugati régiója, USA nyugati régiója stb. Az érték nem megkülönbözteti a kis-és nagybetűket. Ha többszavas formátumot használ a hely megadásához, tegye idézőjelek közé az értéket, például: `-- location "West US"`.
>

1. A Azure Cloud Shell használatával hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

    A következő példában létrehozunk egy *contoso-US-Resource-Group* nevű erőforráscsoportot a *westus* régióban. Ezt a csoportot az ebben a cikkben létrehozott összes erőforráshoz ajánlott használni. Ez a megközelítés megkönnyíti a tisztítást, miután elkészült.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. A Azure Cloud Shell használatával hozzon létre egy eszköz-kiépítési szolgáltatást az az [IOT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) paranccsal. A kiépítési szolgáltatás hozzá lesz adva a *contoso-US-Resource-Group*-hoz.

    Az alábbi példa egy *contoso-kiépítés-Service-1098* nevű kiépítési szolgáltatást hoz létre a *westus* helyen. Egyedi szolgáltatásnevet kell használnia. Hozzon létre saját utótagot a szolgáltatás nevében a **1098**helyett.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    A parancs végrehajtása több percet is igénybe vehet.

3. A Azure Cloud Shell használatával hozza létre a **contoso kenyérpirítók Division** IoT hubot az az [IoT hub Create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Az IoT hub hozzá lesz adva a *contoso-US-Resource-Group*-hoz.

    Az alábbi példa egy *contoso-kenyérpirítós-hub-1098* nevű IoT-hubot hoz létre a *westus* helyen. Egyedi hub-nevet kell használnia. Hozzon létre saját utótagot a hub neveként a **1098**helyett. Az egyéni kiosztási szabályzathoz tartozó mintakód `-toasters-`t igényel a hub nevében.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs végrehajtása több percet is igénybe vehet.

4. A Azure Cloud Shell használatával hozza létre a **contoso Heat szivattyúk Division** IoT hubot az az [IoT hub Create](/cli/azure/iot/hub#az-iot-hub-create) paranccsal. Ez az IoT hub a *contoso-US-Resource-Group*-hoz is hozzá lesz adva.

    A következő példa egy *contoso-heatpumps-hub-1098* nevű IoT-hubot hoz létre a *westus* helyen. Egyedi hub-nevet kell használnia. Hozzon létre saját utótagot a hub neveként a **1098**helyett. Az egyéni kiosztási szabályzathoz tartozó mintakód `-heatpumps-`t igényel a hub nevében.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    A parancs végrehajtása több percet is igénybe vehet.

## <a name="create-the-custom-allocation-function"></a>Az egyéni foglalási függvény létrehozása

Ebben a szakaszban egy Azure-függvényt hoz létre, amely megvalósítja az egyéni foglalási szabályzatot. Ez a függvény határozza meg, hogy melyik IoT hub-eszközt kell regisztrálni ahhoz, hogy a regisztrációs azonosítója tartalmazza a következő karakterláncot: **contoso-tstrsd-007** vagy **-contoso-hpsd-088**. Azt is beállítja, hogy az eszköz egy kenyérpirító vagy egy hőszivattyú legyen.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com). A kezdőlapon válassza az **+ erőforrás létrehozása**lehetőséget.

2. A *Keresés a piactéren* mezőbe írja be a "függvényalkalmazás" kifejezést. A legördülő listában válassza a **függvényalkalmazás**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

3. **Függvényalkalmazás** Létrehozás lap **alapok** lapján adja meg az új Function alkalmazás következő beállításait, majd válassza a **felülvizsgálat + létrehozás**lehetőséget:

    **Erőforráscsoport**: válassza ki a **contoso-US-Resource-Group** elemet, hogy az ebben a cikkben létrehozott összes erőforrást együtt őrizze meg.

    **Függvényalkalmazás neve**: adjon meg egy egyedi Function app-nevet. Ez a példa a **contoso-Function-app-1098-** et használja.

    **Közzététel**: Ellenőrizze, hogy a **kód** ki van-e választva.

    **Futásidejű verem**: válassza a **.net Core** elemet a legördülő menüből.

    **Régió**: válassza ki ugyanazt a régiót, mint az erőforráscsoportot. Ez a példa az **USA nyugati**régióját használja.

    > [!NOTE]
    > Alapértelmezés szerint a Application Insights engedélyezve van. A Application Insights nem szükséges ehhez a cikkhez, de segíthet megérteni és megvizsgálni az egyéni foglalással kapcsolatban felmerülő problémákat. Ha szeretné, letilthatja Application Insights a **figyelés** lapon, majd a **nem** lehetőséget választva **engedélyezheti a Application Insights**.

    ![Azure-függvényalkalmazás létrehozása az egyéni foglalási funkció üzemeltetéséhez](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget a Function alkalmazás létrehozásához. Az üzembe helyezés több percet is igénybe vehet. Ha befejeződik, válassza **az Ugrás erőforráshoz**lehetőséget.

5. A Function app **– Áttekintés** lap bal oldali paneljén válassza a **függvények** elem melletti **+** új függvény hozzáadásához.

    ![Függvény hozzáadása a függvényalkalmazás](./media/how-to-use-custom-allocation-policies/create-function.png)

6. A **.net – első lépések lap Azure functions** a **központi telepítési környezet kiválasztása** lépésnél válassza ki a **portálon belüli** csempét, majd kattintson a **Folytatás**gombra.

    ![Válassza ki a portál fejlesztési környezetét](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. A következő lapon a **függvény létrehozása** lépésnél válassza ki a **webhook + API** csempét, majd kattintson a **Létrehozás**elemre. Létrejön egy **HttpTrigger1** nevű függvény, és a portál megjeleníti a **Run. CSX** fájl tartalmát.

8. A szükséges Nuget-csomagok hivatkozása. A kezdeti eszköz Twin csomag létrehozásához az egyéni foglalási függvény két Nuget-csomagban definiált osztályokat használ, amelyeket be kell töltenie az üzemeltetési környezetbe. A Azure Functions a Nuget-csomagokat egy *function. Host* fájl hivatkozik. Ebben a lépésben egy *function. Host* fájlt ment és tölt fel.

    1. Másolja a következő sorokat a kedvenc szerkesztőjébe, és mentse a fájlt a számítógépre a *function. Host*néven.

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

    2. A **HttpTrigger1** függvénynél bontsa ki az ablak jobb oldalán található **fájlok megtekintése** lapot.

        ![Megnyitott fájlok megtekintése](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Válassza a **feltöltés**lehetőséget, keresse meg a **function. Proj** fájlt, és válassza a **Megnyitás** lehetőséget a fájl feltöltéséhez.

        ![Fájl feltöltése elem kiválasztása](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Cserélje le a **HttpTrigger1** függvény kódját a következő kódra, majd válassza a **Mentés**lehetőséget:

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

Ebben a szakaszban egy új beléptetési csoportot fog létrehozni, amely az egyéni kiosztási szabályzatot használja. Az egyszerűség kedvéért ez a cikk [szimmetrikus kulcsú tanúsítványokat](concepts-symmetric-key-attestation.md) használ a beléptetéshez. A biztonságosabb megoldás érdekében érdemes lehet az [X. 509 tanúsítvány-igazolást](concepts-security.md#x509-certificates) használni egy megbízhatósági lánc használatával.

1. Még mindig a [Azure Portalon](https://portal.azure.com)nyissa meg a kiépítési szolgáltatást.

2. Válassza a **regisztrációk kezelése** lehetőséget a bal oldali ablaktáblán, majd kattintson a **regisztrációs csoport hozzáadása** gombra az oldal tetején.

3. A **regisztrációs csoport hozzáadása**lapon adja meg a következő adatokat, majd kattintson a **Save (Mentés** ) gombra.

    **Csoportnév**: írja be a **contoso-Custom-lefoglalt-Devices**nevet.

    **Igazolás típusa**: válassza a **szimmetrikus kulcs**lehetőséget.

    **Kulcsok automatikus generálása**: ezt a jelölőnégyzetet már be kell jelölni.

    **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a** **központokhoz: válassza az egyéni (Azure-függvény használata)** lehetőséget.

    ![Egyéni foglalási beléptetési csoport hozzáadása a szimmetrikus kulcs igazolásához](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. A **beléptetési csoport hozzáadása**lapon válassza az **új IoT hub csatolása** lehetőséget az új kiosztott IoT-hubok összekapcsolásához.

    Hajtsa végre ezt a lépést mindkét kiosztott IoT-hubhoz.

    **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben létrehozta a kiosztott IoT hubokat.

    **IoT hub**: válassza ki a létrehozott részlegi hubok egyikét.

    **Hozzáférési szabályzat**: válassza a **iothubowner**lehetőséget.

    ![A IoT-hubok összekapcsolása a létesítési szolgáltatással](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. A **regisztrációs csoport hozzáadásakor**, ha a IoT hubok is össze lettek kapcsolva, ki kell választania azokat IoT hub csoportként a beléptetési csoport számára az alábbi ábrán látható módon:

    ![Az üzembe helyezési központ csoportjának létrehozása a regisztrációhoz](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. A **regisztrációs csoport hozzáadása**lapon görgessen le az **Azure-függvény kiválasztása** szakaszhoz, és válassza ki az előző szakaszban létrehozott Function alkalmazást. Ezután válassza ki a létrehozott függvényt, és válassza a mentés lehetőséget a beléptetési csoport mentéséhez.

    ![Válassza ki a függvényt, és mentse a beléptetési csoportot](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Miután mentette a beléptetést, nyissa meg újra, és jegyezze fel az **elsődleges kulcsot**. Először mentenie kell a beléptetést, hogy a kulcsok létrejöttek legyenek. A rendszer ezt a kulcsot használja a szimulált eszközökhöz tartozó egyedi eszközök kulcsának létrehozásához.

## <a name="derive-unique-device-keys"></a>Egyedi eszköz kulcsának származtatása

Ebben a szakaszban két egyedi eszköz kulcsát hozza létre. Egy szimulált kenyérpirító eszközhöz egy kulcs lesz használatban. A rendszer egy szimulált hőszivattyús eszközhöz használja a másik kulcsot.

Az eszköz kulcsának létrehozásához használja a korábban feljegyzett **elsődleges kulcsot** , hogy kiszámítsa az eszköz regisztrációs azonosítójának [HMAC sha256](https://wikipedia.org/wiki/HMAC) az egyes eszközökön, és az eredményt Base64 formátumra alakítsa át. A származtatott eszközök regisztrációs csoportokkal való létrehozásával kapcsolatos további információkért tekintse meg a [szimmetrikus kulcs igazolásának](concepts-symmetric-key-attestation.md)csoportos regisztrációk című szakaszát.

A jelen cikkben szereplő példához használja a következő két eszköz regisztrációs azonosítóját, és számítsa ki mindkét eszköz kulcsát. Mindkét regisztrációs azonosító érvényes utótaggal működik együtt az egyéni foglalási szabályzathoz tartozó mintakód használatával:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Linux-munkaállomások

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

### <a name="windows-based-workstations"></a>Windows-alapú munkaállomások

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

Ebben a szakaszban az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)létrehozásához használt fejlesztési környezetet készíti elő. Az SDK tartalmazza a szimulált eszközhöz tartozó mintakód kódját. A szimulált eszköz a beléptetést az rendszerindítási során fogja megkísérelni.

Ez a szakasz a Windows-alapú munkaállomás irányába mutat. Linux-példaként tekintse meg a virtuális gépek beállítása a [bérlős](how-to-provision-multitenant.md)való kiépítésével foglalkozó témakört.

1. Töltse le a [Csatlakozáskezelő felügyeleti csomag Build-szolgáltatását](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (a Visual Studio és az "asztali fejlesztés C++munkaterheléssel") telepítve legyenek a gépen a `CMake` telepítésének megkezdése **előtt** . Ha az előfeltételek teljesülnek, és a letöltés ellenőrzése megtörtént, telepítse a CMak-Build rendszerét.

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

    Ha `cmake` nem találja a C++ fordítót, előfordulhat, hogy a parancs futtatásakor hibaüzeneteket fog kiépíteni. Ha ez történik, próbálja meg futtatni a parancsot a [Visual Studio-parancssorban](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

Ebben a szakaszban egy **prov\_dev\_ügyfél\_minta** nevű kiépítési mintát frissít, amelyet a korábban beállított Azure IOT C SDK-ban talál.

Ez a mintakód szimulál egy eszköz rendszerindítási sorozatot, amely elküldi a kiépítési kérést az eszköz kiépítési szolgáltatásának példányára. A rendszerindítási folyamat azt eredményezi, hogy a kenyérpirító eszköz fel lesz ismerve, és hozzá lesz rendelve az IoT hub-hoz az egyéni kiosztási házirend használatával.

1. Az Azure Portalon válassza ki az eszközkiépítési szolgáltatás **Áttekintés** lapját, és jegyezze fel az **_Azonosító hatóköre_** értéket.

    ![Az eszközkiépítési szolgáltatás végpontadatainak kinyerése a portál paneljéről](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. A Visual Studióban nyissa meg azt a **azure_iot_sdks. SLN** -megoldást, amelyet korábban a CMAK futtatása hozott létre. A megoldásfájlnak a következő helyen kell lennie:

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

### <a name="simulate-the-contoso-toaster-device"></a>A contoso kenyérpirító eszközének szimulálása

1. A Toaster-eszköz szimulálásához keresse meg a `prov_dev_set_symmetric_key_info()`t a **prov\_dev\_ügyfél\_sample. c** címen, amely megjegyzésként szerepel.

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

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésének megadásához válassza az **Igen**lehetőséget, ha a Futtatás előtt szeretné újraépíteni a projektet.

    A következő kimenet egy példa arra, hogy a szimulált kenyérpirító eszköz sikeresen elindult-e, és csatlakozik a kiépítési szolgáltatáshoz, amelyet az egyéni kiosztási szabályzat rendel hozzá a IoT hub-hoz:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>A contoso hőszivattyú eszköz szimulálása

1. A hőszivattyús eszköz szimulálásához frissítse a `prov_dev_set_symmetric_key_info()` a **prov\_dev\_ügyfél\_sample. c** -ben, a HŐSZIVATTYÚ regisztrációs azonosítóját és a korábban létrehozott származtatott eszköz kulcsát. A **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** alább látható kulcs értéke szintén csak példaként van megadva.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Mentse a fájlt.

2. A Visual Studio menüjében válassza a **Debug** > **Start without debugging** (Hibakeresés > Indítás hibakeresés nélkül) lehetőséget a megoldás futtatásához. A projekt újraépítésének megadásához válassza az **Igen** lehetőséget a projekt újraépítéséhez a futtatása előtt.

    Az alábbi kimenet egy példa arra, hogy a szimulált hőszivattyús eszköz sikeresen elindult-e, és csatlakozik a kiépítési szolgáltatáshoz, amelyet a contoso Heat pumps IoT hubhez rendel hozzá az egyéni kiosztási szabályzat szerint:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Egyéni foglalási házirendek hibaelhárítása

Az alábbi táblázat a várt forgatókönyveket és az eredményül kapott hibakódokat mutatja. Ez a táblázat segítséget nyújt az egyéni kiosztási házirendekkel kapcsolatos hibák elhárításához a Azure Functions.

| Forgatókönyv | Regisztrációs eredmény a kiépítési szolgáltatástól | Az SDK-eredmények kiépítés |
| -------- | --------------------------------------------- | ------------------------ |
| A webhook a 200 OK értéket adja vissza a "iotHubHostName" értékkel egy érvényes IoT hub-állomásnévre. | Eredmény állapota: hozzárendelve  | Az SDK visszaadja a PROV_DEVICE_RESULT_OK a hub információi mellett |
| A webhook 200 OK értéket ad vissza a válaszban szereplő "iotHubHostName" értékkel, de üres sztringre vagy NULL értékre van állítva. | Eredmény állapota: sikertelen<br><br> Hibakód: CustomAllocationIotHubNotSpecified (400208) | Az SDK visszaadja PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| A webhook visszaadja a 401-et | Eredmény állapota: sikertelen<br><br>Hibakód: CustomAllocationUnauthorizedAccess (400209) | Az SDK visszaadja PROV_DEVICE_RESULT_UNAUTHORIZED |
| Egyéni regisztráció lett létrehozva az eszköz letiltásához | Eredmény állapota: letiltva | Az SDK visszaadja PROV_DEVICE_RESULT_DISABLED |
| A webhook a következő hibakódot adja vissza: > = 429 | A DPS-előkészítés számos alkalommal próbálkozik újra. Az újrapróbálkozási szabályzat jelenleg a következőket hajtja végre:<br><br>&nbsp;&nbsp;– újrapróbálkozás száma: 10<br>&nbsp;&nbsp;– kezdeti időköz: 1s<br>&nbsp;&nbsp;– növekmény: 9-es | Az SDK figyelmen kívül hagyja a hibát, és egy másik lekérési állapotjelző üzenetet küld a megadott időn belül |
| A webhook bármely más állapotkódot ad vissza | Eredmény állapota: sikertelen<br><br>Hibakód: CustomAllocationFailed (400207) | Az SDK visszaadja PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a jelen cikkben létrehozott erőforrásokkal való munkát, meghagyhatja őket. Ha nem tervezi tovább használni az erőforrásokat, a következő lépésekkel törölheti az ebben a cikkben létrehozott összes erőforrást a szükségtelen költségek elkerülése érdekében.

Az itt leírt lépések azt feltételezik, hogy a cikkben szereplő összes erőforrást a **contoso-US-Resource-Group**nevű erőforráscsoport utasításai szerint hozta létre.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Az erőforráscsoport törlése név szerint:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **szűrés név szerint...** szövegmezőbe írja be az erőforrásokat tartalmazó erőforráscsoport nevét, a **contoso-US-Resource-Group**nevet. 

3. Az erőforráscsoport jobb oldalán, az eredmények listájában válassza a **...** , majd az **erőforráscsoport törlése**elemet.

4. A rendszer kérni fogja, hogy erősítse meg az erőforráscsoport törlését. A megerősítéshez írja be ismét az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

* További információ: [IoT hub eszköz](concepts-device-reprovision.md) újraépítése 
* További részletekért lásd: [az előzőleg](how-to-unprovision-devices.md) kiépített eszközök kiépítése. 
