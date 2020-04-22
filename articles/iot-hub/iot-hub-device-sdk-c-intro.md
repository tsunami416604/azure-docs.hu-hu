---
title: Az Azure IoT-eszköz SDK C | Microsoft dokumentumok
description: Ismerkedjen meg az Azure IoT-eszköz C-hez készült SDK-val, és ismerje meg, hogyan hozhat létre olyan eszközalkalmazásokat, amelyek egy IoT-központtal kommunikálnak.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b9b27bb142cb729536a3b7a561ed8b8ff5e0ccf5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731310"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-eszköz SDK C-hez

Az **Azure IoT-eszköz SDK** egy könyvtárkészlet célja, hogy egyszerűsítse a folyamat üzenetek küldésének és fogadásának az **Azure IoT Hub** szolgáltatás. Az SDK különböző változatai vannak, amelyek mindegyike egy adott platformot céloz meg, de ez a cikk ismerteti az **Azure IoT-eszköz C-hez sdk-t.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az Azure IoT-eszköz SDK C-hez ansi C (C99) a hordozhatóság maximalizálása érdekében. Ez a funkció teszi a könyvtárak kiválóan alkalmas arra, hogy működik több platformon és eszközön, különösen, ha minimálisra lemez és memória lábnyom prioritást élvez.

Vannak széles platformok, amelyeken az SDK tesztelték (lásd az [Azure Certified for IoT eszköz katalógus](https://catalog.azureiotsolutions.com/) a részletekért). Bár ez a cikk a Windows platformon futó mintakód forgatókönyveit tartalmazza, a cikkben leírt kód megegyezik a támogatott platformok teljes tartományában.

Az alábbi videó áttekintést nyújt az Azure IoT C-hez készült SDK-ról:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Ez a cikk bemutatja az Azure IoT-eszköz C-hez való architektúráját. Bemutatja, hogyan inicializálhatja az eszközkönyvtárat, hogyan küldhet adatokat az IoT Hubnak, és hogyan fogadhat üzeneteket belőle. A cikkben szereplő információknak elegendőnek kell lenniük az SDK használatának megkezdéséhez, de a tárakra vonatkozó további információkra is útmutatást adnak.

## <a name="sdk-architecture"></a>SDK architektúra

Az Azure [**IoT-eszköz SDK-t**](https://github.com/Azure/azure-iot-sdk-c) a C GitHub-tárházhoz, és megtekintheti az API részleteit a [C API referencia.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

A könyvtárak legújabb verziója a tárház **főágában** található:

  ![Képernyőkép a tárház fő ágáról](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Az SDK alapvető megvalósítása az **iothub\_ügyfélmappában** található, amely az SDK legalacsonyabb API-rétegének, az **IoTHubClient** könyvtárnak a megvalósítását tartalmazza. Az **IoTHubClient-kódtár** olyan API-kat tartalmaz, amelyek nyers üzenetküldést valósítanak meg az IoT Hubba történő üzenetek küldéséhez és az IoT Hubról érkező üzenetek fogadásához. A tár használatakor ön felelős az üzenetek szerializálásának megvalósításáért, de az IoT Hubbal való kommunikáció egyéb részleteit az Ön kezeli.

* A **szerializáló** mappa súgófüggvényeket és mintákat tartalmaz, amelyek bemutatják, hogyan szerializálhatja az adatokat, mielőtt az ügyfélkódtár használatával elküldené az Azure IoT Hubba. A szerializáló használata nem kötelező, és kényelmi jellegként biztosított. A **szerializáló** tár használatához meg kell adnia egy modellt, amely meghatározza az IoT Hubnak küldő adatokat és az attól várhatóan fogadott üzeneteket. A modell definiálása után az SDK egy API-felületet biztosít, amely lehetővé teszi, hogy könnyedén dolgozzon az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetekhez anélkül, hogy aggódnia kellene a szerializálás részletei miatt. A tár más nyílt forráskódú táraktól függ, amelyek olyan protokollok használatával valósítják meg az átvitelt, mint az MQTT és az AMQP.

* Az **IoTHubClient-kódtár** más nyílt forráskódú kódtáraktól függ:

  * Az [Azure C megosztott segédprogram-könyvtár,](https://github.com/Azure/azure-c-shared-utility) amely közös funkciókat biztosít az alapvető feladatokhoz (például a karakterláncok, a listakezelés és az IO) számos Azure-hoz kapcsolódó C SDK-k.

  * Az [Azure uAMQP-kódtár,](https://github.com/Azure/azure-uamqp-c) amely az AMQP ügyféloldali implementációja az erőforrás-korlátozott eszközökre optimalizálva.

  * Az [Azure uMQTT-könyvtár,](https://github.com/Azure/azure-umqtt-c) amely egy általános célú könyvtár, amely az MQTT protokollt valósítja meg, és erőforrás-korlátozott eszközökre van optimalizálva.

Ezeknek a könyvtáraknak a használata könnyebben érthető a példakód megnézésével. A következő szakaszok végigvezetik az SDK-ban található mintaalkalmazásokon. Ez a forgatókönyv jó érzést ad az SDK architekturális rétegeinek különböző képességeihez, és bemutatja az API-k működését.

## <a name="before-you-run-the-samples"></a>A minták futtatása előtt

Mielőtt futtathatja a mintákat az Azure IoT-eszköz SDK C, létre kell [hoznia egy példányt az IoT Hub szolgáltatás az](iot-hub-create-through-portal.md) Azure-előfizetésben. Ezután hajtsa végre a következő feladatokat:

* A fejlesztőkörnyezet előkészítése
* Eszközhitelesítő adatok beszerzése.

### <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A csomagok közös platformokhoz (például A Windows hoz vagy a Apt_get Debianhoz és Ubuntuhoz) érhetők el, és a minták ezeket a csomagokat használják, ha rendelkezésre állnak. Bizonyos esetekben le kell fordítania az SDK-t az eszközére vagy az eszközére. Ha le kell fordítania az SDK-t, [olvassa el a fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) a GitHub-tárházban című témakört.

A mintaalkalmazás kódjának beszerzéséhez töltse le az SDK egy példányát a GitHubról. A forrás másolatának beszereznie a [GitHub-tárház](https://github.com/Azure/azure-iot-sdk-c) **fő** ágából.


### <a name="obtain-the-device-credentials"></a>Az eszköz hitelesítő adatainak beszerzése

Most, hogy rendelkezik a minta forráskódjával, a következő teendő az eszköz hitelesítő adatainak beszerezése. Ahhoz, hogy egy eszköz képes legyen hozzáférni egy IoT hubhoz, először hozzá kell adnia az eszközt az IoT Hub identitásbeállítási beállításjegyzékéhez. Amikor hozzáadja az eszközt, egy eszközhitelesítő adatokat kap, amelyekaz IOt hubhoz való csatlakozáshoz szükséges eszközhöz szükséges. A következő szakaszban tárgyalt mintaalkalmazások ezeket a hitelesítő adatokat **eszközkapcsolati karakterlánc**formájában várják.

Számos nyílt forráskódú eszköz segít az IoT hub kezeléséhez.

* Egy Azure [IoT Explorer](https://github.com/Azure/azure-iot-explorer)nevű Windows-alkalmazás.

* Az [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)nevű platformfüggetlen Visual Studio-kódbővítmény.

* Egy platformfüggetlen Python CLI [nevű IoT-bővítmény az Azure CLI.](https://github.com/Azure/azure-iot-cli-extension)

Ez az oktatóanyag a grafikus *eszközkezelő* eszközt használja. Használhatja az *Azure IoT-eszközök VS-kód,* ha a VS-kód ban fejleszti. Az *Azure CLI 2.0 eszköz IoT-bővítményét* is használhatja, ha cli-eszközt szeretne használni.

Az eszközkezelő eszköz az Azure IoT-szolgáltatástárak segítségével különböző funkciókat hajthat végre az IoT Hubon, beleértve az eszközök hozzáadását is. Ha az eszközkezelő eszközzel ad hozzá egy eszközt, akkor kap egy csatlakozási karakterláncot az eszközhöz. A mintaalkalmazások futtatásához szüksége van erre a kapcsolati karakterláncra.

Ha nem ismeri az eszközkezelő eszközt, az alábbi eljárás azt ismerteti, hogyan lehet eszköz hozzáadása és eszközkapcsolati karakterlánc beszerzése.

1. Az eszközkezelő eszköz telepítéséről az [Eszközkezelő használata IoT Hub-eszközökhöz](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)című témakörben található.

1. A program futtatásakor a következő felület jelenik meg:

   ![Az Eszközkezelő ikerképe](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Írja be az **IoT Hub kapcsolati karakterláncát** az első mezőbe, és kattintson a **Frissítés gombra.** Ez a lépés úgy konfigurálja az eszközt, hogy kommunikáljon az IoT Hubbal. 

A **kapcsolati karakterlánc** az **IoT Hub szolgáltatás** > **beállításai** > **megosztott hozzáférési házirend** > **iothubowner**területen található.

1. Ha az IoT Hub kapcsolati karakterlánca konfigurálva van, kattintson a **Kezelés** fülre:

   ![Device Explorer Twin / Management képernyőkép](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Ez a lap, ahol kezelheti az IT hub regisztrált eszközök.

1. Az eszköz létrehozásához kattintson a **Létrehozás** gombra. Egy párbeszédablak előre kitöltött (elsődleges és másodlagos) kulcsokkal jelenik meg. Írja be **az eszközazonosítót,** majd kattintson **a Létrehozás gombra.**

   ![Eszköz létrehozása képernyőkép](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Az eszköz létrehozásakor az Eszközök lista frissíti az összes regisztrált eszközt, beleértve az imént létrehozottat is. Ha a jobb gombbal kattint az új eszközre, a következő menü jelenik meg:

   ![Az Eszközkezelő ikergépének jobb gombbal való kattintás eredménye](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Ha a **kijelölt eszközhöz a Kapcsolati karakterlánc másolása**lehetőséget választja, a program az eszköz kapcsolati karakterláncát a vágólapra másolja. Őrizze meg az eszköz kapcsolati karakterláncának másolatát. A következő szakaszokban ismertetett mintaalkalmazások futtatásakor szüksége van rá.

Miután elvégezte a fenti lépéseket, készen áll néhány kód futtatására. A legtöbb minta a fő forrásfájl tetején állandóval rendelkezik, amely lehetővé teszi egy kapcsolati karakterlánc megadását. Például a **iothub_client minták\_\_megfelelő** sora iothub_convenience_sample alkalmazás a következőképpen jelenik meg.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Az IoTHubClient-tár használata

Az [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) tárházban található **iothub\_ügyfélmappában** van egy **mintamappa,** amely egy **iothub\_ügyfélminta\_mqtt\_** nevű alkalmazást tartalmaz.

A **iothub_client\_mintaverziója\_iothub_convenience_sample** alkalmazás a következő Visual Studio-megoldást tartalmazza:

  ![Visual Studio megoldáskezelő](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Ha a Visual Studio arra kéri, hogy a projektet a legújabb verzióra célozza meg, fogadja el a kérdést.

Ez a megoldás egyetlen projektet tartalmaz. Ebben a megoldásban négy NuGet csomag van telepítve:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Az SDK-val végzett munka során mindig szüksége van a **Microsoft.Azure.C.SharedUtility** csomagra. Ez a minta az MQTT protokollt használja, ezért a **Microsoft.Azure.umqtt** és a **Microsoft.Azure.IoTHub.MqttTransport** csomagokat kell tartalmaznia (egyenértékű csomagok vannak az AMQP és https protokollhoz). Mivel a minta az **IoTHubClient-könyvtárat** használja, a **Microsoft.Azure.IoTHub.IoTHubClient** csomagot is tartalmaznia kell a megoldásban.

A mintaalkalmazás implementációját a **\_iothub_client\_minták iothub_convenience_sample** forrásfájlban találja.

A következő lépések ezt a mintaalkalmazást használja az **IoTHubClient könyvtár** használatához szükséges lépések végigjárásához.

### <a name="initialize-the-library"></a>A tár inicializálása

> [!NOTE]
> Mielőtt elkezdené a könyvtárakkal való munkát, előfordulhat, hogy végre kell hajtania néhány platformspecifikus inicializálást. Ha például az AMQP-t Linuxon szeretné használni, inicializálnia kell az OpenSSL-könyvtárat. A minták a [GitHub-tárházban](https://github.com/Azure/azure-iot-sdk-c) hívja meg a közüzemi függvény **platform\_init,** amikor az ügyfél elindul, és hívja meg a **platform\_deinit** függvény kilépés előtt. Ezek a függvények deklarálva vannak a platform.h fejlécfájlban. Vizsgálja meg ezeknek a függvényeknek a definícióit a célplatformhoz a [tárházban,](https://github.com/Azure/azure-iot-sdk-c) és állapítsa meg, hogy szükség van-e platformspecifikus inicializálási kódra az ügyfélben.

A kódtárak kal való együttműködés megkezdéséhez először rendeljen le egy IoT Hub-ügyfélleírót:

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

Átadja az eszközkezelő eszköztől kapott eszközkapcsolati karakterlánc másolatát erre a funkcióra. Ön is kijelöli a használni kívánt kommunikációs protokollt. Ez a példa az MQTT-t használja, de az AMQP és a HTTPS is választható.

Ha rendelkezik egy érvényes **IOTHUB\_CLIENT\_HANDLE-vel,** megkezdheti az API-k hívását az IoT Hubba és az IoT Hubról érkező és onnan érkező üzenetek küldéséhez és fogadásához.

### <a name="send-messages"></a>Üzenetek küldése

A mintaalkalmazás beállít egy hurkot az IoT hubba való üzenetek küldéséhez. A következő kódrészlet:

- Üzenetet hoz létre.
- Tulajdonság hozzáadása az üzenethez.
- Üzenetet küld.

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

Minden alkalommal, amikor üzenetet küld, megad egy hivatkozást egy visszahívási függvényre, amelyet az adatok küldésekor hív meg a rendszer. Ebben a példában a visszahívási függvény neve **SendConfirmationCallback**. A következő kódrészlet ezt a visszahívási függvényt mutatja:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Jegyezze fel az **IoTHubMessage\_Destroy** függvény hívását, ha végzett az üzenettel. Ez a függvény felszabadítja az üzenet létrehozásakor lefoglalt erőforrásokat.

### <a name="receive-messages"></a>Üzenetek fogadása

Az üzenet fogadása aszinkron művelet. Először regisztrálja a visszahívást, hogy meghívja, amikor az eszköz üzenetet kap:

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

Az utolsó paraméter egy void mutató, amit akarsz. A mintában egy egész számra mutató mutató, de lehet egy összetettebb adatszerkezetre mutató mutató. Ez a paraméter lehetővé teszi, hogy a visszahívási funkció megosztott állapotban működjön a függvény hívójával.

Amikor az eszköz üzenetet kap, a regisztrált visszahívási függvény meghívása lesz. Ez a visszahívási függvény a következőket olvassa be:

* Az üzenet azonosítója és korrelációs azonosítója az üzenetből.
* Az üzenet tartalma.
* Az üzenet bármely egyéni tulajdonsága.

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

Az **IoTHubMessage\_GetByteArray** függvény segítségével olvassa be az üzenetet, amely ebben a példában egy karakterlánc.

### <a name="uninitialize-the-library"></a>A tár inicializálásának levizsgálata

Ha befejezte az események küldését és az üzenetek fogadását, leválaszthatja az IoT-könyvtár inicializálását. Ehhez adja ki a következő függvényhívást:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Ez a hívás felszabadítja az **IoTHubClient\_CreateFromConnectionString** függvény által korábban lefoglalt erőforrásokat.

Amint láthatja, az **IoTHubClient könyvtárral** egyszerűen küldhet és fogadhat üzeneteket. A könyvtár kezeli az IoT Hubbal való kommunikáció részleteit, beleértve azt is, hogy melyik protokollt kell használni (a fejlesztő szemszögéből ez egy egyszerű konfigurációs lehetőség).

Az **IoTHubClient-kódtár** is pontos szabályozást biztosít az eszköz által az IoT Hubnak küldött adatok szerializálásához. Bizonyos esetekben ez a szint az ellenőrzés előnyt jelent, de más esetekben ez egy olyan végrehajtási részlet, amely nem szeretné, hogy az érintett. Ebben az esetben érdemes lehet használni a **szerializáló** könyvtár, amely a következő szakaszban ismertet.

## <a name="use-the-serializer-library"></a>A szerializáló tár használata

Fogalmilag a **szerializáló** könyvtár az **SDK IoTHubClient könyvtárának** tetején található. Az **IoTHubClient-könyvtárat** használja az IoT Hubbal való mögöttes kommunikációhoz, de olyan modellezési képességeket ad hozzá, amelyek eltávolítják az üzenetszerializálás sal kapcsolatos terhét a fejlesztőtől. A könyvtár működésének módját legjobban egy példa szemlélteti.

Az [azure-iot-sdk-c tárházban](https://github.com/Azure/azure-iot-sdk-c)található **szerializáló** mappában található egy **mintamappa,** amely **egy simplesample\_mqtt**nevű alkalmazást tartalmaz. A minta Windows-verziója a következő Visual Studio-megoldást tartalmazza:

  ![Visual Studio megoldás az mqtt mintához](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Ha a Visual Studio arra kéri, hogy a projektet a legújabb verzióra célozza meg, fogadja el a kérdést.

Az előző mintához képest ez is több NuGet csomagot tartalmaz:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

A legtöbb ilyen csomagot az előző mintában látta, de a **Microsoft.Azure.IoTHub.Serializer** új. Ez a csomag szükséges a **szerializáló** könyvtár használatakor.

A mintaalkalmazás megvalósítását a **iothub_client\_\_a minták at iothub_convenience_sample** fájlban találja.

A következő szakaszok végigvezetik a minta kulcsfontosságú részein.

### <a name="initialize-the-library"></a>A tár inicializálása

A **szerializáló** tárral való munkához hívja meg az inicializálási API-kat:

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

A **szerializáló\_init** függvény hívása egyszeri hívás, és inicializálja az alapul szolgáló könyvtárat. Ezután meghívja az **IoTHubClient\_\_LL CreateFromConnectionString** függvényt, amely ugyanaz az API, mint az **IoTHubClient** mintában. Ez a hívás beállítja az eszköz kapcsolati karakterláncát (ez a hívás is ott van, ahol kiválaszthatja a használni kívánt protokollt). Ez a minta az MQTT-t használja átvitelként, de használhatja az AMQP-t vagy a HTTPS-t.

Végül hívja meg a **CREATE\_MODEL\_INSTANCE** függvényt. **WeatherStation** a névtér a modell és **ContosoAnemometer** a neve a modell. A modellpéldány létrehozása után üzenetek küldésének és fogadásának megkezdéséhez használhatja. Azonban fontos megérteni, hogy mi az a modell.

### <a name="define-the-model"></a>A modell meghatározása

A **szerializáló** tárban egy modell határozza meg az okat az üzeneteket, amelyeket az eszköz küldhet az IoT Hubnak, és az üzeneteket, az úgynevezett *műveleteket* a modellezési nyelven, amelyet fogadhat. A modellt C makrók készletével definiálhatja, mint a **iothub_client\_mintákban\_iothub_convenience_sample** mintaalkalmazásban:

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

A **\_BEGIN NÉVTÉR** és **az\_END NAMESPACE** makrók a modell névterét is argumentumként veszik fel. Várható, hogy ezek között a makrók között bármi a modell vagy modellek definíciója, valamint a modellek által használt adatstruktúrák.

Ebben a példában van egy modell nevű **ContosoAnemometer**. Ez a modell két olyan adatot határoz meg, amelyeket az eszköz küldhet az IoT Hubnak: **DeviceId** és **WindSpeed.** Azt is meghatározza, három művelet (üzenetek), hogy a készülék képes fogadni: **TurnFanOn**, **TurnFanOff**, és **SetAirResistance**. Minden adatelemnek van egy típusa, és minden műveletnek van neve (és adott esetben egy paraméterkészlete).

A modellben meghatározott adatok és műveletek egy API-felületet határoznak meg, amely segítségével üzeneteket küldhet az IoT Hubnak, és válaszolhat az eszközre küldött üzenetekre. Ennek a modellnek a használata a legjobban egy példa alapján érthető.

### <a name="send-messages"></a>Üzenetek küldése

A modell határozza meg az IoT Hubnak küldhető adatokat. Ebben a példában ez a **WITH_DATA** makróval definiált két adatelem egyikét jelenti. Az **Eszközazonosító** és a **WindSpeed-értékek** IoT-hubba küldéséhez számos lépés szükséges. Az első az elküldeni kívánt adatok beállítása:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

A korábban definiált modell lehetővé teszi az értékek beállítását a **struct**tagjainak beállításával. Ezután szerializálja az elküldeni kívánt üzenetet:

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

Ez a kód szerializálja az eszköz ről a felhőre egy puffer (hivatkozott **cél).** A kód ezután meghívja a **sendMessage** függvényt az üzenet IoT Hubnak való elküldéséhez:

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

Az **IoTHubClient\_LL\_SendEventAsync** utolsó tól utolsó ig paramétere egy visszahívási függvényre mutató hivatkozás, amely az adatok sikeres küldésekor hívható. A minta a következő visszahívási függvényt írja be:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

A második paraméter a felhasználói környezetre mutató mutató; ugyanazt a mutatót adta át **az IoTHubClient\_LL\_SendEventAsync**. Ebben az esetben a kontextus egy egyszerű számláló, de bármi lehet, amit csak akar.

Ez minden, ami az eszközről a felhőbe irányuló üzenetek küldésére szól. Az egyetlen dolog maradt, hogy fedezze az, hogyan kell fogadni az üzeneteket.

### <a name="receive-messages"></a>Üzenetek fogadása

Az üzenetek fogadása hasonlóan működik, mint az **IoTHubClient** könyvtárban az üzenetek működése. Először regisztráljon egy üzenetvisszahívási függvényt:

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

Ezután írja meg a visszahívási függvényt, amely et az üzenet fogadásakor hív meg:

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

Ez a kód sablonos- minden megoldásnál ugyanaz. Ez a függvény fogadja az üzenetet, és gondoskodik arról, hogy a **parancs végrehajtása\_** parancs hívásával a megfelelő függvényhez irányítsa. Az ezen a ponton hívott függvény a modellben szereplő műveletek definíciójáttól függ.

Amikor definiál egy műveletet a modellben, akkor kell végrehajtania egy függvényt, amely akkor hívható, amikor az eszköz megkapja a megfelelő üzenetet. Ha például a modell határozza meg ezt a műveletet:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Ezzel az aláírással adjon meg egy függvényt:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Figyelje meg, hogy a függvény neve hogyan egyezik a modellben lévő művelet nevével, és hogy a függvény paraméterei megegyeznek a művelethez megadott paraméterek. Az első paraméter mindig szükséges, és a modell példányára mutató mutatót tartalmaz.

Amikor az eszköz olyan üzenetet kap, amely megfelel ennek az aláírásnak, a rendszer a megfelelő függvényt nevezi meg. Ezért amellett, hogy az **IoTHubMessage**sablonkódját is bele kell foglalnia, az üzenetek fogadása csak egy egyszerű függvény definiálása a modellben definiált minden egyes művelethez.

### <a name="uninitialize-the-library"></a>A tár inicializálásának levizsgálata

Ha végzett az adatok küldésével és az üzenetek fogadása, leválaszthatja az IoT-könyvtár inicializálását:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Mindhárom függvény igazodik a korábban ismertetett három inicializálási függvényhez. Ezek az API-k hívása biztosítja, hogy a korábban lefoglalt erőforrások felszabadítása.

## <a name="next-steps"></a>Következő lépések

Ez a cikk az **Azure IoT-eszköz C-hez**való kódtárai használatának alapjait ismerteti. Elegendő információt biztosított ahhoz, hogy megértse, mi található az SDK-ban, annak architektúrájában, és hogyan kezdheti el a Windows-mintákkal való munkát. A következő cikk az SDK leírását az [IoTHubClient könyvtárról ismertetve](iot-hub-device-sdk-c-iothubclient.md)folytatja.

Ha többet szeretne megtudni az IoT Hub fejlesztéséről, tekintse meg az [Azure IoT SDK-k című témakört.](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
