---
title: Az eszközre vonatkozó adatküldés transzparens átjárón keresztül – Machine Learning on Azure IoT Edge | Microsoft Docs
description: A fejlesztői gépet szimulált IoT Edge eszközként használva küldheti el az adatait a IoT Hub számára egy transzparens átjáróként konfigurált eszközön keresztül.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0fe05131268b8a6a6c61323289d3017231e49706
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299818"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Oktatóanyag: Az adatküldés transzparens átjárón keresztül

> [!NOTE]
> Ez a cikk egy sorozat részét képezi a Azure Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. Ha ezt a cikket közvetlenül megérkezett, javasoljuk, hogy kezdje a sorozat [első cikkével](tutorial-machine-learning-edge-01-intro.md) a legjobb eredmények érdekében.

Ebben a cikkben ismét szimulált eszközként használjuk a fejlesztői gépet, de ahelyett, hogy közvetlenül a IoT Hub küldenek adatokat, az eszköz átküldi az adatokat a IoT Edge eszközre, amelyet transzparens átjáróként konfigurált.

A IoT Edge eszköz működését a szimulált eszköz adatküldése közben Figyeljük. Ha az eszköz futása befejeződött, tekintse meg a Storage-fiókban tárolt információt, és ellenőrizze, hogy minden a várt módon működik-e.

Ezt a lépést általában egy felhő vagy egy eszköz fejlesztője hajtja végre.

## <a name="review-device-harness"></a>Eszköz-hám áttekintése

Használja újra a [DeviceHarness projektet](tutorial-machine-learning-edge-03-generate-data.md) az alsóbb rétegbeli (vagy levél) eszköz szimulálásához. Az átlátszó átjáróhoz való csatlakozáshoz két további dolog szükséges:

* Regisztrálja a tanúsítványt, hogy az alsóbb rétegbeli eszköz (ebben az esetben a fejlesztői számítógép) megbízzon a IoT Edge futtatókörnyezet által használt hitelesítésszolgáltatóban.
* Adja hozzá a peremhálózati átjáró teljes tartománynevét (FQDN) az eszköz kapcsolódási karakterláncához.

Tekintse meg a kódot, és figyelje meg, hogy a két elem hogyan lett implementálva.

1. Nyissa meg a fejlesztői gépen a Visual Studio Code-ot.

2. A **fájl** > **megnyitása mappát...** nyissa meg a\\C\\:\\Source IoTEdgeAndMlSample DeviceHarness.

3. Tekintse meg a InstallCertificate () metódust a Program.cs-ben.

4. Vegye figyelembe, hogy ha a kód megkeresi a tanúsítvány elérési útját, meghívja a CertificateManager. InstallCACert metódust a tanúsítvány telepítéséhez a gépen.

5. Most tekintse meg a GetIotHubDevice metódust a TurbofanDevice osztályban.

6. Ha a felhasználó a "-g" kapcsoló használatával adja meg az átjáró teljes tartománynevét, ez az érték a gatewayFqdn, amely az eszköz kapcsolódási karakterláncához lesz hozzáfűzve.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Leaf-eszköz létrehozása és futtatása

1. Ha a DeviceHarness projekt továbbra is meg van nyitva a Visual Studio Code-ban, hozza létre a projektet (CTRL + SHIFT + B vagy **Terminal** > **Run Build feladat...** ), majd válassza a **Létrehozás** lehetőséget a párbeszédpanelről.

2. Keresse meg a peremhálózati átjáró teljes tartománynevét (FQDN) úgy, hogy megkeresi az IoT Edge eszköz virtuális gépét a portálon, és a **DNS-név** értékét másolja az áttekintésből.

3. Nyissa meg a Visual Studio Code**Terminalt (**  > az**új terminált**), és futtassa `<edge_device_fqdn>` a következő parancsot, és cserélje le a elemet a virtuális gépről másolt DNS-névre:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Az alkalmazás megkísérli a tanúsítvány telepítését a fejlesztői gépre. Ha igen, fogadja el a biztonsági figyelmeztetést.

5. Ha a rendszer a IoT Hub kapcsolódási karakterláncra kéri, kattintson a három pontra ( **..** .) az Azure IoT hub-eszközök panelen, majd válassza a **Másolás IoT hub a kapcsolódási karakterlánc**lehetőséget. Illessze be az értéket a terminálba.

6. A következőhöz hasonló kimenetet fog látni:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Jegyezze fel a "GatewayHostName" kifejezést az eszköz kapcsolódási karakterláncához, ami azt eredményezi, hogy az eszköz az IoT Edge transzparens átjárón keresztül kommunikál a IoT Hub.

## <a name="check-output"></a>Kimenet keresése

### <a name="iot-edge-device-output"></a>IoT Edge eszköz kimenete

A avroFileWriter modul kimenete könnyen megfigyelhető a IoT Edge eszköz megkeresésével.

1. SSH-t a IoT Edge virtuális gépre.

2. Keresse meg a lemezre írt fájlokat.

   ```bash
   find /data/avrofiles -type f
   ```

3. A parancs kimenete az alábbi példához hasonlóan fog kinézni:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   A Futtatás időzítésének függvényében több fájl is lehet.

4. Figyeljen az időbélyegekre. A avroFileWriter modul feltölti a fájlokat a felhőbe, ha az utolsó módosítás ideje több mint 10 percet vesz igénybe (lásd a\_uploader.py\_-ben a avroFileWriter modul módosított fájljának időtúllépését).

5. A 10 perc eltelte után a modulnak fel kell töltenie a fájlokat. Ha a feltöltés sikeres, törli a fájlokat a lemezről.

### <a name="azure-storage"></a>Azure Storage tárterület

Megfigyelheti a levélben tárolt adatok eredményét a Storage-fiókok megtekintésével, ahol az adatok továbbítása várható.

1. A fejlesztői gépen nyissa meg a Visual Studio Code-ot.

2. A Tallózás ablak "AZURE STORAGE" paneljén navigáljon a fában, és keresse meg a Storage-fiókját.

3. Bontsa ki a **blob-tárolók** csomópontot.

4. Az oktatóanyag előző részében elvégzett munkából azt várjuk, hogy az **ruldata** -tárolónak tartalmaznia kell az RUL-val rendelkező üzeneteket. Bontsa ki a **ruldata** csomópontot.

5. Egy vagy több, a következőhöz hasonló blob-fájl `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`jelenik meg:.

6. Kattintson a jobb gombbal az egyik fájlra, és válassza a **blob letöltése** lehetőséget a fájl a fejlesztői gépre való mentéséhez.

7. Ezután bontsa ki a **uploadturbofanfiles** csomópontot. Az előző cikkben ezt a helyet a avroFileWriter modul által feltöltött fájlok céljának kell beállítani.

8. Kattintson a jobb gombbal a fájlokra, majd válassza a **blob letöltése** elemet a fejlesztői gépre való mentéshez.

### <a name="read-avro-file-contents"></a>Avro-fájl tartalmának olvasása

Egy egyszerű parancssori segédprogramot tartalmazott egy Avro-fájl olvasásához és a fájlban lévő üzenetek JSON-karakterláncának visszaküldéséhez. Ebben a szakaszban a telepítését és futtatását fogjuk telepíteni.

1. Nyisson meg egy terminált a Visual Studio Code-ban (**terminál** > **új terminál**).

2. A hubavroreader telepítése:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. A hubavroreader használatával olvassa el a **ruldata**-ból letöltött Avro-fájlt.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Vegye figyelembe, hogy az üzenet törzse a várt módon jelenik meg az eszköz azonosítója és az előre jelzett RUL alapján.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

5. Futtassa ugyanezt a parancsot a **uploadturbofanfiles**-ből letöltött Avro-fájl átadásával.

6. A várt módon ezek az üzenetek az eredeti üzenet összes érzékelő-és működési beállítását tartalmazzák. Ezeket az adattípusokat felhasználhatja a RUL modell fejlesztésére a peremhálózati eszközön.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": “<time>”
   }
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja meg a teljes körű oktatóanyag által használt erőforrásokat, várjon, amíg meg nem történik a létrehozott erőforrások tisztítása. Ha nem folytatja a műveletet, a következő lépésekkel törölheti őket:

1. Törölje a fejlesztői virtuális gép tárolásához létrehozott erőforráscsoportot (ka) t, IoT Edge VM, IoT Hub, Storage-fiók, Machine learning Workspace szolgáltatás (és létrehozott erőforrások: tároló-beállításjegyzék, Application bepillantást, Key Vault, Storage-fiók).

2. Törölje a Machine learning-projektet az [Azure jegyzetfüzetek](https://notebooks.azure.com)szolgáltatásban.

3. Ha helyileg klónozotta a tárházat, akkor a helyi tárházra hivatkozó PowerShell-vagy VS Code-alapú Windows-t zárjuk le, majd töröljük a tárház könyvtárat.

4. Ha helyileg hozott létre tanúsítványokat, törölje a c:\\edgeCertificates mappát.

## <a name="next-steps"></a>További lépések

Ebben a cikkben a fejlesztői gépet használtuk arra, hogy az érzékelőt és a működési adatokat a peremhálózati eszközön küldő levélben szimuláljuk. A rendszer ellenőrizte, hogy az eszközön lévő modulok a peremhálózati eszköz valós idejű működésének vizsgálatával, majd a Storage-fiókba feltöltött fájlok megtekintésével átirányították, besorolták, megtartották és feltöltötték az adatforrásokat.

További információt a következő lapokon talál:

* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
* [Az Azure Blob Storage IoT Edge (előzetes verzió) szolgáltatásban tárolhatja az adattárolást](how-to-store-data-blob.md)
