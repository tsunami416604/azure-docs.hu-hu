---
title: Gyors útmutató Azure IoT Edge-eszköz létrehozásához Windows rendszeren | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Edge eszközt, majd távolról is üzembe helyezheti az előre elkészített kódot a Azure Portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d56f17f6c60f30a38431ee347c7bdfc5b200b641
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328589"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Rövid útmutató: az első IoT Edge modul üzembe helyezése virtuális Windows-eszközön

Próbálja ki Azure IoT Edge ebben a rövid útmutatóban, ha egy virtuális Windows IoT Edge eszközre helyez üzembe tároló kódot. IoT Edge lehetővé teszi a kódok távoli kezelését az eszközökön, így több számítási feladatot is elküldhet a peremhálózat számára. Ebben a rövid útmutatóban azt javasoljuk, hogy használjon egy Azure-beli virtuális gépet a IoT Edge eszközhöz. A virtuális gépek segítségével gyorsan létrehozhat egy tesztelési gépet, telepítheti az előfeltételeket, majd törölheti azt, amikor elkészült.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

> [!div class="checklist"]
>
> * Hozzon létre egy IoT hubot.
> * IoT Edge-eszköz regisztrálása az IoT Hubon
> * Telepítse és indítsa el a IoT Edge futtatókörnyezetet a virtuális eszközön.
> * Modul távoli üzembe helyezése IoT Edge-eszközön és Telemetria küldése az IoT Hubnak

![Diagram – az eszköz és a felhő gyors üzembe helyezési architektúrája](./media/quickstart/install-edge-full.png)

Ez a rövid útmutató végigvezeti a Windows rendszerű virtuális gépek létrehozásának és a IoT Edge eszközre való konfigurálásának lépésein. Ezután telepítsen egy modult a Azure Portal az eszközre. Az ebben a rövid útmutatóban használt modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom-és adatterhelési adattípust generál. A többi Azure IoT Edge oktatóanyag az itt végzett munkára építve olyan további modulok üzembe helyezésével végezhető el, amelyek elemzik az üzleti információk szimulált adatait.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Azure CLI-vel az ebben a rövid útmutatóban ismertetett lépések számos lépését elvégezheti. Az Azure IoT rendelkezik egy bővítménnyel, amely lehetővé teszi a további funkciók használatát.

Adja hozzá az Azure IoT bővítményt az Cloud Shell-példányhoz.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Előfeltételek

Felhőerőforrások:

* Egy erőforráscsoport a rövid útmutató során létrehozott összes erőforrás kezelésére.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-eszköz:

* Egy Windows rendszerű virtuális gép, amely IoT Edge eszközként működik. Ezt a virtuális gépet a következő paranccsal hozhatja létre a `{password}` biztonságos jelszó helyett:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Az új virtuális gép létrehozása és elindítása néhány percet is igénybe vehet.

  A virtuális gép elindítása után letöltheti a virtuális géphez való csatlakozáskor használandó RDP-fájlt:

  1. Navigáljon az új Windowsos virtuális géphez a Azure Portal.
  1. Válassza a **Kapcsolódás** lehetőséget.
  1. Az **RDP** lapon válassza az **RDP-fájl letöltése**lehetőséget.

  Nyissa meg ezt a fájlt a Távoli asztali kapcsolat használatával a Windows rendszerű virtuális géphez való kapcsolódáshoz a paranccsal megadott rendszergazdai névvel és jelszóval `az vm create` .

> [!NOTE]
> A Windows rendszerű virtuális gép a Windows 1809-es verziójával (Build 17763) kezdődik, amely a [Windows hosszú távú támogatásának legújabb felépítése](https://docs.microsoft.com/windows/release-information/). A Windows alapértelmezés szerint minden 22 órában automatikusan ellenőrzi a frissítéseket. A virtuális gép ellenőrzését követően a Windows olyan verziófrissítést küld, amely nem kompatibilis a Windows IoT Edgeével, ami megakadályozza a IoT Edge további használatát a Windows-szolgáltatásokban. Javasoljuk, hogy a virtuális gép használatát 22 órán belül korlátozza, vagy [átmenetileg szüneteltesse a Windows-frissítéseket](https://support.microsoft.com/help/4028233/windows-10-manage-updates).
>
> Ez a rövid útmutató az egyszerűség kedvéért egy Windows rendszerű virtuális gépet használ. További információ arról, hogy mely Windows operációs rendszerek érhetők el általánosan az éles környezetekben: [Azure IoT Edge támogatott rendszerek](support.md).
>
> Ha készen áll a IoT Edgehoz tartozó saját Windows-eszköz konfigurálására, beleértve az IoT Core-ot futtató eszközöket, kövesse a [Azure IoT Edge futtatókörnyezet telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakör lépéseit.

## <a name="create-an-iot-hub"></a>IoT-központ létrehozása

A rövid útmutató elindításához hozzon létre egy IoT hubot az Azure CLI-vel.

![Diagram – IoT hub létrehozása a felhőben](./media/quickstart/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta a IoT Hubt, és már rendelkezik egy hubhoz, akkor használhatja ezt az IoT hubot.

A következő kód egy ingyenes **F1** -hubot hoz létre az erőforráscsoporthoz `IoTEdgeResources` . Cserélje le `{hub_name}` az nevet az IoT hub egyedi nevére. A IoT Hub létrehozása néhány percet is igénybe vehet.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Ha hibaüzenet jelenik meg, hogy a IoT Hub neve nem érhető el, az azt jelenti, hogy valaki más már rendelkezik az adott névvel rendelkező hubhoz. Próbálkozzon új névvel.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.
![Diagram – eszköz regisztrálása IoT Hub identitással](./media/quickstart/register-device.png)

Hozzon létre egy eszközidentitást a szimulált eszközhöz, hogy az kommunikálhasson az IoT Hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

Mivel IoT Edge-eszközök viselkedése és kezelése eltérően történik a szokásos IoT-eszközöknél, deklarálja ezt az identitást egy IoT Edge eszközhöz a `--edge-enabled` jelzővel.

1. A Azure Cloud Shell írja be a következő parancsot egy **myEdgeDevice** nevű eszköz létrehozásához a központban.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Ha hibaüzenetet kap a iothubowner, győződjön meg arról, hogy a Cloud Shell az Azure-IOT bővítmény legújabb verzióját futtatja.

2. Megtekintheti az eszköz kapcsolati karakterláncát, amely a fizikai eszközt a IoT Hub identitásával társítja. Tartalmazza az IoT hub nevét, az eszköz nevét, majd egy megosztott kulcsot, amely a kettő közötti kapcsolatokat hitelesíti.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Másolja le a `connectionString` kulcs értékét a JSON-kimenetből, és mentse. Ez az érték az eszköz kapcsolati sztringje. Erre a kapcsolati sztringre a következő szakaszban, az IoT Edge-futtatókörnyezet konfigurálásához lesz szükség.

   ![A CLI-kimenetből származó kapcsolatok karakterláncának beolvasása](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse az Azure IoT Edge-futtatókörnyezetet az IoT Edge-eszközön, és a konfigurálást eszközkapcsolati sztring használatával végezze el.
![Diagram – a futtatókörnyezet elindítása az eszközön](./media/quickstart/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. A *IoT Edge biztonsági démon* minden alkalommal elindul, amikor egy IoT Edge-eszköz elindul, és elindítja az eszközt a IoT Edge ügynök elindításával. A *IoT Edge ügynök* kezeli a IoT Edge eszközön található modulok telepítését és figyelését, beleértve az IoT Edge hubot is. Az *IoT Edge hub* kezeli a IoT Edge eszközön található modulok közötti kommunikációt, valamint az eszköz és a IoT hub között.

A telepítési parancsfájl tartalmaz egy Moby nevű tároló motort is, amely a IoT Edge eszközön lévő tároló lemezképeit kezeli.

A futtatókörnyezet telepítése során a rendszer rá fog kérdezni az eszközkapcsolati sztringre. Ez esetben az Azure CLI-ről lekért sztringet használja. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="connect-to-your-iot-edge-device"></a>Kapcsolódás a IoT Edge eszközhöz

Az ebben a szakaszban foglalt lépések mindegyike a IoT Edge eszközön történik, így most a Távoli asztalról szeretne csatlakozni a virtuális géphez.

### <a name="install-and-configure-the-iot-edge-service"></a>A IoT Edge szolgáltatás telepítése és konfigurálása

Az IoT Edge-futtatókörnyezet letöltése és telepítése a PowerShell használatával történik. Az eszköz konfigurálásához az IoT Hubról lekért eszközkapcsolati sztringet használja.

1. Ha még nem tette meg, kövesse az [új Azure IoT Edge eszköz regisztrálása](how-to-register-device.md) az eszköz regisztrálásához és az eszköz kapcsolódási karakterláncának beolvasásához című témakör lépéseit.

2. A virtuális gépen futtassa a PowerShellt rendszergazdaként.

   >[!NOTE]
   >A PowerShell AMD64-munkamenetének használatával telepítse a IoT Edge, nem a PowerShellt (x86). Ha nem biztos abban, hogy melyik munkamenet-típust használja, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Az **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows rendszerű számítógép támogatott verziójú-e, bekapcsolja a tárolók szolgáltatást, letölti a Moby Runtime-t, majd letölti a IoT Edge futtatókörnyezetet.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Előfordulhat, hogy a gép automatikusan újraindul. Ha a Deploy-IoTEdge parancs kéri az újraindítást, tegye meg most.

5. Futtassa újra a PowerShellt rendszergazdaként.

6. Az **inicializálás-IoTEdge** parancs konfigurálja a IoT Edge futtatókörnyezetet a gépen. A parancs alapértelmezett értéke a Windows-tárolók manuális kiépítés.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Ha a rendszer a **DeviceConnectionString** megadására kéri, akkor illessze be az előző szakaszból átmásolt sztringet. A kapcsolati sztring elé és után ne írjon idézőjeleket.

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Ellenőrizze, hogy a futtatókörnyezet megfelelően lett-e telepítve és konfigurálva. Eltarthat néhány percig, amíg a telepítés befejeződik, és elindul a IoT Edge Agent modul.

1. Ellenőrizze az IoT Edge-szolgáltatás állapotát.

   ```powershell
   Get-Service iotedge
   ```

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. A edgeAgent modul alapértelmezés szerint fut, és az eszközre telepített további modulok telepítését és elindítását is lehetővé teszi.

    ```powershell
    iotedge list
    ```

   ![Egy modul megtekintése az eszközön](./media/quickstart/iotedge-list-1.png)

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Felügyelheti Azure IoT Edge eszközét a felhőből egy olyan modul üzembe helyezéséhez, amely telemetria adatokat küld IoT Hub.

![Diagram – modul üzembe helyezése a felhőből az eszközre](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezt követően a Azure Portal használatával telepítheti az eszközön a IoT Edge modult anélkül, hogy magát az eszközt kellene módosítania.

Ebben az esetben a lekért modul olyan minta környezeti adatfeldolgozást hoz létre, amelyet később tesztelésre használhat. A szimulált érzékelő a gépet és a környezetet is figyeli a gép körül. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalmat, a gép hőmérsékletét, a nyomást és egy timestampet. A IoT Edge oktatóanyagok a modul által létrehozott adatelemzési adatként használják.

Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön.

```powershell
iotedge list
```

   ![Három modul megtekintése az eszközön](./media/quickstart/iotedge-list-2.png)

Megtekintheti a hőmérséklet-érzékelő modulból a felhőbe küldött üzeneteket.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >IoT Edge parancsok a kis-és nagybetűk megkülönböztetésére szolgálnak a modulok neveire való hivatkozáskor.

   ![A modulból származó adatok megtekintése](./media/quickstart/iotedge-logs.png)

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával megtekintheti az IoT hub-ra érkező üzeneteket is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ellenkező esetben törölheti a létrehozott Azure-erőforrásokat a díjak elkerülése érdekében.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze az erőforráscsoport tartalmát, és győződjön meg róla, hogy nem kívánja megőrizni a lépést. Ha nem szeretné törölni a teljes csoportot, az egyes erőforrásokat is törölheti.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza.

Távolítsa el az **IoTEdgeResources** csoportot. Egy erőforráscsoport törlése eltarthat néhány percig.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Az erőforráscsoport törlését az erőforráscsoportok listájának megtekintésével ellenőrizheti.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge Cloud Interface használatával helyezi üzembe a kódot az eszközön. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés a helyi fejlesztési környezet beállítása, amelynek segítségével megkezdheti az üzleti logikát futtató IoT Edge-modulok létrehozását.

> [!div class="nextstepaction"]
> [IoT Edge-modulok fejlesztésének megkezdése Windows-eszközökön](tutorial-develop-for-windows.md)
