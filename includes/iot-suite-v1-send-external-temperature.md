## <a name="configure-the-nodejs-simulated-device"></a>A Node.js szimulált eszköz konfigurálása
1. Kattintson a távoli figyelési irányítópult **+ eszköz hozzáadása** majd adja hozzá a *egyéni eszköz*. Jegyezze fel az IoT Hub állomásnév, az eszközazonosítót és az eszköz kulcsát. Kell őket az oktatóanyag későbbi részében amikor előkészíti a remote_monitoring.js eszközügyfél-alkalmazáshoz.
2. Győződjön meg arról, hogy a Node.js verzió 0.12.x vagy újabb van telepítve a fejlesztői gépen. Futtatás `node --version` parancsot a parancssorba vagy egy rendszerhéj a verzió ellenőrzéséhez. A Node.js telepítéséhez linuxon Csomagkezelő használatával kapcsolatos információkért lásd: [Node.js telepítése a Csomagkezelő keresztül][node-linux].
3. Ha már telepítette a Node.js, klónozza a legújabb verzióját a [azure-iot-sdk-csomópont] [ lnk-github-repo] adattárat a fejlesztői gépére. Mindig használja a **fő** ág a függvénytárak és minták a legújabb verzióra.
4. A helyi példányának a [azure-iot-sdk-csomópont] [ lnk-github-repo] tárházban, a következő két fájlok másolása a csomópont/eszköz/samples mappában az üres mappa a fejlesztői gépen:
   
   * Packages.JSON
   * remote_monitoring.js
5. Nyissa meg a remote_monitoring.js fájlt, és keresse meg a következő változó definícióját:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Cserélje le **[az IoT Hub eszköz kapcsolati karakterláncának]** az eszköz kapcsolati karakterlánccal. Az IoT Hub-állomásnévvel, az eszközazonosítót és jegyezze fel az 1. lépésben végzett eszközkulcs értékeit használja. Egy eszköz kapcsolati karakterláncának formátuma a következő:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Ha az IoT Hub-eszköznév **contoso** és az eszköz azonosítója **sajáteszköz**, a kapcsolati karakterláncnak a következő kódrészlethez hasonló néz ki:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Mentse a fájlt. Futtassa a következő parancsokat egy rendszerhéj vagy a parancssorban a mappában, amely tartalmazza ezeket a fájlokat, majd futtassa a mintaalkalmazást, és a szükséges csomagok telepítéséhez:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Figyelje meg a művelet dinamikus telemetria
Az irányítópulton a meglévő szimulált eszközökről hőmérséklettel és páratartalommal kapcsolatos telemetriát jelenít meg:

![Az alapértelmezett irányítópult][image1]

Ha kiválasztja a szimulált eszköz Node.js, az előző szakaszban futtatta, hőmérséklet, a páratartalmat és a külső hőmérsékleti telemetria láthatja:

![Külső hőmérséklet hozzáadása az irányítópulthoz][image2]

A távoli figyelési megoldás automatikusan észleli a külső hőmérséklet további telemetria típusa, és hozzáadja azt a diagramot az irányítópulton.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png