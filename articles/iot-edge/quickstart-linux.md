---
title: A rövid útmutató az Azure IoT Edge-eszköz létrehozása Linux rendszeren |} A Microsoft Docs
description: Ebben a rövid útmutató egy IoT Edge-eszköz létrehozása és üzembe helyezhesse az előre összeállított kódot távolról az Azure Portalról.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a076f9e6ae67b9a4dc27fcd058945716dfebe75
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384377"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Rövid útmutató: az első IoT Edge modul üzembe helyezése egy virtuális Linux-eszközön

Kipróbálhatja Azure IoT Edge ebben a rövid útmutatóban, ha egy virtuális IoT Edge eszközre helyez üzembe tároló kódot. IoT Edge lehetővé teszi a kódok távoli kezelését az eszközökön, így több számítási feladatot is elküldhet a peremhálózat számára. Ebben a rövid útmutatóban azt javasoljuk, hogy használjon egy Azure-beli virtuális gépet a IoT Edge eszközhöz, amely lehetővé teszi, hogy gyorsan hozzon létre egy tesztelési gépet a telepített előfeltételekkel, majd törölje azt, ha elkészült.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása az IoT Hubon
3. Telepítse és indítsa el a IoT Edge futtatókörnyezetet a virtuális eszközön.
4. Modul távoli üzembe helyezése IoT Edge-eszközön.

![Diagram – rövid útmutató architektúra eszközhöz és a felhő](./media/quickstart-linux/install-edge-full.png)

Ez a rövid útmutató végigvezeti egy olyan linuxos virtuális gép létrehozásán, amely IoT Edge eszközre van konfigurálva. Ezután egy modult helyezhet üzembe az eszközén az Azure Portalról. A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre.

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

* Egy Linux rendszerű eszköz vagy virtuális gép, amely IoT Edge-eszközként szolgál majd. Az Ubuntu virtuális gépen a Microsoft által biztosított [Azure IoT Edget](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) kell használnia, amely az eszközön IoT Edge futtatásához szükséges összes előtelepítést lehetővé teszi. Fogadja el a használati feltételeket, és hozza létre a virtuális gépet az alábbi parancsokkal:

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Az új virtuális gép létrehozása és elindítása néhány percet is igénybe vehet.

   Új virtuális gép létrehozásakor jegyezze fel a **publicIpAddress**, amely a Create Command kimenet részeként van megadva. A nyilvános IP-cím használatával fog csatlakozni a virtuális géphez az útmutató későbbi részében.

* Ha a Azure IoT Edge futtatókörnyezetet a saját eszközén szeretné futtatni, kövesse [az Azure IoT Edge Runtime Linux rendszeren való telepítésének](how-to-install-iot-edge-linux.md)utasításait.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutató elindításához hozzon létre egy IoT hubot az Azure CLI-vel.

![Diagram – a felhőben az IoT hub létrehozása](./media/quickstart-linux/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta az IoT Hubot, és már létrehozott egy ingyenes központot, használhatja azt is. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet.

A következő kód egy ingyenes **F1** központot hoz létre az **IoTEdgeResources** erőforráscsoportban. Cserélje le a `{hub_name}`t az IoT hub egyedi nevére.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Ha a hiba, hogy az IoT Hub-név nem érhető el, az azt jelenti, hogy valaki más már van ilyen nevű hub. Adjon meg új nevet.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Diagram – eszköz regisztrálása IoT Hub identitással](./media/quickstart-linux/register-device.png)

Hozzon létre egy eszköz-identitást a IoT Edge eszközön, hogy az képes legyen kommunikálni az IoT hubhoz. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

Mivel IoT Edge-eszközök viselkedése és kezelése eltérően történik, mint a szokásos IoT-eszközök, deklarálja ezt az identitást egy IoT Edge eszköz számára a `--edge-enabled` jelzővel.

1. Az Azure Cloud Shellben a következő paranccsal hozza létre a **myEdgeDevice** nevű eszközt a központjában.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Ha hibaüzenetet kap kapcsolatos iothubowner szabályzatbejegyzések, győződjön meg arról, hogy a cloud shell fut-e az azure-cli-iot-ext bővítmény legújabb verziója.

2. Kérje le az eszköze kapcsolati sztringjét, amely összeköti a fizikai eszközt az IoT Hubban tárolt identitással.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Másolja a `connectionString` kulcs értékét a JSON-kimenetből, és mentse. Ez az érték az eszköz-kapcsolatok karakterlánca. Ezt a kapcsolódási karakterláncot fogja használni a IoT Edge futtatókörnyezet konfigurálásához a következő szakaszban.

   ![A CLI-kimenetből származó kapcsolatok karakterláncának beolvasása](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>A IoT Edge eszköz konfigurálása

Indítsa el a Azure IoT Edge futtatókörnyezetet a IoT Edge eszközön.

![Diagram – a futtatókörnyezet elindítása az eszközön](./media/quickstart-linux/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. A **IoT Edge biztonsági démon** minden alkalommal elindul, amikor egy IoT Edge-eszköz elindul, és elindítja az eszközt a IoT Edge ügynök elindításával. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön, beleértve az IoT Edge-központot is. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

A futtatókörnyezet konfigurálása során meg kell adnia egy eszközkapcsolati sztringet. Ez esetben az Azure CLI-ről lekért sztringet használja. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Beállítjuk a kapcsolati karakterláncot az IoT Edge-eszközön

Ha az előfeltételekben leírtak szerint az Ubuntu virtuális gépen a Azure IoT Edge használja, akkor az eszközön már telepítve van a IoT Edge futtatókörnyezet. Az eszközt csak az előző szakaszban lekért eszköz-kapcsolódási karakterlánccal kell konfigurálnia. Ezt távolról is megteheti anélkül, hogy csatlakoznia kellene a virtuális géphez. Futtassa a következő parancsot, és cserélje le a `{device_connection_string}`t a saját karakterlánccá.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Ha IoT Edget futtat a helyi gépen vagy egy ARM32 vagy ARM64-eszközön, telepítenie kell a IoT Edge futtatókörnyezetet és annak előfeltételeit az eszközön. Kövesse az [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](how-to-install-iot-edge-linux.md)című témakör útmutatásait, majd térjen vissza ehhez a rövid útmutatóhoz.

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Az ebben a rövid útmutatóban szereplő további parancsok a IoT Edge eszközön történnek, így megtekintheti, hogy mi történik az eszközön. Ha virtuális gépet használ, most a létrehozási parancs kimenetében lévő nyilvános IP-címet használva kapcsolódjon ehhez a géphez. A nyilvános IP-címet a virtuális gép – Áttekintés lapon az Azure Portalon is található. Az alábbi parancs segítségével csatlakozhat a virtuális géphez. Cserélje le `{azureuser}`, ha az előfeltételekben javasolttól eltérő felhasználónevet használt. Cserélje le a `{publicIpAddress}`t a gép címeként.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Ellenőrizze, hogy a futtatókörnyezet sikeresen telepítve és konfigurálva van-e a IoT Edge eszközön.

>[!TIP]
>Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig a parancsok előtt használja a `sudo`.

1. Ellenőrizze, hogy a IoT Edge biztonsági démon rendszerszolgáltatásként fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Tekintse meg a rendszerszolgáltatásként futó IoT Edge démont](./media/quickstart-linux/iotedged-running.png)

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
![diagram – modul üzembe helyezése a felhőből az eszközre](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezt követően a Azure Portal használatával telepítheti az eszközön a IoT Edge modult anélkül, hogy magát az eszközt kellene módosítania.

Ebben az esetben a lekért modul a teszteléshez használható mintaadatok létrehozását hozza létre. A szimulált hőmérséklet-érzékelő modul olyan környezeti adattípusokat hoz létre, amelyeket később tesztelésre használhat. A szimulált érzékelő a gépet és a környezetet is figyeli a gép körül. Előfordulhat például, hogy ez az érzékelő egy kiszolgálói helyiségben, egy gyári emeleten vagy egy szélturbina található. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalomot, a gépi hőmérsékletet és a nyomást, valamint egy időbélyeget. A IoT Edge oktatóanyagok a modul által létrehozott adatelemzési adatként használják.

Nyissa meg a parancssort az IoT Edge-eszközön újra, vagy használja az Azure CLI-vel SSH-kapcsolatot. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

   ```bash
   sudo iotedge list
   ```

   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2.png)

Tekintse meg a hőmérséklet-érzékelő modulból küldött üzeneteket:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge parancsok a kis-és nagybetűk megkülönböztetésére szolgálnak a modulok neveire való hivatkozáskor.

   ![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával megtekintheti az IoT hub-ra érkező üzeneteket is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ellenkező esetben törölheti a létrehozott Azure-erőforrásokat a díjak elkerülése érdekében.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze a győződjön meg arról, hogy ott az erőforráscsoportot, amelybe a tartalmát a semmi meg szeretné tartani. A teljes csoport törlése nem szeretné, ha ehelyett törölheti az egyes erőforrásokat.

Távolítsa el az **IoTEdgeResources** csoportot.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge Cloud Interface használatával helyezi üzembe a kódot az eszközön. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés a helyi fejlesztési környezet beállítása, amelynek segítségével megkezdheti az üzleti logikát futtató IoT Edge-modulok létrehozását.

> [!div class="nextstepaction"]
> [A Linux-eszközökhöz készült IoT Edge modulok fejlesztésének megkezdése](tutorial-develop-for-linux.md)
