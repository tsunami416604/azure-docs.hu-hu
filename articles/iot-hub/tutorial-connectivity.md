---
title: Azure IoT Hub eszközkapcsolatának ellenőrzése
description: Az IoT Hub-eszközökkel fejlesztés közben végezhet hibaelhárítást az IoT Hub eszközkapcsolati problémáin.
services: iot-hub
author: dominicbetts
manager: timlt
ms.author: dobett
ms.custom: mvc
ms.date: 05/29/2018
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: bb9bcfcc5f78ee82f187d331055e8f2fd2ed9e64
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745809"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Oktatóanyag: Szimulált eszköz használata az IoT hub-kapcsolat tesztelése

Ebben az oktatóanyagban az Azure IoT Hub portál eszközeivel és az Azure CLI parancsaival teszteli az eszközkapcsolatot. Az oktatóanyag emellett egy egyszerű eszközszimulátort is használ, amelyet az asztali gépén futtat.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az eszközhitelesítés ellenőrzése
> * Az eszközről a felhőbe irányuló kapcsolat ellenőrzése
> * A felhőből az eszközre irányuló kapcsolat ellenőrzése
> * Ikereszköz-szinkronizálás ellenőrzése

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag során futtatott parancssori felületi szkriptek az [Azure CLI-hez készült IoT-bővítményt](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) használják. A bővítmény telepítéséhez futtassa a következő parancssori felületi parancsot:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Az oktatóanyagban futtatott eszközszimulátor alkalmazás Node.js használatával készült. A fejlesztői gépen szükség lesz a Node.js 4.x.x. vagy újabb változatára.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

Töltse le a Node.js eszközszimulátor mintaprojektjét a https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha egy korábbi rövid útmutató vagy oktatóanyag során létrehozott egy ingyenes vagy standard szintű IoT Hubot, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Eszközhitelesítés ellenőrzése

Az eszközök csak akkor végezhetnek adatcserét a hubbal, ha a hitelesítésük megtörtént. A portál **Eszközkezelés** szakaszában az **IoT-eszközök** eszközzel felügyelheti az eszközeit, és itt ellenőrizheti az általuk használt hitelesítési kulcsokat. Az oktatóanyag ezen szakaszában hozzáad egy új teszteszközt, lekéri a kulcsát, és ellenőrzi, hogy a teszteszköz képes-e csatlakozni a hubhoz. Később visszaállítja a hitelesítési kulcsot, hogy megtekintse, mi történik, amikor egy eszköz egy elavult kulcsot próbál használni. Az oktatóanyag ezen szakaszában az Azure Portal használatával létrehoz, felügyel és monitoroz egy eszközt, valamint a mintaként használt Node.js eszközszimulátort.

Jelentkezzen be a portálra, és keresse meg az IoT Hubot. Ezt követően nyissa meg az **IoT-eszközök** eszközt:

![IoT-eszközök eszköz](media/tutorial-connectivity/iot-devices-tool.png)

Új eszköz regisztrálásához kattintson a **+ Hozzáadás** gombra, az **Eszközazonosítóként** adja meg a **MyTestDevice** nevet, majd kattintson a **Mentés** gombra:

![Új eszköz hozzáadása](media/tutorial-connectivity/add-device.png)

A **MyTestDevice** kapcsolati sztringjének lekéréséhez kattintson rá az eszközlistán, majd másolja a **Kapcsolati sztring – elsődleges kulcs** értékét. A kapcsolati sztring tartalmazza az eszköz *megosztott hozzáférési kulcsát*.

![Az eszköz kapcsolati sztringjének lekérése](media/tutorial-connectivity/copy-connection-string.png)

A **MyTestDevice** által az IoT hubnak küldött telemetria szimulálásához futtassa a Node.js szimulálteszköz-alkalmazást, amelyet korábban letöltött.

A fejlesztői gépen egy terminálablakban keresse meg a letöltött Node.js-mintaprojekt gyökérmappáját. Keresse meg a **iot-hub\Tutorials\ConnectivityTests** mappát.

Futtassa az alábbi parancsokat a terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához. Az eszköz kapcsolati karakterláncának jegyezze fel, amikor az eszköz hozzáadva a portálon végzett használja.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

A terminálablak információkat jelenít meg, miközben csatlakozni próbál a hubhoz:

![Szimulált eszköz csatlakoztatása](media/tutorial-connectivity/sim-1-connected.png)

Sikeresen hitelesített egy eszközről az IoT Hub által létrehozott eszközkulcs használatával.

### <a name="reset-keys"></a>Kulcsok visszaállítása

Ebben a szakaszban visszaállítja az eszközkulcsot, és megfigyeli a hibát, amikor a szimulált eszköz csatlakozni próbál.

A **MyTestDevice** elsődleges eszközkulcsának visszaállításához futtassa a következő parancsokat:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

A fejlesztési gépen a terminálablakban futtassa ismét a szimulálteszköz-alkalmazást:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Ezúttal hitelesítési hibát lát, amikor az alkalmazás csatlakozni próbál:

![Csatlakozási hiba](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Közös hozzáférésű jogosultságkód (SAS) jogkivonat létrehozása

Ha az eszköze az IoT Hub egyik eszköz SDK-ját használja, az SDK kódtár kódja hozza létre a hubbal való hitelesítéshez használt SAS-jogkivonatot. A SAS-jogkivonat a hubja nevéből, az eszköze nevéből és az eszközkulcsból jön létre.

Bizonyos esetekben előfordulhat, például egy felhő protokollátjárójában vagy egy egyéni hitelesítési séma részeként, hogy saját kezűleg kell létrehoznia a SAS-jogkivonatot. A SAS létrehozási kódjának hibaelhárításához hasznos, ha létre tud hozni egy működőként azonosított SAS-jogkivonatot, amelyet használhat a tesztelés során.

> [!NOTE]
> A SimulatedDevice-2.js az SAS-jogkivonat SDK-val és SDK nélkül történő létrehozására is tartalmaz példákat.

Egy működő SAS-jogkivonat parancssori felületen való létrehozásához futtassa a következő parancsot:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Jegyezze fel a létrehozott SAS-jogkivonat teljes szövegét. A SAS-jogkivonatok a következőképpen néznek ki: `'SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307'`

A fejlesztői gépen egy terminálablakban keresse meg a letöltött Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Tutorials\ConnectivityTests\simulated-device** mappára.

Futtassa az alábbi parancsokat a terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

A terminálablak információkat jelenít meg, miközben csatlakozni próbál a hubhoz a SAS-jogkivonat használatával:

![Szimulált eszköz csatlakoztatása egy jogkivonattal](media/tutorial-connectivity/sim-2-connected.png)

Sikeresen hitelesített egy eszközről egy parancssori felületi paranccsal létrehozott tesztelési SAS-jogkivonat használatával. A **SimulatedDevice-2.js** fájl mintakódot tartalmaz, amely megmutatja, hogyan hozhat létre egy SAS-jogkivonatot a kódban.

### <a name="protocols"></a>Protokollok

Az eszközök az alábbi protokollok bármelyikével csatlakozhatnak az IoT Hubhoz:

| Protokoll | Kimenő port |
| --- | --- |
| MQTT |8883 |
| MQTT WebSocketen keresztül |443 |
| AMQP |5671 |
| AMQP WebSocketen keresztül |443 |
| HTTPS |443 |

Ha a kimenő portot blokkolja egy tűzfal, az eszköz nem tud csatlakozni:

![Port blokkolva](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Az eszközről a felhőbe irányuló kapcsolat ellenőrzése

Miután egy eszköz csatlakozik, általában telemetriát próbál küldeni az IoT Hubnak. Ez a szakasz bemutatja, hogyan ellenőrizheti, hogy az eszközök által küldött telemetria eléri-e a hubot.

Először kérje le a szimulált eszköz aktuális kapcsolati sztringjét a következő paranccsal:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Egy üzeneteket küldő szimulált eszköz futtatásához nyissa meg az **iot-hub\Tutorials\ConnectivityTests\simulated-device** mappát a letöltött kódban.

Futtassa az alábbi parancsokat a terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

A terminálablak információkat jelenít meg, miközben telemetriát küld a hubnak:

![A szimulált eszköz üzeneteket küld](media/tutorial-connectivity/sim-3-sending.png)

A portálon a **Metrikák** lehetőséggel ellenőrizheti, hogy a telemetriai üzenetek elérik-e az IoT Hubot:

![IoT Hub metrikáinak megnyitása](media/tutorial-connectivity/metrics-portal.png)

Válassza ki az IoT Hubot az **Erőforrás** legördülő menüből, válassza az **Elküldött telemetriai üzeneteket** metrikaként, majd állítsa az időtartományt az **Elmúlt óra** lehetőségre. A diagram a szimulált eszköz által elküldött üzenetek összegzett darabszámát jeleníti meg:

![IoT Hub metrikáinak megjelenítése](media/tutorial-connectivity/metrics-active.png)

Néhány percig tart, amíg a metrikák elérhetővé válnak, miután elindította a szimulált eszközt.

## <a name="check-cloud-to-device-connectivity"></a>A felhőből az eszközre irányuló kapcsolat ellenőrzése

Ez a szakasz bemutatja, hogyan indíthat egy tesztelési közvetlenmetódus-hívást egy eszközre, hogy ellenőrizze a felhőből az eszközre irányuló kapcsolatot. Egy szimulált eszközt futtat a fejlesztési gépén, hogy figyelje a hubról érkező közvetlenmetódus-hívásokat.

Futtassa az alábbi parancsot egy terminálablakban a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Egy parancssori felületi paranccsal hívjon meg egy közvetlen metódust az eszközön:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

A szimulált eszköz a következő üzenetet jeleníti meg a konzolon, amikor egy közvetlenmetódus-hívást fogad:

![A szimulált eszköz egy közvetlenmetódus-hívást fogad](media/tutorial-connectivity/receive-method-call.png)

Ha a szimulált eszköz sikeresen fogadja a közvetlenmetódus-hívást, nyugtázást küld vissza a hubra:

![Közvetlen metódus nyugtázásának fogadása](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Ikerszinkronizálás ellenőrzése

Az eszközök ikreket használnak, hogy szinkronizálják az eszköz és a hub állapotát. Ebben a szakaszban parancssori felületi parancsok használatával küld _kívánt tulajdonságokat_ egy eszközre, és elolvassa az eszköz által küldött _jelentett tulajdonságokat_.

A szakaszban használt szimulált eszköz minden indításkor elküldi a jelentett tulajdonságokat a hubnak, és a kívánt tulajdonságokat a fogadáskor minden alkalommal megjeleníti a konzolon.

Futtassa az alábbi parancsot egy terminálablakban a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Annak ellenőrzéséhez, hogy a hub fogadta-e a jelentett tulajdonságokat az eszközről, használja a következő parancssori felületi parancsot:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

A parancs kimenetében láthatja a **devicelaststarted** tulajdonságot a jelentett tulajdonságok szakaszban. Ez a tulajdonság a szimulált eszköz legutóbbi indításának dátumát és idejét jeleníti meg.

![Jelentett tulajdonságok megtekintése](media/tutorial-connectivity/reported-properties.png)

Annak ellenőrzéséhez, hogy a hub képes-e kívánt tulajdonságok értékeit elküldeni az eszköznek, használja a következő parancssori felületi parancsot:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

A szimulált eszköz minden alkalommal megjelenít egy üzenetet, amikor egy kívánt tulajdonság frissítését fogadja a hubról:

![Kívánt tulajdonságok fogadása](media/tutorial-connectivity/desired-properties.png)

Amellett, hogy folyamatosan fogadja a kívánt tulajdonságok módosításait, a szimulált eszköz indításakor automatikusan ellenőrzi a kívánt tulajdonságokat is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az IoT Hubra, az erőforráscsoporttal együtt törölje a Portalon. Ehhez válassza ki az IoT Hubot tartalmazó **tutorials-iot-hub-rg** erőforráscsoportot, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan ellenőrizhet eszközkulcsokat, az eszközről a felhőbe irányuló kapcsolatot, a felhőből az eszközre irányuló kapcsolatot, valamint az ikereszköz-szinkronizálást. Az IoT hub monitorozásával kapcsolatos további információkért tekintse meg az IoT Hub monitorozását ismertető útmutatót.

> [!div class="nextstepaction"]
> [Monitorozás a diagnosztikával](iot-hub-monitor-resource-health.md)
