---
title: Protokollpuffereket használata az eszköz szimulálása – Azure |} A Microsoft Docs
description: Ez az útmutató elsajátíthatja Protokollpuffereket használata szerializálni az Eszközszimuláció megoldásgyorsító által küldött telemetriát.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 64470a1497a287f4cc2c3ef3ed29986382aeac9b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285032"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetria Protokollpuffereket használ szerializálása

Protokollpuffereket (Protopuf) olyan bináris szerializálási formátum a strukturált adatok esetében. Protopuf célja, hogy hangsúlyozzák az egyszerűség és a teljesítmény, amelynek az a célja, hogy kisebb és gyorsabban XML.

Eszközszimuláció támogatja a **proto3** a protokoll verziója puffereli nyelv.

Protopuf szerializálni az adatokat a lefordított kódot igényel, mert az Eszközszimuláció testreszabott verzióját kell létrehozni.

A útmutatóval-to-útmutatóban leírt lépések bemutatják, hogyan való:

1. A fejlesztőkörnyezet előkészítése
1. Adja meg a Protopuf formátumot használ egy eszköz modellje
1. A Protopuf formátumának megadása
1. Hozzon létre Protopuf osztályok
1. Helyi tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ez az Útmutató lépéseit kövesse, az alábbiak szükségesek:

* Visual Studio Code. Letöltheti a [Mac, Linux és Windows Visual Studio Code](https://code.visualstudio.com/download).
* A .NET core. Letöltheti a [.NET Core for Mac, Linux és Windows](https://www.microsoft.com/net/download).
* Postman. Letöltheti a [Mac, windows vagy Linux rendszerű Postman](https://www.getpostman.com/apps).
* Egy [üzembe helyezve az Azure-előfizetéshez az IoT hub](../iot-hub/iot-hub-create-through-portal.md). A jelen útmutató lépéseit az IoT hub kapcsolati karakterláncra van szüksége. A kapcsolati karakterlánc kaphat az Azure Portalról.
* A [Cosmos DB-adatbázis üzembe helyezve az Azure-előfizetéshez](../cosmos-db/create-sql-api-dotnet.md#create-a-database-account) , amely az SQL API-t használ, és megfelelően van konfigurálva a [erős konzisztencia](../cosmos-db/manage-account.md). A jelen útmutató lépéseit a Cosmos DB-adatbázis kapcsolati karakterláncra van szüksége. A kapcsolati karakterlánc kaphat az Azure Portalról.
* Egy [üzembe helyezve az Azure-előfizetéshez az Azure storage-fiók](../storage/common/storage-quickstart-create-account.md). A jelen útmutató lépéseit a tárfiók kapcsolati karakterláncra van szüksége. A kapcsolati karakterlánc kaphat az Azure Portalról.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

Hajtsa végre a következő feladatokat a fejlesztési környezet előkészítését:

* Töltse le az eszköz szimulálása mikroszolgáltatás forrását.
* Töltse le a tárolási adapter mikroszolgáltatás forrását.
* A tárolási adapter mikroszolgáltatások helyi futtatásához.

A jelen cikkben lévő utasítások feltételezik, hogy Windows használata esetén. Ha egy másik operációs rendszert használ, szükség lehet néhány fájlelérési utakat és parancsok a környezethez illeszkedve kell módosítani.

### <a name="download-the-microservices"></a>Töltse le a mikroszolgáltatások

Töltse le és csomagolja ki a [távoli figyelés Mikroszolgáltatások](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) egy megfelelő helyre a helyi gépen a githubról. Ez a tárház magában foglalja a tárolási adapter mikroszolgáltatás szüksége az ebben az útmutatóban.

Töltse le és csomagolja ki a [eszköz szimulálása mikroszolgáltatás](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) egy megfelelő helyre a helyi gépen a githubról.

### <a name="run-the-storage-adapter-microservice"></a>A tárolási adapter mikroszolgáltatás futtatása

A Visual Studio Code-ban nyissa meg a **remote-monitoring-services-dotnet-master\storage-adapter** mappát. Kattintson bármelyik **visszaállítása** gombok feloldatlan függőségek javításához.

Nyissa meg a **.vscode/launch.json** fájlt, és rendelje hozzá a Cosmos DB kapcsolati karakterláncot a **számítógépek\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** környezet a változó.

> [!NOTE]
> Amikor futtatja a mikroszolgáltatási helyileg a számítógépen, továbbra is megfelelően működjön az Azure-ban egy Cosmos DB-példányra van szükség.

A tárolási adapter mikroszolgáltatások helyi futtatásához kattintson **Debug \> Start Debugging**.

A **terminálon** ablak a Visual Studio Code többek között a webalkalmazás állapot-ellenőrzése egy URL-címet a futó mikroszolgáltatási kimenetét mutatja: <http://127.0.0.1:9022/v1/status>. Erre a címre lépve, a kell lennie az állapot "OK: tartási és jól".

Hagyja meg a tárolási adapter mikroszolgáltatás jelen példánya a Visual Studio Code-ban futó közben a következő lépéseket.

## <a name="define-your-device-model"></a>Adja meg az eszköz modellje

Nyissa meg a **eszköz-szimuláció-dotnet-master** mappát a Visual Studio Code egy új példányát a Githubról letöltötte. Kattintson bármelyik **visszaállítása** kijavításához gombok feloldatlan függőségek.

Az útmutatóval-to-útmutatóban egy eszköz tracker új eszköz modelljét hoz létre:

1. Hozzon létre egy új eszköz modell fájlt nevű **assettracker-01.json** a a **Services\data\devicemodels** mappát.

1. Az eszköz funkcióinak definiálhatja az eszközmodell **assettracker-01.json** fájlt. A Protopuf eszközmodell telemetriai szakaszában kell:

    * A Protopuf osztály hozza létre az eszköz neve is. A következő szakasz bemutatja, hogyan hozhat létre, ez az osztály.
    * Adja meg a Protopuf az üzenet formátuma.

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "assettracker-01",
      "Version": "0.0.1",
      "Name": "Asset Tracker",
      "Description": "An asset tracker with location, temperature, and humidity",
      "Protocol": "AMQP",
      "Simulation": {
        "InitialState": {
          "online": true,
          "latitude": 47.445301,
          "longitude": -122.296307,
          "temperature": 38.0,
          "humidity": 62.0
        },
        "Interval": "00:01:00",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "assettracker-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "AssetTracker",
        "Location": "Field",
        "Latitude": 47.445301,
        "Longitude": -122.296307
      },
      "Telemetry": [
        {
          "Interval": "00:00:10",
          "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
          "MessageSchema": {
            "Name": "assettracker-sensors;v1",
            "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
            "Format": "Protobuf",
            "Fields": {
              "latitude": "double",
              "longitude": "double",
              "temperature": "double",
              "humidity": "double"
            }
          }
        }
      ]
    }
    ```

### <a name="create-device-behaviors-script"></a>Eszköz viselkedések parancsprogram létrehozása

A viselkedés parancsfájl, amely meghatározza, hogy az eszköz viselkedésének írásához. További információkért lásd: [egy speciális szimulált eszköz létrehozása](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>A Protopuf formátumának megadása

Ha egy eszköz modellje és az üzenet formátuma megállapította, létrehozhat egy **proto** fájlt. Az a **proto** fájlt ad hozzá:

* A `csharp_namespace` , amely megfelel a **ClassName** tulajdonságot az eszköz modellje.
* Egy üzenet az egyes adatstruktúra szerializálásához.
* Egy nevet, írja be az egyes mezőkhöz, az üzenetben.

1. Hozzon létre egy új fájlt **assettracker.proto** a a **Services\Models\Protobuf\proto** mappát.

1. A szintaxist, névtér és az üzenet séma megadása a **proto** fájlt az alábbiak szerint:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

A `=1`, `=2` minden elem jelölőinek adja meg a mező használ egyedi kódot a bináris kódolás. Számok 1-15 egy kisebb, mint a magasabb számok újabb kódolása bájt kell.

## <a name="generate-the-protobuf-class"></a>A Protopuf osztály létrehozása

Ha rendelkezik egy **proto** fájlt, a következő lépésre az üzenetek olvasása és írása szükséges osztályok létrehozásához. Ez a lépés végrehajtásához szükséges a **Protoc** Protopuf fordítóprogram.

1. [A Protopuf fordító letöltése a githubról](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Futtassa a fordító a forráskönyvtár a célkönyvtárban duplikátum és a nevét adja meg a **proto** fájlt. Példa:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Ez a parancs létrehoz egy **Assettracker.cs** fájlt a **Services\Models\Protobuf** mappát.

## <a name="test-protobuf-locally"></a>Teszt Protopuf helyileg

Ebben a szakaszban az előző szakaszokban helyileg létrehozott eszköz tracker eszköz teszteléséhez.

### <a name="run-the-device-simulation-microservice"></a>Az eszköz szimulálása mikroszolgáltatás futtatása

Nyissa meg a **.vscode/launch.json** fájlt, és rendelje hozzá a:

* Az IoT Hub kapcsolati karakterláncot a **számítógépek\_IOTHUB\_CONNSTRING** környezeti változót.
* Tárfiók kapcsolati sztringje, a **számítógépek\_AZURE\_tárolási\_fiók** környezeti változót.
* A cosmos DB kapcsolati karakterláncot a **számítógépek\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** környezeti változót.

Nyissa meg a **WebService/Properties/launchSettings.json** fájlt, és rendelje hozzá a:

* Az IoT Hub kapcsolati karakterláncot a **számítógépek\_IOTHUB\_CONNSTRING** környezeti változót.
* Tárfiók kapcsolati sztringje, a **számítógépek\_AZURE\_tárolási\_fiók** környezeti változót.
* A cosmos DB kapcsolati karakterláncot a **számítógépek\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** környezeti változót.

Nyissa meg a **WebService\appsettings.ini** fájlt, és módosítsa a beállításokat az alábbiak szerint:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Az új eszköz modell fájlokról a megoldás konfigurálása

Alapértelmezés szerint az új eszköz modellje JSON és .js fájlokkal nem másolhatók a beépített megoldásba. Tünteti kell.

Adjon hozzá egy bejegyzést, hogy a **services\services.csproj** az egyes fájlokra kívánt része. Példa:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

A mikroszolgáltatások helyi futtatásához kattintson **Debug \> Start Debugging**.

A **terminálon** ablak a Visual Studio Code-ban a futó mikroszolgáltatási kimenetét jeleníti meg.

Hagyja meg az eszköz szimulálása mikroszolgáltatás jelen példánya a Visual Studio Code-ban futó a következő lépések végrehajtása.

### <a name="set-up-a-monitor-for-device-events"></a>Állítsa be az eszközeseményeket egy figyelő.

Ebben a szakaszban használhatja az Azure CLI-figyelő beállítása csatlakozik az IoT hub az eszközök által küldött telemetriai adatok megtekintéséhez.

Az alábbi parancsfájl feltételezi, hogy az IoT hub nevére **eszköz-szimuláció-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Hagyja meg a figyelő a szimulált eszközök tesztelése során.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Hozzon létre egy szimuláció az eszközintelligencia tracker eszköz típusa

Ebben a szakaszban használatával a Postman eszközzel kérése az eszköz szimulálása mikroszolgáltatás-szimuláció, az eszköz tracker eszköz típus használatával. Postman olyan eszköz, amely lehetővé teszi a webszolgáltatások REST-kérelmeket küldjön.

A Postman beállítása:

1. Nyissa meg a Postmant a helyi gépen.

1. Kattintson a **fájl \> importálás**. Kattintson a **fájlok kiválasztása**.

1. Válassza ki **Azure IoT-eszköz szimulálása megoldás accelerator.postman\_gyűjtemény** és **Azure IoT-eszköz szimulálása megoldás accelerator.postman\_környezet** és Kattintson a **nyílt**.

1. Bontsa ki a **Azure IoT-eszköz szimulálása megoldásgyorsító** elküldheti a kérések megtekintéséhez.

1. Kattintson a **nem környezet** válassza **Azure IoT-eszköz szimulálása megoldásgyorsító**.

Most már rendelkezik egy gyűjtemény és a Postman-munkaterületen, amellyel kommunikálhat az eszköz szimulálása mikroszolgáltatás betöltött környezetben.

Konfigurálja, és a szimuláció futtatása:

1. A Postman-gyűjteményben, válassza ki a **eszköz tracker szimuláció létrehozása** kattintson **küldése**. Ezt a kérést hoz létre a szimulált eszköz tracker eszköz típusú négy példány.

1. Az esemény figyelése az Azure parancssori felület ablakában megjelenítheti a telemetriát a szimulált eszközökről.

A szimuláció leállításához válassza ki a **szimuláció leállítása** kérelem Postman, majd kattintson a **küldése**.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a két helyileg futó mikroszolgáltatásokat a Visual Studio Code-példányok (**Debug \> hibakeresés leállításához**).

Ha már nincs szüksége az IoT Hub és a Cosmos DB-példányokhoz, törölheti őket a felesleges költségek elkerülése érdekében az Azure-előfizetésből.

## <a name="iot-hub-support"></a>Az IoT Hub-támogatás

Az IoT Hub számos funkciót nem támogatják natív módon, Protopuf vagy más bináris formátumban. Ha például nem hajthat végre útválasztást üzenet hasznos adattartalmából alapján, mert az IoT Hub nem tudja feldolgozni az üzenet hasznos adattartalmából. Akkor is, azonban irányíthatja a fejlécek alapján.

## <a name="next-steps"></a>További lépések

Most már bemutattuk, hogyan szabhatja testre az Eszközszimuláció telemetriai adatokat küldhet a Protopuf használatával lehet, a következő lépésben most megtudhatja, hogy-e [egyéni rendszerkép üzembe helyezése a felhőben](iot-accelerators-device-simulation-deploy-image.md).
