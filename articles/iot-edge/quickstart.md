---
title: Rövid útmutató Azure IoT Edge-eszköz létrehozása Windows rendszeren | Microsoft dokumentumok
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy IoT Edge-eszközt, és hogyan helyezheti üzembe az előre összeállított kódot távolról az Azure Portalról.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/06/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 54efe7b5c392ad2b4cc3a0de414e04951b268508
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78674244"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Rövid útmutató: Az első IoT Edge-modul üzembe helyezése egy virtuális Windows-eszközre

Próbálja ki az Azure IoT Edge-et egy virtuális IoT Edge-eszközre való üzembe helyezéssel. Az IoT Edge lehetővé teszi, hogy távolról kezelje a kódot az eszközökön, így több számítási feladatot küldhet a peremszélre. Ehhez a rövid útmutatóhoz azt javasoljuk, hogy egy Azure virtuális gép az IoT Edge-eszköz. A virtuális gép használatával gyorsan létrehozhat egy tesztgépet, telepítheti az előfeltételeket, majd törölheti, ha végzett.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

1. Hozzon létre egy IoT hubot.
2. IoT Edge-eszköz regisztrálása az IoT Hubon
3. Telepítse és indítsa el az IoT Edge futási időt a virtuális eszközön.
4. Modul távoli üzembe helyezése IoT Edge-eszközön és Telemetria küldése az IoT Hubnak

![Diagram – Rövid útmutató architektúra az eszközhöz és a felhőhöz](./media/quickstart/install-edge-full.png)

Ez a rövid útmutató végigvezeti a Windows virtuális gép létrehozásán, és IoT Edge-eszközként konfigurálásán. Ezután egy modult helyezhet üzembe az eszközén az Azure Portalról. A jelen rövid útmutatóban üzembe helyezett modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom- és nyomásadatokat állít elő. A további Azure IoT Edge-oktatóanyagok az itt elvégzett munkára építkeznek olyan modulok üzembe helyezésével, amelyek a szimulált adatok elemzésével üzleti megállapításokat hoznak létre.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Az Azure CLI segítségével számos lépést ebben a rövid útmutatóban hajtson végre. Az Azure IoT rendelkezik egy bővítményt, amely további funkciókat tesz lehetővé.

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

* Egy Windows virtuális gép, amely IoT Edge-eszközként működik. Ezt a virtuális gépet a következő `{password}` paranccsal hozhatja létre, amelyet biztonságos jelszóval helyettesít:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Az új virtuális gép létrehozása és elindítása néhány percet is igénybe vehet. Ezután letöltheti az RDP-fájlt a virtuális géphez való csatlakozáshoz:

  1. Keresse meg az új Windows virtuális gépet az Azure Portalon.
  1. Kattintson a **Csatlakozás** gombra.
  1. Az **RDP** lapon válassza az **RDP-fájl letöltése**lehetőséget.

  Nyissa meg ezt a fájlt a Távoli asztali kapcsolat szolgáltatással, és csatlakozzon a Windows virtuális géphez a `az vm create` parancssal megadott rendszergazdai névvel és jelszóval.

> [!NOTE]
> Ez a rövid útmutató egy Windows asztali virtuális gépet használ az egyszerűség kedvéért. Arról, hogy mely Windows operációs rendszerek érhetők el általánosan éles környezetben, az [Azure IoT Edge által támogatott rendszerek](support.md)című témakörben talál tájékoztatást.
>
> Ha készen áll saját Windows-eszközének konfigurálására az IoT Edge-hez, beleértve az IoT Core-t futtató eszközöket is, kövesse [az Azure IoT Edge futtatókörnyezet telepítése Windows rendszeren](how-to-install-iot-edge-windows.md)című témakör lépéseit.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Indítsa el a rövid útmutatót egy IoT-központ azure CLI-vel való létrehozásával.

![Diagram – IoT-központ létrehozása a felhőben](./media/quickstart/create-iot-hub.png)

Ehhez a rövid útmutatóhoz az IoT Hub ingyenes csomagja is elegendő. Ha korábban már használta az IoT Hubot, és már létrehozott egy ingyenes központot, használhatja azt is. Mindegyik előfizetés csak egy ingyenes IoT-központtal rendelkezhet.

A következő kód létrehoz egy ingyenes **F1** hubot az erőforráscsoportban. `IoTEdgeResources` Cserélje `{hub_name}` le az IoT hub egyedi nevét.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Ha hibaüzenetet kap, mert az előfizetése már tartalmaz egy ingyenes központot, akkor módosítsa az SKU-t **S1**-re. Ha hibaüzenetet kap, hogy az IoT Hub neve nem érhető el, az azt jelenti, hogy valaki más már rendelkezik ezzel a névvel rendelkező központtal. Próbáljon ki egy új nevet.

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Regisztráljon egy IoT Edge-eszközt az újonnan létrehozott IoT Hubon.
![Diagram – Egy Eszköz regisztrálása IoT Hub-identitással](./media/quickstart/register-device.png)

Hozzon létre egy eszközidentitást a szimulált eszközhöz, hogy az kommunikálhasson az IoT Hubbal. Az eszközidentitás a felhőben található, és egy egyedi eszközkapcsolati sztringgel társíthat fizikai eszközt az eszközidentitáshoz.

Mivel az IoT Edge-eszközök viselkednek, és a tipikus IoT-eszközöktől eltérően kezelhetők, `--edge-enabled` deklarálják ezt az identitást a jelzővel rendelkező IoT Edge-eszközszámára.

1. Az Azure Cloud Shellben a következő paranccsal hozza létre a **myEdgeDevice** nevű eszközt a központjában.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   Ha hibát kap az iothubowner házirend-kulcsok, győződjön meg arról, hogy a felhőbeli rendszerhéj az azure-iot bővítmény legújabb verzióját futtatja.

2. Kérje le az eszköze kapcsolati sztringjét, amely összeköti a fizikai eszközt az IoT Hubban tárolt identitással.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Másolja a kulcs `connectionString` értékét a JSON kimenetből, és mentse azt. Ez az érték az eszköz kapcsolati sztringje. Erre a kapcsolati sztringre a következő szakaszban, az IoT Edge-futtatókörnyezet konfigurálásához lesz szükség.

   ![Kapcsolati karakterlánc beolvasása a CLI-kimenetből](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Az IoT Edge-futtatókörnyezet telepítése és elindítása

Telepítse az Azure IoT Edge-futtatókörnyezetet az IoT Edge-eszközön, és a konfigurálást eszközkapcsolati sztring használatával végezze el.
![Diagram – A futásidő indítása az eszközön](./media/quickstart/start-runtime.png)

Az IoT Edge-futtatókörnyezet minden IoT Edge-eszközön üzembe van helyezve. Három összetevőből áll. Az **IoT Edge biztonsági démon** elindul minden alkalommal, amikor egy IoT Edge-eszköz elindul, és az IoT Edge-ügynök indításával az eszközt. Az **IoT Edge-ügynök** kezeli a modulok üzembe helyezését és figyelését az IoT Edge-eszközön, beleértve az IoT Edge hubot is. Az **IoT Edge hub** kezeli a modulok közötti kommunikáció az IoT Edge-eszközön, valamint az eszköz és az IoT Hub között.

A telepítési parancsfájl egy Moby nevű tárolómotort is tartalmaz, amely az IoT Edge-eszközön lévő tárolórendszerképeket kezeli.

A futtatókörnyezet telepítése során a rendszer rá fog kérdezni az eszközkapcsolati sztringre. Ez esetben az Azure CLI-ről lekért sztringet használja. Ez a sztring társítja a fizikai eszközt az IoT Edge-eszköz identitásához az Azure-ban.

### <a name="connect-to-your-iot-edge-device"></a>Csatlakozás az IoT Edge-eszközhöz

Az ebben a szakaszban található lépések mind az IoT Edge-eszközön történik, ezért most távoli asztalon keresztül szeretne csatlakozni a virtuális géphez.

### <a name="install-and-configure-the-iot-edge-service"></a>Az IoT Edge szolgáltatás telepítése és konfigurálása

Az IoT Edge-futtatókörnyezet letöltése és telepítése a PowerShell használatával történik. Az eszköz konfigurálásához az IoT Hubról lekért eszközkapcsolati sztringet használja.

1. Ha még nem tette meg, kövesse az [új Azure IoT Edge-eszköz regisztrálása](how-to-register-device.md) az eszköz regisztrálásához és az eszköz kapcsolati karakterláncának beolvasásához című lépéseit.

2. A virtuális gépen futtassa a PowerShellt rendszergazdaként.

   >[!NOTE]
   >Az IoT Edge telepítéséhez használja a PowerShell AMD64-munkamenetét, ne a PowerShellt (x86). Ha nem biztos abban, hogy melyik munkamenettípust használja, futtassa a következő parancsot:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. A **Deploy-IoTEdge** parancs ellenőrzi, hogy a Windows-gép egy támogatott verzió, bekapcsolja a tárolók funkciót, letölti a Moby futásidejű, majd letölti az IoT Edge futásidejű.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Előfordulhat, hogy a készülék automatikusan újraindul. Ha a Deploy-IoTEdge parancs újraindítja a kérdést, tegye meg most.

5. Futtassa újra a PowerShellt rendszergazdaként.

6. Az **Initialize-IoTEdge** parancs konfigurálja az IoT Edge futásidejét a számítógépen. A parancs alapértelmezés szerint manuálisan létesít és windowsos tárolókat.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Ha a rendszer a **DeviceConnectionString** megadására kéri, akkor illessze be az előző szakaszból átmásolt sztringet. A kapcsolati sztring elé és után ne írjon idézőjeleket.

### <a name="view-the-iot-edge-runtime-status"></a>Az IoT Edge-futtatókörnyezet állapotának megtekintése

Ellenőrizze, hogy a futtatókörnyezet megfelelően lett-e telepítve és konfigurálva.

1. Ellenőrizze az IoT Edge-szolgáltatás állapotát.

   ```powershell
   Get-Service iotedge
   ```

2. Ha hibaelhárításra van szükség, kérje le a szolgáltatás naplóit.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Tekintse meg az IoT Edge-eszközön futó összes modult. Mivel első alkalommal indította el ezt a szolgáltatást, csak az **edgeAgent** modulnak szabad futnia. Az edgeAgent modul alapértelmezés szerint fut, és segít telepíteni és elindítani az eszközre telepített további modulokat.

    ```powershell
    iotedge list
    ```

   ![Egy modul megtekintése az eszközön](./media/quickstart/iotedge-list-1.png)

A telepítés befejeződése és az IoT Edge-ügynök modul indítása néhány percet is igénybe vehet.

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Az Azure IoT Edge-eszköz kezelése a felhőből egy modul üzembe helyezéséhez, amely telemetriai adatokat küld az IoT Hub.
![Diagram - modul telepítése a felhőből az eszközre](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban regisztrált egy IoT Edge-eszközt, és telepítette az IoT Edge futásidejű rajta. Ezután az Azure Portalon üzembe helyezhet egy IoT Edge-modult az eszközön való futtatáshoz anélkül, hogy módosítania kellene magát az eszközt.

Ebben az esetben a leadott modul mintaadatokat hoz létre, amelyek tesztelésre használhatók. A szimulált hőmérséklet-érzékelő modul környezeti adatokat hoz létre, amelyeket később tesztelésre használhat. A szimulált érzékelő figyeli mind a gépet, mind a gép körüli környezetet. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalmat, a gép hőmérsékletét, a nyomást és egy timestampet. Az IoT Edge oktatóanyagok a modul által létrehozott adatokat használják elemzési tesztadatokként.

Győződjön meg arról, hogy a felhőből üzembe helyezett modul fut az IoT Edge-eszközön.

```powershell
iotedge list
```

   ![Három modul megtekintése az eszközön](./media/quickstart/iotedge-list-2.png)

Tekintse meg a hőmérséklet-érzékelő modulból a felhőbe küldött üzeneteket.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Az IoT Edge-parancsok a kis- és nagybetűket megkülönböztetők, amikor a modulnevekre hivatkoznak.

   ![A modulból származó adatok megtekintése](./media/quickstart/iotedge-logs.png)

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
> [IoT Edge-modulok fejlesztésének megkezdése Windows-eszközökhöz](tutorial-develop-for-windows.md)
