## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban:

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol
* Aktivál egy szimulált belsővezérlőprogram-frissítést
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és utolsó belsővezérlőprogram-frissítésük időpontjának megállapítására

1. Hozzon létre egy **manageddevice** nevű üres mappát.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```

2. Telepítse az **azure-iot-device** és az **azure-iot-device-mqtt** eszközoldali SDK-csomagokat. Ehhez futtassa a parancssorban a következő parancsot a **manageddevice** mappában:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_fwupdate_device.js** fájlt a **manageddevice** mappában.

4. Adja hozzá a következő require utasításokat a **dmpatterns_fwupdate_device.js** fájl elejéhez:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt. Cserélje le a `{yourdeviceconnectionstring}` helyőrzőt az „Eszközidentitás létrehozása” szakaszban korábban feljegyzett kapcsolati sztringre:
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a jelentett tulajdonságok frissítésére szolgáló alábbi függvényt:
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```

7. Adja hozzá a vezérlőprogram-rendszerkép letöltését és alkalmazását szimuláló alábbi függvényeket:
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```

8. Adja hozzá a vezérlőprogram-frissítés állapotát a jelentett tulajdonságok alapján **waiting** értékre állító alábbi függvényt. Az eszközök általában értesülnek ez elérhető frissítésekről, és egy rendszergazdai házirendnek megfelelően elkezdik letölteni és alkalmazni a frissítést. Ebben a függvényben kell futnia a házirendet engedélyező logikának. Az egyszerűség kedvéért a minta megvárja-e a folytatás előtt letölti a belső vezérlőprogram lemezképet négy másodperc:
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```

9. Adja hozzá a belsővezérlőprogram-frissítés állapotát a jelentett tulajdonságok alapján **downloading** értékre állító alábbi függvényt. A függvény ezután szimulálja a belső vezérlőprogram letöltését, és végül frissíti a belsővezérlőprogram-frissítés állapotát **downloadFailed** vagy **downloadComplete** értékre:
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```

10. Adja hozzá a belsővezérlőprogram-frissítés állapotát a jelentett tulajdonságok alapján **applying** értékre állító alábbi függvényt. A függvény ezután szimulálja a belső vezérlőprogram rendszerképének alkalmazását, és végül frissíti a belsővezérlőprogram-frissítés állapotát **applyFailed** vagy **applyComplete** értékre:
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```

11. Adja hozzá a **firmwareUpdate** közvetlen metódust kezelő és a többfázisú belsővezérlőprogram-frissítési folyamatot inicializáló alábbi függvényt:
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = request.payload.fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```

12. Végül adja hozzá az IoT Hubhoz való csatlakozásra szolgáló alábbi kódot:
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése](https://msdn.microsoft.com/library/hh675232.aspx).
> 
> 