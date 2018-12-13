---
title: A rövid útmutató az Azure IoT Edge-eszköz létrehozása Linux rendszeren |} A Microsoft Docs
description: Ebben a rövid útmutató egy IoT Edge-eszköz létrehozása és üzembe helyezhesse az előre összeállított kódot távolról az Azure Portalról.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 7f56a03264ddb81ef4cced2437a649e2f1eaaa54
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083546"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Rövid útmutató: Az első IoT Edge-modul üzembe helyezése x64-es Linux-eszközön

Az Azure IoT Edge kiterjeszti a felhő képességeit IoT-eszközeire. Ebben a rövid útmutatóban megismerheti, hogyan helyezhet üzembe előre összeállított kódot távolról egy IoT Edge-eszközön a felhőalapú felület használatával.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása az IoT Hubon
3. Az IoT Edge-futtatókörnyezet telepítése és elindítása az eszközén.
4. Modul távoli üzembe helyezése IoT Edge-eszközön.

![Diagram – rövid útmutató architektúra eszközhöz és a felhő](./media/quickstart-linux/install-edge-full.png)

Ebben a rövid útmutatóban Linux rendszerű számítógépét vagy virtuális gépét IoT Edge-eszközzé alakíthatja. Ezután egy modult helyezhet üzembe az eszközén az Azure Portalról. A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre.

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

* Egy Linux rendszerű eszköz vagy virtuális gép, amely IoT Edge-eszközként szolgál majd. Azt javasoljuk, hogy használja a Microsoft által biztosított [Ubuntu rendszeren az Azure IoT Edge](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) virtuális géphez, amely az IoT Edge-futtatókörnyezet rendszer előtelepítése. Hozza létre a virtuális gép a következő paranccsal:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Amikor létrehoz egy új virtuális gépet, jegyezze fel a a **publicIpAddress**, amely a create parancs kimenete részeként van megadva. A nyilvános IP-cím használatával fog csatlakozni a virtuális géphez az útmutató későbbi részében.

* Ha inkább a helyi rendszeren az Azure IoT Edge-futtatókörnyezet futtatásához kövesse az utasításokat, [(x64) linuxon az Azure IoT Edge-futtatókörnyezet telepítéséhez](how-to-install-iot-edge-linux.md).

* Ha szeretne egy alapú ARM32 az eszköz, például a Raspberry Pi-utasításai [(ARM32v7/armhf) Linux rendszeren telepítse az Azure IoT Edge-futtatókörnyezet](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutató első lépéseként hozza létre az IoT Hubot az Azure CLI használatával.

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

Hozzon létre egy eszközidentitást a szimulált eszközhöz, hogy az kommunikálhasson az IoT Hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz. 

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

3. Másolja és mentse a kapcsolati sztringet. Erre az értékre a következő szakaszban, az IoT Edge-futtatókörnyezet konfigurálásához lesz szükség. 

## <a name="connect-the-iot-edge-device-to-iot-hub"></a>Az IoT Edge-eszköz csatlakoztatása az IoT hubhoz

Telepítse és indítsa el IoT Edge-eszközén az Azure IoT Edge-futtatókörnyezetet. 
![Diagram - indítása a futtatókörnyezet az eszközön](./media/quickstart-linux/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. Az **IoT Edge biztonsági démon** az Edge-eszközök indulásakor lép működésbe, és az IoT Edge-ügynök elindításával elvégzi az eszköz rendszerindítását. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön, beleértve az IoT Edge-központot is. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli. 

A futtatókörnyezet konfigurálása során meg kell adnia egy eszközkapcsolati sztringet. Ez esetben az Azure CLI-ről lekért sztringet használja. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban. 

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Beállítjuk a kapcsolati karakterláncot az IoT Edge-eszközön

* Ubuntu virtuális gépen az Azure IoT Edge használata esetén használja az eszköz kapcsolati karakterláncának korábban vágólapra másolt és így távolról konfigurálhat az IoT Edge-eszköz:

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

   A fennmaradó lépéseiért beolvasni a nyilvános IP-címet, amely a létrehozási parancs kimeneti volt. A nyilvános IP-címet a virtuális gép – Áttekintés lapon az Azure Portalon is található. Az alábbi parancs segítségével csatlakozhat a virtuális géphez. Cserélje le **{publicIpAddress}** számítógépe címmel. 

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

* IoT Edge futtatásakor a helyi számítógépre vagy egy ARM32 eszközön, nyissa meg a konfigurációs fájlban található /etc/iotedge/config.yaml és frissítse a **device_connection_string** változó értékét a korábban vágólapra másolt, majd indítsa újra a az IoT Edge biztonsági démon a módosítások alkalmazásához:

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig alkalmazza a **sudo** kifejezést a parancsok előtt. 

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Ellenőrizze, hogy a futtatókörnyezet megfelelően lett-e telepítve és konfigurálva.

1. Ellenőrizze, hogy az Edge biztonsági démon rendszerszolgáltatásként fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Az Edge démon rendszerszolgáltatásként való futásának megtekintése](./media/quickstart-linux/iotedged-running.png)

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

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portal segítségével úgy futtatta az IoT Edge-modult az eszközön, hogy magát az eszközt nem kellett módosítania. Ebben az esetben az Ön által továbbított modul az oktatóanyagokhoz használható környezeti adatokat hoz létre.

Nyissa meg a parancssort az IoT Edge-eszközön újra, vagy használja az Azure CLI-vel SSH-kapcsolatot. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

   ```bash
   sudo iotedge list
   ```

   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2.png)

Tekintse meg a tempSensor modul által küldött üzeneteket:

   ```bash
   sudo iotedge logs tempSensor -f
   ```

![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

Előfordulhat, hogy a hőmérsékletérzékelő modul az Edge Hubhoz való csatlakozásra vár, ha a napló utolsó sora `Using transport Mqtt_Tcp_Only`. Próbálja meg leállítani a modult, és hagyja, hogy az Edge-ügynök újraindítsa. A modult a következő paranccsal állíthatja le: `sudo docker stop tempSensor`.

Az üzeneteket az IoT hub kiszolgálófarmban használatával is megtekintheti a [Azure IoT-eszközkészlet bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ha nem, törölheti a létrehozott Azure-erőforrásokat, és eltávolíthatja az IoT Edge-futtatókörnyezetet az eszközről.

### <a name="delete-azure-resources"></a>Azure-erőforrások törlése

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze a győződjön meg arról, hogy ott az erőforráscsoportot, amelybe a tartalmát a semmi meg szeretné tartani. A teljes csoport törlése nem szeretné, ha ehelyett törölheti az egyes erőforrásokat.

Távolítsa el az **IoTEdgeResources** csoportot.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet eltávolítása

Ha el szeretné távolítani a telepítéseket az eszközéről, azt a következő parancsokkal teheti meg.  

Távolítsa el az IoT Edge-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Ha eltávolította az IoT Edge-futtatókörnyezetet, az általa létrehozott tárolók leállnak, de továbbra is ott lesznek az eszközön. Tekintse meg az összes tárolót.

   ```bash
   sudo docker ps -a
   ```

Törölje azokat a tárolókat, amelyeket az IoT Edge-futtatókörnyezet hozott létre az eszközén. Ha más nevet adott neki, módosítsa a tempSensor tároló nevét. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Távolítsa el a tároló-futtatókörnyezetet.

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutató minden IoT Edge-oktatóanyag előfeltétele. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, hogyan alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Érzékelők adatainak szűrése Azure Functions-függvényekkel](tutorial-deploy-function.md)
