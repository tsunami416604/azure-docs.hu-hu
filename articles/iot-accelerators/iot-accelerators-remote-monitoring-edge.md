---
title: A megoldási oktatóanyag szélén észlelt rendellenességek észlelése – Azure | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti IoT Edge-eszközeit a távoli figyelési megoldás gyorsító használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "66117511"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Oktatóanyag: rendellenességek észlelése a szélén a távoli figyelési megoldás gyorsítása révén

Ebben az oktatóanyagban a távoli figyelési megoldást úgy konfigurálja, hogy válaszoljon IoT Edge eszköz által észlelt rendellenességekre. IoT Edge eszközök lehetővé teszik a telemetria feldolgozását a megoldásba küldött telemetria csökkentése és az eszközökön zajló események gyorsabb reagálásának érdekében. Ha többet szeretne megtudni a peremhálózat-feldolgozás előnyeiről, tekintse meg a [Mi az Azure IoT Edge](../iot-edge/about-iot-edge.md).

A távoli figyeléssel végzett Edge-feldolgozás bevezetéséhez ez az oktatóanyag egy szimulált olajszivattyú Jack-eszközt használ. Ezt az olajszivattyú-csatlakozót egy contoso nevű szervezet felügyeli, és a távoli figyelési megoldáshoz kapcsolódik. Az olaj-szivattyú alsó mérési hőmérsékletének és nyomásának érzékelői. A contoso üzemeltetői tisztában vannak azzal, hogy a hőmérséklet rendellenes növekedése miatt az olaj-szivattyú Jack lelassul. A contoso üzemeltetői nem kell figyelniük az eszköz hőmérsékletét, ha az a normál tartományon belül van.

A contoso egy intelligens Edge-modult szeretne üzembe helyezni az olaj-szivattyú aljzatába, amely észleli a hőmérsékleti rendellenességeket. Egy másik peremhálózati modul riasztásokat küld a távoli figyelési megoldásnak. Ha riasztás érkezik, a contoso-operátor elküldheti a karbantartási technikust. A contoso egy automatizált műveletet is beállíthat, például egy e-mailt küld, amely akkor fut le, amikor a megoldás riasztást kap.

A következő ábrán az oktatóanyag forgatókönyvének főbb összetevői láthatók:

![Áttekintés](media/iot-accelerators-remote-monitoring-edge/overview.png)

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * IoT Edge eszköz hozzáadása a megoldáshoz
> * Edge-jegyzékfájl létrehozása
> * A jegyzékfájl importálása csomagként, amely meghatározza az eszközön futtatandó modulokat.
> * A csomag üzembe helyezése a IoT Edge eszközön
> * Riasztások megtekintése az eszközről

A IoT Edge eszközön:

* A futtatókörnyezet fogadja a csomagot, és telepíti a modulokat.
* A stream Analytics modul észleli a szivattyúk hőmérsékleti rendellenességeit, és a parancsokat küldi el a probléma megoldásához.
* A stream Analytics modul szűrt adatátvitelt továbbít a megoldás-gyorsító felé.

Ez az oktatóanyag egy linuxos virtuális gépet használ IoT Edge eszközként. Egy peremhálózati modult is telepít, amely szimulálja az olaj-szivattyú Jack-eszközét.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>IoT Edge-eszköz hozzáadása

Két lépéssel adhat hozzá IoT Edge eszközt a távoli figyelési megoldás-gyorsító szolgáltatáshoz. Ebből a szakaszból megtudhatja, hogyan használhatja a következőt:

* Vegyen fel egy IoT Edge eszközt a távoli figyelés webes felhasználói felületének **Device Explorer** lapján.
* Telepítse a IoT Edge futtatókörnyezetet egy Linux rendszerű virtuális gépen (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>IoT Edge eszköz hozzáadása a megoldáshoz

Ha IoT Edge eszközt szeretne hozzáadni a távoli figyelési megoldás-gyorsító eszközhöz, navigáljon a webes felületen található **Device Explorer** lapra, és kattintson az **+ új eszköz**elemre.

Az **új eszköz** panelen válassza a **IoT Edge eszköz** lehetőséget, és adja meg az **olaj-szivattyút** az eszköz azonosítójaként. A többi beállítás alapértelmezett értékeit is meghagyhatja. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![IoT Edge eszköz hozzáadása](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Jegyezze fel az eszköz-csatlakoztatási karakterláncot, amire szüksége lesz az oktatóanyag következő szakaszában.

Ha a távoli figyelési megoldás-gyorsító eszközben regisztrálja az eszközt az IoT hub-ban, az a webes felhasználói felületen a **Device Explorer** oldalon jelenik meg:

[![Új IoT Edge eszköz](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Annak érdekében, hogy könnyebben kezelje a megoldás IoT Edge eszközeit, hozzon létre egy erőforráscsoportot, és adja hozzá a IoT Edge eszközt:

1. Válassza ki az **olaj-szivattyú** eszközt a **Device Explorer** lapon lévő listában, majd kattintson a **feladatok**lehetőségre.

1. Hozzon létre egy feladatot a **IsEdge** címke az eszközhöz való hozzáadásához a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ----- |
    | Feladat     | Címkék  |
    | Feladat neve | AddEdgeTag |
    | Kulcs     | IsOilPump |
    | Érték   | Y     |
    | Típus    | Szöveg  |

    [![Címke hozzáadása](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Kattintson az **alkalmaz**, majd a **Bezárás**elemre.

1. A **Device Explorer** lapon kattintson az **erőforráscsoportok kezelése**elemre.

1. Kattintson az **új eszközcsoport létrehozása**elemre. Hozzon létre egy új erőforráscsoportot a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név    | OilPumps |
    | Mező   | Címkék. IsOilPump |
    | Operátor | = Egyenlő |
    | Érték    | Y |
    | Típus     | Szöveg |

    [![Eszközcsoport létrehozása](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Kattintson a **Mentés** gombra.

IoT Edge eszköz most már a **OilPumps** csoportban található.

### <a name="install-the-edge-runtime"></a>A peremhálózati futtatókörnyezet telepítése

Az Edge-eszköz telepítéséhez a peremhálózati futtatókörnyezetnek kell futnia. Ebben az oktatóanyagban egy Azure linuxos virtuális gépen telepíti a peremhálózati futtatókörnyezetet a forgatókönyv teszteléséhez. A következő lépések az Azure Cloud shellt használják a virtuális gép telepítése és konfigurálása során:

1. Linuxos virtuális gép létrehozásához az Azure-ban futtassa a következő parancsokat. A következő helyekhez lehet közel lenni:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Ha az Edge-futtatókörnyezetet az eszköz kapcsolódási karakterláncával szeretné konfigurálni, futtassa az alábbi parancsot a korábban jegyzett eszköz-kapcsolódási karakterlánc használatával:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Ügyeljen arra, hogy dupla idézőjelek között szerepeljen a kapcsolódási karakterlánc.

Most telepítette és konfigurálta a IoT Edge futtatókörnyezetet egy Linux-eszközön. Az oktatóanyag későbbi részében a távoli figyelési megoldás használatával IoT Edge modulokat telepíthet erre az eszközre.

## <a name="create-an-edge-manifest"></a>Edge-jegyzékfájl létrehozása

Az olaj Jack szivattyú eszközének szimulálásához hozzá kell adnia a következő modulokat a peremhálózati eszközhöz:

* Hőmérséklet-szimulációs modul.
* Azure Stream Analytics anomáliák észlelése.

A következő lépések bemutatják, hogyan hozhat létre olyan peremhálózati telepítési jegyzéket, amely tartalmazza ezeket a modulokat. Az oktatóanyag későbbi részében ezt a jegyzékfájlt csomagként importálja a távoli figyelési megoldás gyorsító csomagjába.

### <a name="create-the-azure-stream-analytics-job"></a>A Azure Stream Analytics-feladatok létrehozása

A Stream Analytics feladatot a portálon kell megadnia, mielőtt peremhálózati modulként becsomagolja.

1. A Azure Portal hozzon létre egy Azure Storage-fiókot az **IoTEdgeDevices** erőforráscsoport alapértelmezett beállításaival. Jegyezze fel a kiválasztott nevet.

1. A Azure Portal hozzon létre egy **stream Analytics feladatot** a **IoTEdgeDevices** erőforráscsoporthoz. A következő konfigurációs értékek használata:

    | Beállítás | Érték |
    | ------ | ----- |
    | Feladat neve | EdgeDeviceJob |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | IoTEdgeDevices |
    | Hely | USA keleti régiója |
    | Üzemeltetési környezet | Edge |
    | Streamelési egységek | 1 |

1. Nyissa meg a **EdgeDeviceJob** stream Analytics feladatot a portálon, kattintson a bemenetek elemre, és vegyen fel egy **telemetria**nevű **Edge hub** stream-bemenetet.

1. A portál **EdgeDeviceJob** stream Analyticsján kattintson a **kimenetek** elemre, és adjon hozzá egy **kimenet**nevű **peremhálózati hub** -kimenetet.

1. A portál **EdgeDeviceJob** stream Analyticsján kattintson a **kimenetek** elemre, és adjon hozzá egy, a **riasztás**nevű második **peremhálózati hub** -kimenetet.

1. A portál **EdgeDeviceJob** stream Analyticsjában kattintson a **lekérdezés** elemre, és adja hozzá a következő **Select** utasítást:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. A **EdgeDeviceJob** stream Analytics a portálon kattintson a **Storage Fiókbeállítások**lehetőségre. Adja hozzá a **IoTEdgeDevices** erőforráscsoporthoz hozzáadott Storage-fiókot a szakasz elejéhez. Hozzon létre egy új, **edgeconfig**nevű tárolót.

A következő képernyőfelvétel a mentett Stream Analytics feladatot mutatja:

[![Stream Analytics feladatok](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Már definiált egy Stream Analytics feladatot a peremhálózati eszközön való futtatáshoz. A feladatban egy 5 másodperces időszak átlagos hőmérséklete számítható ki. A feladatot a rendszer riasztást küld, ha egy 3 másodperces időszak átlagos hőmérséklete 400-nál nagyobb.

### <a name="create-the-iot-edge-deployment"></a>A IoT Edge központi telepítésének létrehozása

Ezután létre kell hoznia egy IoT Edge telepítési jegyzéket, amely meghatározza a peremhálózati eszközön futtatandó modulokat. A következő szakaszban importálja ezt a jegyzékfájlt csomagként a távoli figyelési megoldásba.

1. A Azure Portal navigáljon a IoT hubhoz a távoli figyelési megoldásban. Az IoT hub a távoli figyelési megoldás nevével megegyező nevű erőforráscsoporthoz is megkereshető.

1. Az IoT központban kattintson a **IoT Edge** elemre az **automatikus eszközkezelés** szakaszban. Kattintson  **a IoT Edge központi telepítés hozzáadása**lehetőségre.

1. A **központi telepítés létrehozása > név és címke** lapon adja meg az **Oil-Pump-Device**nevet. Kattintson a **Tovább** gombra.

1. A **központi telepítés létrehozása > modulok hozzáadása** lapon kattintson a **+ Hozzáadás**gombra. Válassza **IoT Edge modult**.

1. A **IoT Edge egyéni modulok** panelen írja be a **hőmérséklet-érzékelő** nevet és a **asaedgedockerhubtest/ASA-Edge-test-Module: Sensor-ad-Linux-amd64** értéket a rendszerkép URI-jaként. Kattintson a **Mentés** gombra.

1. A **központi telepítés létrehozása > modulok hozzáadása** lapon kattintson a **+ Hozzáadás** elemre egy második modul hozzáadásához. Válassza **Azure stream Analytics modult**.

1. Az **Edge Deployment (peremhálózati telepítés** ) panelen válassza ki az előfizetését és az előző szakaszban létrehozott **EdgeDeviceJob** . Kattintson a **Mentés** gombra.

1. A **központi telepítés létrehozása > modulok hozzáadása** lapon kattintson a **tovább**gombra.

1. A **központi telepítés létrehozása > útvonalak megadása** lapon adja hozzá a következő kódot:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Ez a kód a Stream Analytics modul kimenetét a megfelelő helyekre irányítja.

    Kattintson a **Tovább** gombra.

1. A **központi telepítés létrehozása > a metrikák meghatározása** lapon kattintson a **tovább**gombra.

1. A **központi telepítési > cél eszközök** lapon adja meg a 10-es értéket prioritásként. Kattintson a **Tovább** gombra.

1. A központi **telepítés létrehozása > áttekintés** lapon kattintson a **Küldés**gombra:

    [![Központi telepítés áttekintése](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. A fő **IoT Edge** lapon kattintson a **IoT Edge központi telepítések**elemre. A központi telepítések listájában látható az **olaj-szivattyú – eszköz** .

1. Kattintson az **olaj-szivattyú-eszköz** telepítés elemre, majd kattintson a **IoT Edge jegyzékfájl letöltése**elemre. Mentse a fájlt **oil-pump-device.jsa** megfelelő helyre a helyi gépen. Ezt a fájlt az oktatóanyag következő szakaszában kell megadnia.

Ezzel létrehozott egy IoT Edge jegyzékfájlt, amely csomagként importálható a távoli figyelési megoldásba. A fejlesztők általában létrehozzák a IoT Edge modulokat és a jegyzékfájlt.

## <a name="import-a-package"></a>Csomag importálása

Ebben a szakaszban a peremhálózati jegyzékfájlt csomagként importálja a távoli figyelési megoldásban.

1. A távoli figyelés webes KEZELŐFELÜLETén navigáljon a **csomagok** lapra, és kattintson az **+ új csomag**lehetőségre:

    [![Új csomag](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Az **új csomag** panelen válassza a **Edge manifest** lehetőséget csomag típusaként, kattintson a **Tallózás** gombra a helyi gépen található fájl **oil-pump-device.js** megkereséséhez, majd kattintson a **feltöltés**gombra:

    [![Csomag feltöltése](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A csomagok listája mostantól tartalmazza a csomag **oil-pump-device.js** .

A következő szakaszban olyan központi telepítést hoz létre, amely alkalmazza a csomagot a peremhálózati eszközre.

## <a name="deploy-a-package"></a>Csomag üzembe helyezése

Most már készen áll a csomag üzembe helyezésére az eszközön.

1. A távoli figyelés webes KEZELŐFELÜLETén navigáljon a **központi telepítések** lapra, és kattintson az **+ új központi telepítés**lehetőségre:

    [![Új központi telepítés](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Az **új központi telepítés** panelen hozzon létre egy központi telepítést a következő beállításokkal:

    | Beállítás | Érték |
    | ------ | ----- |
    | Név   | OilPumpDevices |
    | Csomag típusa | Edge-jegyzékfájl |
    | Csomag | oil-pump-device.jsbekapcsolva |
    | Eszközcsoport | OilPumps |
    | Prioritás | 10 |

    [![Üzemelő példány létrehozása](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

Várjon néhány percet, amíg a csomag üzembe lesz helyezve az eszközön, és a telemetria az eszközről áramlanak.

[![Aktív üzembe helyezés](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

A **központi telepítések** lap a következő metrikákat jeleníti meg:

* A **cél** az eszközcsoport eszközeinek számát mutatja.
* **Alkalmazva** érték a központi telepítési tartalommal rendelkező eszközök számát mutatja.
* A **sikeres** művelet azt mutatja, hogy hány peremhálózati eszköz szerepel a központi telepítési jelentésben a IoT Edge ügyfél futtatókörnyezetében.
* A **nem sikerült** megjelenni az üzemelő példányok az IoT Edge ügyfél futtatókörnyezetből való központi telepítésének hibáját jelző peremhálózati eszközök számát.

## <a name="monitor-the-device"></a>Az eszköz figyelése

A távoli monitorozás webes FELÜLETén megtekintheti az olajszivattyú-eszköz telemetria hőmérsékletét:

1. Navigáljon a **Device Explorer** lapra, és válassza ki az olajszivattyú eszközét.
1. Az **eszköz részletei** panel **telemetria** szakaszában kattintson a **hőmérséklet**:

    [![Telemetria megtekintése](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Láthatja, hogyan emelkedik a hőmérséklet, amíg el nem éri a küszöbértéket. Az Stream Analytics Edge-modul észleli, ha a hőmérséklet eléri ezt a küszöbértéket, és egy parancsot küld az eszköznek, hogy azonnal csökkentse a hőmérsékletet. Az összes feldolgozás a felhővel való kommunikáció nélkül történik az eszközön.

Ha a küszöbérték elérésekor értesíteni szeretné a kezelőket, létrehozhat egy szabályt a távoli figyelés webes felhasználói felületén:

1. Navigáljon a **szabályok** lapra, és kattintson az **+ új szabály**elemre.
1. Hozzon létre egy új szabályt a következő beállításokkal:

    | Beállítás | Érték |
    | ------ | ----- |
    | Szabály neve | Olaj-szivattyú hőmérséklete |
    | Description | Az olaj-szivattyú hőmérséklete túllépte a 300 |
    | Eszközcsoport | OilPumps |
    | Számítás | Azonnali |
    | Mező | hőmérséklet |
    | Operátor | > |
    | Érték | 300 |
    | Súlyossági szint | Információ |

    [![Szabály létrehozása](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

1. Navigáljon az **irányítópult** lapra. Egy riasztás a **riasztások** panelen jelenik meg, ha az **olaj-szivattyú** eszköz hőmérséklete 300-ra csökken.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és konfigurálhat egy IoT Edge eszközt a távoli figyelési megoldás gyorsító eszközében. Ha többet szeretne megtudni a IoT Edge-csomagok a távoli figyelési megoldásban való használatáról, tekintse meg a következő útmutatókat:

> [!div class="nextstepaction"]
> [IoT Edge csomag importálása a távoli figyelési megoldás-gyorsító eszközbe](iot-accelerators-remote-monitoring-import-edge-package.md)

Az IoT Edge futtatókörnyezet telepítésével kapcsolatos további tudnivalókért tekintse [meg a Azure IoT Edge Runtime telepítése Linux rendszeren (x64)](../iot-edge/how-to-install-iot-edge-linux.md)című témakört.

Az Edge-eszközök Azure Stream Analyticsával kapcsolatos további tudnivalókért tekintse meg a [Azure stream Analytics üzembe helyezése IoT Edge modulként](../iot-edge/tutorial-deploy-stream-analytics.md)című témakört.
