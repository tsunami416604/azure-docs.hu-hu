---
title: Útmutató a C modul fejlesztéséhez a Windows rendszerhez – Azure IoT Edge | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy IoT Edge-modult C kóddal, és hogyan helyezheti üzembe azt egy rendszert futtató Windows-eszközön IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b69000e251bb71bc08ea97b24a7422daf2cd89e4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813826"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Oktatóanyag: C IoT Edge modul fejlesztése Windows-eszközökhöz

A Visual Studióval fejlesztheti C-kódját, és telepítheti azt egy Azure IoT Edge rendszert futtató Windows-eszközre. 

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * A Visual Studio használatával hozzon létre egy IoT Edge modult, amely a C SDK-ra épül.
> * A Visual Studio és a Docker használatával hozzon létre egy Docker-rendszerképet, és tegye közzé azt a beállításjegyzékben.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet a **C** modult a **Visual Studio 2019**használatával, és hogyan telepítheti azt egy **Windows-eszközre**. Ha Linux-eszközökhöz fejleszt modulokat, ugorjon [a C IoT Edge modul fejlesztése Linux-eszközökhöz](tutorial-c-module.md) című lépésre. 

A következő táblázat segítségével megismerheti a C modulok Windows-eszközökre való fejlesztésének és üzembe helyezésének lehetőségeit: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![C modulok fejlesztése a WinAMD64 a Visual Studióban](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt el kellett volna végeznie az előző oktatóanyagot a Windows-tárolók fejlesztéséhez szükséges fejlesztési környezet beállításához: [IoT Edge-modulok fejlesztése Windows-eszközökhöz](tutorial-develop-for-windows.md). Az oktatóanyag befejezése után a következő előfeltételek szükségesek: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge rendszert futtató Windows-eszköz](quickstart.md).
* Egy tároló-beállításjegyzék, például [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* A [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) konfigurálva van a [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítménnyel.
* A [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) Windows-tárolók futtatására van konfigurálva.
* Telepítse a Windows x64 rendszerhez készült Azure IoT C SDK-t a vcpkg-on keresztül:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Ha a Visual Studio 2017-es verzióját (15,7-es vagy újabb verziót) használja, töltse le és telepítse [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for vs 2017 alkalmazást a Visual Studio piactérről

## <a name="create-a-module-project"></a>Modul-projekt létrehozása

A következő lépések egy IoT Edge modul-projektet hoznak létre, amely a Visual Studio és a Azure IoT Edge Tools bővítmény használatával a C SDK-ra épül. Miután létrehozta a projekt sablonját, vegyen fel új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságok alapján. 

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C-megoldást, amelyet a saját kódjával testreszabhat.

1. Indítsa el a Visual Studio 2019 alkalmazást, és válassza az **új projekt létrehozása**lehetőséget.

2. Az új projekt ablakban keresse meg **IoT Edge** projektet, és válassza ki a **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra. 

   ![Új Azure IoT Edge projekt létrehozása](./media/tutorial-c-module-windows/new-project.png)

3. Az új projekt konfigurálása ablakban nevezze át a projektet és a megoldást valami olyan leíróra, mint a **CTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra. 

   ![Új Azure IoT Edge projekt konfigurálása](./media/tutorial-c-module-windows/configure-project.png)

4. A IoT Edge alkalmazás és modul ablakban konfigurálja a projektet a következő értékekkel: 

   | Mező | Value |
   | ----- | ----- |
   | Sablonválasztás | Válassza a **C modul**lehetőséget. | 
   | Modul projekt neve | A modulnak adja a **CModule** nevet. | 
   | Docker-rendszerkép tárháza | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló képe előre fel van töltve a modul projekt neve értékből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> A rendszerkép utolsó tárháza a \<következőhöz\>hasonló: beállításjegyzék neve. azurecr.IO/cmodule. |

   ![A projekt konfigurálása a céleszköz, a modul típusa és a tároló-beállításjegyzék számára](./media/tutorial-c-module-windows/add-application-and-module.png)

5. A módosítások alkalmazásához kattintson **az OK gombra** . 

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

Az üzembe helyezési jegyzék megosztja a tároló beállításjegyzékének hitelesítő adatait a IoT Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure Container Registry **hozzáférési kulcsok** szakaszának hitelesítő adatait. 

1. A Visual Studio Solution Explorerben nyissa meg a **Deployment. template. JSON** fájlt. 

2. Keresse meg a **registryCredentials** tulajdonságot a $edgeAgent kívánt tulajdonságban. 

3. Frissítse a tulajdonságot a hitelesítő adataival, a következő formátumban: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Mentse a Deployment. template. JSON fájlt. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modul kódja üzeneteket fogad egy bemeneti várólistán, és egy kimeneti várólistán keresztül továbbítja azokat. Vegyünk fel néhány további kódot, hogy a modul feldolgozza az üzeneteket a peremen, mielőtt továbbítaná őket a IoT Hubba. Frissítse a modult úgy, hogy az minden üzenetben elemezze a hőmérsékleti adatokat, és csak akkor küldje el az üzenetet IoT Hub, ha a hőmérséklet meghaladja az adott küszöbértéket. 


1. Ebben a forgatókönyvben az érzékelőről származó adatok JSON formátumban szerepelnek. A JSON formátumú üzenetek szűréséhez importáljon egy C-hez készült JSON-kódtárat. Ez az oktatóanyag Parson-kódtárat használ.

   1. Töltse le a [plébános GitHub-tárházát](https://github.com/kgabis/parson). Másolja a **plébános. c** és a **plébános. h** fájlokat a **CModule** projektbe.

   2. A Visual Studióban nyissa meg a **CMakeLists. txt** fájlt a CModule Project mappából. A fájl tetején importálja a Parson-fájlokat **my_parson** nevű kódtárként.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adja hozzá a **my_parson** a CMakeLists. txt fájl **target_link_libraries** szakaszában található könyvtárak listájához.

   4. Mentse a **CMakeLists.txt** fájlt.

   5. Nyissa meg a **CModule** > **Main. c**. A belefoglalási utasítások listájának alján adjon hozzá egy újat a JSON- `parson.h` támogatáshoz:

      ```c
      #include "parson.h"
      ```

2.  A **Main. c** fájlban adjon hozzá egy nevű `temperatureThreshold` globális változót a messagesReceivedByInput1Queue változó mellett. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Keresse meg `CreateMessageInstance` a függvényt a Main. c-ben. Cserélje le a belső if-Else utasítást a következő kódra, amely néhány sornyi funkciót is felvesz: 

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

   Az else utasításban szereplő új sorok új tulajdonságot adhatnak hozzá az üzenethez, amely riasztásként felcímkézi az üzenetet. Ez a kód minden olyan üzenetet felcímkéz, amely riasztásként jelenik meg, mert olyan funkciókat adunk hozzá, amelyek csak akkor küldenek üzeneteket IoT Hub, ha magas hőmérsékletet jelentenek. 

4. Keresse meg `InputQueue1Callback` a függvényt, és cserélje le a teljes függvényt a következő kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt. Üzenet érkezésekor ellenőrzi, hogy a jelentett hőmérséklet meghaladja-e a küszöbértéket. Ha igen, akkor a kimeneti várólistán keresztül továbbítja az üzenetet. Ha nem, akkor figyelmen kívül hagyja az üzenetet. 

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
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

6. Keresse meg `SetupCallbacksForModule` a függvényt. Cserélje le a függvényt a következő kódra, amely egy **Else if** utasítást tartalmaz, amely azt vizsgálja, hogy a Twin modul frissítve lett-e. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Mentse a Main. c fájlt.

8. Nyissa meg a **Deployment. template. JSON** fájlt. 

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

## <a name="build-and-push-your-module"></a>Modul létrehozása és leküldése

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a **CModule** , hogy kiszűrje azokat az üzeneteket, amelyekben a jelentett gépi hőmérséklet az elfogadható küszöbérték alá esik. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba. 

1. A következő parancs használatával jelentkezzen be a Docker-be a fejlesztői gépre. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure Container registryből. Ezeket az értékeket a beállításjegyzék **hozzáférési kulcsok** részéből kérheti le a Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Biztonsági figyelmeztetés jelenhet meg, `--password-stdin`amely a használatát javasolja. Habár az ajánlott eljárás az éles környezetekben javasolt, az oktatóanyag hatókörén kívül esik. További információkért lásd a [Docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referenciáját.

2. A Visual Studio Solution Explorerben kattintson a jobb gombbal a létrehozni kívánt projekt nevére. Az alapértelmezett név a **AzureIotEdgeApp1** , és a Windows-modul létrehozása óta a bővítménynek **Windows. Amd64**-nek kell lennie. 

3. Válassza ki **az IoT Edge-modulok kiépítése és leküldése**lehetőséget. 

   A build és a push parancs három műveletet indít el. Először létrehoz egy új mappát a **konfigurációban** , amely tartalmazza a teljes telepítési jegyzékfájlt, kiépítve a központi telepítési sablonban található információkat, és egyéb megoldásokat is tartalmaz. Másodszor, futtatja `docker build` a tároló rendszerképét a célként megadott architektúra megfelelő Docker alapján. Ezután futtatja `docker push` , hogy leküldi a rendszerkép-tárházat a tároló-beállításjegyzékbe. 

## <a name="deploy-modules-to-device"></a>Modulok üzembe helyezése az eszközön

A Visual Studio Cloud Explorer és a Azure IoT Edge Tools bővítmény használatával telepítse a modul-projektet a IoT Edge eszközre. Már van előkészítve egy üzembe helyezési jegyzékfájl a forgatókönyvhöz, a **telepítési. JSON** fájl a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Ellenőrizze, hogy a IoT Edge eszköz működik-e. 

1. A Visual Studio Cloud Explorerben bontsa ki az erőforrásokat a IoT-eszközök listájának megtekintéséhez. 

2. Kattintson a jobb gombbal annak a IoT Edge eszköznek a nevére, amelyről szeretné fogadni a központi telepítést. 

3. Válassza a **központi telepítés létrehozása**lehetőséget.

4. A Fájlkezelőben válassza ki a **Deployment. Windows-amd64** fájlt a megoldás konfigurációs mappájába. 

5. Frissítse a Cloud Explorert, és tekintse meg az eszköz alatt felsorolt telepített modulokat. 


## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak. 

A IoT Edge Tools bővítmény használatával megtekintheti az üzeneteket, ahogy megérkeznek a IoT Hub. 

1. A Visual Studio Cloud Explorerben válassza ki a IoT Edge eszköz nevét. 

2. A **műveletek** listában válassza a **figyelés beépített esemény végpontjának elindítása**lehetőséget. 

3. Megtekintheti a IoT Hub érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mivel a IoT Edge eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután megvárja a CModule-kód módosításait, amíg a gép hőmérséklete 25 fokkal nem éri el az üzenetek küldését. Az üzenet típusú **riasztást** is hozzáadja az adott hőmérsékleti küszöbértéket elérő üzenetekhez. 

   ![IoT Hubra érkező üzenetek megtekintése](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul két különálló szerkesztése

A 2. CModule modult használtuk a hőmérsékleti küszöbérték 25 fokos beállításához. A Twin modul használatával módosíthatja a funkciót anélkül, hogy frissítenie kellene a modul kódját.

1. A Visual Studióban nyissa meg a **Deployment. Windows-amd64. JSON** fájlt. (Nem a Deployment. template fájl. Ha nem látja a telepítési jegyzékfájlt a konfigurációs fájlban a megoldás Explorerben, jelölje be az **összes fájl megjelenítése** ikont az Explorer eszköztárán.)

2. Keresse meg a CModule Twin értéket, és módosítsa a **temperatureThreshold** paraméter értékét egy új 5 fokos hőmérsékletre, a legutóbbi jelentett hőmérsékletnél 10 fokkal magasabbra. 

3. Mentse a **Deployment. Windows-amd64. JSON** fájlt.

4. Kövesse az üzembe helyezési lépéseket, és alkalmazza a frissített üzembe helyezési jegyzéket az eszközre. 

5. A bejövő eszközről a felhőbe irányuló üzenetek figyelése. Ekkor az új hőmérsékleti küszöb eléréséig az üzenetek leállnak. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok létrehozására, többet is megtudhat a [saját IoT Edge moduljainak fejlesztéséről](module-development.md) , illetve a [Visual Studióval történő modulok fejlesztéséről](how-to-visual-studio-develop-module.md). Folytassa a következő oktatóanyagokkal, amelyből megtudhatja, hogyan hozhatja Azure IoT Edge az Azure Cloud Services üzembe helyezését az adathordozón lévő adatfeldolgozás és-elemzés során.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [stream Analytics](tutorial-deploy-stream-analytics.md)[](tutorial-deploy-custom-vision.md) [Machine learning](tutorial-deploy-machine-learning.md)Custom Vision Service
> 
> 
