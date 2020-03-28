---
title: 'Oktatóanyag: Eszközadatok küldése transzparens átjárón keresztül – Machine Learning az Azure IoT Edge-en'
description: Ez az oktatóanyag bemutatja, hogyan használhatja a fejlesztői gépet szimulált IoT Edge-eszközként az IoT Hubba egy transzparens átjáróként konfigurált eszközön keresztül.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706911"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Oktatóanyag: Adatok küldése transzparens átjárón keresztül

> [!NOTE]
> Ez a cikk az Azure Machine Learning IoT Edge-en való használatával kapcsolatos oktatóanyag sorozatának része. Ha megérkezett ezt a cikket közvetlenül, javasoljuk, hogy kezdődik az [első cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredményt.

Ebben a cikkben ismét a fejlesztői gépet szimulált eszközként használjuk, de ahelyett, hogy közvetlenül az IoT Hubnak küldenénk az adatokat, az eszköz adatokat küld az Átlátszó átjáróként konfigurált IoT Edge-eszköznek.

Figyeljük az IoT Edge-eszköz működését, miközben a szimulált eszköz adatokat küld. Miután az eszköz futása befejeződött, megnézzük a tárfiókban lévő adatokat, hogy minden a várt módon működjön.

Ezt a lépést általában egy felhő- vagy eszközfejlesztő hajtja végre.

## <a name="review-device-harness"></a>Az eszköz kábelkötegjnek áttekintése

Használja újra a [DeviceHarness projektet](tutorial-machine-learning-edge-03-generate-data.md) az alsóbb rétegbeli (vagy levél) eszköz szimulálására. Az átlátszó átjáróhoz való csatlakozáshoz két további dologra van szükség:

* Regisztrálja a tanúsítványt, hogy az alsóbb rétegbeli eszköz (ebben az esetben a fejlesztői gép) megbízik az IoT Edge futásideje által használt hitelesítésszolgáltatóban.
* Adja hozzá a peremhálózati átjáró teljesen minősített tartománynevét (FQDN) az eszköz kapcsolati karakterláncához.

Nézd meg a kódot, hogy hogyan valósítják meg ezt a két elemet.

1. A fejlesztőgépen nyissa meg a Visual Studio Code alkalmazást.

2. Használja **a Fájlmegnyitása** > **mappát...** a C:\\source\\IoTEdgeAndMlSample\\DeviceHarness megnyitásához.

3. Tekintse meg az InstallCertificate() metódust Program.cs.

4. Ne feledje, hogy ha a kód megtalálja a tanúsítvány elérési útját, meghívja a CertificateManager.InstallCACert metódust a tanúsítvány telepítéséhez a számítógépen.

5. Most nézd meg a GetIotHubDevice metódust a TurbofanDevice osztályban.

6. Amikor a felhasználó megadja az átjáró teljes tartománynát a "-g" beállítással, akkor ez az érték átjáróFqdn-ként kerül átadásra ennek a metódusnak, amely hozzáfűzi az eszköz kapcsolati karakterláncához.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Levéleszköz létrehozása és futtatása

1. Ha a DeviceHarness projekt még mindig meg van nyitva a Visual Studio-kódban, építse fel a projektet (Ctrl + Shift + B vagy **Terminál** > **futtatási buildelési feladat...**), és válassza a **párbeszédpanel Build** lehetőséget.

2. Keresse meg a teljes mértékben minősített tartománynevet (FQDN) a peremhálózati átjáróhoz úgy, hogy az IoT Edge-eszköz virtuális gépére navigál a portálon, és átmásolja a **DNS-név** értékét az áttekintésből.

3. Nyissa meg a Visual**Terminal** > Studio Code terminált ( Terminal `<edge_device_fqdn>` New**Terminal**), és futtassa a következő parancsot, és cserélje ki a virtuális gépről másolt DNS-nevet:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Az alkalmazás megpróbálja telepíteni a tanúsítványt a fejlesztői gépre. Ha ez megtörténik, fogadja el a biztonsági figyelmeztetést.

5. Amikor a rendszer kéri az IoT Hub kapcsolati karakterláncát, kattintson a három pontra (**...**) az Azure IoT Hub eszközök panelen, és válassza az **IoT Hub-kapcsolati karakterlánc másolása**lehetőséget. Illessze be az értéket a terminálba.

6. Látni fogja kimenet, mint például:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Vegye figyelembe a "GatewayHostName" hozzáadása az eszköz kapcsolati karakterláncához, ami miatt az eszköz az IoT-központon keresztül kommunikál az IoT Edge transzparens átjárón keresztül.

## <a name="check-output"></a>Kimenet ellenőrzése

### <a name="iot-edge-device-output"></a>IoT Edge-eszköz kimenete

Az avroFileWriter modul kimenete könnyen megfigyelhető az IoT Edge-eszköz vizsgálatával.

1. SSH-t az IoT Edge virtuális gépbe.

2. Keresse meg a lemezre írt fájlokat.

   ```bash
   find /data/avrofiles -type f
   ```

3. A parancs kimenete a következő példához hasonlóan fog kinézni:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Előfordulhat, hogy a futtatás időzítésétől függően egynél több fájl is előfordulhat.

4. Figyeljen az időbélyegre. Az avroFileWriter modul feltölti a fájlokat a felhőbe, ha az utolsó módosítás\_\_ido több mint 10 perc a múltban (lásd a MÓDOSÍTOTT FÁJL TIMEOUT uploader.py az avroFileWriter modul).

5. A 10 perc eltelte után a modulnak fel kell töltenie a fájlokat. Ha a feltöltés sikeres, törli a fájlokat a lemezről.

### <a name="azure-storage"></a>Azure Storage-tárterület

Megfigyelhetjük a levéleszköz adatok küldésének eredményeit, ha megtekintjük azokat a tárfiókokat, ahol az adatok továbbítására számítunk.

1. A fejlesztőgépen nyissa meg a Visual Studio Code.On the development machine open Visual Studio Code.

2. A felfedezésablak "AZURE STORAGE" paneljén navigáljon a fában a tárfiók megkereséséhez.

3. Bontsa ki a **Blob-tárolók** csomópontját.

4. A munka tettünk az előző része az oktatóanyag, azt várjuk, hogy a **ruldata** tároló tartalmaznia kell üzeneteket RUL. Bontsa ki a **ruldata** csomópontot.

5. Egy vagy több blobfájl neve `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`a következő: .

6. Kattintson a jobb gombbal az egyik fájlra, és válassza a **Blob letöltése** parancsot a fájl fejlesztési gépre való mentéséhez.

7. Ezután bontsa ki a **uploadturbofanfiles** csomópontot. Az előző cikkben ezt a helyet az avroFileWriter modul által feltöltött fájlok célként állítottuk be.

8. Kattintson a jobb gombbal a fájlokat, és válassza **a Blob letöltése** gombot, hogy mentse a fejlesztői gépre.

### <a name="read-avro-file-contents"></a>Az Avro-fájl tartalmának olvasása

Mi is egy egyszerű parancssori segédprogram olvasására Avro fájlt, és visszatér a JSON string az üzenetek a fájlban. Ebben a szakaszban telepítjük és futtatjuk.

1. Nyisson meg egy terminált a Visual Studio Code **(Terminal** > **New Terminal) programban.**

2. Telepítse hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. A hubavroreader segítségével olvassa el a **ruldata-ból**letöltött Avro-fájlt.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Vegye figyelembe, hogy az üzenet törzse úgy néz ki, ahogy vártuk az eszközazonosító val és az előre jelzett RUL.Note that the body of the message looks as we expected with device ID and predicted RUL.

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

5. Futtassa ugyanazt a parancsot halad az Avro fájlt, amit letöltött **uploadturbofanfiles**.

6. Ahogy az várható volt, ezek az üzenetek tartalmazzák az összes érzékelő adatok és működési beállításokat az eredeti üzenetet. Ezeket az adatokat fel lehet használni, hogy javítsa a RUL modell a mi edge eszköz.

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
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy feltárja a végpontok utáni oktatóanyag által használt erőforrásokat, várjon, amíg befejezi a létrehozott erőforrások karbantartását. Ha nem tervezi a folytatást, törölje őket az alábbi lépésekkel:

1. Törölje a fejlesztői virtuális gép, az IoT Edge virtuális gép, az IoT Hub, a tárfiók, a gépi tanulási munkaterület-szolgáltatás (és létrehozott erőforrások: tároló beállításjegyzék, alkalmazáselemzési adatok, key vault, tárfiók tárolására létrehozott erőforráscsoport(ok)at.

2. Törölje a gépi tanulási projektet az [Azure-jegyzetfüzetekben.](https://notebooks.azure.com)

3. Ha helyileg klónozta a tárház, zárja be a Helyi tárházra hivatkozó PowerShell- vagy VS-kód-ablakokat, majd törölje a tárházkönyvtárat.

4. Ha helyileg hozta létre a tanúsítványokat, törölje a c:\\edgeCertificates mappát.

## <a name="next-steps"></a>További lépések

Ebben a cikkben fejlesztési gépünket használtuk egy levéleszköz szimulálására, amely érzékelőt és működési adatokat küld a peremhálózati eszközünkre. Ellenőriztük, hogy az eszközön lévő modulok irányították, osztályozták, megőrizték és feltöltötték az adatokat először a peremhálózati eszköz valós idejű működésének vizsgálatával, majd a tárfiókba feltöltött fájlok vizsgálatával.

További információ a következő oldalakon található:

* [Lefelé irányuló eszköz csatlakoztatása Azure IoT Edge-átjáróhoz](how-to-connect-downstream-device.md)
* [Adatok tárolása a peremhálózaton az Azure Blob Storage segítségével az IoT Edge-en (előzetes verzió)](how-to-store-data-blob.md)
