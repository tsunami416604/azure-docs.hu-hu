---
title: A c nyelvhez készült Azure IoT eszközoldali SDK-t |} A Microsoft Docs
description: Ismerkedés az Azure IoT eszközoldali SDK-t a c nyelvhez készült, és megtudhatja, hogyan hozhat létre, amely kommunikálni eszközalkalmazások IoT hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: 7734a273f6e07290ec7124def5cb3b095f7f067b
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674158"
---
# <a name="azure-iot-device-sdk-for-c"></a>Az Azure IoT eszközoldali SDK-t a c nyelvhez készült

A **Azure IoT eszközoldali SDK-t** kódtárak, amely leegyszerűsíti az üzenetek az üzenetek küldése és fogadása folyamata van a **Azure IoT Hub** szolgáltatás. Az SDK egy adott platform célzó másik változata, de ez a cikk ismerteti a **Azure IoT eszközoldali SDK-t a c nyelvhez készült**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A c nyelvhez készült Azure IoT eszközoldali SDK-t (C99) hordozhatóságot ANSI C nyelven van megírva. Ez a funkció lehetővé teszi a kódtárakat jól alkalmazható tevékenységekről a művelethez használandó többféle platformra és eszközre, különösen akkor, ha minimálisra csökkentik a lemez és memóriaigénye prioritást.

Nincsenek, amelyre az SDK-t tesztelték platformok széles köre (lásd a [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsuite.com/) részletekért). Bár ez a cikk a Windows-platformon futó mintakód forgatókönyvek tartalmaz, a jelen cikkben ismertetett kód megegyezik a támogatott platformokról a tartomány.

Az alábbi videó mutatja be az Azure IoT SDK-t a C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Ez a cikk bemutatja a az Azure IoT eszközoldali SDK architektúrájának a c-hez Azt mutatja be a hálózatieszköz-könyvtár inicializálása, adatok küldését az IoT Hub és-üzeneteket fogadjon. Ebben a cikkben található információk kell lennie ahhoz, hogy az SDK-t, de a kódtárakat további információkra mutató hivatkozások is biztosít.

## <a name="sdk-architecture"></a>SDK-architektúra

Annak a [ **a c nyelvhez készült Azure IoT eszközoldali SDK** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárházat és a nézet részletei az API-t a [C API-referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

A könyvtárak legújabb verziója található a **fő** a főága:

  ![A tárház főágába képernyőképe](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Az SDK fő végrehajtása szerepel a **iothub\_ügyfél** megvalósítása a legalacsonyabb, az SDK API-réteget tartalmazó mappa: a **Iothubclientről** könyvtár. A **Iothubclientről** könyvtára végrehajtási üzeneteket az IoT hub üzenetek küldése és fogadása az IoT hubról a nyers üzenetkezelési API-kat tartalmazza. Ez a kódtár használata esetén Ön viseli a felelősséget üzenet szerializálási megvalósításához, de más részleteit az IoT hubbal való kommunikációhoz kezeli az Ön számára.

* A **szerializáló** mappa tartalmazza az segédfüggvények és a minták azt mutatják be, hogyan lehet szerializálni az adatokat az Azure IoT hubra elküldése előtt az ügyféloldali kódtár használatával. A szerializáló használata nem kötelező, és van megadva a kényelem. Használatához a **szerializáló** könyvtár, megadhat egy modellt, amely meghatározza az adatok küldése az IoT Hub és az üzeneteket kaphat belőle. Miután a modell van megadva, az SDK biztosít egy API-felület, amely lehetővé teszi, hogy egyszerűen az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek nem kell bajlódnunk a szerializálási részleteit. A könyvtár egyéb protokollok, például mqtt-ről és az AMQP használatával átviteli megvalósító nyílt forráskódú függvénytárak függ.

* A **Iothubclientről** könyvtár más nyílt forráskódú függvénytárak függ:

  * A [Azure C megosztott segédprogram](https://github.com/Azure/azure-c-shared-utility) könyvtár, amely több Azure-hoz kapcsolódó C SDK-k között szükséges alapvető feladatokhoz (például karakterláncok, lista adatkezelési és IO) általános funkciókat biztosít.

  * A [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) könyvtár, amely egy korlátozott erőforrás eszközök optimalizált AMQP ügyféloldali megvalósítását.

  * A [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) könyvtár, amely egy általános célú tár, az MQTT protokoll megvalósítása, és korlátozott erőforrás eszközök optimalizálva.

Ezek a kódtárak használata megérteni a példakód megtekintésével. A következő szakaszok végigvezetik az alkalmazásokra, amelyek szerepelnek az SDK számos. Ez a forgatókönyv kell biztosítanak a helyes működést a architekturális rétegeket, az SDK-t és az API-k működését bemutató különböző képességeit.

## <a name="before-you-run-the-samples"></a>A minta futtatása előtt

A minták az Azure IoT eszközoldali SDK-t a c nyelvhez készült futtatásához, meg kell [hozzon létre egy példányt az IoT Hub szolgáltatás](iot-hub-create-through-portal.md) az Azure-előfizetésében. Ezután hajtsa végre a következő feladatokat:

* A fejlesztőkörnyezet előkészítése
* Szerezze be az eszköz hitelesítő adatait.

### <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

Csomagok közös platformokon (például a NuGet for Windows vagy a Debian és Ubuntu apt_get), ha a mintát használni ezeket a csomagokat, ha elérhető. Bizonyos esetekben kell fordítsa le az SDK számára, vagy az eszközön. Ha fordítsa le az SDK van szüksége, tekintse meg [a fejlesztési környezet előkészítését](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a GitHub-adattárában.

Szerezze be a mintakódot, töltse le az SDK egy példányát a Githubról. A forrás a le a **fő** ága a [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Az eszköz hitelesítő adatok beszerzése

Most, hogy a forrás mintakód, ehhez a következő lépés az eszköz hitelesítő adatait beolvasni. Egy eszköz hozzáférhet egy IoT hubot először hozzá kell adnia az eszköz, az IoT Hub identitásjegyzékében. Az eszköz hozzáadásakor kap az eszköz hitelesítő adatait, amely az eszköz csatlakozni az IoT hubra van szüksége. A következő szakaszban tárgyalt mintaalkalmazásból ezeket a hitelesítő adatokat várt formájában egy **eszköz kapcsolati karakterláncának**.

Nincsenek számos nyílt forráskódú eszközöket, amelyek segítségével kezelheti az IoT hubnak.

* Egy Windows-alkalmazás nevű [device explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

* A platformok közötti átjárhatóságról a Visual Studio Code bővítmény nevű [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Egy Python platformfüggetlen CLI nevű [az IoT-bővítmény, az Azure CLI-vel](https://github.com/Azure/azure-iot-cli-extension).

Ebben az oktatóanyagban a grafikus *device explorer* eszközt. Használhatja a *Azure IoT-eszközök a VS Code* Ha fejleszt, a VS Code-ban. Is használhatja a *az IoT-bővítmény, az Azure CLI 2.0* eszközt, ha szeretné használni a CLI eszközt.

A device explorer eszköz különböző funkciókat hajthatja végre az IoT Hub, beleértve az eszközök hozzáadása az Azure IoT service kódtárak használja. Ha hozzáad egy eszközt a device explorer eszköz használatával, kap egy kapcsolati karakterláncot az eszközt. Ezt a kapcsolati karakterláncot a mintaalkalmazások futtatásához szüksége lesz.

Ha még nem ismeri a device explorer eszközzel, az alábbi eljárás ismerteti, amellyel egy eszköz hozzáadásához és a egy eszköz kapcsolati karakterláncának beszerzése.

1. A device explorer eszköz telepítése: [a Device Explorer használata az IoT Hub-eszközök](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Futtassa a programot, ha ez az interfész jelenik meg:

  ![Device Explorer Ikereszköz képernyőképe](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Adja meg a **IoT Hub kapcsolati karakterláncra** az első mezőt, és kattintson a **frissítés**. Ebben a lépésben konfigurálja az eszközt, úgy, hogy képes legyen kommunikálni az IoT Hub. 

A **kapcsolati karakterlánc** területen található **IoT Hub szolgáltatás** > **beállítások** > **megosztott elérési házirendet**  >  **iothubowner**.

1. Ha az IoT Hub kapcsolati karakterláncra van konfigurálva, kattintson a **felügyeleti** lapon:

  ![Device Explorer Ikereszköz / felügyeleti képernyőképe](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

A rendszer ezen a lapon kezelheti az IoT hub-ben regisztrált eszközök.

1. Kattintva olyan eszközt hoz létre a **létrehozás** gombra. Egy párbeszédpanel jelenik meg az olyan előre összeállított kulcsokat (elsődleges és másodlagos). Adjon meg egy **Eszközazonosító** majd **létrehozás**.

  ![Hozzon létre az eszköz képernyőfelvétel](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Ha az eszköz jön létre, az eszközök frissítések és a regisztrált eszközök, beleértve a most létrehozott egy listája. Ha a jobb gombbal az új eszközt, ez a menü jelenik meg:

  ![Device Explorer Ikereszköz kattintson a jobb gombbal eredménye](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Ha úgy dönt, **kijelölt eszközhöz tartozó kapcsolati karakterlánc másolása**, az eszköz kapcsolati karakterláncának másolja a vágólapra. Tartsa meg az eszköz kapcsolati karakterláncának másolatát. Meg kell futtatásakor a következő szakaszok ismertetik a mintaalkalmazásokat.

Amikor végzett a fenti lépéseket, készen áll bizonyos kód futtatásával elindításához. A legtöbb minták állandónak kell felső részén a fő forrásfájlt, amely lehetővé teszi, hogy adjon meg egy kapcsolati karakterláncot. Például a megfelelő sort a **iothub_client\_minták\_iothub_convenience_sample** alkalmazás a következőképpen jelenik meg.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>A Iothubclientről könyvtár használata

Belül a **iothub\_ügyfél** mappájában a [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) tárházban, van egy **minták** mappába, amelyben egy alkalmazás nevű **iothub\_ügyfél\_minta\_mqtt**.

A Windows verziója a **iothub_client\_minták\_iothub_convenience_sample** alkalmazás tartalmaz a következő Visual Studio-megoldást:

  ![Visual Studio Solution Explorer](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Ha megnyitja a projektet a Visual Studio 2017, fogadja el az utasításokat a legújabb verzióra a projekt átirányítása.

Ez a megoldás egyetlen projekt tartalmazza. Van telepítve Ez a megoldás a négy NuGet-csomagok:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Erre mindig szüksége van a **Microsoft.Azure.C.SharedUtility** csomag, az SDK-val működik. Ez a minta az MQTT protokoll használja, ezért meg kell adni a **Microsoft.Azure.umqtt** és **Microsoft.Azure.IoTHub.MqttTransport** csomagok (nincsenek egyenértékű csomagok AMQP-és HTTPS). Mivel a példa a **Iothubclientről** könyvtárban is fel kell a **Microsoft.Azure.IoTHub.IoTHubClient** megoldását a csomagot.

A megvalósítás a mintaalkalmazáshoz annak a **iothub_client\_minták\_iothub_convenience_sample** forrásfájl.

Az alábbi lépéseket a mintaalkalmazás segítségével végigvezetik mi használatához szükséges a **Iothubclientről** könyvtár.

### <a name="initialize-the-library"></a>A kódtár inicializálása

> [!NOTE]
> Mielőtt elkezdené a munkát a kódtárakat, szükség lehet a platformspecifikus inicializálást végrehajtásához. Ha azt tervezi, hogy az AMQP használata a linuxon futó például kell az OpenSSL kódtár inicializálása. A minták a [GitHub-adattár](https://github.com/Azure/azure-iot-sdk-c) segédprogram függvény **platform\_init** amikor az ügyfél elindul, és hívja a **platform\_deinit**függvény való kilépés előtt. Ezek a függvények a platform.h fejlécfájl vannak deklarálva. Vizsgálja meg ezeket a funkciókat a célplatformhoz a definíciókat a [tárház](https://github.com/Azure/azure-iot-sdk-c) meghatározni, hogy kell-e bármilyen platform-specifikus inicializálási kódot tartalmaznak az ügyfélben.

A szalagtárak használatának megkezdéséhez, először egy IoT Hub ügyfél leírójának lefoglalása:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Az eszköz kapcsolati karakterláncának kapott, a device explorer eszköz a függvény egy példányát adja át. Azt is megadhatja, a kommunikációs protokollt használja. Ebben a példában MQTT-t használ, de az AMQP- és HTTPS opció is.

Ha rendelkezik egy érvényes **IOTHUB\_ügyfél\_KEZELNI**, elkezdheti a küldhet és fogadhat üzeneteket az IoT Hub az API-k hívása.

### <a name="send-messages"></a>Üzenetek küldése

A mintaalkalmazás beállítja a hurok az IoT hubnak küldött üzenetek küldéséhez. A következő kódrészletet:

- Létrehoz egy üzenetet.
- Az üzenetet ad hozzá egy tulajdonságot.
- Egy üzenetet küld.

Először hozzon létre egy üzenetet:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Minden alkalommal, amikor egy üzenet küldéséhez meg kell adnia egy hivatkozást egy visszahívási függvény, amelyek akkor aktiválódnak, ha elküldi az adatokat. Ebben a példában a visszahívási függvényt hívták **SendConfirmationCallback**. A következő kódrészlet azt mutatja be, a visszahívási függvény:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Vegye figyelembe a hívást a **IoTHubMessage\_Destroy** működni, amikor végzett az üzenet. Ez a függvény az üzenet létrehozásakor lefoglalt erőforrások felszabadulnak.

### <a name="receive-messages"></a>Üzenetek fogadása

Üzenet fogadása az aszinkron művelet. Először regisztrálnia a visszahívás meghívni, amikor az eszköz egy üzenetet kapja:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Utolsó paraméter bármilyen kívánt void mutató. A mintában egy egész számot mutató, de lehet, hogy olyan összetettebb adatszerkezet mutató. Ez a paraméter lehetővé teszi, hogy a visszahívási függvény, amely ezt a funkciót a hívónak a megosztott állapota alapján.

Amikor az eszköz egy üzenetet kap, a regisztrált visszahívást függvény meghívása. A visszahívási függvény kéri le:

* Az üzenet azonosítója, és az üzenet korrelációs azonosítót.
* Az üzenet tartalma.
* Egyéni tulajdonságokat, az üzenet alapján.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Használja a **IoTHubMessage\_GetByteArray** függvényt az üzenet, amely ebben a példában egy karakterlánc lekéréséhez.

### <a name="uninitialize-the-library"></a>A könyvtár meghívná

Eseményeket küldő és fogadó üzenetek elkészült, az IoT-kódtár meghívná is. Ehhez adja ki a következő függvény hívásához szükséges:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Ez a hívás szabadít fel az erőforrások korábban már lefoglalta a **Iothubclientről\_CreateFromConnectionString** függvény.

Amint láthatja, könnyebbé vált az üzenetek küldése és fogadása a **Iothubclientről** könyvtár. A könyvtárban való kommunikációhoz, az IoT hubbal, beleértve a használandó protokoll részleteit kezeli (a fejlesztői szempontjából, ez a lehetőség egy egyszerű konfigurálás).

A **Iothubclientről** szalagtár is pontosabban szabályozhatja, hogyan lehet szerializálni az adatokat az IoT hubnak küldi az eszköz biztosít. Bizonyos esetekben ez az érték egy nagy előnnyel jár, de mások egy implementálási részlete, amelyet szeretne az érintett. Ha ebben az esetben érdemes lehet használatával a **szerializáló** könyvtár, amely a következő szakaszban leírt.

## <a name="use-the-serializer-library"></a>A szerializáló könyvtár használata

Elméleti szinten a **szerializáló** könyvtár helyezkedik el a a **Iothubclientről** könyvtár az SDK-ban. Használja a **Iothubclientről** könyvtár az IoT Hub, de a mögöttes kommunikáció szerializálási üzenet többé vesződnie a sérült szolgáltatás eltávolítása a fejlesztőtől származó modellezési funkciók ad hozzá. Hogyan a szalagtár működése a legjobb egy példán keresztül mutatja be.

Belül a **szerializáló** mappájában a [azure-iot-sdk-c adattár](https://github.com/Azure/azure-iot-sdk-c), van egy **minták** nevű mappát, amely tartalmazza az alkalmazás **simplesample\_mqtt**. Ez a minta Windows verziója tartalmazza a következő Visual Studio-megoldást:

  ![A Visual Studio-megoldás mqtt-minta](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Ha megnyitja a projektet a Visual Studio 2017, fogadja el az utasításokat a legújabb verzióra a projekt átirányítása.

Csakúgy, mint az előző mintától, ez egy több NuGet-csomagot tartalmazza:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

A legtöbb ezeket a csomagokat az előző példában láthatta, de **Microsoft.Azure.IoTHub.Serializer** jelent meg. Ezt a csomagot kötelező, ha használja a **szerializáló** könyvtár.

A mintaalkalmazáshoz végrehajtásának annak a ***iothub_client\_minták\_iothub_convenience_sample** fájlt.

A következő szakaszok végigvezetik a minta fő részét.

### <a name="initialize-the-library"></a>A kódtár inicializálása

Az való használatának megkezdéséhez a **szerializáló** könyvtár, az inicializálás API-k hívása:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

A hívást a **szerializáló\_init** függvény egy egyszeri hívás, és a mögöttes kódtár inicializálása. Ezután hívja a **Iothubclientről\_LL\_CreateFromConnectionString** függvény, amely az azonos API megfelelően: a **Iothubclientről** minta. Ez a hívás beállítja az eszköz kapcsolati karakterláncát (Ez a hívás is, ha úgy dönt, hogy a protokollt használni kívánt). Ez a minta átviteli eszközként MQTT-t használ, de az AMQP vagy HTTPS használatával.

Végezetül hívja meg a **létrehozás\_MODELL\_példány** függvény. **WeatherStation** az a névtér a modell és **ContosoAnemometer** a modell neve. A modell-példány létrehozása után használhatja üzenetek küldése és fogadása elindításához. Azonban fontos tudni, mely egy modell.

### <a name="define-the-model"></a>A modellek meghatározásához

A modell a **szerializáló** könyvtár határozza meg, hogy az eszköz IoT hubbal küldhet üzeneteket és az üzenetek, az úgynevezett *műveletek* a modellezési nyelven, amely megkaphatja a. Egy modellt, mint a C makrók használatával meghatározhatja a **iothub_client\_minták\_iothub_convenience_sample** mintaalkalmazást:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

A **MEGKEZDÉSÉHEZ\_NÉVTÉR** és **záró\_NÉVTÉR** makrók is igénybe vehet a névteret a modell argumentumként. Valószínű, hogy bármit, ezek a makrók között-e a modell vagy modelleket, és a modellek használó adatstruktúrák definíciója.

Ebben a példában van egy adott modellt nevű **ContosoAnemometer**. Ez a modell meghatározza, két darab, amelyek az eszköz küldhet az IoT hubhoz: **DeviceId** és **szélsebesség**. Is meghatározza, hogy az eszköz megkaphatja három műveletet (üzenetek): **TurnFanOn**, **TurnFanOff**, és **SetAirResistance**. Összes adatelemének típussal rendelkező, és minden műveletet nevét (és opcionálisan különböző paraméterek).

Az adatok és a modellben meghatározott műveletek határozza meg, hogy üzeneteket küldenek az IoT Hub használatával, és az eszközre küldött üzenetek válaszol egy API-felületet. Ez a modell használatát legjobban értendő egy példán keresztül.

### <a name="send-messages"></a>Üzenetek küldése

A modell meghatározza az IoT hubbal küldhet adatokat. Ebben a példában jelenti az egyik adatelemeket a két meghatározott használatával a **WITH_DATA** makra. Számos lépést elküldéséhez szükséges **DeviceId** és **szélsebesség** értékeket egy IoT hubra. Az első, hogy állítsa be az adatokat szeretne küldeni:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

A korábban megadott modell lehetővé teszi állítsa az értékeket úgy tagjai egy **struct**. Ezután szerializálni az üzenetet szeretne küldeni:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Ez a kód az eszköz-felhő egy pufferbe szerializálja (által hivatkozott **cél**). A kód ezután meghívja a **sendMessage** függvényt az üzenet elküldéséhez az IoT hubnak:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

A második utolsó paraméteréhez **Iothubclientről\_LL\_SendEventAsync** eszköztáblára mutató hivatkozás egy visszahívási függvény, amely nevezzük, amikor sikeresen elküldi az adatokat. A minta a következő a visszahívási függvény:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

A második paraméter értéke a felhasználói környezet; mutató az azonos mutató átadott **Iothubclientről\_LL\_SendEventAsync**. Ebben az esetben a környezet egy egyszerű számlálót, de azok bármit.

Ez minden eszközt a felhőbe irányuló üzenetek küldésének van. Ahhoz, hogy biztosítsák a csak hátra üzenetek fogadása.

### <a name="receive-messages"></a>Üzenetek fogadása

Egy üzenet works hasonlóan fogadása üzenetek hogyan működnek a **Iothubclientről** könyvtár. Először regisztrálnia egy üzenet visszahívási függvény:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Ezután ír a visszahívási függvény, amelyek akkor aktiválódnak, ha egy üzenet jelenik meg:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Ez a kód bolierplate – megegyezik bármilyen megoldáshoz rendelkezésre állnak. Ez a függvény fogadja az üzenetet, és gondoskodik az Útválasztás, a megfelelő függvény hívása keresztül **EXECUTE\_parancs**. A hívott függvény ezen a ponton a modellben szereplő műveletek definíciója függ.

Amikor meghatároz egy műveletet a modell, meg kell megvalósítani egy függvényt, amely nevezzük, amikor az eszköz megkapja a megfelelő üzenetet. Például ha a modell meghatározza a művelet:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Adja meg a függvény ezt az aláírást és:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Vegye figyelembe, hogyan a függvény neve megegyezik-e a művelet a modellben, és, hogy a függvény paraméterei a művelethez megadott paraméterek megegyeznek-e. Az első paraméter mindig szükség, és a példány a modell mutatót tartalmaz.

Ha az eszköz, amely megfelel az aláírás üzenetet kap, a megfelelő függvény neve. Ezért azokat a kivéve kell megadni a sablonkód a **IoTHubMessage**, fogadja az üzeneteket: mindössze egy egyszerű függvény a modellben meghatározott műveletek meghatározása.

### <a name="uninitialize-the-library"></a>A könyvtár meghívná

Adatokat küldő és fogadó üzenetek elkészült, az IoT-kódtár segítségével meghívná:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Ezek a függvények mindegyike igazítja korábban leírt három inicializálási függvényekkel. Ezekkel az API-hívás biztosítja, hogy a korábban kiosztott erőforrásokat ingyenes.

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta található könyvtárak használatával kapcsolatos alapfogalmakat a **Azure IoT eszközoldali SDK-t a c nyelvhez készült**. Megismerheti, mit kínál az SDK-t, elegendő információt adott meg architektúrájának, és hogyan kezdheti el a Windows-minták használata. A következő cikk elmagyarázza továbbra is az SDK-t leírása [további információ az Iothubclientről-tár](iot-hub-device-sdk-c-iothubclient.md).

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
