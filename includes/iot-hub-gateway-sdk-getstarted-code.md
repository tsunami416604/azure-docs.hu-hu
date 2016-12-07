## <a name="typical-output"></a>Tipikus kimenet
Az alábbiakban a Hello World minta által a naplófájlba írt kimenet példáját láthatja. A sortörés és a tabulátor karakterek az olvashatóság kedvéért lettek hozzáadva:

```
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
Ez a szakasz a Hello World minta kódjának néhány fő részét tárgyalja.

### <a name="gateway-creation"></a>Átjáró létrehozása
A fejlesztőnek *átjárófolyamatot* kell írnia. Ez a program létrehoz egy belső infrastruktúrát (a közvetítő), betölti a modulokat, és mindent úgy állít be, hogy megfelelően működjön. Az SDK-ban megtalálható a **Gateway_Create_From_JSON** függvényt, amellyel elindíthat egy átjárót a JSON-fájlokból. A **Gateway_Create_From_JSON** függvényt a használata előtt továbbítania kell egy olyan a JSON-fájl elérési útjára, amely meghatározza a betöltendő modulokat. 

Az átjárófolyamat kódját a Hello World mintában találja, a [main.c][lnk-main-c] fájlban. Az olvashatóság érdekében az alábbi részlet az átjáró folyamatkódjának rövidített verzióját mutatja. Ez a program létrehoz egy átjárót, majd megvárja, amíg a felhasználó lenyomja az **ENTER** billentyűt, mielőtt lebontja az átjárót. 

```
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

A JSON-beállításfájl tartalmazza a betöltendő modulok listáját. Minden modulnak meg kell határoznia a következőket:

* **module_name**: a modul egyedi neve.
* **module_path**: a modult tartalmazó könyvtár elérési útja. Linux esetén ez egy .so fájl, Windows rendszeren pedig .dll fájl.
* **args**: a modul által igényelt konfigurációs információk.

A JSON-fájl is tartalmazza a modulok közötti hivatkozásokat, amelyek a rendszer átad a közvetítőnek. Egy hivatkozás két tulajdonsággal rendelkezik:

* **source**: modulnév a `modules` szakaszból vagy „\*”.
* **sink**: modulnév a `modules` szakaszból.

Minden hivatkozás meghatároz egy üzenetútvonalat és irányt. A `source` modulból érkező üzeneteket a `sink` modulnak kell továbbítani. A `source` modult „\*” értékűre is be lehet állítani, ami azt jelzi, hogy a moduloktól érkező üzeneteket a `sink` fogja fogadni.

A következő minta a Linuxon a Hello World minta konfigurálásához használt JSON-beállításfájlt mutatja be. A `logger` modul a `hello_world` modul által létrehozott összes üzenetet fel fogja használni. A modul kialakításától függ, hogy egy modulnak szüksége van-e argumentumra. Ebben a példában a naplózómodul a kimeneti fájlt elérési útját használja argumentumként, a Hello World modul pedig nem vesz fel argumentumokat:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "loading args": {
              "module path" : "./modules/logger/liblogger_hl.so"
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "loading args": {
              "module path" : "./modules/hello_world/libhello_world_hl.so"
            },
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>A Hello World modul üzenet-közzététele
Megkeresheti a „Hello World” modul által használt kódot, hogy közzétegye a [„hello_world.c”][lnk-helloworld-c] fájlban lévő üzeneteket. Az alábbi részletben egy módosított verzió látható, további megjegyzésekkel és az olvashatóság érdekében kevesebb hibakezelési kóddal:

```
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

### <a name="hello-world-module-message-processing"></a>A Hello World modul üzenetfeldolgozása
A Hello World modulnak soha nem kell olyan üzeneteket feldolgoznia, amelyeket más modulok tesznek közzé a közvetítőn. A Hello World modulban ezért az üzenet-visszahívás megvalósítása művelet nélküli függvény.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>A naplózómodul üzenet-közzététele és -feldolgozása
A naplózó modul üzeneteket fogad a közvetítőtől, és egy fájlba írja őket. Soha nem tesz közzé üzeneteket. A naplózó modul kódja ezért soha nem hívja meg a **Broker_Publish** függvényt.

A közvetítő a [logger.c][lnk-logger-c] fájlban lévő **Logger_Recieve** függvény indításával kézbesíti az üzeneteket a naplózó modulnak. Az alábbi részletben egy módosított verzió látható, további megjegyzésekkel és az olvashatóság érdekében kevesebb hibakezelési kóddal:

```
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
Az IoT Gateway SDK használatának megismeréséhez tekintse meg a következőket:

* [IoT Gateway SDK – eszközről a felhőbe irányuló üzeneteket küldhet egy szimulált eszközzel Linuxon][lnk-gateway-simulated].
* [Azure IoT Gateway SDK][lnk-gateway-sdk] a GitHubon.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!--HONumber=Nov16_HO3-->


