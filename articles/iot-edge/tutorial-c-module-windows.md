---
title: Az oktatóanyagban a C modul fejlesztése a Windows - az Azure IoT Edge |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozzon létre egy IoT Edge-modul C kóddal és üzembe helyezése az IoT Edge-es Windows-eszközök
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 93910fd0baeace9da474073960dbdb83251a1a63
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576557"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Oktatóanyag: Windows-eszközökhöz a C IoT Edge-modul fejlesztése

A Visual Studio használatával C kódjának fejlesztése és üzembe helyezése az Azure IoT Edge-es Windows eszközök. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * A Visual Studio használatával hozzon létre egy IoT Edge-modul, amely a .NET Core SDK 2.1-es alapul.
> * A Visual Studio és a Docker használatával egy Docker-rendszerkép létrehozása és közzététele a beállításjegyzékbe.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatókör

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy moduljában lévő **C** használatával **Visual Studio 2017**, és hogyan helyezhet üzembe, hogy egy **Windows eszköz**. Ha a Linux rendszerű eszközök modulok fejleszt, lépjen a [fejlesztése a C IoT Edge-modul a Linux rendszerű eszközök](tutorial-c-module.md) helyette. 

A következő táblázat segítségével fejleszteni és üzembe helyezni a C-modulok Windows-eszközökre a lehetőségek megismerése: 

| C | Visual Studio Code | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Windows-AMD64** |  | ![A Visual Studióban WinAMD64 C modulok fejlesztése](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban Windows tároló fejlesztését lehetővé tévő fejlesztési környezet beállítása: [Windows-eszközök IoT Edge-modulok fejlesztése](tutorial-develop-for-windows.md). Miután befejezte az oktatóanyag, helyben kell rendelkeznie a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Windows-eszközök Azure IoT Edge futtatásakor](quickstart.md).
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), konfigurált 15.7 vagy újabb verzió, a [Azure IoT Edge-eszközök](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) bővítmény.
* [A docker CE](https://docs.docker.com/install/) konfigurált Windows-tárolók futtatásához.
* C-hez készült Azure IoT SDK 

## <a name="create-a-module-project"></a>A modul projekt létrehozása

Az alábbi lépéseket az IoT Edge modul projekt a Visual Studio és az Azure IoT Edge Tools bővítmény használatával a .NET Core 2.0 SDK alapján hozzon létre. Miután létrehozott projektsablon, adjon hozzá új kódot úgy, hogy a modul üzeneteket a jelentett tulajdonságok alapján kiszűri. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C-megoldást, amelyet a saját kódjával testreszabhat.

1. Futtassa a Visual Studiót rendszergazdaként.

2. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget. 

3. Az új projekt ablakról, válassza ki a **Azure IoT** típus projektre, és válassza ki a **Azure IoT Edge** projekt. Nevezze át a projekt és a megoldás leíró valami hasonló **CTutorialApp**. A projekt létrehozásához válassza az **OK** lehetőséget. 

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-c-module-windows/new-project.png)

4. Az IoT Edge-alkalmazás és a modul ablakban, a projekt konfigurálásához a következő értékeket: 

   | Mező | Érték |
   | ----- | ----- |
   | Alkalmazásplatform | Törölje a jelet **Linux Amd64**, és ellenőrizze **WindowsAmd64**. |
   | Sablonválasztás | Válassza ki **C modul**. | 
   | A modul projekt neve | A modulnak adja a **CModule** nevet. | 
   | Docker-rendszerkép tárház | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének előre van töltve, a modul projekt neve értékből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/cmodule. |

   ![A céleszközön, modultípus és tároló-beállításjegyzék-projekt konfigurálása](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Válassza ki **OK** alkalmazza a módosításokat. 

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Manifest nasazení az IoT Edge-futtatókörnyezet osztanak meg a tárolóregisztrációs adatbázis hitelesítő adatait. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. A hitelesítő adatokat használja a **hozzáférési kulcsok** az Azure container registry szakaszában. 

1. A Visual Studio megoldáskezelőjében nyissa meg a **deployment.template.json** fájlt. 

2. Keresse meg a **registryCredentials** tulajdonság a $edgeAgent a kívánt tulajdonságok. 

3. Frissítse a hitelesítő adatait, ez a formátum a következő tulajdonság: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 


1. The data from the sensor in this scenario comes in JSON format. To filter messages in JSON format, import a JSON library for C. This tutorial uses Parson.

   1. Download the [Parson GitHub repository](https://github.com/kgabis/parson). Copy the **parson.c** and **parson.h** files into the **CModule** project.

   2. In Visual Studio, open the **CMakeLists.txt** file from the CModule project folder. At the top of the file, import the Parson files as a library called **my_parson**.

      ```
      add_library (my_parson parson.c parson.h)
      ```

   3. Add **my_parson** to the list of libraries in the **target_link_libraries** section of the CMakeLists.txt file.

   4. Save the **CMakeLists.txt** file.

   5. Open **CModule** > **main.c**. At the bottom of the list of include statements, add a new one to include `parson.h` for JSON support:

      ```c
      #include "parson.h"
      ```

2.  Az a **main.c** fájlt, adjon hozzá egy globális változó nevű `temperatureThreshold` messagesReceivedByInput1Queue változó mellett. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Keresse meg a `CreateMessageInstance` main.c függvényt. Cserélje le a következő kódot, amely néhány sornyi funkciókat ad hozzá a belső ha / egyébként utasítást: 

   ```c
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
   ```

   Az új kódsorokat az "else" utasítás egy új tulajdonság hozzáadása az üzenetet, amely az üzenetet egy riasztást, címkézi. Ezt a kódot feliratok riasztásai összes üzenetet, mert funkciók, amelyek csak akkor küld üzeneteket az IoT hub magas hőmérséklet, ha hozzáadunk. 

4. Keresse meg a `InputQueue1Callback` függvényt, és cserélje le a teljes funkció az alábbi kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt. Amikor egy üzenet érkezik, ellenőrzi-e a jelentett hőmérséklet meghaladja a küszöbértéket. Ha igen, majd azt továbbítja az üzenetet a kimeneti várólista keresztül. Ha nem, majd figyelmen kívül hagyja az üzenetet. 

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

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Adjon hozzá egy `moduleTwinCallback` függvényt. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Az összes modulnál megjelenik a saját modul iker, ahol konfigurálhatja a kódot közvetlenül a felhőből modul keretrendszeren belül fut.

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

6. Keresse meg a `SetupCallbacksForModule` függvény. Cserélje le a függvény a következő kódra, amely hozzáad egy **vagy ha** utasítás ellenőrizheti, ha az ikermodul frissítve lett. 

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

7. Mentse a main.c fájlt.

8. Nyissa meg a **deployment.template.json** fájlt. 

9. Adja hozzá a CModule ikermodult az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a `moduleContent` szakasz alján, az `$edgeHub` modul ikerdokumentuma után:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![A CModule ikermodul hozzáadása az üzembehelyezési sablonhoz](./media/tutorial-c-module-windows/module-twin.png)

1. Mentse a **deployment.template.json** fájlt.

## <a name="build-and-push-your-module"></a>És a modul leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzáadja a kódot a **CModule** üzenetek kiszűréséhez, ahol a gép jelentett hőmérséklet nem éri el a elfogadható küszöbértéket. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A következő paranccsal jelentkezzen be a fejlesztői gépen. Jelentkezzen be a felhasználónév, jelszó és az Azure container Registry bejelentkezési kiszolgáló. Kérheti, hogy ezeket az értéteket a **hozzáférési kulcsok** szakasz a tárolójegyzék az Azure Portalon.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

2. A Visual Studio megoldáskezelőben kattintson a jobb gombbal a projekt nevét, amely kíván létrehozni. Alapértelmezés szerint ez **AzureIotEdgeApp1** és a egy Windows-modul létrehozásakor, mert a bővítmény lehet **Windows.Amd64**. 

3. Válassza ki **hozhat létre, és küldje le az IoT Edge-modulok**. 

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és más megoldás fájlokat tárolja. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból. 

## <a name="deploy-modules-to-device"></a>Eszköz modulok telepítése

A Visual Studio cloud explorer és az Azure IoT Edge Tools bővítmény használatával a modul projekt telepítése az IoT Edge-eszköz. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a **deployment.json** config mappában található fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig. 

1. A Visual Studio cloud explorer bontsa ki az erőforrásokat az IoT-eszközök listájának megtekintéséhez. 

2. Kattintson a jobb gombbal az IoT Edge-eszköz, amely szeretne kapni a központi telepítés nevét. 

3. Válassza ki **. üzemelő példány létrehozása**.

4. A Fájlkezelőben válassza ki a **deployment.windows-amd64** , hogy a megoldás config mappában található fájl. 

5. Frissítse a cloud explorer felsorolva az eszköz a telepített modulok megtekintéséhez. 


## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak. 

Használhatja az IoT Edge Tools bővítmény üzenetek megtekintéséhez, hogy az IoT Hub érkezik. 

1. A Visual Studio cloud explorer válassza ki az IoT Edge-eszköz nevét. 

2. Az a **műveletek** listáról válassza ki **figyelési D2C üzenet Start**. 

3. Az IoT Hub érkező üzenetek megjelenítése. Az üzenetek kimarad, míg igénybe vehet, mert az IoT Edge-eszköz kapni az új központi telepítést, és indítsa el a modult. Azt a CModule kóddal végzett módosításokat, majd várjon, amíg a gép hőmérséklet 25 fok eléri üzenetek elküldése előtt. Hozzáadja az üzenettípus **riasztási** , az üzenetek, eléri a hőmérséklet-küszöbérték. 

   ![Az IoT Hub érkező üzenetek megtekintése](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul ikereszköz szerkesztése

CModule ikermodulja segítségével állítsa be a hőmérséklet küszöbérték 25 fok. Az ikermodul segítségével anélkül, hogy a modul kódot kellene működésének módosításához.

1. A Visual Studióban nyissa meg a **deployment.windows-amd64.json** fájlt. (Nem a deployment.template fájlt. Ha nem látja az üzembe helyezés eredményezi a konfigurációs fájlt a megoldáskezelőben, válassza ki a **minden fájl megjelenítése** ikonra az explorer eszköztárán.)

2. Keresse meg a CModule iker, és módosítsa az értéket, a **temperatureThreshold** paraméter a legújabb jelentett hőmérséklet meghaladja a 10 értéket 5 fokkal új hőmérséklet. 

3. Mentse a **deployment.windows-amd64.json** fájlt.

4. Hajtsa végre újra a központi telepítési lépéseket az üzemelő példány frissítése jegyzékfájl alkalmazza az eszközre. 

5. A bejövő eszköz a felhőbe irányuló üzenetek figyeléséhez. Az üzenetek, amíg az új hőmérséklet küszöbérték elérése kell megjelennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások használt ebben a cikkben díjak elkerülése érdekében. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok hozhat létre, hogy többet is megtudhat [saját IoT Edge-modulok fejlesztése](module-development.md) , illetve [fejlesztése a Visual Studióval modulok](how-to-visual-studio-develop-module.md). Továbbra is be a következő oktatóanyagok az Azure IoT Edge segítségével üzembe helyezheti az Azure cloud services, a peremhálózaton adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
