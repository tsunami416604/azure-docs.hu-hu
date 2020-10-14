---
title: Kiépítés szimmetrikus kulcsokkal – Azure IoT Edge | Microsoft Docs
description: A telepítés után hozzon létre egy IoT Edge eszközt szimmetrikus kulcsokkal a kapcsolati karakterláncának használatával, és hitelesítse magát a IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: f5371539c1b45c14b519729c7c07003bf74847a0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043867"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Azure IoT Edge eszköz beállítása szimmetrikus kulcsos hitelesítéssel

Ez a cikk bemutatja, hogyan regisztrálhat új IoT Edge eszközt a IoT Hubban, és hogyan konfigurálhatja az eszközt a szimmetrikus kulcsokkal való hitelesítéshez.

A cikkben ismertetett lépések végigvezetik a manuális kiépítés folyamatán, ahol az egyes eszközöket manuálisan csatlakoztathatja az IoT hub-hoz. A másik lehetőség az automatikus kiépítés a IoT Hub Device Provisioning Service használatával, ami hasznos lehet, ha sok eszközt kell kiépíteni.

<!--TODO: Add auto-provision info/links-->

A manuális kiépítés esetén két lehetősége van IoT Edge eszközök hitelesítésére:

* **Szimmetrikus kulcs**: amikor új eszköz-identitást hoz létre IoT Hubban, a szolgáltatás két kulcsot hoz létre. Elhelyezi a kulcsok egyikét az eszközön, és megjeleníti a hitelesítő IoT Hub kulcsát.

  Ez a hitelesítési módszer gyorsabb a kezdéshez, de nem biztonságos.

* **X. 509 önaláírt**: két x. 509 identitás-tanúsítványt hoz létre, és elhelyezi őket az eszközön. Amikor új eszköz-identitást hoz létre IoT Hubban, mindkét tanúsítványból ujjlenyomatai megfelelnek biztosít. Amikor az eszköz hitelesíti IoT Hub, megjeleníti a tanúsítványait, és IoT Hub ellenőrizheti, hogy egyeznek-e a ujjlenyomatai megfelelnek.

  Ez a hitelesítési módszer biztonságosabb, és éles környezetekben ajánlott.

Ez a cikk végigvezeti a regisztrációs és kiépítési folyamaton a szimmetrikus kulcsos hitelesítéssel. Ha meg szeretné tudni, hogyan állíthat be egy X. 509 tanúsítvánnyal rendelkező eszközt, tekintse meg [az x. 509 tanúsítványalapú hitelesítéssel rendelkező Azure IoT Edge eszköz beállítását](how-to-manual-provision-x509.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzése előtt rendelkeznie kell egy olyan eszközzel, amelyen telepítve van a IoT Edge futtatókörnyezet. Ha nem, kövesse az [Azure IoT Edge futtatókörnyezet telepítése vagy eltávolítása](how-to-install-iot-edge.md)című témakör lépéseit.

## <a name="register-a-new-device"></a>Új eszköz regisztrálása

A IoT Hubhoz csatlakozó összes eszközhöz tartozik egy eszköz-azonosító, amely a felhőből az eszközre vagy az eszközről a felhőbe irányuló kommunikáció nyomon követésére szolgál. Konfigurálnia kell egy eszközt a kapcsolati adataival, beleértve az IoT hub állomásnevét, az eszköz AZONOSÍTÓját és az eszköz által a IoT Hub való hitelesítéshez használt információkat.

A szimmetrikus kulcsos hitelesítéshez ezeket az információkat egy olyan, a IoT Hubból lekérhető és a IoT Edge eszközre helyező, egy *kapcsolatok karakterlánca* gyűjti.

Számos eszköz használatával regisztrálhat egy új IoT Edge eszközt a IoT Hubban, és lekérheti a kapcsolati karakterláncot a beállítástól függően.

# <a name="portal"></a>[Portál](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>A Azure Portal előfeltételei

Az Azure-előfizetésében ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md) .

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>IoT Edge-eszköz létrehozása a Azure Portal

A Azure Portal IoT Hub a IoT Edge eszközöket külön hozza létre és kezeli a nem engedélyezett IOT-eszközökről.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktáblán válassza a menü **IoT Edge** elemét, majd válassza a **IoT Edge eszköz hozzáadása**lehetőséget.

   ![IoT Edge-eszköz hozzáadása a Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Az **eszköz létrehozása** lapon adja meg a következő információkat:

   * Hozzon létre egy leíró eszköz AZONOSÍTÓját.
   * A hitelesítési típusként válassza a **szimmetrikus kulcs** lehetőséget.
   * Használja az alapértelmezett beállításokat a hitelesítési kulcsok automatikus létrehozásához és az új eszköz csatlakoztatásához a központhoz.

1. Kattintson a **Mentés** gombra.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>IoT Edge eszközök megtekintése a Azure Portal

Az IoT hub-hoz csatlakozó összes Edge-kompatibilis eszköz megjelenik a **IoT Edge** oldalon.

![Az IoT hub összes IoT Edge eszközének megtekintése a Azure Portal használatával](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>A Azure Portal a kapcsolatok karakterláncának beolvasása

Ha készen áll az eszköz beállítására, szüksége lesz arra a kapcsolati karakterláncra, amely a fizikai eszközt az IoT hub identitásával társítja.

1. A portál **IoT Edge** lapján kattintson az eszköz azonosítójára a IoT Edge eszközök listájáról.
2. Másolja az **elsődleges** vagy **másodlagos kapcsolatok karakterláncának**értékét.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>A Visual Studio Code előfeltételei

* Az Azure-előfizetéshez tartozó ingyenes vagy standard szintű [IoT hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>Jelentkezzen be az IoT hub eléréséhez

A Visual Studio Code-hoz készült Azure IoT Extensions használatával műveleteket hajthat végre a IoT Hub. Ezeknek a műveleteknek a működéséhez be kell jelentkeznie az Azure-fiókjába, és ki kell választania a IoT Hub.

1. A Visual Studio Code-ban nyissa meg a **Explorer** nézetet.
1. Az Explorer alján bontsa ki az **Azure IoT hub** szakaszt.

   ![Az Azure IoT Hub-eszközök szakasz kibontása](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. Kattintson a **...** elemre az **Azure IoT hub** szakasz fejlécében. Ha nem látja a három pontot, kattintson vagy vigye a kurzort a fejléc fölé.
1. Válassza a **IoT hub kiválasztása**lehetőséget.
1. Ha nem jelentkezett be az Azure-fiókjába, kövesse az utasításokat.
1. Válassza ki az Azure-előfizetését.
1. Válassza ki az IoT hubot.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>IoT Edge-eszköz létrehozása a Visual Studio Code-ban

1. A VS Code Explorerben bontsa ki az **Azure IoT hub-eszközök** szakaszt.
1. Kattintson a **...** elemre az **Azure IoT hub eszközök** szakasz fejlécében. Ha nem látja a három pontot, kattintson vagy vigye a kurzort a fejléc fölé.
1. Válassza a **létrehozás IoT Edge eszköz**lehetőséget.
1. A megnyíló szövegmezőben adja meg az eszköz AZONOSÍTÓját.

A kimenet képernyőn láthatja a parancs eredményét. A rendszer kinyomtatja az eszköz adatait, beleértve a megadott **deviceId** -t, valamint azt a **ConnectionString** -t, amellyel a fizikai eszközt csatlakoztathatja az IoT hubhoz.

A kimenet képernyőn láthatja a parancs eredményét. A rendszer kinyomtatja az eszköz adatait, beleértve a megadott **deviceId** -t, valamint azt a **ConnectionString** -t, amellyel a fizikai eszközt csatlakoztathatja az IoT hubhoz.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>A Visual Studio Code-ban IoT Edge eszközök megtekintése

Az IoT hub-hoz csatlakozó összes eszköz megjelenik a Visual Studio Code Explorer **Azure IoT hub** szakaszában. IoT Edge eszközök a nem peremhálózati eszközöktől eltérő ikonnal vannak elkülönítve, és az a tény, hogy az **$edgeAgent** és **$edgeHub** modulokat minden IoT Edge eszközön telepítik.

![A VS Code használatával megtekintheti az IoT hub összes IoT Edge eszközét](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>A Visual Studio Code-beli kapcsolatok karakterláncának beolvasása

Ha készen áll az eszköz beállítására, szüksége lesz arra a kapcsolati karakterláncra, amely a fizikai eszközt az IoT hub identitásával társítja.

1. Kattintson a jobb gombbal az eszköz AZONOSÍTÓJÁRA az **Azure IoT hub** szakaszban.
1. Válassza az **eszköz csatlakoztatási karakterláncának másolása**lehetőséget.

   A rendszer átmásolja a kapcsolódási karakterláncot a vágólapra.

Azt is megteheti, hogy az eszköz **információinak beolvasása** lehetőségre kattint a jobb gombbal, és megjeleníti a kimeneti ablakban található összes eszközt, beleértve a kapcsolódási karakterláncot is.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Az Azure CLI előfeltételei

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében.
* [Azure CLI](/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az --version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer.
* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>IoT Edge-eszköz létrehozása az Azure CLI-vel

Az az [IOT hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) paranccsal hozzon létre egy új eszköz-identitást az IOT hub-ban. Például:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Ez a parancs három paramétert tartalmaz:

* `--device-id` vagy `-d` : adjon meg egy leíró nevet, amely egyedi az IoT hub számára.
* `hub-name` vagy `-n` : adja meg az IoT hub nevét.
* `--edge-enabled` vagy `--ee` : állapítsa meg, hogy az eszköz egy IoT Edge eszköz.

   ![az IOT hub Device-Identity Create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>IoT Edge eszközök megtekintése az Azure CLI-vel

Az az [IOT hub Device-Identity List](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) paranccsal megtekintheti az IOT hub összes eszközét. Például:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

A IoT Edge eszközként regisztrált eszközökön a tulajdonság képességei lesznek. a **iotEdge** értéke **true (igaz**).

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>A kapcsolatok karakterláncának beolvasása az Azure CLI-vel

Ha készen áll az eszköz beállítására, szüksége lesz arra a kapcsolati karakterláncra, amely a fizikai eszközt az IoT hub identitásával társítja. Az az [IOT hub Device-Identity show-kapcsolat-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) paranccsal visszaállíthatja egy adott eszköz kapcsolati karakterláncát:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

A `device-id` paraméter értéke megkülönbözteti a kis-és nagybetűket. Ne másolja az idézőjeleket a kapcsolatok sztring köré.

---

## <a name="provision-an-iot-edge-device"></a>IoT Edge-eszköz regisztrációja

Ha a IoT Edge eszköz rendelkezik identitással a IoT Hubban, és egy, a hitelesítéshez használható kapcsolódási sztringet, akkor az eszközt magával az információval kell kiépítenie.

Egy Linux-eszközön a config. YAML fájl szerkesztésével adja meg a kapcsolatok karakterláncát. Egy Windows-eszközön egy PowerShell-szkript futtatásával adja meg a kapcsolatok karakterláncát.

# <a name="linux"></a>[Linux](#tab/linux)

A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

```bash
sudo nano /etc/iotedge/config.yaml
```

Keresse meg a fájl kiépítési konfigurációit, és a **manuális üzembe helyezési konfigurációt a kapcsolatok karakterlánc szakasz használatával** írja vissza. 

**Device_connection_string** értékének frissítése a IoT Edge eszközhöz tartozó kapcsolatok karakterláncával. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel. Győződjön meg arról, hogy a **kiépítés:** sor nem tartalmaz korábbi szóközt, és a beágyazott elemek két szóközzel vannak behúzva.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

A vágólap tartalmának a Nanoba vagy a sajtóba való beillesztéséhez `Shift+Right Click` `Shift+Insert` .

Mentse és zárja be a fájlt.

   `CTRL + X`, `Y`, `Enter`

A konfigurációs fájlban szereplő kiépítési információk megadása után indítsa újra a démont:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. A IoT Edge eszközön futtassa a PowerShellt rendszergazdaként.

2. Az [inicializálás-IoTEdge](reference-windows-scripts.md#initialize-iotedge) parancs használatával konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Ha Linux-tárolókat használ, adja hozzá a `-ContainerOs` paramétert a jelzőhöz. Konzisztensnek kell lennie a `Deploy-IoTEdge` korábban futtatott paranccsal kiválasztott tároló beállítással.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Ha a IoTEdgeSecurityDaemon.ps1 szkriptet offline vagy adott verzióra történő telepítésre töltötte le az eszközön, akkor ügyeljen arra, hogy a parancsfájl helyi példányára hivatkozzon.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Ha a rendszer kéri, adja meg az előző szakaszban lekért eszköz-kapcsolódási karakterláncot. Az eszköz-összekapcsolási karakterlánc a fizikai eszközt a IoT Hub eszköz azonosítójával társítja, és hitelesítési adatokat biztosít.

   Az eszköz-kapcsolatok karakterlánca a következő formátumot veszi figyelembe, és nem tartalmazhat idézőjeleket: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Amikor manuálisan épít ki egy eszközt, további paramétereket is használhat a folyamat módosításához, beleértve a következőket:

* Egy proxykiszolgálón keresztüli közvetlen forgalom
* Egy adott edgeAgent-tároló rendszerképének deklarálása, és a hitelesítő adatok megadása, ha azok egy privát beállításjegyzékben találhatók

További információ ezekről a további paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a [IoT Edge-modulok üzembe](how-to-deploy-modules-portal.md) helyezésével, hogy megtudja, hogyan helyezhet üzembe modulokat az eszközön.