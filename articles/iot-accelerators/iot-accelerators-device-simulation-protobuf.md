---
title: Protokollpufferek használata eszközszimulációval – Azure| Microsoft dokumentumok
description: Ebben az útmutatóútmutatóban megtudhatja, hogyan szerializálhatja a protokollpuffereket az eszközszimulációs megoldásgyorsítóból küldött telemetriai adatok használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: bc08cd5183bcaac6cb77ccb0938b07893f082862
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250210"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetria szerializálása protokollpufferekkel

A protokollpufferek (Protobuf) strukturált adatok bináris szerializálási formátuma. A Protobuf célja, hogy hangsúlyozza az egyszerűség et és a teljesítményt azzal a céllal, hogy kisebb és gyorsabb, mint az XML.

Az Eszközszimuláció támogatja a protokollpuffernyelv **proto3** verzióját.

Mivel a Protobuf lefordított kódszükséges az adatok szerializálásához, az Eszközszimuláció egyéni verzióját kell készítenie.

Az útmutató lépései bemutatják, hogyan:

1. Fejlesztési környezet előkészítése
1. A Protobuf formátum használatával történő megadása eszközmodellben
1. A Protobuf formátum meghatározása
1. Protobuf osztályok létrehozása
1. Helyi tesztelés

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek követéséhez a következőkre van szükség:

* Visual Studio Code. A [Visual Studio Code for Mac, Linux és Windows alkalmazást](https://code.visualstudio.com/download)letöltheti.
* .NET Core. A [.NET Core letölthető Mac, Linux és Windows rendszerre](https://www.microsoft.com/net/download).
* Postás. Letöltheti [postman Mac, Windows, vagy Linux](https://www.getpostman.com/apps).
* [Az Azure-előfizetésében üzembe helyezett IoT-központ.](../iot-hub/iot-hub-create-through-portal.md) Az útmutatóban ismertetett lépések végrehajtásához az IoT hub kapcsolati karakterláncára van szükség. A kapcsolati karakterlánc az Azure Portalon.
* Az [Azure-előfizetésbe telepített Cosmos DB-adatbázis,](../cosmos-db/create-sql-api-dotnet.md#create-account) amely az SQL API-t használja, és [erős konzisztenciára](../cosmos-db/manage-account.md)van konfigurálva. A Cosmos DB adatbázis kapcsolati karakterláncának az útmutatóban leírt lépések végrehajtásához szükséges. A kapcsolati karakterlánc az Azure Portalon.
* [Az Azure-előfizetésbe üzembe helyezett Azure storage-fiók.](../storage/common/storage-account-create.md) Az útmutató ban leírt lépések végrehajtásához a tárfiók kapcsolati karakterláncára van szükség. A kapcsolati karakterlánc az Azure Portalon.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A fejlesztői környezet előkészítéséhez hajtsa végre a következő feladatokat:

* Töltse le az eszközszimulációs mikroszolgáltatás forrását.
* Töltse le a forrást a tárolóadapter mikroszolgáltatás.
* Futtassa a tárolóadapter mikroszolgáltatását helyileg.

A cikkben található utasítások feltételezik, hogy a Windows rendszert használja. Ha más operációs rendszert használ, előfordulhat, hogy a környezetének megfelelően módosítania kell néhány fájlelérési utat és parancsot.

### <a name="download-the-microservices"></a>A mikroszolgáltatások letöltése

Töltse le és csomagolja ki a [Távoli figyelési mikroszolgáltatásokat](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) a GitHubról a helyi számítógépen lévő megfelelő helyre. Ez a tárház tartalmazza a tárolóadapter mikroszolgáltatás szükséges ehhez a how-to.

Töltse le és csomagolja ki az [eszközszimulációs mikroszolgáltatást](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a GitHubról a helyi számítógépen lévő megfelelő helyre.

### <a name="run-the-storage-adapter-microservice"></a>A tárolóadapter mikroszolgáltatásának futtatása

A Visual Studio-kódban nyissa meg a **remote-monitoring-services-dotnet-master\storage-adapter** mappát. A **feloldatlan** függőségek javításához kattintson bármelyik Visszaállítás gombra.

Nyissa meg a **.vscode/launch.json** fájlt, és rendelje hozzá a Cosmos DB kapcsolati karakterláncot a **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** környezeti változóhoz.

> [!NOTE]
> Ha a mikroszolgáltatás helyileg fut a gépen, továbbra is szükség van egy Cosmos DB-példány az Azure-ban a megfelelő működéséhez.

A tárolóadapter mikroszolgáltatásának helyi futtatásához kattintson a **Hibakeresés \> indítása gombra.**

A Visual Studio-kód **Terminál** ablaka a futó mikroszolgáltatás kimenetét jeleníti <http://127.0.0.1:9022/v1/status>meg, beleértve a webszolgáltatás állapot-ellenőrzésének URL-címét: . Amikor erre a címre navigál, az állapotnak "OK: Él és virul" állapotnak kell lennie.

Hagyja futni a tárolóadapter mikroszolgáltatását a Visual Studio-kód ezen példányában, amíg végrehajtja az alábbi lépéseket.

## <a name="define-your-device-model"></a>Az eszköztípus meghatározása

Nyissa meg a GitHubról letöltött **eszköz-szimulációs-dotnet-master** mappát a Visual Studio-kód új példányában. A **feloldatlan** függőségek javításához kattintson a Visszaállítás gombra.

Ebben az útmutatóban új eszközmodellt hoz létre egy eszközkövetőhöz:

1. Hozzon létre egy új eszközmodell-fájlt, az **assettracker-01.json** nevű fájlt a **Services\data\devicemodels** mappában.

1. Adja meg az eszköz funkcióját az **eszközmodell assettracker-01.json** fájljában. A Protobuf eszközmodell telemetriai szakaszának:

   * Adja meg az eszközhöz létrehozott Protobuf osztály nevét. A következő szakasz bemutatja, hogyan hozhat létre ezt az osztályt.
   * Adja meg a Protobuf-ot üzenetformátumként.

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

### <a name="create-device-behaviors-script"></a>Eszközviselkedési parancsfájl létrehozása

Írja be a viselkedési parancsfájlt, amely meghatározza az eszköz működését. További információt a Speciális szimulált eszköz létrehozása című [témakörben talál.](iot-accelerators-device-simulation-advanced-device.md)

## <a name="define-your-protobuf-format"></a>A Protobuf formátum meghatározása

Ha rendelkezik eszközmodellel, és meghatározta az üzenet formátumát, létrehozhat egy **proto** fájlt. A **proto** fájlban a következőt adja hozzá:

* A, `csharp_namespace` amely megfelel az eszközmodell **ClassName** tulajdonságának.
* Minden egyes adatszerkezetszerialmára vonatkozó üzenet.
* Az üzenet minden mezőjének neve és típusa.

1. Hozzon létre egy **assettracker.proto** nevű új fájlt a **Services\Models\Protobuf\proto** mappában.

1. Adja meg a szintaxist, a névteret és az üzenetsémát a **protofájlban** az alábbiak szerint:

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

Az `=1` `=2` egyes elemek jelölői egyedi címkét adnak meg, amelyet a mező a bináris kódolásban használ. Az 1–15-ös számok kódolásához eggyel kevesebb bájt szükséges, mint a magasabb számok.

## <a name="generate-the-protobuf-class"></a>A Protobuf osztály létrehozása

ha van egy **proto** fájlt, a következő lépés az, hogy létrehoz az osztályok szükséges olvasni és írni az üzeneteket. A lépés végrehajtásához szüksége van a **Protoc** Protobuf fordítóra.

1. [A Protobuf fordító letöltése a GitHubról](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Futtassa a fordítót, megadva a forráskönyvtárat, a célkönyvtárat és a **protofájl** nevét. Példa:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Ez a parancs **Assettracker.cs** fájlt hoz létre a **Services\Models\Protobuf** mappában.

## <a name="test-protobuf-locally"></a>Teszt Protobuf helyben

Ebben a szakaszban az előző szakaszokban létrehozott eszközkövető eszközt helyileg tesztelheti.

### <a name="run-the-device-simulation-microservice"></a>Az eszközszimulációs mikroszolgáltatás futtatása

Nyissa meg a **.vscode/launch.json** fájlt, és rendelje hozzá a következőket:

* IoT Hub kapcsolati karakterlánc a **PCS\_\_IOTHUB CONNSTRING** környezeti változóhoz.
* A tárfiók kapcsolati karakterlánca a **PCS\_AZURE\_STORAGE-FIÓK\_** környezeti változóhoz.
* Cosmos DB kapcsolati karakterlánc a **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** környezeti változóhoz.

Nyissa meg a **WebService/Properties/launchSettings.json** fájlt, és rendelje hozzá a következőket:

* IoT Hub kapcsolati karakterlánc a **PCS\_\_IOTHUB CONNSTRING** környezeti változóhoz.
* A tárfiók kapcsolati karakterlánca a **PCS\_AZURE\_STORAGE-FIÓK\_** környezeti változóhoz.
* Cosmos DB kapcsolati karakterlánc a **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** környezeti változóhoz.

Nyissa meg a **WebService\appsettings.ini** fájlt, és módosítsa a beállításokat az alábbiak szerint:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>A megoldás konfigurálása az új eszközmodell-fájlok felvételére

Alapértelmezés szerint az új eszközmodell JSON- és JS-fájljai nem lesznek átmásolva a beépített megoldásba. Ezeket kifejezetten bele kell foglalnia.

Adjon hozzá egy bejegyzést a **services\services.csproj** fájlhoz minden egyes fájlhoz, amelyet be szeretne venni. Példa:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

A mikroszolgáltatás helyi futtatásához kattintson a **Hibakeresés \> indítása gombra.**

A Visual Studio-kód **Terminál** ablaka a futó mikroszolgáltatás kimenetét jeleníti meg.

Hagyja futni az eszközszimulációs mikroszolgáltatást a Visual Studio-kód ezen példányában a következő lépések végrehajtásával.

### <a name="set-up-a-monitor-for-device-events"></a>Eszközesemények figyelőjének beállítása

Ebben a szakaszban az Azure CLI használatával egy eseményfigyelő telemetriai adatok megtekintéséhez az IoT hubhoz csatlakoztatott eszközökről küldött telemetriai adatok megtekintéséhez.

A következő parancsfájl feltételezi, hogy az IoT hub neve **eszköz-szimuláció-teszt.**

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Hagyja futni az eseményfigyelőt a szimulált eszközök tesztelése közben.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Szimuláció létrehozása az eszközkövető eszköztípussal

Ebben a szakaszban a Postman eszközzel kérheti az eszközszimulációs mikroszolgáltatás az eszközkövető eszköz típusával szimuláció futtatásához. Postman egy olyan eszköz, amely lehetővé teszi, hogy küldjön REST-kérelmek egy webszolgáltatás.

A Postman beállítása:

1. Nyisd meg a postása a helyi gépen.

1. Kattintson **a Fájlimportálás \> gombra.** Ezután kattintson **a Fájlok kiválasztása gombra.**

1. Válassza az **Azure IoT-eszközszimulációs\_megoldásgyorsító.postman gyűjtemény** és **az Azure IoT-eszközszimulációs megoldásgyorsító.postman\_környezetet,** majd kattintson a **Megnyitás gombra.**

1. Bontsa ki az **Azure IoT-eszközszimulációs megoldásgyorsítót** a küldhető kérések megtekintéséhez.

1. Kattintson **a Nincs környezet elemre,** és válassza **az Azure IoT-eszközszimulációs megoldásgyorsítót.**

Most már van egy gyűjtemény és környezet betöltve a Postman-munkaterület, amely segítségével az eszköz szimulációs mikroszolgáltatás.

A szimuláció konfigurálása és futtatása:

1. A Postman gyűjteményben válassza az **Eszközkövető szimuláció létrehozása lehetőséget,** és kattintson a **Küldés**gombra. Ez a kérelem négy példányt hoz létre a szimulált eszközkövető eszköz típusából.

1. Az Azure CLI ablakesemény-figyelő kimenete a szimulált eszközök telemetriai adatokat jeleníti meg.

A szimuláció leállításához válassza a **Szimuláció leállítása** kérést a Postman alkalmazásban, és kattintson a **Küldés**gombra.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Leállíthatja a két helyileg futó mikroszolgáltatásokat a Visual Studio-kódpéldányaikban (**Hibakeresés \> leállítása**).

Ha már nincs szüksége az IoT Hub és a Cosmos DB-példányokra, törölje őket az Azure-előfizetésből a szükségtelen díjak elkerülése érdekében.

## <a name="iot-hub-support"></a>IoT Hub támogatása

Számos IoT Hub-szolgáltatás nem támogatja natívmódon a Protobuf vagy más bináris formátumokat. Például nem irányíthatja az üzenet hasznos adata alapján, mert az IoT Hub nem fogja tudni feldolgozni az üzenet hasznos adatát. Az útvonal azonban az üzenetfejlécek alapján is megtehető.

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan szabhatja testre az eszközszimulációt a Protobuf használatával telemetriai adatok küldéséhez, a következő lépés az [egyéni lemezkép üzembe helyezése a felhőbe.](iot-accelerators-device-simulation-deploy-image.md)
