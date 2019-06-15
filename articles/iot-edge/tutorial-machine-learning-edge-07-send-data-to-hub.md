---
title: Transzparens átjáró – Machine Learning az Azure IoT Edge-en keresztül Eszközadatok küldése |} A Microsoft Docs
description: A fejlesztői gépén használjuk egy szimulált IoT Edge-eszköz adatokat küldeni az IoT hubon az eszközön a transzparens átjáróként konfigurált eszköz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12793ff28bf13f26bc2cc3d436b644601fc48ac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081384"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Oktatóanyag: Transzparens átjáró adatküldést

> [!NOTE]
> Ez a cikk egy sorozat része az IoT Edge-ben az Azure Machine Learning használatával kapcsolatos oktatóanyagot. Ha érkezett, ez a cikk közvetlenül, javasoljuk, hogy először a [először cikk](tutorial-machine-learning-edge-01-intro.md) a sorozat a legjobb eredmények.

Ebben a cikkben egy szimulált eszközt a fejlesztői gépen ismét vesszük, de helyett adatokat közvetlenül az IoT hubnak küldi az eszköz adatokat küld a transzparens átjáróként konfigurált IoT Edge-eszközökön.

A művelet az IoT Edge-eszköz azt figyelheti, amíg a szimulált eszköz adatokat küld. Miután befejezte az eszköz fut, nézzük meg azt az adatok a storage-fiók érvényesítéséhez, minden rendben működött a várt módon.

Ezt a lépést általában a felhőben vagy az eszköz fejlesztő végzi.

## <a name="review-device-harness"></a>Tekintse át az eszköz hasznosítása

Újból felhasználhatja a [DeviceHarness projekt](tutorial-machine-learning-edge-03-generate-data.md) eszköz szimulálása az alsóbb rétegbeli (vagy levél). A transzparens átjáróhoz való csatlakozáskor szükséges további két dolgokat:

* Regisztráljon, hogy az alsóbb rétegbeli eszköz a tanúsítvány (jelen esetben a fejlesztői gépen) az IoT Edge-futtatókörnyezete által használt hitelesítésszolgáltató.
* Adja hozzá a peremhálózati átjáró teljes tartománynevét (FQDN), az eszköz kapcsolati karakterláncát.

Tekintse meg a kódot, hogy hogyan valósíthatók meg a két elem.

1. A fejlesztési számítógépén nyissa meg a Visual Studio Code-ot.

2. Használat **fájl** > **mappa megnyitása...**  C: megnyitásához\\forrás\\IoTEdgeAndMlSample\\DeviceHarness.

3. Tekintse meg a program.cs fájlban a InstallCertificate() módszert.

4. Vegye figyelembe, hogy a kódot úgy találja, a tanúsítvány elérési útja, ha meghívja a CertificateManager.InstallCACert metódust a tanúsítvány telepítése a gépre.

5. Most nézzük meg a GetIotHubDevice metódus a TurbofanDevice osztályban.

6. Amikor a felhasználó az átjáró használatával teljes Tartománynevét adja meg a "-g" lehetőséget, hogy objektumnak átadott érték ezt a módszert, gatewayFqdn, amely az eszköz kapcsolati karakterláncának beolvasása hozzáfűzve.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Hozza létre és futtassa a levél eszköz

1. A projekttel DeviceHarness még mindig nyitva a Visual Studio Code, állítsa össze a projektet (Ctrl + Shift + B vagy **terminálon** > **létrehozása feladat futtatása...** ), és válassza ki **összeállítása** a párbeszédpanelről.

2. Az IoT Edge eszköz virtuális géphez a portálon, és másolja az értéket a teljesen minősített tartománynevét (FQDN) a peremhálózati átjáró található **DNS-név** ismeretanyagot.

3. Nyissa meg a Visual Studio Code terminált (**terminál** > **új terminál**), és futtassa a következő parancsot, és cserélje le `<edge_device_fqdn>` a DNS-névvel, a virtuális gépről kimásolt:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Az alkalmazást próbál meg telepíteni a tanúsítványt a fejlesztői gépére. Ha igen, fogadja el a biztonsági figyelmeztetést.

5. Ha a rendszer kéri az IoT Hub kapcsolati karakterlánc kattintson a három pontra ( **...** ) az Azure IoT Hub az Eszközök panelen, és válassza ki **másolási IoT Hub kapcsolati karakterláncra**. Illessze be az értéket a terminálon.

6. Hasonló kimenet jelenik meg:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Megjegyzés: a "GatewayHostName" az eszköz kapcsolati karakterláncát, amely az eszköz kommunikáljon az IoT hubon az IoT Edge transzparens átjárón keresztül történő hozzáadását.

## <a name="check-output"></a>Kimenet ellenőrzése

### <a name="iot-edge-device-output"></a>IoT Edge-eszköz kimenete

A kimenet a avroFileWriter modul könnyen figyelhető megnézzük az IoT Edge-eszközökön.

1. SSH-t az IoT Edge virtuális gép.

2. Írt fájlokat lemezre.

   ```bash
   find /data/avrofiles -type f
   ```

3. A parancs kimenete az alábbihoz hasonlóan fog kinézni:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Előfordulhat, hogy több mint függően időzítését, a Futtatás egyetlen fájl rendelkezik.

4. Az időbélyegek figyelmet fordítania. A avroFileWriter modul feltölti a fájlokat a felhőben, az utolsó módosítás időpontja a múltban a több mint 10 perc után (lásd: a módosított\_fájl\_a avroFileWriter modul uploader.py IDŐKORLÁTJA).

5. Ha a 10 perc eltelt, a modul kell töltse fel a fájlokat. Ha a feltöltés sikeres, a lemezről törli a fájlokat.

### <a name="azure-storage"></a>Azure Storage tárterület

Azt is figyelje meg az eredményeket az adatok küldése a storage-fiókok megnézzük, ha várhatóan át szeretné irányítani adatok levél eszköz.

1. A fejlesztési számítógépén nyissa meg a Visual Studio Code-ot.

2. Az "AZURE STORAGE" panelen az Intéző ablakban keresse meg a fán, keresse meg a tárfiókot.

3. Bontsa ki a **Blobtárolók** csomópont.

4. Az oktatóanyag előző részében végrehajtott munkát, a várhatóan a **ruldata** tároló üzenetek az rul-JÉNEK tartalmaznia kell. Bontsa ki a **ruldata** csomópont.

5. Láthatja, hogy egy vagy több hasonló nevű blob fájlokat: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. A jobb gombbal a egy fájlt, és válassza ki **Blob letöltése** , mentse a fájlt a fejlesztői gépére.

7. Ezután bontsa ki a **uploadturbofanfiles** csomópont. Az előző cikkben azt állítja be ezen a helyen a cél a avroFileWriter modul által feltöltött fájlokat.

8. A jobb gombbal a fájlokat, és válassza ki **Blob letöltése** arra, hogy mentse a fejlesztői gépére.

### <a name="read-avro-file-contents"></a>Olvassa el az Avro-fájl tartalma

Egyszerű parancssori segédprogram az Avro-fájlból olvasva és a egy JSON-karakterlánc, az üzenetek visszaadása a fájl tartalmazza azt. Ebben a szakaszban azt fogja telepíteni, és futtathatja.

1. Nyisson meg egy terminált a Visual Studio Code (**terminál** > **új terminál**).

2. Hubavroreader telepítése:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Olvassa el a Avro-fájlt a letöltött hubavroreader használatával **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Vegye figyelembe, hogy az üzenet törzse úgy tűnik, hogy Eszközazonosítót használó várt és az előre jelzett rul-JÉNEK.

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

5. Futtassa ugyanezt a parancsot a Avro-fájlt a letöltött passing **uploadturbofanfiles**.

6. Megfelelően működik, ezeket az üzeneteket tartalmaznak a érzékelőktől kapott adatok és működési beállításait az eredeti üzenet alapján. Ezek az adatok javítása az RUL-modellről az edge-eszközön használható.

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

Ha azt tervezi, ez az oktatóanyag teljes körű által használt erőforrások tallózása, várjon, amíg az elkészült, az Ön által létrehozott erőforrásokat. Ha nem tervezi, a következő lépések használatával törölheti őket:

1. A fejlesztési VM, IoT Edge virtuális gép, IoT Hub, tárfiók, machine learning-munkaterület szolgáltatási tárolására szolgáló erőforrás csoport(ok) törlése (és a létrehozott erőforrások: tárolóregisztrációs adatbázis, az application insights, a key vault, a storage-fiók).

2. A machine learning-projektet a Törlés [Azure notebookok](https://notebooks.azure.com).

3. Ha Ön az adattár klónja helyileg, zárja be a helyi tárháznak hivatkozó bármely PowerShell vagy a VS Code windows, majd törölje a tárház könyvtárat.

4. Ha tanúsítványokat helyileg létrehozott, törölje a mappát a c:\\edgeCertificates.

## <a name="next-steps"></a>További lépések

Ez a cikk a fejlesztői gépen használtuk, amelyek egy levél eszköz küldő érzékelő- és operatív adatok az edge-eszközön. Azt ellenőrzi, hogy a modulokat az eszközön irányítja, besorolni, megőrzött, és adatokat töltött fel az először az edge-eszköz valós idejű működésének megvizsgálásával és megnézzük a storage-fiókhoz feltöltött fájlokat.

További információkat tekinthet meg a következő lapokon:

* [Egy alárendelt eszköz csatlakoztatása az Azure IoT Edge-átjáró](how-to-connect-downstream-device.md)
* [Az Azure Blob Storage a peremhálózaton data Store az IoT Edge-ben (előzetes verzió)](how-to-store-data-blob.md)
