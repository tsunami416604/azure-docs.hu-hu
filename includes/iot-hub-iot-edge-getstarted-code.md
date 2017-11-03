## <a name="typical-output"></a>Tipikus kimenet

A következő példa bemutatja a kimeneti által a Hello World PéldaAlkalmazás ír a naplófájlba. A kimenet az olvashatóság érdekében formázva van:

```json
[{
    "time": "Mon Apr 11 13:42:50 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:42:50 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:42:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:43:00 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:45:00 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Kódrészletek

Ez a szakasz a hello\_world minta kódjának néhány szakaszát tárgyalja.

### <a name="iot-edge-gateway-creation"></a>Az IoT-peremhálózati átjáró létrehozása

Átjáró létrehozásához valósítja meg a *átjáró folyamat*. A program hoz létre a belső infrastruktúra (broker), az IoT-Edge modulok betölti és konfigurálja az átjáró folyamat. Az IoT Edge-ben megtalálható a **Gateway\_Create\_From\_JSON** függvény, amellyel elindíthat egy átjárót a JSON-fájlokból. Használatához a **átjáró\_létrehozása\_a\_JSON** működik, adja át az elérési út egy JSON-fájl, amely meghatározza az IoT-Edge modulok betöltése.

A kód találhat meg az átjáró folyamatot a *Hello World* a minta a [main.c] [ lnk-main-c] fájlt. Az olvashatóság érdekében a következő részlet az átjáró folyamatkódjának rövidített verzióját mutatja. Ez a példaprogram létrehoz egy átjárót, majd megvárja, amíg a felhasználó lenyomja az **ENTER** billentyűt, mielőtt lebontja az átjárót.

```C
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

A JSON-beállítások fájl IoT peremhálózati modulok betöltése és a modulok közötti kapcsolatok listáját tartalmazza. Minden egyes IoT peremhálózati modul a: kell megadnia.

* **name**: a modul egyedi neve.
* **loader**: olyan betöltő, amely képes a kívánt modul betöltésére. A betöltők a különböző modultípusok betöltésére szolgáló bővítménypontok. IoT peremhálózati betöltők számára biztosít a natív C, Node.js, Java és .NET írt modulok. A Hello World PéldaAlkalmazás csak használja a natív C betöltő, mert ez a példa a modulok c nyelven írt dinamikus függvénytárak Különböző nyelveken írt IoT peremhálózati modulok használatával kapcsolatos további információkért lásd: a [Node.js](https://github.com/Azure/iot-edge/blob/master/samples/nodejs_simple_sample/), [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample), vagy [.NET](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample) minták.
    * **név**: a modul betöltéséhez betöltő nevét.
    * **entrypoint**: a modult tartalmazó könyvtár elérési útja. Linux ezt a szalagtárat .so fájl, a Windows ezt a szalagtárat .dll kiterjesztésű fájlnak. A belépési pont a használt betöltőtípustól függ. A Node.js betöltő belépési pont egy .js kiterjesztésű fájl. A Java-betöltő belépési pont, egy classpath és az osztályhoz nevet. A .NET-betöltő belépési pont, egy szerelvény és osztály nevét.

* **args**: a modul által igényelt konfigurációs információk.

A következő kód bemutatja, meg kell adnia az IoT-él a Hello World PéldaAlkalmazás lehetővé tevő modulokat Linux használt JSON. A modul kialakításától függ, hogy egy modulnak szüksége van-e argumentumokra. Ebben a példában a naplózómodul a kimeneti fájl elérési útját használja argumentumként, a hello\_world modul pedig nem rendelkezik argumentumokkal.

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

* **forrás**: egy modul nevét a `modules` területen vagy `\*`.
* **sink**: modulnév a `modules` szakaszból.

Minden hivatkozás meghatároz egy üzenetútvonalat és irányt. Érkező üzenetek a **forrás** modul szállítják ki a **fogadó** modul. Beállíthatja a **forrás** modul `\*`, amely azt jelzi, hogy a **fogadó** modult a modulok üzeneteket fogad.

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

```C
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

A hello\_globális modul soha nem dolgozza fel a más IoT peremhálózati modulok közzététele az átvitelszervező-kezelési üzenetek. A hello\_world modulban ezért az üzenet-visszahívás megvalósítása művelet nélküli függvény.

```C
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-processing"></a>Naplózó modul üzenet feldolgozása

A naplózó modul üzeneteket fogad a közvetítőtől, és egy fájlba írja őket. Soha nem tesz közzé üzeneteket. A naplózó modul kódja ezért soha nem hívja meg a **Broker_Publish** függvényt.

A **Logger_Receive** működni a [logger.c] [ lnk-logger-c] fájl a visszahívást a broker hív meg a tranzakciónaplókat tartalmazó modulra üzenetek továbbítására. Az alábbi részletben egy módosított verzió látható, hozzáfűzött megjegyzésekkel és – az olvashatóság érdekében – kevesebb hibakezelési kóddal:

```C
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

Ebben a cikkben egy egyszerű, a naplófájlba írja az üzenetek IoT peremhálózati átjáró futtatta. Futtasson egy mintát, amely üzeneteket küld az IoT-központ, olvassa el:

- [IoT peremhálózati – eszközről a felhőbe üzeneteket küldeni a Linux használatával szimulált eszköz][lnk-gateway-simulated-linux] 
- [Az IoT-Edge – eszközről a felhőbe üzeneteket küldeni a szimulált eszköz a Windows][lnk-gateway-simulated-windows].


<!-- Links -->
[lnk-main-c]: https://github.com/Azure/iot-edge/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/iot-edge/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/iot-edge/blob/master/modules/logger/src/logger.c
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-gateway-simulated-linux]: ../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md
[lnk-gateway-simulated-windows]: ../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md