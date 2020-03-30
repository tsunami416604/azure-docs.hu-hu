---
title: Rövid útmutató azure IoT Edge-eszköz létrehozása Linuxon | Microsoft dokumentumok
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy IoT Edge-eszközt, és hogyan helyezheti üzembe az előre összeállított kódot távolról az Azure Portalról.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 52258a8bc287df36158ec143e4aad74c34455ea4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80236077"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Rövid útmutató: Telepítse az első IoT Edge-modult egy virtuális Linux-eszközre

Tesztelje az Azure IoT Edge-et ebben a rövid útmutatóban, és telepítse a tárolóba helyezett kódot egy virtuális IoT Edge-eszközre. Az IoT Edge lehetővé teszi, hogy távolról kezelje a kódot az eszközökön, így több számítási feladatot küldhet a peremszélre. Ebben a rövid útmutatóban azt javasoljuk, hogy egy Azure virtuális gép az IoT Edge-eszköz, amely lehetővé teszi, hogy gyorsan hozzon létre egy tesztgép az összes előfeltétel telepítve van, majd törölje azt, ha elkészült.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. IoT Hub létrehozása
2. IoT Edge-eszköz regisztrálása az IoT Hubon
3. Telepítse és indítsa el az IoT Edge futási időt a virtuális eszközön.
4. Modul távoli üzembe helyezése IoT Edge-eszközön.

![Diagram – Rövid útmutató architektúra az eszközhöz és a felhőhöz](./media/quickstart-linux/install-edge-full.png)

Ez a rövid útmutató végigvezeti az IoT Edge-eszközként konfigurált Linux-virtuális gép létrehozásán. Ezután egy modult helyezhet üzembe az eszközén az Azure Portalról. A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A rövid útmutató számos lépésében az Azure CLI használatára van szükség, és az Azure IoT egyik bővítményével további funkciókhoz férhet hozzá.

Adja hozzá az Azure IoT bővítményt a Cloud Shell-példányhoz.

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

* Egy Linux rendszerű eszköz vagy virtuális gép, amely IoT Edge-eszközként szolgál majd. A Microsoft által biztosított [Azure IoT Edge-et az Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) virtuális gépen kell használnia, amely előtelepít mindent, amire szüksége van az IoT Edge futtatásához egy eszközön. Fogadja el a használati feltételeket, és hozza létre ezt a virtuális gépet a következő parancsokkal:

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Az új virtuális gép létrehozása és elindítása néhány percet is igénybe vehet.

   Amikor új virtuális gépet hoz létre, jegyezze fel a create parancskimenet részeként biztosított **publicIpAddress**címet. Ezt a nyilvános IP-címet fogja használni a virtuális géphez való csatlakozáshoz később a rövid útmutatóban.

* Ha az Azure IoT Edge-futási időt a saját eszközén szeretné futtatni, kövesse [az Azure IoT Edge futásidejű linuxos telepítése](how-to-install-iot-edge-linux.md)című témakörutasításait.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Indítsa el a rövid útmutatót egy IoT-központ azure CLI-vel való létrehozásával.

![Diagram – IoT-központ létrehozása a felhőben](./media/quickstart-linux/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta az IoT Hubot, és már létrehozott egy ingyenes központot, használhatja azt is. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet.

A következő kód egy ingyenes **F1** központot hoz létre az **IoTEdgeResources** erőforráscsoportban. Cserélje `{hub_name}` le az IoT hub egyedi nevét.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Ha hibaüzenetet kap, hogy az IoT Hub neve nem érhető el, az azt jelenti, hogy valaki más már rendelkezik ezzel a névvel rendelkező központtal. Próbáljon ki egy új nevet.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.

![Diagram – Egy Eszköz regisztrálása IoT Hub-identitással](./media/quickstart-linux/register-device.png)

Hozzon létre egy eszközidentitást az IoT Edge-eszközhöz, hogy kommunikáljon az IoT hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

Mivel az IoT Edge-eszközök viselkednek, és a tipikus IoT-eszközöktől eltérően kezelhetők, `--edge-enabled` deklarálják ezt az identitást a jelzővel rendelkező IoT Edge-eszközszámára.

1. Az Azure Cloud Shellben a következő paranccsal hozza létre a **myEdgeDevice** nevű eszközt a központjában.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Ha hibajelzést kap az iothubowner szabályzatkulcsokról, győződjön meg róla, hogy a cloud shell az azure-cli-iot-ext bővítmény legújabb verzióját futtatja.

2. Kérje le az eszköze kapcsolati sztringjét, amely összeköti a fizikai eszközt az IoT Hubban tárolt identitással.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Másolja a kulcs `connectionString` értékét a JSON kimenetből, és mentse azt. Ez az érték az eszköz kapcsolati sztringje. Erre a kapcsolati sztringre a következő szakaszban, az IoT Edge-futtatókörnyezet konfigurálásához lesz szükség.

   ![Kapcsolati karakterlánc beolvasása a CLI-kimenetből](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Az IoT Edge-eszköz konfigurálása

Indítsa el az Azure IoT Edge-futásidejű az IoT Edge-eszközön.

![Diagram – A futásidő indítása az eszközön](./media/quickstart-linux/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. Az **IoT Edge biztonsági démon** elindul minden alkalommal, amikor egy IoT Edge-eszköz elindul, és az IoT Edge-ügynök indításával az eszközt. Az **IoT Edge-ügynök** a modulok üzembe helyezését és monitorozását segíti az IoT Edge-eszközön, beleértve az IoT Edge-központot is. Az **IoT Edge-központ** az IoT Edge-eszközön lévő modulok, valamint az eszköz és az IoT Hub közötti kommunikációt kezeli.

A futtatókörnyezet konfigurálása során meg kell adnia egy eszközkapcsolati sztringet. Ez esetben az Azure CLI-ről lekért sztringet használja. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>A kapcsolati karakterlánc beállítása az IoT Edge-eszközön

Ha az Azure IoT Edge-et az Azure virtuális gépen az előfeltételekben leírtak szerint használja, akkor az eszköz már telepítette az IoT Edge futásidejűt. Csak be kell állítania az eszközt az előző szakaszban lekért eszközkapcsolati karakterlánccal. Ezt távolról is megteheti anélkül, hogy kapcsolódnia kellene a virtuális géphez. Futtassa a `{device_connection_string}` következő parancsot, és cserélje le a saját karakterláncát.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Ha az IoT Edge-et a helyi számítógépen vagy egy ARM32-es vagy ARM64-eszközön futtatja, telepítenie kell az IoT Edge futtatói teendőit és annak előfeltételeit az eszközre. Kövesse az [Azure IoT Edge-futásidő Linuxon való telepítése](how-to-install-iot-edge-linux.md)című témakör utasításait, majd térjen vissza ehhez a rövid útmutatóhoz.

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

A rövid útmutató ban a többi parancs az IoT Edge-eszközön történik, így láthatja, mi történik az eszközön. Ha virtuális gépet használ, csatlakozzon a géphez most a létrehozási parancs által leadott nyilvános IP-címet használva. A nyilvános IP-címet a virtuális gép áttekintő lapján is megtalálhatja az Azure Portalon. Használja a következő parancsot a virtuális géphez való csatlakozásra. Cserélje `{azureuser}` le, ha az előfeltételekben javasolttól eltérő felhasználónevet használt. Cserélje `{publicIpAddress}` ki a gép címét.

   ```console
   ssh azureuser@{publicIpAddress}
   ```

Ellenőrizze, hogy a futásidejű sikeresen telepítve és konfigurálva az IoT Edge-eszközön.

>[!TIP]
>Az `iotedge` parancsok futtatásához megemelt jogosultsági szint szükséges. Amikor az IoT Edge-futtatókörnyezet telepítése után kijelentkezik, majd először újra bejelentkezik a gépre, az engedélyei automatikusan frissülnek. Addig is `sudo` használja a parancsok előtt.

1. Ellenőrizze, hogy az IoT Edge biztonsági démon rendszerszolgáltatásként fut-e.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Tekintse meg az IoT Edge démon rendszerszolgáltatásként futó](./media/quickstart-linux/iotedged-running.png)

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
![Diagram - modul telepítése a felhőből az eszközre](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezután az Azure Portalon üzembe helyezhet egy IoT Edge-modult az eszközön való futtatáshoz anélkül, hogy módosítania kellene magát az eszközt.

Ebben az esetben a leadott modul mintaadatokat hoz létre, amelyek tesztelésre használhatók. A szimulált hőmérséklet-érzékelő modul környezeti adatokat hoz létre, amelyeket később tesztelésre használhat. A szimulált érzékelő figyeli mind a gépet, mind a gép körüli környezetet. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalmat, a gép hőmérsékletét, a nyomást és egy timestampet. Az IoT Edge oktatóanyagok a modul által létrehozott adatokat használják elemzési tesztadatokként.

Nyissa meg ismét a parancssort a IoT Edge-eszközön, vagy használja az SSH-kapcsolatokat az Azure CLI-ről. Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön:

   ```bash
   sudo iotedge list
   ```

   ![Három modul megtekintése az eszközön](./media/quickstart-linux/iotedge-list-2.png)

A hőmérséklet-érzékelő modulból küldött üzenetek megtekintése:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Az IoT Edge-parancsok a kis- és nagybetűket megkülönböztetők, amikor a modulnevekre hivatkoznak.

   ![A modulból származó adatok megtekintése](./media/quickstart-linux/iotedge-logs.png)

Az IT-központba érkező üzeneteket is megtekintheti az [Azure IoT Hub-bővítmény visual studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább szeretne dolgozni az IoT Edge-oktatóanyagokkal, használhatja az ebben a rövid útmutatóban regisztrált és létrehozott eszközt. Ellenkező esetben törölheti az Azure-erőforrásokat, amelyeket a költségek elkerülése érdekében hozhat létre.

Ha a virtuális gépet és az IoT Hubot egy új erőforráscsoportban hozta létre, törölheti azt a csoportot és az összes társított erőforrást. Ellenőrizze az erőforráscsoport tartalmát, és győződjön meg arról, hogy nincs-e megtartani kívánt tartalom. Ha nem szeretné törölni az egész csoportot, törölheti az egyes erőforrásokat.

Távolítsa el az **IoTEdgeResources** csoportot.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge-eszközt, és az Azure IoT Edge felhőfelületét használta a kód üzembe helyezéséhez az eszközre. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés a helyi fejlesztési környezet beállítása, hogy megkezdheti az Üzleti logikát futó IoT Edge-modulok létrehozását.

> [!div class="nextstepaction"]
> [IoT Edge-modulok fejlesztésének megkezdése Linux-eszközökhöz](tutorial-develop-for-linux.md)
