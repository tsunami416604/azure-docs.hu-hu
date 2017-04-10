## <a name="typical-output"></a>Tipikus kimenet

Az alábbiakban a Hello World minta által a naplófájlba írt kimenet példáját láthatja. A kimenet az olvashatóság érdekében formázva van:

```json
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Kódrészletek

Ez a szakasz a hello\_world minta kódjának néhány szakaszát tárgyalja.

### <a name="gateway-creation"></a>Átjáró létrehozása

A fejlesztőnek *átjárófolyamatot* kell írnia. Ez a program létrehoz egy belső infrastruktúrát (a közvetítő), betölti a modulokat, és mindent úgy állít be, hogy megfelelően működjön. Az SDK-ban megtalálható a **Gateway\_Create\_From\_JSON** függvény, amellyel elindíthat egy átjárót a JSON-fájlokból. A **Gateway\_Create\_From\_JSON** függvény használata előtt meg kell adnia a függvénynek egy olyan JSON-fájl elérési útját, amely meghatározza a betöltendő modulokat.

Az átjárófolyamat kódját a Hello World példában találja, a [main.c][lnk-main-c] fájlban. Az olvashatóság érdekében a következő részlet az átjáró folyamatkódjának rövidített verzióját mutatja. Ez a példaprogram létrehoz egy átjárót, majd megvárja, amíg a felhasználó lenyomja az **ENTER** billentyűt, mielőtt lebontja az átjárót.

```c
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

A JSON-beállításfájl tartalmazza a betöltendő modulok listáját és a modulok közötti hivatkozásokat. Minden modulnak meg kell határoznia a következőket:

* **name**: a modul egyedi neve.
* **loader**: olyan betöltő, amely képes a kívánt modul betöltésére. A betöltők a különböző modultípusok betöltésére szolgáló bővítménypontok. Betöltőket a natív C, Node.js, Java és .NET nyelven írt modulokhoz biztosítunk. A Hello World példa kizárólag a natív C nyelven írt betöltőt használja, mivel a példa összes modulja C nyelven írt dinamikus könyvtár. Az egyéb nyelveken írt modulok használatával kapcsolatban bővebb információt a [Node.js-](https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/nodejs_simple_sample/), [Java-](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/java_sample) és [.NET-](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/dotnet_binding_sample)példákban találhat.
    * **name**: a modul betöltéséhez használt betöltő neve.
    * **entrypoint**: a modult tartalmazó könyvtár elérési útja. Linux rendszeren ez a könyvtár egy .so fájl, Windows rendszeren pedig egy .dll fájl. A belépési pont a használt betöltőtípustól függ. A Node.js betöltő belépési pontja egy .js fájl. A Java betöltő belépési pontja egy osztályútvonal plusz egy osztálynév. A .NET betöltő belépési pontja egy szerelvénynév plusz egy osztálynév.

* **args**: a modul által igényelt konfigurációs információk.

A következő kód a Linuxon a Hello World minta összes moduljának deklarálásához használt JSON-beállításfájlt mutatja be. A modul kialakításától függ, hogy egy modulnak szüksége van-e argumentumokra. Ebben a példában a naplózómodul a kimeneti fájl elérési útját használja argumentumként, a hello\_world modul pedig nem rendelkezik argumentumokkal.

```json
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

A JSON-fájl is tartalmazza a modulok közötti hivatkozásokat, amelyeket a rendszer átad a közvetítőnek. Egy hivatkozás két tulajdonsággal rendelkezik:

* **source**: modulnév a `modules` szakaszból vagy „\*”.
* **sink**: modulnév a `modules` szakaszból.

Minden hivatkozás meghatároz egy üzenetútvonalat és irányt. A `source` modulból érkező üzenetek a `sink` modulnak vannak továbbítva. A `source` modult „\*” értékűre is be lehet állítani, ami azt jelzi, hogy a moduloktól érkező üzeneteket a `sink` fogadja.

A következő kód a Linuxon a hello\_world minta moduljai közötti hivatkozások konfigurálásához használt JSON-beállításfájlt mutatja be. A `hello_world` modul által létrehozott összes üzenetet a `logger` modul használja.

```json
"links":
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="helloworld-module-message-publishing"></a>A hello\_world modul üzenet-közzététele

A hello\_world modul által használt kódot felhasználhatja ahhoz, hogy közzétegye a [„hello_world.c”][lnk-helloworld-c] fájlban lévő üzeneteket. Az alábbi részletben a kód egy módosított verziója látható, hozzáfűzött megjegyzésekkel és – az olvashatóság érdekében – kevesebb hibakezelési kóddal:

```c
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="helloworld-module-message-processing"></a>A hello\_world modul üzenetfeldolgozása

A hello\_world modul soha nem dolgoz fel olyan üzeneteket, amelyeket más modulok tesznek közzé a közvetítőn. A hello\_world modulban ezért az üzenet-visszahívás megvalósítása művelet nélküli függvény.

```c
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>A naplózómodul üzenet-közzététele és -feldolgozása

A naplózó modul üzeneteket fogad a közvetítőtől, és egy fájlba írja őket. Soha nem tesz közzé üzeneteket. A naplózó modul kódja ezért soha nem hívja meg a **Broker_Publish** függvényt.

A közvetítő a [logger.c][lnk-logger-c] fájlban lévő **Logger_Recieve** függvény indításával kézbesíti az üzeneteket a naplózó modulnak. Az alábbi részletben egy módosított verzió látható, hozzáfűzött megjegyzésekkel és – az olvashatóság érdekében – kevesebb hibakezelési kóddal:

```c
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Következő lépések

Az IoT Gateway SDK használatának megismeréséhez tekintse meg a következő cikkeket:

* [IoT Gateway SDK – eszközről a felhőbe irányuló üzeneteket küldhet egy szimulált eszközzel Linuxon][lnk-gateway-simulated].
* [Azure IoT Gateway SDK][lnk-gateway-sdk] a GitHubon.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md