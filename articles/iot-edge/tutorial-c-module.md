---
title: Azure IoT Edge C – oktatóanyag | Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult C-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön.
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 31560cbd4d8b4572ce930db7ffb8753f3e4a4bc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425918"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Oktatóanyag: C-alapú IoT Edge-modul fejlesztése és üzembe helyezése szimulált eszközön

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

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.

Felhőerőforrások:

* Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C/C++-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) a Visual Studio Code-hoz.
* [Azure IoT Edge-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code-hoz.
* [Docker CE](https://docs.docker.com/install/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Ebben az oktatóanyagban a VS Code-hoz készült Azure IoT Edge bővítménnyel épít fel egy modult és hoz létre egy **tárolórendszerképet** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Ehhez az oktatóanyaghoz bármilyen Docker-kompatibilis beállításjegyzéket használhat. A felhőben elérhető két népszerű Docker-beállításjegyzékszolgáltatás az [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

Az alábbi Azure CLI-parancs egy regisztrációs adatbázist hoz létre az **IoTEdgeResources** nevű erőforráscsoportban. Cserélje le az **{acr_name}** elemet az adatbázis egyedi nevére. 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

Kérje le az adatbázis hitelesítő adatait. 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

Másolja ki a **felhasználónevet** és valamelyik jelszót. Ezeket az értékeket az oktatóanyag későbbi részében fogja használni, amikor közzéteszi a Docker-rendszerképet a beállításjegyzékben, és amikor a beállításjegyzékhez tartozó hitelesítő adatokat ad hozzá az Edge-futtatókörnyezethez. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
A következő lépések azt mutatják be, hogyan hozhat létre egy .NET core 2.0-alapú IoT Edge-modulprojektet a Visual Studio Code és az Azure IoT Edge bővítmény használatával.
1. A Visual Studio Code-ban válassza a **View** > **Integrated Terminal** (Nézet, Integrált terminál) elemet a VS Code integrált terminál megnyitásához.
2. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 
3. A parancskatalógusban írja be és futtassa az **Azure: Sign in** (Azure: bejelentkezés) parancsot, és az utasításokat követve jelentkezzen be Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
4. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: új IoT Edge-megoldás) parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához: 
   1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni. 
   2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
   3. A modul sablonjaként válassza a **C-modult**. 
   4. A modulnak adja a **CModule** nevet. 
   5. Adja meg az előző szakaszban létrehozott Azure Container Registryt az első modul rendszerképadattáraként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére. A sztring végül a következőképpen néz ki: **\<tárolóregisztrációs adatbázis neve\>.azurecr.io/cmodule**.
 
4. A VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. Egy **modules** mappát, egy **.vscode** mappát, egy üzembe helyezési jegyzéksablonfájlt és egy **.env** fájlt tartalmaz. A modul alapértelmezett kódja folyamatmodulként van megvalósítva. 

5. A JSON-formátumú üzenetek szűréséhez egy C-hez készült JSON-kódtárat kell importálni. Bármely JSON-kódtárat választhatja, vagy írhat egy sajátot is a JSON elemzéséhez a C-moduljában. Az alábbi lépésekben a [Parson](https://github.com/kgabis/parson) kódtárat használjuk példaként.
   1. Töltse le a **parson.c** és a **parson.h** fájlt a [Parson Github-adattárból](https://github.com/kgabis/parson). Ezután másolja be ezt a két fájlt a **CModule** mappába.
   2. Nyissa meg a **modules** > **CModule** > **CMakeLists.txt** fájlt. Adja hozzá az alábbi sorokat, amelyek my_parson néven importálják a parson-kódtárat.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. A **CMakeLists.txt** `target_link_libraries` szakaszában adja hozzá a következőt: `my_parson`.
   4. Nyissa meg a **modules** > **CModule** > **main.c** fájlt. Az include szakasz alján adja hozzá az alábbi kódot a JSON-t támogató `parson.h` fájl beillesztése céljából:

      ```c
      #include "parson.h"
      ```

6. Adja hozzá a(z) `temperatureThreshold` globális változót az include szakasz után. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Cserélje le a teljes `CreateMessageInstance` függvényt az alábbi kódra. Ez a függvény lefoglal egy környezetet a visszahívás számára. 

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

8. Cserélje le a teljes `InputQueue1Callback` függvényt az alábbi kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt. 

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

9. Adjon hozzá egy `moduleTwinCallback` függvényt. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

10. Adja hozzá a(z) `moduleTwinCallback` egy új visszahívását a(z) `SetupCallbacksForModule` függvényben. Cserélje le a teljes `SetupCallbacksForModule` függvényt az alábbi kódra.

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

11. Mentse el ezt a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és hozzáadott egy kódot a CModule modulhoz, amely kiszűri az olyan üzeneteket, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe, ahonnan a modul rendszerképét leküldheti az Azure Container Registrybe: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registryből kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy kérje le őket ismét az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. Az `$edgeAgent` a fájlból tudja meg, hogy üzembe kell helyezni két modult: a **tempSensor** és a **CModule** modult. A `CModule.image` értéke a rendszerkép Linux amd64-es verziójára lett beállítva. További információt az üzembehelyezési jegyzékekről az [IoT Edge-modulok használatát, konfigurálását és újrahasznosítását](module-composition.md) ismertető cikkben olvashat.

3. A **deployment.template.json** fájl tartalmaz egy **registryCredentials** szakaszt, amely tárolja a Docker beállításjegyzék hitelesítő adatait. A tényleges felhasználónév és jelszó párokat az .env fájl tárolja, amelyet a git figyelmen kívül hagy.

4. Adja hozzá a CModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után: 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Mentse el ezt a fájlt.
5. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build IoT Edge solution** (IoT Edge-megoldás összeállítása) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy `deployment.json` fájlt egy új **config** mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a `CModule.dll` elemet, majd leküldi a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A rendszerkép címe a `module.json` fájlban lévő információk alapján áll össze az **\<adattár\>:\<verzió\>-\<platform\>** formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: **registryname.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

1. Az Azure IoT-eszközkészlet bővítmény konfigurálása kapcsolati sztringgel az IoT hubhoz: 
    1. Nyissa meg a VS Code Explorert a **View** > **Explorer** (Nézet, Explorer) elem kiválasztásával. 
    2. Az Explorerben kattintson az **AZURE IOT HUB DEVICES** (Azure IoT Hub-eszközök), majd a **...** elemre. Kattintson a **Select IoT Hub** (IoT Hub kiválasztása) lehetőségre. Kövesse az utasításokat az Azure-fiókba való bejelentkezéshez és az IoT-központ kiválasztásához. 
       A telepítést elvégezheti a **Set IoT Hub Connection String** (IoT Hub kapcsolati sztring beállítása) elemre kattintva is. A felugró ablakban adja meg annak az IoT-központnak a kapcsolati sztringjét, amelyhez az IoT Edge-eszköz csatlakozik.

2. Az Azure Iot Hub Devices Explorerben kattintson a jobb gombbal az IoT Edge-eszközére, majd kattintson a **Create Deployment for IoT Edge device** (Üzembe helyezés létrehozása Edge-eszközhöz) elemre. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre.

3. Kattintson a frissítés gombra. Látható, hogy az új **CModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett. 

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

1. Az IoT Hubra érkező adatok monitorozásához kattintson a **...** elemre, majd válassza a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Az adatok monitorozásának leállításához futtassa a parancskatalógusban az **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: D2C üzenetek monitorozásának leállítása) parancsot. 
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza ki az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, és kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítheti a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt, és megkeresheti helyileg a futó modulokat a Docker Explorerben. A helyi menüben kattintson a **Show Logs** (Naplók megjelenítése) lehetőségre az integrált terminálban való megtekintéshez.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat és konfigurációkat.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

> [!IMPORTANT]
> Az Azure-erőforráscsoportok törlése nem vonható vissza. A törlést követően az erőforráscsoport és a benne foglalt erőforrások véglegesen törölve lesznek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Ha csak az IoT Hubot szeretné törölni, hajtsa végre az alábbi parancsot a saját hubja és a saját erőforráscsoportja nevével:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


A teljes erőforráscsoport név alapján való törléséhez:

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha saját modulokat kíván létrehozni, a témáról további információt [a C-modulok a Visual Studio Code és az Azure IoT Edge segítségével történő fejlesztését](how-to-develop-c-module.md) ismertető cikkben talál. Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
