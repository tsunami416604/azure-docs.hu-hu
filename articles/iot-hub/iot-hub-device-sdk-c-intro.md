---
title: "Az Azure IoT-eszközök SDK C-hez |} Microsoft Docs"
description: "Ismerkedés az Azure IoT-eszközök SDK C-hez, és megtudhatja, hogyan hozzon létre egy IoT-központot kommunikáló eszközön futó alkalmazások."
services: iot-hub
documentationcenter: 
author: olivierbloch
manager: timlt
editor: 
ms.assetid: e448b061-6bdd-470a-a527-15ec03cca7b9
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: obloch
ms.openlocfilehash: 459b630f28fe48064f4ba280974f3fdbdb82f0a6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-iot-device-sdk-for-c"></a>Az Azure IoT-eszközök SDK C-hez

A **Azure IoT-eszközök SDK** úgy tervezték, hogy az üzenetek üzenetek küldése és fogadása egyszerűbbé szalagtárak készlete a **Azure IoT Hub** szolgáltatás. Az SDK-t és egy adott platform célzó eltérő változata van, de ez a cikk ismerteti a **C-hez készült SDK Azure IoT-eszközök**.

Az Azure IoT-eszközök SDK c ANSI C maximalizálása hordozhatóságát (C99) nyelven van megírva. A szolgáltatás elérhetővé teszi a könyvtárak az való működésre több platformokon és eszközökön, különösen akkor, ha a lemez minimalizálja a szolgáltatást, és memóriaigény prioritását.

Számos platformon, amelyre az SDK-t tesztelték van (lásd: a [Azure IoT eszköz katalógus hitelesített](https://catalog.azureiotsuite.com/) részletekért). Bár ez a cikk a Windows-platformon futó mintakód forgatókönyvek tartalmaz, akkor a jelen cikkben ismertetett kódot megegyezik a támogatott platformok tartományán keresztül.

Ez a cikk bemutatja az Azure IoT-eszközök SDK architektúrája a c kiszolgálóra. Azt mutatja be az eszköz kódtár inicializálása adatokat küldeni az IoT-központ és üzenetek fogadása. A cikkben szereplő információkat kell lennie ahhoz, hogy az SDK használatának első, de a szalagtárakkal kapcsolatos további információkra mutató hivatkozások is biztosít.

## <a name="sdk-architecture"></a>SDK-architektúra

Megtalálhatja az [ **C-hez készült SDK Azure IoT-eszközök** ](https://github.com/Azure/azure-iot-sdk-c) GitHub tárház és nézet adatai az API-nak a [C API-referencia](https://azure.github.io/azure-iot-sdk-c/index.html).

A könyvtárak legújabb verziója található a **fő** a tárház főágába:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* Az SDK alapvető megvalósítása szerepel a **IOT hubbal\_ügyfél** mappát, amely tartalmazza az SDK-ban a legalacsonyabb API réteg végrehajtásának: a **IoTHubClient** könyvtár. A **IoTHubClient** könyvtár végrehajtási nyers üzenetkezelési üzenetek küldése az IoT-központ és az üzenetek fogadása az IoT-központ API-kat tartalmaz. Ha ezt a szalagtárat, üzenet szerializálási végrehajtásáért felelős, de egyéb adatainak kommunikál az IoT-központ történik meg.
* A **szerializáló** mappa tartalmazza súgófunkciókat és példák bemutatják a előtt Azure IoT Hub küldése szerializálni az adatokat az ügyféloldali kódtár segítségével. A szerializáló használata nem kötelező, és biztosítja a könnyebb. Használatához a **szerializáló** könyvtár, modell, amely meghatározza az IoT-központ és az üzenetek kap, várhatóan küldött adatok megadása. Ha a modell van definiálva, az SDK-t biztosít, amelynek segítségével egyszerűen dolgozhat eszközről a felhőbe és felhő eszközre üzenetek anélkül, hogy szerializációs részleteinek API felületéhez. A könyvtár más nyílt forráskódú tárak, amelyek megvalósítják az átviteli protokollal például MQTT és AMQP függ.
* A **IoTHubClient** könyvtár más nyílt forráskódú szalagtárak függ:
  * A [Azure C megosztott segédprogram](https://github.com/Azure/azure-c-shared-utility) könyvtárban, amely több Azure-hoz kapcsolódó C SDK között szükséges alapvető feladatokhoz (például karakterláncok, lista manipuláció és IO) közös funkciókat biztosít.
  * A [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) könyvtárban, és azt egy AMQP korlátozott erőforrás eszközök optimalizálva ügyféloldali megvalósítása.
  * A [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) könyvtárban, amely segíti a MQTT protokoll megvalósítását egy általános célú függvénytár, és korlátozott erőforrás eszközök optimalizálva.

Ezek a könyvtárak használata kódpéldákat megnézzük megérteni. A következő szakaszok végigvezetik Önt az alkalmazásokra, amelyek szerepelnek az SDK számos. Ez a forgatókönyv biztosítani fogja a helyes működését a különböző képességeinek a architekturális rétegek az SDK és az API-k működése bemutatása.

## <a name="before-you-run-the-samples"></a>A minták futtatása előtt

A minták c futtathatja az Azure IoT-eszközök SDK, előtt [hozzon létre egy példányt az IoT-központ szolgáltatás](iot-hub-create-through-portal.md) az Azure-előfizetésben. Fejezze be a következő feladatokat:

* A fejlesztőkörnyezet előkészítése
* Szerezze be a hitelesítő adatai.

### <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

Csomagok (például NuGet Windows vagy apt_get Debian és Ubuntu) közös platformok kapnak, és a minták használja ezeket a csomagokat, ha elérhető. Bizonyos esetekben kell lefordítani az SDK-t a vagy az eszközön. Ha az SDK fordítási szüksége, tekintse meg [a fejlesztőkörnyezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a GitHub-tárházban.

A minta alkalmazáskód beszerzéséhez töltse le az SDK-t a Githubról. A forrás a példányt a **fő** ága a [GitHub-tárházban](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Szerezze be a hitelesítő adatai

Most, hogy a forrás mintakód, a következő lépés az eszköz hitelesítő adatokat lekérni. Az eszközök hozzáférhetnek az IoT-központ először hozzá kell az eszközt biztosít az IoT-központ identitásjegyzékhez. Az eszköz hozzáadásakor le, amelyekre szüksége van az eszköz kell kapcsolódnia kell az IoT hub eszköz hitelesítő adatokat. A következő szakaszban tárgyalt mintaalkalmazások ezeket a hitelesítő adatokat várt formájában egy **eszköz kapcsolati karakterlánc**.

Számos nyílt forráskódú eszköz kezeléséhez az IoT hub van.

* Egy nevű Windows-alkalmazás [eszköz explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* A platformok közötti node.js parancssori eszköz neve [IOT hubbal-explorer](https://github.com/azure/iothub-explorer).

Ez az oktatóanyag használja a grafikus *eszköz explorer* eszköz. Használhatja a *IOT hubbal-explorer* eszköze, ha szeretné használni a parancssori eszköz.

Az eszköz explorer eszközt az Azure IoT szolgáltatás-tárakat használ az IoT-központ, beleértve az eszközök különféle funkciók elvégzésére. Ha az eszköz explorer eszköz segítségével hozzáad egy eszközt, az eszközhöz kapott egy kapcsolati karakterláncot. Ez a kapcsolati karakterlánc a minta-alkalmazások futtatására van szüksége.

Ha nem ismeri az eszköz explorer eszközzel, az alábbi eljárás ismerteti, hogyan használhatja hozzáad egy eszközt, és szerezze be az eszköz kapcsolati karakterláncot.

Az eszköz explorer eszköz telepítéséhez tekintse át [az IoT Hub-eszközöknek eszköz kezelővel](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

Amikor futtatja a programot, ez az interfész jelenik meg:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Adja meg a **IoT Hub kapcsolati karakterlánc** a első mezőben, majd kattintson a **frissítés**. Ebben a lépésben konfigurálja az eszközt, hogy kommunikáljon az IoT-központ.

Az IoT-központ kapcsolati karakterlánc konfigurálását, kattintson a **felügyeleti** lapon:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Ez a lap majdnem, amelyeken kezelheti az IoT hub-ben regisztrált eszközök.

Egy eszköz kattintva hozhat létre a **létrehozása** gombra. Olyan előre megadott kulcsokat (elsődleges és másodlagos) egy párbeszédpanelt jelenít meg. Adjon meg egy **Eszközazonosító** majd **létrehozása**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Amikor az eszköz jön létre, az eszközök frissítések a regisztrált eszközökkel, beleértve a most létrehozott egy listán. Az új eszköz a jobb gombbal, az ebben a menüben jelenik meg:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Ha úgy dönt, **másolja a kijelölt eszközre vonatkozóan a kapcsolati karakterlánc**, az eszköz kapcsolati karakterláncot a vágólapra másolódik. Tartsa meg az eszköz kapcsolati karakterlánc másolatát. Meg kell az alábbi szakaszokban ismertetett mintaalkalmazások futtatásakor.

A fenti lépések végrehajtását, készen áll arra, hogy elindítsa néhány kódot. Mindkét minták állandó rendelkezik, amely lehetővé teszi, hogy adjon meg egy kapcsolati karakterláncot fő forrásfájl tetején. Például a megfelelő sor a **IOT hubbal\_ügyfél\_minta\_mqtt** alkalmazás a következőképpen jelenik meg.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>A IoTHubClient könyvtárban

Belül a **IOT hubbal\_ügyfél** mappájában a [azure iot-sdk--c](https://github.com/azure/azure-iot-sdk-c) -tárházban, van egy **minták** nevű mappát, amely tartalmazza az alkalmazás **IOT hubbal\_ügyfél\_minta\_mqtt**.

A Windows-verzión a **IOT hubbal\_ügyfél\_minta\_mqtt** alkalmazás tartalmazza a következő Visual Studio megoldás:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Ha megnyitja a projektet a Visual Studio 2017, fogadja el az utasításokat a legújabb verzióra a projekt átirányítása.

Ez a megoldás egyetlen projektet tartalmaz. Nincsenek telepítve az ebben a megoldásban négy NuGet-csomagok:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Mindig van szüksége a **Microsoft.Azure.C.SharedUtility** csomag, amikor az SDK kezel. Ez a minta a MQTT protokollt használja, ezért meg kell adni a **Microsoft.Azure.umqtt** és **Microsoft.Azure.IoTHub.MqttTransport** csomagok (van egyenértékű csomagjai amqp-t és HTTP). Mivel a mintát használ a **IoTHubClient** könyvtár, fel kell venni a **Microsoft.Azure.IoTHub.IoTHubClient** csomag a megoldásban.

A mintaalkalmazáshoz végrehajtására is megtalálhatja a **IOT hubbal\_ügyfél\_minta\_mqtt.c** forrásfájl.

Az alábbi lépéseket a mintaalkalmazás segítségével végigvezetik mi van kell használniuk a **IoTHubClient** könyvtárban.

### <a name="initialize-the-library"></a>A kódtár inicializálása

> [!NOTE]
> A könyvtárakkal használatának megkezdése, előtt szükség lehet néhány platform-specifikus inicializálási végrehajtásához. Például ha AMQP használatához Linux inicializálja a OpenSSL-könyvtárban. A minták a [GitHub-tárházban](https://github.com/Azure/azure-iot-sdk-c) segédprogram függvény **platform\_init** mikor az ügyfél elindul, és hívja meg a **platform\_deinit** függvény való kilépés előtt. Ezek a funkciók platform.h fejlécfájlt van deklarálva. Ezeket a funkciókat a célplatformot a definíciók vizsgálja meg a [tárház](https://github.com/Azure/azure-iot-sdk-c) annak meghatározásához, hogy szükséges-e a platform-specifikus inicializálási kód szerepeljenek az ügyfél.

A könyvtárakkal elindításához foglaljon le egy IoT-központ ügyfél leíró:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Adja meg az eszköz kapcsolati karakterlánc szerezte be az eszköz explorer eszközből a függvény egy példányát. Is megjelölhetők használt kommunikációs protokollt. Ez a példa MQTT, de amqp-t és HTTP is lehetősége.

Ha rendelkezik olyan érvényes **IOT HUBBAL\_ügyfél\_KEZELNI**, megkezdheti a hívja az API-k és az IoT-központ üzeneteket küldjön és fogadjon.

### <a name="send-messages"></a>Üzenetek küldése

A mintaalkalmazás üzeneteket küldhet az IoT hub beállítja a hurok. A következő kódrészletet:

- Létrehoz egy üzenetet.
- Tulajdonság hozzáadása az üzenetet.
- Üzenet küldése.

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

Minden alkalommal, amikor egy üzenetet küld, meg kell adnia egy hivatkozást egy visszahívási függvény, amelyet az adatok elküldése során. Ebben a példában a visszahívási függvény hívása esetén **SendConfirmationCallback**. Az alábbi kódrészletben láthatja a visszahívási függvény:

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

Vegye figyelembe a hívást a **IoTHubMessage\_Destroy** működik, ha az üzenettel elkészült. Ez a funkció ennyi helyet szabadít a feladatoknak az üzenet létrehozásakor.

### <a name="receive-messages"></a>Hibaüzenetek

Egy üzenet fogadását egy aszinkron művelet. Először regisztrálnia a visszahívás meghívni, amikor az eszköz egy üzenetet kapja:

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

Az utolsó paramétere "void" mutató függetlenül szeretné. A minta egész mutató, de akkor lehet, hogy olyan összetettebb adatszerkezet mutató hivatkozások. Ez a paraméter lehetővé teszi, hogy működik a funkció a hívó rendelkező megosztott állapot visszahívási függvényt.

Amikor az eszköz üzenetet kap, a regisztrált visszahívási függvényt hívják. A visszahívási függvény kéri le:

* Üzenet azonosítója és az üzenet korrelációs azonosítója.
* Az üzenet tartalmát.
* Az üzenet egyéni tulajdonságokat.

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

Használja a **IoTHubMessage\_GetByteArray** működnek, mint az üzenet, amely ebben a példában a karakterlánc beolvasása.

### <a name="uninitialize-the-library"></a>A könyvtár meghívná

Amikor elkészült, eseményeket küldő és fogadó üzenetek, akkor is meghívná az IoT-könyvtárban. Ehhez adja ki a következő függvény hívásához szükséges:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Ez a hívás területet szabadít fel az erőforrások korábban le van foglalva a **IoTHubClient\_CreateFromConnectionString** függvény.

Ahogy látja, nem az üzeneteket küldjön és fogadjon a **IoTHubClient** könyvtárban. A könyvtár kommunikál az IoT-központot, beleértve a használandó protokoll részleteit kezeli (a fejlesztői szempontjából, ez a lehetőség egy egyszerű konfiguráció).

A **IoTHubClient** kódtár is biztosít a pontos szabályozhatják, hogyan kell szerializálni az adatokat az IoT-központ küld az eszköz. Egyes esetekben ez az érték előnyös, de más nem kívánt az érintett megvalósításához tartozó részlet. Ha ez a helyzet, akkor érdemes használatával a **szerializáló** függvénytár, amely a következő szakaszban ismertetett.

## <a name="use-the-serializer-library"></a>A szerializáló könyvtárban

Hasonlóak a **szerializáló** könyvtár helyezkedik el, a a **IoTHubClient** könyvtár az SDK-ban. Használja a **IoTHubClient** könyvtár az IoT-központ, de a mögöttes kommunikáció hozzáadja az terheket üzenet szerializálási való eltávolításához a fejlesztőtől származó modellezési képességekkel. Hogyan a szalagtár works legjobb bemutatott példa szerint.

Belül a **szerializáló** mappájában a [azure iot-sdk--c tárház](https://github.com/Azure/azure-iot-sdk-c), van egy **minták** nevű mappát, amely tartalmazza az alkalmazás **simplesample\_mqtt**. Ez a minta Windows verziója a következő Visual Studio megoldás tartalmazza:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Ha megnyitja a projektet a Visual Studio 2017, fogadja el az utasításokat a legújabb verzióra a projekt átirányítása.

A fenti példában az a több NuGet-csomagok foglalja magában:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

Most láthatta, ezeket a csomagokat az előző minta a legtöbb, de **Microsoft.Azure.IoTHub.Serializer** új. Ez a csomag szükséges használata esetén a **szerializáló** könyvtár.

A mintaalkalmazáshoz végrehajtásának megtalálhatja a **simplesample\_mqtt.c** fájlt.

A következő szakaszok ismerteti a minta kulcs részei között.

### <a name="initialize-the-library"></a>A kódtár inicializálása

A munka megkezdéséhez a **szerializáló** könyvtár, hívja az API-k inicializálása:

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

A hívás a **szerializáló\_init** függvény egyszeri hívás és inicializálja az alapul szolgáló könyvtár. Ezután meghívja a **IoTHubClient\_inden\_CreateFromConnectionString** -függvény, mint az azonos API a **IoTHubClient** minta. Ez a hívás beállítja az eszköz kapcsolati karakterlánc (Ez a hívás is, ha úgy dönt, hogy a protokoll használni kívánt). Ez a minta MQTT használja, mint az átvitel, de AMQP vagy HTTP használhatja.

Végezetül hívja a **létrehozása\_MODELL\_példány** függvény. **WeatherStation** a modell névtere és **ContosoAnemometer** a modell neve. A modell-példány létrehozása után üzenetek küldése és fogadása indításához használhatja. Fontos azonban megérteni milyen modell.

### <a name="define-the-model"></a>A modell meghatározása

Egy modell a(z) a **szerializáló** könyvtár határozza meg, az üzenetek, amely az eszköz küldhet az IoT-központ és az üzenetek, az úgynevezett *műveletek* megkaphatja a modellezési nyelven. A modell és a C makrók használatával megadhatja a **simplesample\_mqtt** mintaalkalmazást:

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

A **MEGKEZDÉSÉHEZ\_NÉVTÉR** és **END\_NÉVTÉR** makrók is igénybe vehet a névtér a modell argumentumként. Valószínű, hogy bármi ezek makrók között-e a modell vagy modellek és a adatstruktúrák, amelyek a modellek.

Ebben a példában nincs nevű egyetlen modellt **ContosoAnemometer**. Ez a modell határozza meg, amely az eszköz küldhet az IoT-központ adatok kétféle: **DeviceId** és **szélsebesség**. Az eszköz megkaphatja három műveleteket (üzenet) is definiálja: **TurnFanOn**, **TurnFanOff**, és **SetAirResistance**. Minden adatelemre típussal rendelkezik, és minden művelet nevét (és nem kötelező paraméterek).

Az adatok és a modellben definiált műveletek határozza meg, hogy üzeneteket küldhet IoT-központot, és az eszközre küldött üzenetek válaszol API felületéhez. Ez a modell használatának legjobb értendő egy példán keresztül.

### <a name="send-messages"></a>Üzenetek küldése

A modell határozza meg az IoT-központ küldhet adatokat. Ebben a példában, hogy azt jelenti, hogy egy, a két adatelemek segítségével meghatározott a **WITH_DATA** makró. Nincs több lépésre van szükség a küldési **DeviceId** és **szélsebesség** az IoT-központ értékeket. Az első is, hogy az adatokat szeretne küldeni:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

A korábban meghatározott modell lehetővé teszi, hogy meg kell adnia az értékeket úgy, hogy tagjai egy **struct**. A következő szerializálni az üzenetet szeretne küldeni:

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

Ezt a kódot az eszközről-a-felhőbe a pufferbe rendezi sorba (által hivatkozott **cél**). A kódot, majd meghívja a **sendMessage** függvény az üzenetet az IoT hubhoz:

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


A második utolsó paraméterének **IoTHubClient\_inden\_SendEventAsync** hivatkozás a visszahívási függvénynek, amely nevezzük, amikor az adatok elküldése megtörtént. Itt kell megadni a visszahívási függvény a minta:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

A második paraméter mutató felhasználói környezet; az azonos kapott **IoTHubClient\_inden\_SendEventAsync**. Ebben az esetben a környezetben egy egyszerű számlálót, de bármilyen lehet.

Ez minden, az eszközről a felhőbe üzenetküldésre van. Fedik le a bal oldali egyedül, hogyan üzeneteket fogadni.

### <a name="receive-messages"></a>Hibaüzenetek

Egy üzenet működik mint fogadó üzenetek hogyan működnek a **IoTHubClient** könyvtárban. Először regisztrálnia üzenet visszahívási függvény:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Ezután ír a visszahívási függvény, amelyet ha egy üzenet jelenik meg:

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

Ez a kód bolierplate--azonos minden megoldás. Ez a függvény az üzenetet kap, és gondoskodik összegzésére, valamint a megfelelő függvény hívása keresztül **EXECUTE\_parancs**. A hívott függvény a modellben műveletek definíciója ezen a ponton függ.

Ha egy műveletet a modell, módosítania nevezzük, amikor az eszköz megkapja a megfelelő üzenetet függvény végrehajtásához. Ha például a modell határozza meg a műveletet:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Adja meg a függvény a következő aláírást:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Vegye figyelembe, hogyan függvény neve megegyezik-e a művelet neve, a modell és, hogy a függvény paraméterei egyeznek-e a művelet a megadott paraméter. Az első paraméter megadása mindig kötelező, és a modell példányának mutatót tartalmaz.

Ha az eszköz az aláírásnak megfelelő üzenetet kap, a megfelelő függvény hívása esetén. Ezért vezérelt kell megadni a bolierplate kódot **IoTHubMessage**, üzenetek fogadására csak egy egyszerű függvény a modellben megadott műveleteket meghatározása.

### <a name="uninitialize-the-library"></a>A könyvtár meghívná

Amikor elkészült, adatokat küldő és fogadó üzenetek, akkor is meghívná az IoT-könyvtár:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Mindegyik funkcióhoz három igazodik a korábban ismertetett három inicializálási funkciók. Ezen API-k hívása biztosítja, hogy a korábban kiosztott erőforrásokat ingyenes.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a tárak használatának alapjaival jelez a **C-hez készült SDK Azure IoT-eszközök**. Elegendő információt az SDK tartalma megérteni a megadott, az architektúra és első lépések a Windows-minták használata. A következő cikk leírja, hogy az SDK által, amely ismerteti, hogy továbbra is fennáll. [további információk a IoTHubClient könyvtár](iot-hub-device-sdk-c-iothubclient.md).

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure IoT SDK-k][lnk-sdks].

Az IoT-központ képességeit további megismeréséhez lásd:

* [Egy eszköz szimulálva Azure IoT oldala][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
