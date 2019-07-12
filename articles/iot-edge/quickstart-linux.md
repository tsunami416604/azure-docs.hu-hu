---
title: A rövid útmutató az Azure IoT Edge-eszköz létrehozása Linux rendszeren |} A Microsoft Docs
description: Ebben a rövid útmutató egy IoT Edge-eszköz létrehozása és üzembe helyezhesse az előre összeállított kódot távolról az Azure Portalról.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/09/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: e4b2f9eaa243c0cbef66f88544be769481dd6722
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798658"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-device"></a>Gyors útmutató: Az első IoT Edge-modul üzembe helyezése Linux-eszközön

Az Azure IoT Edge kiterjeszti a felhő képességeit IoT-eszközeire. Ebben a rövid útmutatóban megismerheti, hogyan helyezhet üzembe előre összeállított kódot távolról egy IoT Edge-eszközön a felhőalapú felület használatával.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása az IoT Hubon
3. Az IoT Edge-futtatókörnyezet telepítése és elindítása az eszközén.
4. Modul távoli üzembe helyezése IoT Edge-eszközön.

![Diagram – rövid útmutató architektúra eszközhöz és a felhő](./media/quickstart-linux/install-edge-full.png)

Ez a rövid útmutató végigvezeti egy Azure virtuális gépen, amely úgy van konfigurálva, hogy IoT Edge-eszköz létrehozása. Ezután egy modult helyezhet üzembe az eszközén az Azure Portalról. A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A rövid útmutató számos lépésében az Azure CLI használatára van szükség, és az Azure IoT egyik bővítményével további funkciókhoz férhet hozzá.

Adja hozzá az Azure IoT bővítményt a Cloud Shell-példányhoz.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Előfeltételek

Felhőerőforrások:

* Egy erőforráscsoport a rövid útmutató során létrehozott összes erőforrás kezelésére.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-eszköz:

* Egy Linux rendszerű eszköz vagy virtuális gép, amely IoT Edge-eszközként szolgál majd. A Microsoft által biztosított használjon [Ubuntu rendszeren az Azure IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) virtuális gépet, mely előtelepíti tudnivalót az IoT Edge futtatják egy eszközön. Fogadja el a használati feltételeket, és hozzon létre a virtuális gép a következő parancsokkal:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Hozzon létre, és az új virtuális gép elindítása néhány percig is eltarthat.

   Amikor létrehoz egy új virtuális gépet, jegyezze fel a a **publicIpAddress**, amely a create parancs kimenete részeként van megadva. A nyilvános IP-cím használatával fog csatlakozni a virtuális géphez az útmutató későbbi részében.

* Ha inkább a saját eszközön futtathatók az Azure IoT Edge-futtatókörnyezet, kövesse az utasításokat, [(x64) linuxon az Azure IoT Edge-futtatókörnyezet telepítéséhez](how-to-install-iot-edge-linux.md) vagy [(ARM32v7/armhf) Linux rendszeren telepítse az Azure IoT Edge-futtatókörnyezet](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Először a rövid útmutató egy IoT hub létrehozása az Azure CLI használatával.

![Diagram – a felhőben az IoT hub létrehozása](./media/quickstart-linux/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta az IoT Hubot, és már létrehozott egy ingyenes központot, használhatja azt is. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet.

A következő kód egy ingyenes **F1** központot hoz létre az **IoTEdgeResources** erőforráscsoportban. A *{hub_name}* elemet cserélje le az IoT Hub központ egyedi nevére.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Ha a hiba, hogy az IoT Hub-név nem érhető el, az azt jelenti, hogy valaki más már van ilyen nevű hub. Adjon meg új nevet.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Diagram - eszköz regisztrálása az IoT Hub identitással](./media/quickstart-linux/register-device.png)

Hozzon létre egy új eszközidentitást az IoT Edge-eszköz számára, így az képes legyen kommunikálni az IoT hub. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

IoT Edge-eszközök viselkednek, és működnek, mint a tipikus IoT-eszközök felügyelhetők, mivel ezt az identitást, az IoT Edge-eszköz, a deklarálja a `--edge-enabled` jelzőt.

1. Az Azure Cloud Shellben a következő paranccsal hozza létre a **myEdgeDevice** nevű eszközt a központjában.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Ha hibaüzenetet kap kapcsolatos iothubowner szabályzatbejegyzések, győződjön meg arról, hogy a cloud shell fut-e az azure-cli-iot-ext bővítmény legújabb verziója.

2. Kérje le az eszköze kapcsolati sztringjét, amely összeköti a fizikai eszközt az IoT Hubban tárolt identitással.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Másolja az értéket, a `connectionString` a JSON-kimeneteket a kulcsát, és mentse azt. Ezt az értéket az eszköz kapcsolati karakterláncának. Ez a kapcsolati karakterlánc használatával fog az IoT Edge-futtatókörnyezet konfigurálása a következő szakaszban.

   ![Kapcsolati karakterlánc lekérése a CLI-kimenetből](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Az IoT Edge-eszköz konfigurálása

Indítsa el az Azure IoT Edge-futtatókörnyezet az IoT Edge-eszközön.

![Diagram - indítása a futtatókörnyezet az eszközön](./media/quickstart-linux/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. A **IoT Edge biztonsági démon** minden alkalommal, amikor egy IoT Edge-eszköz indul el, és csatlakoztatja az eszközt az IoT Edge-ügynök elindításával kezdődik. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön, beleértve az IoT Edge-központot is. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

A futtatókörnyezet konfigurálása során meg kell adnia egy eszközkapcsolati sztringet. Ez esetben az Azure CLI-ről lekért sztringet használja. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Beállítjuk a kapcsolati karakterláncot az IoT Edge-eszközön

Ha az Azure IoT Edge Ubuntu virtuális gép használ, az Előfeltételek leírtak szerint, majd az eszköz már telepítve van az IoT Edge-futtatókörnyezet. Csak az eszköz konfigurálásához az eszköz kapcsolati karakterlánccal, amely az előző szakaszban lekért kell. Ehhez távolról csatlakozhat a virtuális géphez nélkül. Futtassa a következő parancsot, és cserélje le **{device_connection_string}** saját karakterlánccal.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

IoT Edge futtatásakor a helyi számítógépre vagy egy ARM32 eszközön, az eszközön az IoT Edge-futtatókörnyezet és előfeltételei telepíteni szeretné. Kövesse a [(x64) linuxon az Azure IoT Edge-futtatókörnyezet telepítéséhez](how-to-install-iot-edge-linux.md) vagy [(ARM32v7/armhf) Linux rendszeren telepítse az Azure IoT Edge-futtatókörnyezet](how-to-install-iot-edge-linux-arm.md), majd térjen vissza ehhez a rövid.

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Ebben a rövid útmutatóban szereplő parancsok a többi elvégezze az IoT Edge-eszköz, így láthatja, mi történik az eszközön. Ha egy virtuális gépet használ, csatlakozzon, hogy a gép most már használja a nyilvános IP-címet, amely a létrehozási parancs kimeneti volt. A nyilvános IP-címet a virtuális gép – Áttekintés lapon az Azure Portalon is található. Az alábbi parancs segítségével csatlakozhat a virtuális géphez. Cserélje le **{azureuser}** javasolt az előfeltételeket, mint más felhasználónevet használatakor. Cserélje le **{publicIpAddress}** számítógépe címmel.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Győződjön meg arról, hogy sikeres volt-e telepítve a modul, és az IoT Edge-eszköz konfigurálva.

>[!TIP]
>Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig alkalmazza a **sudo** kifejezést a parancsok előtt.

1. Ellenőrizze, hogy az IoT Edge biztonsági démon fut-e a rendszer szolgáltatásként.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Tekintse meg az IoT Edge démon rendszer szolgáltatásként fut.](./media/quickstart-linux/iotedged-running.png)

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```bash
   journalctl -u iotedge
   ```

3. Tekintse meg az eszközön futó modulokat.

   ```bash
   sudo iotedge list
   ```

   ![Egy modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-1.png)

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Azure IoT Edge-eszközeit kezelheti a felhőből, és üzembe helyezhet egy olyan modult, amely telemetriaadatokat küld az IoT Hubra.
![Diagram - eszközre a felhőből modul üzembe helyezése](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Az Azure Portalon, majd az eszköz maga az eszköz módosításához nélkül futtathat egy IoT Edge-modul telepítéséhez használt.

Ebben az esetben a modul, amely a leküldött teszteléshez használható mintaadatokat hoz létre. A szimulált hőmérsékleti érzékelő modul újabb teszteléséhez használható környezeti adatok állít elő. A szimulált érzékelő által figyelt, a gép körül a környezet és a egy gép. Az érzékelő Előfordulhat például, egy kiszolgáló szobában, egy gyárban, vagy egy szélturbina sürgősségi. Az üzenet tartalmazza a környezeti hőmérséklet és páratartalom, gép hőmérséklet és nyomás és a egy időbélyegző. Az IoT Edge-oktatóanyagok a Tesztadatok elemzéshez, a modul által létrehozott adatokat használja.

Nyissa meg a parancssort az IoT Edge-eszközön újra, vagy használja az Azure CLI-vel SSH-kapcsolatot. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

   ```bash
   sudo iotedge list
   ```

   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2.png)

A hőmérséklet-érzékelő modul küldött üzenetek megjelenítése:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge-parancsok modulneveket kontextusban való megnevezésekor nagybetűk között.

   ![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

Az üzeneteket az IoT hub kiszolgálófarmban használatával is megtekintheti a [Azure IoT Hub-eszközkészlet bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábbi nevén Azure IoT-eszközkészlet bővítmény).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ellenkező esetben az Azure-erőforrások, Ön által létrehozott díjak elkerülése érdekében törölheti.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze a győződjön meg arról, hogy ott az erőforráscsoportot, amelybe a tartalmát a semmi meg szeretné tartani. A teljes csoport törlése nem szeretné, ha ehelyett törölheti az egyes erőforrásokat.

Távolítsa el az **IoTEdgeResources** csoportot.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>További lépések


Ebben a rövid útmutatóban létrehozott egy IoT Edge-eszközön, és az eszközre kód üzembe helyezése az Azure IoT Edge felhőalapú interfésze használt. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés az, hogy, hozzákezdhet az IoT Edge modulokról az üzleti logikát a helyi fejlesztési környezet beállítása. 

> [!div class="nextstepaction"]
> [A fejlesztési munka a Linux rendszerű eszközök IoT Edge-modulok](tutorial-develop-for-linux.md)
