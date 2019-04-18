---
title: Az oktatóanyag egyéni C modul - létrehozása az Azure IoT Edge |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult C-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: eeaff4769dba5b6e6951665d09cd12d13f22af07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273711"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Oktatóanyag: A C IoT Edge-modul fejlesztése és üzembe helyezése a szimulált eszköz

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * C-kódban írt IoT Edge-modul létrehozása a Visual Studio Code-dal
> * Docker-rendszerkép létrehozása és közzététele a tárolóregisztrációs adatbázisban a Visual Studio Code és a Docker használatával
> * A modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhatja az Azure virtuális gép IoT Edge-eszköz esetében ez a rövid útmutató lépéseit követve [Linux](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md). 

   >[!TIP]
   >Ez az oktatóanyag a Visual Studio Code használatával egy Linux-tárolók használatával C modul fejlesztése. Ha azt szeretné, C, a Windows-tárolók fejlesztéshez, szeretné használni a Visual Studio 2017-ben. További információkért lásd: [használatát a Visual Studio 2017 fejlesztésről és hibakeresésről modulok az Azure IoT Edge](how-to-visual-studio-develop-module.md).

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/).
* [C/C++-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) a Visual Studio Code-hoz.
* [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban használhatja az Azure IoT-eszközök a Visual Studio Code fel egy modult, és hozzon létre egy **tárolórendszerkép** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.

Minden olyan Docker-kompatibilis beállításjegyzéket a tárolólemezképek tárolására használható. Két népszerű Docker beállításjegyzék szolgáltatások [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja.

Ha még nem rendelkezik egy tároló-beállításjegyzéket, az alábbi lépésekkel hozzon létre egy új Azure-ban:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

2. Adja meg a következő értékeket a tárolóregisztrációs adatbázis létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Beállításjegyzék neve | Egyedi nevet adjon meg. |
   | Előfizetés | A legördülő listából válasszon egy előfizetést. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Rendszergazdai felhasználó | Állítsa **Engedélyezés** értékre. |
   | SKU | Válassza az **Alapszintű** lehetőséget. |

5. Kattintson a **Létrehozás** gombra.

6. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, majd válassza a **Hozzáférési kulcsok** elemet.

7. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. A tároló-beállításjegyzékbe való hozzáférés biztosításához az oktatóanyag későbbi részében használja ezeket az értékeket.

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
A következő lépések show, hogyan hozhat létre egy IoT Edge-modul projekt alapján a .NET core 2.0 használatával, a Visual Studio Code és az Azure IoT-eszközöket.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Létrehozhat egy C-megoldást, amelyet a saját kódjával testreszabhat.

1. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet.

2. A parancskatalógus, írja be, és futtassa a parancsot **Azure: Jelentkezzen be a** , és kövesse az utasításokat követve jelentkezzen be az Azure-fiókjával. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

3. A parancskatalógus, írja be, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **C modul**. |
   | Provide a module name (Modulnév megadása) | A modulnak adja a **CModule** nevet. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/cmodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-c-module/repository.png)

A VS Code-ablak betölti az IoT Edge megoldás munkaterület öt legfelső szintű összetevőkkel. A **modulok** mappa tartalmazza a C kódjának a modul és a egy tárolórendszerképbe, a modul docker-fájlok. Az **\.env** fájl a tárolóregisztrációs adatbázis hitelesítő adatait tárolja. A **deployment.template.json** fájl az IoT Edge-futtatókörnyezet által a modulok eszközön való üzembe helyezéséhez használt információkat tartalmazza. És **deployment.debug.template.json** tárolók a hibakeresési verzió-modulok fájlt. Ebben az oktatóanyagban nem fogja szerkeszteni a **\.vscode** mappát vagy a **\.gitignore** fájlt.

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Adja hozzá a kódot a C-modul, amely lehetővé teszi, hogy ellenőrizze, hogy a gép jelentett hőmérséklet túllépte egy biztonságos küszöbértéket. Túl magas hőmérséklet esetén a modul ad hozzá egy riasztási paraméter az üzenetet az IoT hubhoz az adatok elküldése előtt. 

1. Ebben a forgatókönyvben az érzékelőről származó adatok JSON formátumban szerepelnek. A JSON formátumú üzenetek szűréséhez importáljon egy C-hez készült JSON-kódtárat. Ez az oktatóanyag Parson-kódtárat használ.

   1. Töltse le a [Parson GitHub-adattár](https://github.com/kgabis/parson). Másolja a **parson.c** és a **parson.h** fájlokat a **CModule** mappába.

   2. Nyissa meg a **modules** > **CModule** > **CMakeLists.txt** fájlt. A fájl tetején importálja a Parson-fájlokat **my_parson** nevű kódtárként.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adja a **my_parson** kódtárat a CMakeLists.txt fájl **target_link_libraries** függvényében lévő kódtárak listájához.

   4. Mentse a **CMakeLists.txt** fájlt.

   5. Nyissa meg a **modules** > **CModule** > **main.c** fájlt. Az a lista alján utasításokat tartalmaznak, adjon hozzá egy új felvenni `parson.h` JSON-támogatás:

      ```c
      #include "parson.h"
      ```

1. A **main.c** fájlban adjon hozzá egy `temperatureThreshold` nevű globális változót az include szakasz után. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Cserélje le a teljes `CreateMessageInstance` függvényt az alábbi kódra. Ez a függvény lefoglal egy környezetet a visszahívás számára.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

1. Cserélje le a teljes `InputQueue1Callback` függvényt az alábbi kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt.

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Adjon hozzá egy `moduleTwinCallback` függvényt. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Az összes modulnál megjelenik a saját modul iker, ahol konfigurálhatja a kódot közvetlenül a felhőből modul keretrendszeren belül fut.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Cserélje le a `SetupCallbacksForModule` függvényt az alábbi kódra.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Mentse a main.c fájlt.

1. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. A fájl arra utasítja az IoT Edge-ügynök, mely modulok üzembe helyezéséhez ebben az esetben **tempSensor** és **CModule**, és az IoT Edge hubot jelzi, hogyan irányítsa az üzenetek közöttük. A Visual Studio Code-bővítmény automatikusan kitölti a legtöbb, hogy a központi telepítési sablont a szükséges, de győződjön meg arról, hogy minden működik-e a megoldás pontos információ: 

   1. Az IoT Edge az alapértelmezett platform értékre van állítva **amd64** a VS Code állapotsorban, ami azt jelenti, a **CModule** a lemezkép verziószámát Linux AMD64-es értékre van állítva. Módosítsa az alapértelmezett platform az állapotsorban **amd64** való **arm32v7** , amely az IoT Edge-eszköz architektúra esetén. 

      ![A modul lemezképplatformmal frissítése](./media/tutorial-c-module/image-platform.png)

   2. Ellenőrizze, hogy a sablonban a megfelelő modulnév van-e megadva az alapértelmezett **SampleModule** név helyett, amelyet az IoT Edge-megoldás létrehozásakor módosított.

   3. A **registryCredentials** szakasz tárolja a Docker registry hitelesítő adataival, úgy, hogy az IoT Edge-ügynök a modul rendszerképének kérheti le. A tényleges felhasználónév–jelszó párokat az .env fájl tárolja, amelyet a Git figyelmen kívül hagy. Ha még nem tette, vegye fel a hitelesítő adatait a .env fájlba.  

   4. Ha szeretne további tudnivalók a központi telepítési jegyzékek, [megtudhatja, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

1. Adja hozzá a CModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után:

   ```json
       "CModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![A CModule ikermodul hozzáadása az üzembehelyezési sablonhoz](./media/tutorial-c-module/module-twin.png)

1. Mentse a **deployment.template.json** fájlt.

## <a name="build-and-push-your-solution"></a>És a megoldás leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzá kódot a CModule, hogy a rendszer kiszűri azokat az üzeneteket a gép jelentett hőmérséklet esetén az elfogadható határokon belül. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet.

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe. Be kell jelentkeznie az Azure Container Registry hitelesítő adataival, hogy a beállításjegyzékbe küldhesse le a modul rendszerképét.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registryből kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy kérje le őket ismét az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként létrehoz egy `deployment.json` fájlt egy új **config** mappában. A deployment.json fájl információit a rendszer a frissített sablonfájlból, a tárolóregisztrációs adatbázis hitelesítő adatainak tárolásához használt .env fájlból és a CModule mappában lévő module.json fájlból gyűjti be.

Ezután a Visual Studio Code a következő két parancsot futtatja az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a `CModule.dll` elemet, majd leküldi a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba.

A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A rendszerkép címe a `module.json` fájlban lévő információk alapján áll össze az **\<adattár\>:\<verzió\>-\<platform\>** formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Ha hibaüzenet jelenik meg, és a modul leküldéses próbál, győződjön meg arról, a következő ellenőrzések:
>* Jelentkezett be a Docker, a Visual Studio Code hitelesítő adatok használatával a tárolóregisztrációs adatbázisból? Ezeket a hitelesítő adatokat eltérnek az Azure Portalra való bejelentkezéshez használja azokat.
>* A tároló-beállításjegyzékbe a helyes? Nyissa meg **modulok** > **cmodule** > **module.json** , és keresse meg a **tárház** mező. A lemezképtár hasonlóan kell kinéznie  **\<registryname\>.azurecr.io/cmodule**. 
>* Tárolók rendszert futtató fejlesztői gépen azonos típusú készít? Visual Studio Code-ot alapértelmezett Linux-tárolók amd64. A fejlesztői gépén arm32v7 Linux-tárolók fut, a platform a tárolóplatform megfelelően a VS Code-ablak alján kék állapotsávját frissíteni. Windows-tárolókként C modulok nem hozható létre. 

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. Az Azure IoT Hub-eszközkészlet bővítmény (korábbi nevén Azure IoT-eszközkészlet bővítmény) használata a Visual Studio Code-modulok is telepítheti. Már elő van készítve egy üzembehelyezési jegyzék a forgatókönyvhöz, a **deployment.json** fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

1. Futtassa a VS Code parancskatalógus **Azure IoT hubbal: Válassza ki az IoT Hub**.

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot.

3. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt.

4. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-c-module/create-deployment.png)

5. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

6. Kattintson a frissítés gombra. Látható, hogy az új **CModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

Az IoT Edge-eszköz magát, a központi telepítési modulok, a parancs állapotát megtekintheti `iotedge list`. Négy modult kell látnia: a két IoT Edge-futtatókörnyezeti modult, a tempSensort és az ebben az oktatóanyagban létrehozott egyéni modult. Az összes modul elindítása eltarthat néhány percig, ezért ha nem látja mindet azonnal, futtassa újra a parancsot.

Az egyes modulok által létrehozott üzenetek megtekintéséhez használja az `iotedge logs <module name>` parancsot.

Az IoT Hubra beérkező üzenetek a Visual Studio Code segítségével tekinthetők meg.

1. Az IoT Hubra érkező adatok monitorozásához kattintson a **...** elemre, majd válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Adatok monitorozásának leállításához futtassa a parancsot **Azure IoT hubbal: D2C üzenet monitorozásának leállítása** a parancskatalógus.
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza ki az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, és kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítheti a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt, és megkeresheti helyileg a futó modulokat a Docker Explorerben. A helyi menüben kattintson a **Show Logs** (Naplók megjelenítése) lehetőségre az integrált terminálban való megtekintéshez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [a C-modulok a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](how-to-develop-c-module.md) ismertető cikkben talál. Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal](tutorial-store-data-sql-server.md)

