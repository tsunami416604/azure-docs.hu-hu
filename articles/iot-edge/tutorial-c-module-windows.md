---
title: Oktatóanyag C modul fejlesztése Windowshoz - Azure IoT Edge | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Edge-modult C-kóddal, és hogyan telepítheti egy IoT Edge-et futtató Windows-eszközre
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 09d039801107a44df4f3bf3745a1e074e6d708b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760964"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Oktatóanyag: C IoT Edge modul fejlesztése Windows-eszközökhöz

A Visual Studio segítségével c-kódot fejleszthet, és üzembe helyezheti egy Azure IoT Edge-et futtató Windows-eszközre.

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio segítségével hozzon létre egy IoT Edge-modult, amely a C SDK-n alapul.
> * A Visual Studio és a Docker segítségével hozzon létre egy Docker-lemezképet, és tegye közzé a beállításjegyzékben.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet ki egy modult **a C** programban a Visual **Studio 2019** használatával, és hogyan telepítheti azt **Windows-eszközre.** Ha linuxos eszközökhöz fejleszt modulokat, keresse [fel a C IoT Edge-modul linuxos eszközökhöz való fejlesztése](tutorial-c-module.md) lehetőséget.

Az alábbi táblázat ból megismerheti a C-modulok Windows-eszközökre történő fejlesztésének és telepítésének lehetőségeit:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![C modulok fejlesztése a WinAMD64-hez a Visual Studióban](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végig kellett volna mennie az előző oktatóanyagon, hogy beállítsa a Windows-tárolók fejlesztéséhez a fejlesztői környezetet: [IoT Edge-modulok fejlesztése Windows-eszközökhöz](tutorial-develop-for-windows.md). Befejezése után, hogy az oktatóanyag, akkor rendelkeznie kell a következő előfeltételeket a helyén:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Windows-eszköz.](quickstart.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) bővítménysel konfigurált Visual Studio [2019.](https://docs.microsoft.com/visualstudio/install/install-visual-studio)
* [A Docker Desktop](https://docs.docker.com/docker-for-windows/install/) windowsos tárolók futtatására van konfigurálva.
* Telepítse az Azure IoT C SDK for Windows x64-et a vcpkg-on keresztül:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Ha a Visual Studio 2017-et (15.7-es vagy újabb verzió) használja, töltse le és telepítse az [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017-et a Visual Studio piactérről

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

A következő lépések hozzon létre egy IoT Edge modul projekt, amely a C SDK a Visual Studio és az Azure IoT Edge Tools bővítmény használatával. Miután létrehozott egy projektsablont, adjon hozzá új kódot, hogy a modul kiszűrje az üzeneteket a jelentett tulajdonságaik alapján.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy C-megoldást, amelyet a saját kódjával testreszabhat.

1. Indítsa el a Visual Studio 2019-et, és válassza **az Új projekt létrehozása lehetőséget.**

2. Keressen az **IoT Edge-et,** és válassza az **Azure IoT Edge (Windows amd64)** projektet. Kattintson a **Tovább** gombra.

   ![Új Azure IoT Edge-projekt létrehozása](./media/tutorial-c-module-windows/new-project.png)

3. Nevezze át a projektet és a megoldást valami leíróra, mint a **CTutorialApp**. A projekt létrehozásához kattintson a **Létrehozás** gombra.

   ![Új Azure IoT Edge-projekt konfigurálása](./media/tutorial-c-module-windows/configure-project.png)

4. Konfigurálja a projektet a következő értékekkel:

   | Mező | Érték |
   | ----- | ----- |
   | Sablon kiválasztása | Válassza a **C modul lehetőséget.** |
   | Modulprojekt neve | A modulnak adja a **CModule** nevet. |
   | Docker-rendszertár | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve a modulprojekt névértékéből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br> A végső képtár \<úgy\>néz ki, mint a rendszerleíró adatbázis neve .azurecr.io/cmodule. |

   ![A projekt konfigurálása céleszközhöz, modultípushoz és tárolóbeállítás-beállításjegyzékhez](./media/tutorial-c-module-windows/add-application-and-module.png)

5. A projekt létrehozásához válassza a **Hozzáadás** lehetőséget.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A központi telepítési jegyzékfájl megosztja a tároló beállításjegyzék hitelesítő adatait az IoT Edge futásidejű. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. Használja az Azure-tároló beállításjegyzékének **Access-kulcsok** szakaszában található hitelesítő adatokat.

1. Nyissa meg a **Deployment.template.json** fájlt a Visual Studio-megoldáskezelőben.

2. Keresse meg a **registryCredentials tulajdonságot** a kívánt tulajdonságok $edgeAgent. A beállításjegyzék címét automatikusan ki kell tölteni a projekt létrehozásakor megadott adatokból. A felhasználónév és a jelszó mezőknek változóneveket kell tartalmazniuk. Példa:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Nyissa meg az **.env** fájlt a modulmegoldásban. (Alapértelmezés szerint el van rejtve a Megoldáskezelőben, ezért előfordulhat, hogy a megjelenítéshez ki kell **választania** az Összes fájl megjelenítése gombot.) Az .env fájlnak ugyanazokat a felhasználónevet és jelszóváltozókat kell tartalmaznia, mint a deployment.template.json fájlban.

4. Adja hozzá a **felhasználónév** és **a jelszó** értékeket az Azure-tároló beállításjegyzékből.

5. Mentse a módosításokat az .env fájlba.

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

Az alapértelmezett modulkód fogadja az üzeneteket egy bemeneti várólistán, és továbbítja azokat egy kimeneti várólistán keresztül. Adjunk hozzá néhány további kódot, hogy a modul feldolgozza az üzeneteket a szélén, mielőtt továbbítana őket az IoT Hubra. Frissítse a modult úgy, hogy elemezze az egyes üzenetekben lévő hőmérsékleti adatokat, és csak akkor küldi el az üzenetet az IoT Hubnak, ha a hőmérséklet túllép egy bizonyos küszöbértéket.

1. Ebben a forgatókönyvben az érzékelőről származó adatok JSON formátumban szerepelnek. A JSON formátumú üzenetek szűréséhez importáljon egy C-hez készült JSON-kódtárat. Ez az oktatóanyag Parson-kódtárat használ.

   1. Töltse le a [Parson GitHub-tárházat.](https://github.com/kgabis/parson) Másolja a **parson.c** és **a parson.h** fájlokat a **CModule** projektbe.

   2. A Visual Studióban nyissa meg a **CMakeLists.txt** fájlt a CModule projektmappából. A fájl tetején importálja a Parson-fájlokat **my_parson** nevű kódtárként.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adja `my_parson` hozzá a CMakeLists.txt fájl **target_link_libraries** szakaszának táraihoz.

   4. Mentse a **CMakeLists.txt** fájlt.

   5. Nyissa **meg a CModule** > **main.c.** A include utasítások listájának alján adjon hozzá egy `parson.h` újat a JSON-támogatáshoz:

      ```c
      #include "parson.h"
      ```

2. A **main.c** fájlban adjon hozzá `temperatureThreshold` egy globális változót, amelyet az messagesReceivedByInput1Queue változó mellé hívnak meg. Ez a változó azt az értéket állítja be, amelyet a mért hőmérsékletnek túl kell lépnie ahhoz, hogy a rendszer elküldje az adatokat az IoT Hubnak.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Keresse `CreateMessageInstance` meg a funkciót main.c. Cserélje le a belső if-else utasítást a következő kódra, amely néhány sornyi funkcionalitást ad hozzá:

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

   Az új kódsorok az else utasításban új tulajdonságot adnak az üzenethez, amely az üzenetet figyelmeztetésként címkézi. Ez a kód az összes üzenetet figyelmeztetésként címkézi, mert olyan funkciókat adunk hozzá, amelyek csak akkor küldüzeneteket az IoT Hubnak, ha magas hőmérsékletet jelentenek.

4. Keresse `InputQueue1Callback` meg a függvényt, és cserélje le az egész függvényt a következő kódra. Ez a függvény valósítja meg a tényleges üzenetszűrőt. Üzenet érkezésekor ellenőrzi, hogy a jelentett hőmérséklet meghaladja-e a küszöbértéket. Ha igen, akkor továbbítja az üzenetet a kimeneti várólistán keresztül. Ha nem, akkor figyelmen kívül hagyja az üzenetet.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
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

5. Adjon hozzá egy `moduleTwinCallback` függvényt. Ez a metódus a kívánt tulajdonságok frissítéseit fogadja a modul ikerdokumentumától, és ennek megfelelően frissíti a **temperatureThreshold** változót. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

6. Keresse `SetupCallbacksForModule` meg a funkciót. Cserélje le a függvényt a következő kódra, amely egy **más, ha** utasítást ad hozzá annak ellenőrzéséhez, hogy az ikermodul frissítve lett-e.

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

10. Mentse a **deployment.template.json** fájlt.

## <a name="build-and-push-your-module"></a>Építsd meg és nyomd meg a modult

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és kódot adott hozzá a **CModule-hez,** hogy kiszűrje az üzeneteket, ahol a jelentett géphőmérséklet az elfogadható küszöbérték alatt van. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A következő paranccsal jelentkezzen be a Docker be a fejlesztői gépen. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure-tároló beállításjegyzékéből. Ezeket az értékeket az Azure Portalon a beállításjegyzék **Access-kulcsok** szakaszából kérheti le.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

2. A Visual Studio megoldáskezelőjében kattintson a jobb gombbal a létrehozandó projektnevére. Az alapértelmezett név **az AzureIotEdgeApp1,** és mivel Windows-modult hoz, a bővítménynek **Windows.Amd64-nek**kell lennie.

3. Válassza **a Build and Push IoT Edge Modules (IoT edge-modulok) lehetőséget.**

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, beépített információk a központi telepítési sablonban, és más megoldásfájlokat. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

## <a name="deploy-modules-to-device"></a>Modulok telepítése az eszközre

A Visual Studio felhőkezelőés az Azure IoT Edge Tools bővítmény segítségével telepítse a modulprojektet az IoT Edge-eszközre. Már rendelkezik egy központi telepítési jegyzékfájllal a forgatókönyvhöz, a **deployment.json** fájlhoz a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. A Visual Studio felhőkezelőjében bontsa ki az erőforrásokat az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal annak az IoT Edge-eszköznek a nevére, amelyet a központi telepítéshez szeretne fogadni.

3. Válassza **a Központi telepítés létrehozása**lehetőséget.

4. A fájlkezelőben válassza ki a **deployment.windows-amd64** fájlt a megoldás konfigurációs mappájában.

5. Frissítse a felhőkezelőt az eszköz alatt felsorolt telepített modulok megtekintéséhez.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge Tools-bővítmény használatával megtekintheti az üzeneteket, amint azok megérkeznek az IoT Hubba.

1. A Visual Studio felhőkezelőben válassza ki az IoT Edge-eszköz nevét.

2. A **Műveletek** listában válassza a **Beépített eseményvégpont figyelésének megkezdése**lehetőséget.

3. Tekintse meg az IoT Hubhoz érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek, mert az IoT Edge-eszköznek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután a CModule kódon végrehajtott módosítások megvárják, amíg a gép hőmérséklete eléri a 25 fokot, mielőtt üzeneteket küldene. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri az adott hőmérsékleti küszöbértéket.

   ![Az IoT Hubra érkező üzenetek megtekintése](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>A modul szerkesztése iker

A CModule modul ikermodulját használtuk, hogy a hőmérsékleti küszöböt 25 fokra állítsuk. Az ikermodul segítségével a modulkód frissítése nélkül módosíthatja a funkciót.

1. Nyissa meg a **Deployment.windows-amd64.json** fájlt a Visual Studióban. (Nem a deployment.template fájlt. Ha a telepítési jegyzékfájl nem látható a megoldáskezelő konfigurációs fájljában, jelölje be az **Összes fájl megjelenítése** ikont a felfedező eszköztárán.)

2. Keresse meg a CModule iker és módosítsa az értékét a **hőmérsékletKüszöb** paraméter egy új hőmérséklet 5 fokkal 10 fokkal magasabb, mint a legutóbbi jelentett hőmérséklet.

3. Mentse a **deployment.windows-amd64.json** fájlt.

4. Kövesse a központi telepítési lépéseket ismét a frissített központi telepítési jegyzékfájl eszközre való alkalmazásához.

5. Figyelje a bejövő eszközről a felhőbe irányuló üzeneteket. Az új hőmérsékleti küszöbérték eléréséig meg kell jelennie az üzeneteknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a helyi konfigurációk és az Azure-erőforrások, amelyek ebben a cikkben a költségek elkerülése érdekében.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat [saját IoT Edge-moduljainak fejlesztéséről,](module-development.md) illetve arról, hogyan [fejleszthet modulokat a Visual Studio segítségével.](how-to-visual-studio-develop-module.md) Példák az IoT Edge-modulok, beleértve a szimulált hőmérséklet modul, lásd: [IoT Edge modul minták](https://github.com/Azure/iotedge/tree/master/edge-modules) és [IoT C SDK-minták.](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)

Folytathatja a következő oktatóanyagokat, hogy megtudja, az Azure IoT Edge hogyan segíthet az Azure felhőszolgáltatásainak üzembe helyezésében a peremhálózati adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Egyéni vision szolgáltatás](tutorial-deploy-custom-vision.md)
