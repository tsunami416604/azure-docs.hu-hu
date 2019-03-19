---
title: Észlelheti a rendellenességeket a peremhálózaton egy megoldás-oktatóanyag – Azure |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan figyelheti az IoT Edge-eszközök használata a távoli figyelési megoldásgyorsító.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58166752"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Oktatóanyag: A távoli figyelési megoldásgyorsító anomáliadetektálásra a peremhálózaton

Ebben az oktatóanyagban a távoli figyelési megoldás IoT Edge-eszköz által észlelt rendellenességek válaszolni fog konfigurálni. IoT Edge-eszközök segítségével dolgozza fel a telemetriát a peremhálózaton, a megoldás küldött telemetriai adatok mennyiségének csökkentése és a gyorsabb válaszokat események eszközök engedélyezése. Edge feldolgozás előnyeivel kapcsolatos további tudnivalókért lásd: [Mi az Azure IoT Edge](../iot-edge/about-iot-edge.md).

Edge feldolgozás olyan távoli megfigyelés bevezetni, az ebben az oktatóanyagban egy szimulált olaj szivattyú jack eszközt használ. Az olaj szivattyú jack egy Contoso nevű vállalat által felügyelt, és a távoli figyelési megoldásgyorsító csatlakozik. Az olaj szivattyú jack érzékelő hőmérséklet és nyomás mérjük. Contoso operátorok tudja, hogy a hőmérséklet rendellenes növekedése lassítani az olaj szivattyú jack okozhat. Contoso operátorok nem kell az eszköz hőmérséklete figyelésére, amikor a normál tartományon belül van.

A Contoso biztosítani szeretné egy intelligens edge-modul üzembe helyezése az olaj szivattyú jack, amely észleli a rendellenességeket hőmérséklet. Edge-modul riasztásokat küld a távoli figyelési megoldás. Egy riasztás fogadásakor, a Contoso operátor karbantartási technikus is csatolva. Contoso is beállíthat automatikus műveletek, például az e-mail küldése a futtatását, amikor a megoldás egy riasztást kap.

Az oktatóanyagban használt forgatókönyv az alábbi ábrán látható a legfontosabb összetevők:

![Áttekintés](media/iot-accelerators-remote-monitoring-edge/overview.png)

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Az IoT Edge-eszközöket a megoldás hozzáadása
> * Az Edge jegyzékfájl létrehozása
> * A jegyzékfájl importálja, amely meghatározza a modulokat az eszközön futtatni csomagként
> * A csomag az IoT Edge-eszköz üzembe helyezése
> * Az eszközről riasztások megtekintése

Az IoT Edge-eszközön:

* A futtatókörnyezet fogadja a csomagot, és telepíti a modulokat.
* A stream analytics-modul hőmérséklet rendellenességeket a szivattyú észleli, és elküldi a parancsok a probléma megoldásához.
* A stream analytics-modul a megoldásgyorsító szűrt adatokat továbbítja.

Ebben az oktatóanyagban egy Linux rendszerű virtuális gép, IoT Edge-eszköz. Az olaj szivattyú jack eszköz szimulálása az edge-modul is telepíti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>IoT Edge-eszköz hozzáadása

IoT Edge-eszköz hozzáadása a távoli figyelési megoldásgyorsító két lépésből áll. Ez a szakasz bemutatja, hogyan használhatja:

* Az IoT Edge-eszköz hozzáadása a **Device Explorer** oldal a távoli figyelési webes felhasználói felületen.
* Az IoT Edge-futtatókörnyezet telepítése egy Linux rendszerű virtuális gép (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>IoT Edge-eszköz hozzáadása a megoldáshoz

A távoli figyelési megoldásgyorsító IoT Edge-eszköz hozzáadásához lépjen a **Device Explorer** a webes felhasználói felületen oldalra, majd kattintson a **+ új eszköz**.

Az a **új eszköz** panel, válassza a **IoT Edge-eszköz** , és adja meg **olaj-szivattyú** az eszköz azonosítójával. Hagyhatja az alapértelmezett értékeket a többi beállítás esetében. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![IoT Edge-eszköz hozzáadása](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Jegyezze fel az eszköz kapcsolati karakterláncának, szüksége lesz rá az oktatóanyag a következő szakaszban.

A távoli figyelési megoldásgyorsító az IoT hub-eszközök regisztrálásakor az megjelenik a **Device Explorer** oldal a webes felhasználói felületen:

[![Új IoT Edge-eszköz](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Hogy egyszerűbb legyen a megoldásban az IoT Edge-eszközök kezeléséhez, hozzon létre egy eszközcsoportot, és adja hozzá az IoT Edge-eszköz:

1. Válassza ki a **olaj-szivattyú** eszközre a listában lévő a **Device Explorer** lapon, majd kattintson **feladatok**.

1. Hozzon létre egy feladatot, adja hozzá a **IsEdge** címkét az eszközön, a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ----- |
    | Feladat     | Címkék  |
    | Feladat neve | AddEdgeTag |
    | Kulcs     | IsOilPump |
    | Érték   | I     |
    | Typo    | Szöveg  |

    [![Címke hozzáadása](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Kattintson a **alkalmazása**, majd **Bezárás**.

1. Az a **Device Explorer** kattintson **eszközcsoportok kezelése**.

1. Kattintson a **új csoport létrehozása a eszköz**. Hozzon létre egy új eszköz csoportot a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név)    | OilPumps |
    | Mező   | Tags.IsOilPump |
    | Művelet | = Egyenlő |
    | Érték    | I |
    | Typo     | Szöveg |

    [![Eszközcsoport létrehozása](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Kattintson a **Save** (Mentés) gombra.

Már létezik az Ön IoT Edge-eszköz a **OilPumps** csoport.

### <a name="install-the-edge-runtime"></a>Telepítse az Edge-futtatókörnyezet

Edge-eszköz kell telepíteni az Edge-futtatókörnyezet szükséges. Ebben az oktatóanyagban egy Azure Linux VM, a forgatókönyv teszteléséhez az Edge-futtatókörnyezet telepítenie. Az alábbi lépéseket az Azure cloud shell használata a telepítés, és konfigurálja a virtuális Gépet:

1. Linux virtuális gép létrehozása az Azure-ban, futtassa a következő parancsokat. Hely közelében, itt is használhatja:

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

1. Az eszköz kapcsolati karakterláncának konfigurálása az Edge-futtatókörnyezet, futtassa a következő parancsot, korábban végzett jegyezze fel, az eszköz kapcsolati karakterláncának használata:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Mindenképp adja hozzá a kapcsolati karakterláncot az idézőjelek belül.

Sikeresen telepítve és konfigurálva, az IoT Edge-futtatókörnyezet egy Linux rendszerű eszközön. Az oktatóanyag későbbi részében használhatja a távoli figyelési megoldás üzembe helyezése IoT Edge-modulok ezen az eszközön.

## <a name="create-an-edge-manifest"></a>Az Edge jegyzékfájl létrehozása

Az olaj jack szivattyú eszköz szimulálása, a következő modulok hozzáadása az Edge-eszközt kell:

* Hőmérséklet-szimuláció modul.
* Az Azure Stream Analytics rendellenesség-észlelést.

A következő lépések bemutatják, hogyan hozhat létre egy Edge manifest nasazení, amely tartalmazza ezeket a modulokat. Az oktatóanyag későbbi részében a jegyzékfájlt a távoli figyelési megoldásgyorsító a csomag importálása.

### <a name="create-the-azure-stream-analytics-job"></a>Az Azure Stream Analytics-feladat létrehozása

A Stream Analytics-feladat a portálra csomagolására, mint az Edge-modul előtt határozhatja meg.

1. Az Azure Portalon hozzon létre egy Azure storage-fiókot az alapértelmezett beállításokkal a **IoTEdgeDevices** erőforráscsoportot. Jegyezze fel a választott név.

1. Az Azure Portalon hozzon létre egy **Stream Analytics-feladat** a a **IoTEdgeDevices** erőforráscsoportot. A következő konfigurációs értékeket használja:

    | Beállítás | Érték |
    | ------ | ----- |
    | Feladat neve | EdgeDeviceJob |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | IoTEdgeDevices |
    | Hely | USA keleti régiója |
    | Üzemeltetési környezet | Edge |
    | Streamelési egységek | 1 |

1. Nyissa meg a **EdgeDeviceJob** Stream Analytics feladat látható a portálon, majd bemenetei között, és adjon hozzá egy **Edge hubot** nevű bemeneti adatfolyam **telemetriai**.

1. Az a **EdgeDeviceJob** Stream Analytics-feladatot a Portalon, kattintson a **kimenetek** , és adja hozzá egy **Edge hubot** nevű kimeneti **kimeneti**.

1. Az a **EdgeDeviceJob** Stream Analytics-feladatot a Portalon, kattintson a **kimenetek** , és vegyen fel egy második **Edge hubot** nevű kimeneti **riasztás**.

1. Az a **EdgeDeviceJob** Stream Analytics-feladatot a Portalon, kattintson a **lekérdezés** , és adja hozzá a következő **kiválasztása** utasítást:

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

1. Az a **EdgeDeviceJob** Stream Analytics-feladatot a Portalon, kattintson a **Tárfiók-beállítások**. Az adott tárfiók hozzáadása a **IoTEdgeDevices** erőforráscsoporthoz tartozik, mint ez a szakasz elején. Hozzon létre egy új tárolót nevű **edgeconfig**.

Az alábbi képernyőfelvételen a mentett Stream Analytics-feladatot:

[![Stream Analytics-feladat](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Stream Analytics-feladat futtatása az edge-eszköz most már meghatározta. A feladat átlaghőmérséklet keresztül egy 5 másodperces ablak számítja ki. A feladat is küld riasztást, ha az átlaghőmérséklet 3 másodperces ablakban túllépik a 400-as.

### <a name="create-the-iot-edge-deployment"></a>Az IoT Edge üzemelő példány létrehozása

Ezután hozzon létre egy IoT Edge manifest nasazení, amely meghatározza a modulok futtatását az Edge-eszközön. A következő szakaszban a jegyzékfájlt a távoli figyelési megoldásban csomag importálása.

1. Az Azure Portalon lépjen a távoli figyelési megoldásban az IoT hubnak. Az IoT hub az erőforráscsoportot, amelynek a neve megegyezik a távoli figyelési megoldás találhatja meg.

1. Kattintson az IoT hub **IoT Edge** a a **automatikus kezelés** szakaszban. Kattintson a **adjon hozzá egy IoT Edge-példányban**.

1. Az a **központi telepítés létrehozása > név és címke** lap, adja meg a nevét **olaj-szivattyú-eszköz**. Kattintson a **tovább**.

1. Az a **központi telepítés létrehozása > modulok hozzáadása** kattintson **+ Hozzáadás**. Válasszon **IoT Edge-modul**.

1. Az a **IoT Edge-egyéni modulok** panelen, adja meg, **hőmérséklet-érzékelő** -nevet és **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** , a lemezkép URI-ja. Kattintson a **Save** (Mentés) gombra.

1. Az a **központi telepítés létrehozása > modulok hozzáadása** kattintson **+ Hozzáadás** hozzáadni egy második modul. Válasszon **Azure Stream Analytics modul**.

1. Az a **üzembe helyezési él** panel, válassza ki az előfizetését és a **EdgeDeviceJob** az előző szakaszban létrehozott. Kattintson a **Save** (Mentés) gombra.

1. Az a **központi telepítés létrehozása > modulok hozzáadása** kattintson **tovább**.

1. Az a **központi telepítés létrehozása > útvonalak megadása** lap, adja hozzá a következő kódot:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Ez a kód a Stream Analytics modul kimenetét a megfelelő helyre irányítja.

    Kattintson a **tovább**.

1. Az a **központi telepítés létrehozása > metrikákat adja meg** kattintson **tovább**.

1. Az a **központi telepítés létrehozása > Céleszközök** lap, adja meg a 10-es, a prioritás. Kattintson a **tovább**.

1. A a **központi telepítés létrehozása > tekintse át a telepítési** kattintson **küldés**:

    [![Tekintse át a központi telepítés](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. A fő **IoT Edge** kattintson **IoT Edge-telepítések**. Látható **olaj-szivattyú-eszköz** központi telepítések listájában.

1. Kattintson a **olaj-szivattyú-eszköz** üzembe helyezés, majd kattintson **töltse le az IoT Edge-jegyzékfájl**. Mentse a fájlt az **olaj-szivattyú-device.json** egy megfelelő helyre a helyi gépen. Ez az oktatóanyag a következő szakaszban fájl szükséges.

Sikeresen létrehozott egy IoT Edge-jegyzékfájl a távoli figyelési megoldás csomag importálásához. Általában a fejlesztők és hoz létre az IoT Edge modulok Alkalmazásjegyzék-fájl.

## <a name="import-a-package"></a>Csomag importálása

Ebben a szakaszban az Edge-jegyzékfájl a távoli figyelési megoldásban csomag importálása.

1. A távoli figyelési webes felhasználói felületen, keresse meg a **csomagok** lapot, és kattintson **+ új csomag**:

    [![Új csomag](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. A a **új csomag** panel, válassza a **Edge Manifest** a csomag típusaként kattintson **Tallózás** található a **olaj-szivattyú-device.json** fájlok az a helyi gépen, és kattintson **feltöltése**:

    [![Csomag feltöltése.](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A csomagok listájában mostantól tartalmazza a **olaj-szivattyú-device.json** csomagot.

A következő szakaszban hozzon létre egy telepítés alkalmazza a csomagot az Edge-eszköz.

## <a name="deploy-a-package"></a>Telepítsen központilag egy csomagot

Most már készen áll a csomag telepítése az eszközre.

1. A távoli figyelési webes felhasználói felületen, keresse meg a **központi telepítések** lapot, és kattintson **+ új üzembe helyezési**:

    [![Új központi telepítés](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Az a **új üzembe helyezési** panelen, a központi telepítés létrehozásához a következő beállításokat:

    | Beállítás | Érték |
    | ------ | ----- |
    | Name (Név)   | OilPumpDevices |
    | Csomag típusa | Edge-jegyzékfájl |
    | Csomag | oil-pump-device.json |
    | Eszközcsoport | OilPumps |
    | Prioritás | 10 |

    [![Üzemelő példány létrehozása](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

A csomag telepítése az eszközre és elindítani az eszköz küldenek telemetriai adatokat néhány percet várnia kell.

[![Aktív központi telepítés](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

A **központi telepítések** lapon látható a következő metrikákat:

* **Megcélzott** az eszköz csoportban található eszközök számát jeleníti meg.
* **Alkalmazott** kellett volna a alkalmazni telepítési tartalmakhoz eszközök számát jeleníti meg.
* **Sikeres** az üzembe helyezés sikeressége az IoT Edge-ügyfél futtatókörnyezet reporting az Edge-eszközök számát jeleníti meg.
* **Nem sikerült** a jelentési hiba az IoT Edge-ügyfél futtatókörnyezet a központi telepítésben lévő peremhálózati eszközök számát jeleníti meg.

## <a name="monitor-the-device"></a>Az eszköz figyelése

A távoli figyelési webes felhasználói felületen az olaj szivattyú eszközről hőmérsékleti telemetria tekintheti meg:

1. Keresse meg a **Device Explorer** lapon, és válassza ki az olaj szivattyú eszközt.
1. Az a **Telemetriai** szakaszában a **eszközadatok** panelen, kattintson a **hőmérséklet**:

    [![Telemetria megtekintése](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Láthatja, hogyan nő a hőmérséklet, amíg elér egy küszöbértéket. A Stream Analytics Edge-modul felismeri, amikor a hőmérséklet eléri ezt a küszöbértéket, és a egy parancsot küld az eszköz hőmérséklete azonnal csökkentése érdekében. Az összes ezt a feldolgozást történik az eszközön, anélkül, hogy a felhőbe való kommunikáció során.

Ha szeretne értesíteni operátorok, a küszöbérték elérésekor, a távoli figyelési webes felhasználói felületen hozhat létre egy szabályt:

1. Keresse meg a **szabályok** lapot, és kattintson **+ új szabály**.
1. Hozzon létre egy új szabályt a következő beállításokkal:

    | Beállítás | Érték |
    | ------ | ----- |
    | Szabály neve | Olaj szivattyú hőmérséklet |
    | Leírás | Olaj szivattyú hőmérséklet meghaladja a 300 |
    | Eszközcsoport | OilPumps |
    | Számítás | Azonnali |
    | Mező | hőmérséklet |
    | Művelet | > |
    | Érték | 300 |
    | Súlyossági szint | Információ |

    [![Szabály létrehozása](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

1. Keresse meg a **irányítópult** lapot. Riasztást jeleníti meg a a **riasztások** panelen, amikor a hőmérséklet a **olaj-szivattyú** eszköz több mint 300 kerül.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan adhat hozzá, és a távoli figyelési megoldásgyorsító az IoT Edge-eszköz konfigurálása. Ha további információra van szüksége a távoli figyelési megoldásban az IoT Edge-csomagokkal kapcsolatban, tekintse meg a következő útmutató:

> [!div class="nextstepaction"]
> [A távoli figyelési megoldásgyorsító egy IoT Edge-csomag importálása](iot-accelerators-remote-monitoring-import-edge-package.md)

Az IoT Edge-futtatókörnyezet telepítésével kapcsolatos további tudnivalókért lásd: [(x64) linuxon az Azure IoT Edge-futtatókörnyezet telepítéséhez](../iot-edge/how-to-install-iot-edge-linux.md).

További tudnivalók az Azure Stream Analytics peremhálózati eszközökön, lásd: [üzembe helyezése az Azure Stream Analytics az IoT Edge-modul is](../iot-edge/tutorial-deploy-stream-analytics.md).
