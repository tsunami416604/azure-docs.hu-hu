## <a name="configure-the-nodejs-simulated-device"></a>A Node.js szimulált eszköz konfigurálása
1. A távoli figyelési irányítópulton kattintson **+ hozzáad egy eszközt** majd adja hozzá a *egyéni eszköz*. Jegyezze fel az IoT Hub állomásnév, eszközazonosító és eszközkulcs. Kell őket az oktatóanyag későbbi részében a remote_monitoring.js eszköz ügyfélalkalmazás előkészítésekor.
2. Győződjön meg arról, hogy a Node.js-verzió 0.12.x, vagy később telepítve van a fejlesztési számítógépén. Futtatás `node --version` parancsot a parancssorba vagy egy rendszerhéj verziójának. A Csomagkezelő Node.js telepítését Linux használatával kapcsolatos információkért lásd: [keresztül Csomagkezelő telepítésének Node.js][node-linux].
3. Ha már telepítette a Node.js, klónozza a legújabb verzióját a [azure iot-sdk-csomópontos] [ lnk-github-repo] tárház a fejlesztési számítógépen. Mindig a **fő** ág legújabb verzióját a függvénytárak és minták.
4. Helyi másolatát a [azure iot-sdk-csomópontos] [ lnk-github-repo] -tárházban, a következő két fájlok másolása a csomópont/eszköz/minták mappából üres mappához a fejlesztési számítógépén:
   
   * Packages.JSON
   * remote_monitoring.js
5. Nyissa meg a remote_monitoring.js fájlt, és keresse meg a következő változó definíciója:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Cserélje le **[IoT Hub eszköz kapcsolati karakterlánc]** az eszköz kapcsolati karakterlánccal. Az IoT-központ állomásnév, eszközazonosító és jegyezze fel az 1. lépésben végzett eszközkulcs értékeit használja. Egy eszköz kapcsolati karakterlánc formátuma a következő:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Ha az IoT-központ állomásnevet **contoso** , és az eszközazonosítót **mydevice**, a kapcsolati karakterláncot a következőképpen néz a következő kódrészletet:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Mentse a fájlt. A következő parancsokat a rendszerhéj vagy a parancssorban a mappában, amely tartalmazza ezeket a fájlokat a szükséges csomagok telepítése, és futtassa a mintaalkalmazást:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Vegye figyelembe a dinamikus telemetriai művelet:
Az irányítópulton látható a hőmérséklet és a páratartalom telemetriai adatokat a meglévő szimulált eszközökről:

![Az alapértelmezett irányítópult][image1]

Ha az előző szakaszban futtatta a Node.js szimulált eszköz, látni hőmérséklet, páratartalom és külső hőmérséklet telemetriai adatokat:

![Külső hőmérséklet hozzáadása az irányítópult][image2]

A távoli felügyeleti megoldás automatikusan észleli a további külső hőmérséklet telemetriai adatok típusát, és hozzáadja azt a diagram az irányítópulton.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png