---
title: Gyors útmutató Azure IoT Edge-eszköz létrehozásához Windows rendszeren | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Edge eszközt, majd távolról is üzembe helyezheti az előre elkészített kódot a Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f3af2b7839465f886d1edba01eb9988419761dac
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630979"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Rövid útmutató: az első IoT Edge modul üzembe helyezése Windows-eszközön (előzetes verzió)

Próbálja ki Azure IoT Edge ebben a rövid útmutatóban, ha a Windows IoT Edge eszközön egy Linux rendszerbe helyez üzembe egy tároló kódot. IoT Edge lehetővé teszi a kódok távoli kezelését az eszközökön, így több számítási feladatot is elküldhet a peremhálózat számára. Ebben a rövid útmutatóban azt javasoljuk, hogy használja a saját eszközét, hogy megtekintse, milyen egyszerűen használható Azure IoT Edge Linux rendszerhez Windows rendszeren.

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

* Hozzon létre egy IoT hubot.
* IoT Edge-eszköz regisztrálása az IoT Hubon
* Telepítse és indítsa el a Linux rendszerhez készült IoT Edge a Windows Runtime eszközön.
* Modul távoli üzembe helyezése egy IoT Edge eszközön és telemetria küldése.

![Diagram – az eszköz és a felhő gyors üzembe helyezési architektúrája](./media/quickstart/install-edge-full.png)

Ez a rövid útmutató bemutatja, hogyan állíthatja be a Linux rendszerhez készült Azure IoT Edge a Windows-eszközökön. Ezután telepítsen egy modult a Azure Portal az eszközre. Az ebben a rövid útmutatóban használt modul egy szimulált érzékelő, amely hőmérséklet-, páratartalom-és adatterhelési adattípust generál. A többi Azure IoT Edge oktatóanyag az itt végzett munkára építve olyan további modulok üzembe helyezésével végezhető el, amelyek elemzik az üzleti információk szimulált adatait.

Ha nem rendelkezik aktív Azure-előfizetéssel, kezdetnek hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).

>[!NOTE]
>A Windows rendszerhez készült Linux IoT Edge [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Felhőerőforrások:

* Egy erőforráscsoport a rövid útmutató során létrehozott összes erőforrás kezelésére.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-eszköz:

* Az eszköznek Windows rendszerű SZÁMÍTÓGÉPnek vagy kiszolgálónak, 1809-es vagy újabb verziónak kell lennie
* Legalább 4 GB memória, ajánlott 8 GB memória
* 10 GB szabad lemezterület

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

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

Telepítse a Linux rendszerhez készült IoT Edge a Windowson az eszközön, és konfigurálja azt egy eszköz-csatlakoztatási karakterlánccal.

![Diagram – a IoT Edge futtatókörnyezet elindítása az eszközön](./media/quickstart/start-runtime.png)

1. [Töltse le a Windows felügyeleti központot](https://aka.ms/WACDownloadEFLOW).
2. Az eszközön a Windows felügyeleti központ beállításához kövesse a telepítővarázsló utasításait.
3. Ha a Windows felügyeleti központban van, a képernyő jobb felső részén kattintson a **Beállítások fogaskerék ikonra** .  
4. A beállítások menü átjáró területén válassza a **bővítmények** lehetőséget.
5. Az **elérhető bővítmények** listájából válassza a **Azure IoT Edge**
6. A bővítmény **telepítése**

7. Miután telepítette a bővítményt, navigáljon a fő irányítópult lapra a képernyő bal felső sarkában a **Windows felügyeleti központ** lehetőség kiválasztásával.

8. Ekkor megjelenik a helyi gazdagép-kapcsolatok, amely a Windows felügyeleti központot futtató számítógépet jelöli.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Képernyőfelvétel – Windows-rendszergazda kezdőlapja":::

9. Válassza a **Hozzáadás** elemet.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Képernyőfelvétel – Windows-rendszergazda Kezdőlap hozzáadása gomb":::

10. Keresse meg a Azure IoT Edge csempét, és válassza az **új létrehozása** lehetőséget. Ekkor elindul a telepítővarázsló.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Képernyőfelvétel – Azure IoT Edge Linux rendszeren a Windows csempén":::

11. A végfelhasználói licencszerződés elfogadásához folytassa a telepítővarázsló lépéseit, és válassza a **Next (tovább** ) lehetőséget.

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Képernyőfelvétel – varázsló – üdvözli":::

12. Válassza ki a **választható diagnosztikai adatait** , hogy kibővített diagnosztikai adatait biztosítson, amelyek segítségével a Microsoft figyeli és karbantarthatja a szolgáltatás minőségét, és kattintson a **Tovább gombra: üzembe helyezés**

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Képernyőfelvétel – diagnosztikai adatszolgáltatások":::

13. A **cél eszköz kiválasztása** képernyőn válassza ki a kívánt céleszköz annak ellenőrzéséhez, hogy az megfelel-e a minimális követelményeknek. Ebben a rövid útmutatóban IoT Edget telepítünk a helyi eszközön, ezért válassza a localhost-kapcsolatokat. Miután megerősítette **, válassza a Tovább gombot** a folytatáshoz.

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Képernyőfelvétel – céleszköz kiválasztása":::

14. Fogadja el az alapértelmezett beállításokat a **Next (tovább**) lehetőség választásával.

15. A telepítési képernyő a csomag letöltésének folyamatát, a csomag telepítését, a gazdagép konfigurálását és a Linux rendszerű virtuális gép végleges beállítását mutatja be.  A sikeres üzembe helyezés a következőképpen fog megjelenni:

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Képernyőfelvétel – a varázsló sikeres üzembe helyezése":::

16. Kattintson a Next (tovább) gombra **: kapcsolódjon** a folytatáshoz, és folytassa az utolsó lépéssel, hogy kiépítse Azure IoT Edge eszközét az IoT hub-példány eszköz-azonosítójával.

17. Másolja az eszközről az Azure-IoT Hub a kapcsolatok karakterláncát, és illessze be az eszköz-kapcsolatok karakterlánc mezőjébe. Ezután válassza **a kiépítés lehetőséget a kiválasztott módszerrel**.

    > [!NOTE]
    > A kapcsolódási karakterlánc lekéréséhez tekintse meg az előző szakasz 3. lépését, [regisztráljon IoT Edge eszközt](#register-an-iot-edge-device).

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Képernyőfelvétel – varázsló kiépítés":::

18. A kiépítés befejezése után válassza a **Befejezés** lehetőséget, és térjen vissza a Windows felügyeleti központ kezdőképernyőn. Most már láthatja, hogy az eszköz IoT Edge eszközként jelenik meg.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Képernyőfelvétel – Windows felügyeleti központ Azure IoT Edge eszköz":::

19. Válassza ki Azure IoT Edge eszközét az irányítópult megtekintéséhez. Látnia kell, hogy az eszközön lévő munkaterhelések üzembe helyezése az Azure IoT Hub. A **IoT Edge modulnak** egy, a-t, a **edgeAgent**-t és a **IoT Edge-állapotot** futtató modult **kell megjelenítenie.**

Ezzel konfigurálta az IoT Edge-eszközt. Az eszköz készen áll a felhőben üzembe helyezett modulok futtatására.

## <a name="deploy-a-module"></a>Modul üzembe helyezése

Felügyelheti Azure IoT Edge eszközét a felhőből egy olyan modul üzembe helyezéséhez, amely telemetria adatokat küld IoT Hub.

![Diagram – modul üzembe helyezése a felhőből az eszközre](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Ebben a rövid útmutatóban létrehozott egy új IoT Edge-eszközt, és telepítette rajta az IoT Edge-futtatókörnyezetet. Ezt követően a Azure Portal használatával telepítheti az eszközön a IoT Edge modult anélkül, hogy magát az eszközt kellene módosítania.

Ebben az esetben a lekért modul olyan minta környezeti adatfeldolgozást hoz létre, amelyet később tesztelésre használhat. A szimulált érzékelő a gépet és a környezetet is figyeli a gép körül. Ez az érzékelő lehet például egy kiszolgálóteremben, egy üzemben vagy akár szélturbinán. Az üzenet tartalmazza a környezeti hőmérsékletet, a páratartalmat, a gép hőmérsékletét, a nyomást és egy timestampet. A IoT Edge oktatóanyagok a modul által létrehozott adatelemzési adatként használják.

Ellenőrizze, hogy a felhőben üzembe helyezett modul fut-e a IoT Edge eszközön a Windows felügyeleti központban a parancssori felületre való navigálással.

1. Kapcsolódás az újonnan létrehozott IoT Edge eszközhöz

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Képernyőfelvétel – eszköz csatlakoztatása":::

2. Az **Áttekintés** oldalon látni fogja a **IoT Edge modul listáját** , és **IoT Edge állapotot** , ahol megtekintheti a különböző telepített modulokat, valamint az eszköz állapotát.  

3. Az **eszközök** területen válassza a **parancs rendszerhéj** elemet. A parancshéj egy PowerShell-terminál, amely automatikusan SSH-t (Secure Shell) használ a Azure IoT Edge-eszköz Linux rendszerű virtuális géphez való kapcsolódásra a Windows-számítógépen.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Képernyőfelvétel – Command Shell":::

4. Az eszközön található három modul ellenőrzéséhez futtassa a következő **bash-parancsot**:

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Képernyőfelvétel – parancssori rendszerhéj-lista":::

5. Megtekintheti a hőmérséklet-érzékelő modulból a felhőbe küldött üzeneteket.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge parancsok a kis-és nagybetűk megkülönböztetésére szolgálnak a modulok neveire való hivatkozáskor.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Képernyőfelvétel – hőmérséklet-érzékelő":::

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

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>A Linux rendszerhez készült Azure IoT Edge tiszta eltávolítása Windows rendszeren

A Windows rendszerű számítógépeken a Windows felügyeleti központban található irányítópult-bővítmény segítségével távolíthatja el a Linux rendszerhez IoT Edge készült Azure IoT Edge.

1. Kapcsolódjon a Linux rendszerhez készült Azure IoT Edge Windows-eszközök kapcsolatáról a Windows felügyeleti központban. Az Azure Dashboard Tool bővítmény betöltődik.
2. Válassza az **Eltávolítás** lehetőséget. A Windows rendszerű Linux rendszeren való Azure IoT Edge eltávolítását követően a Windows felügyeleti központ megnyitja a kezdőlapot, és eltávolítja a listából a Azure IoT Edge eszköz kapcsolódási bejegyzését.

A Azure IoT Edge a Windows rendszerből való eltávolításának egy másik módja, ha a  >  IoT Edge eszközön a Start **Settings**  >  **apps**  >  **Azure IoT Edge**  >  **Eltávolítás** elemre lép. Ezzel eltávolítja Azure IoT Edge a IoT Edge eszközről, de a Windows felügyeleti központban marad. A Windows felügyeleti központ a beállítások menüből is eltávolítható.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Edge eszközt, és a Azure IoT Edge Cloud Interface használatával helyezi üzembe a kódot az eszközön. Most már van egy teszteszköze, amely nyers adatokat állít elő a környezetéről.

A következő lépés a helyi fejlesztési környezet beállítása, amelynek segítségével megkezdheti az üzleti logikát futtató IoT Edge-modulok létrehozását.

> [!div class="nextstepaction"]
> [IoT Edge-modulok fejlesztésének megkezdése](tutorial-develop-for-linux.md)
