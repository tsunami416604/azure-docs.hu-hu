---
title: A megoldásoktató anyag szélén lévő rendellenességek észlelése - Azure | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan figyelheti az IoT Edge-eszközöket a távoli figyelési megoldásgyorsító használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "66117511"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Oktatóanyag: A távoli figyelési megoldásgyorsítóval a szélén lévő rendellenességek észlelése

Ebben az oktatóanyagban konfigurálja a távoli figyelési megoldást az IoT Edge-eszköz által észlelt anomáliák megválaszolására. Az IoT Edge-eszközök lehetővé teszik a telemetriai adatok feldolgozását a peremhálózaton, hogy csökkentse a megoldásnak küldött telemetriai adatok mennyiségét, és gyorsabb válaszokat adjon az eseményekre az eszközökön. További információ a peremhálózati feldolgozás előnyeiről: [Mi az Azure IoT Edge.](../iot-edge/about-iot-edge.md)

A peremhálózati feldolgozás távoli figyeléssel történő bevezetéséhez ez az oktatóanyag szimulált olajszivattyú-emelő eszközt használ. Ezt az olajszivattyú-csatlakozót egy Contoso nevű szervezet kezeli, és csatlakozik a távfigyelési megoldásgyorsítóhoz. Érzékelők az olajszivattyú jack intézkedés hőmérséklet és nyomás. A Contoso üzemeltetői tudják, hogy a rendellenes hőmérséklet-emelkedés az olajszivattyú csatlakozójának lassulását okozhatja. A Contoso operátorainak nem kell figyelniük az eszköz hőmérsékletét, ha az a normál tartományon belül van.

A Contoso egy intelligens élmodult szeretne telepíteni az olajszivattyú csatlakozójára, amely észleli a hőmérsékleti anomáliákat. Egy másik peremhálózati modul riasztásokat küld a távoli figyelési megoldásnak. Riasztás érkezésekor a Contoso operátor kiküldhet egy karbantartó technikust. A Contoso konfigurálhat egy automatikus műveletet is, például egy e-mailt, amely akkor fut, amikor a megoldás riasztást kap.

Az alábbi ábra az oktatóanyag-forgatókönyv legfontosabb összetevőit mutatja be:

![Áttekintés](media/iot-accelerators-remote-monitoring-edge/overview.png)

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * IoT Edge-eszköz hozzáadása a megoldáshoz
> * Szegélyjegyzék létrehozása
> * A jegyzékfájl importálása olyan csomagként, amely meghatározza az eszközön futtatandó modulokat
> * A csomag üzembe helyezése az IoT Edge-eszközre
> * Riasztások megtekintése az eszközről

Az IoT Edge-eszközön:

* A futásidejű megkapja a csomagot, és telepíti a modulokat.
* Az adatfolyam-elemzési modul észleli a hőmérsékleti anomáliákat a szivattyúban, és parancsokat küld a probléma megoldásához.
* Az adatfolyam-elemzési modul továbbítja a szűrt adatokat a megoldásgyorsítónak.

Ez az oktatóanyag egy Linux virtuális gépet használ IoT Edge-eszközként. Azt is telepíteni egy él modulszimulálni az olajszivattyú jack eszköz.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>IoT Edge-eszköz hozzáadása

Két lépésből áll, hogy egy IoT Edge-eszközt adjon hozzá a távoli figyelési megoldás gyorsítóhoz. Ez a rész a következőkhasználatát mutatja be:

* Adjon hozzá egy IoT Edge-eszközt az **Eszközkezelő** lapon a távoli figyelés webes felhasználói felületén.
* Telepítse az IoT Edge futásidejű egy Linux virtuális gép (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>IoT Edge-eszköz hozzáadása a megoldáshoz

Ha IoT Edge-eszközt szeretne hozzáadni a távfigyelési megoldás gyorsítójához, keresse meg az **Eszközkezelő** lapot a webes felhasználói felületen, és kattintson a **+ Új eszköz gombra.**

Az **Új eszköz** panelen válassza az **IoT Edge eszközt,** és adja meg az **olajszivattyút** eszközazonosítóként. A többi beállítás alapértelmezett értékeit is meghagyhatja. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![IoT Edge-eszköz hozzáadása](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Jegyezze fel az eszköz kapcsolati karakterláncát, szüksége van rá az oktatóanyag következő szakaszában.

Amikor regisztrál egy eszközt az IoT hubbal a távfigyelési megoldás gyorsítójában, az megjelenik az **Eszközkezelő** lapon a webes felhasználói felületen:

[![Új IoT Edge-eszköz](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Az IoT Edge-eszközök kezelése a megoldásban egyszerűbben, hozzon létre egy eszközcsoportot, és adja hozzá az IoT Edge-eszközt:

1. Jelölje ki az **olajszivattyú-eszközt** az **Eszközkezelő** lap listájában, majd kattintson a **Feladatok**gombra.

1. Hozzon létre egy feladatot az **IsEdge** címke eszközhöz való hozzáadásához a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ----- |
    | Feladat     | Címkék  |
    | Feladat neve | AddEdgeTag |
    | Kulcs     | IsOilPump |
    | Érték   | I     |
    | Típus    | Szöveg  |

    [![Címke hozzáadása](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Kattintson **az Alkalmaz**gombra, majd **a Bezárás gombra.**

1. Az **Eszközkezelő** lapon kattintson az **Eszközcsoportok kezelése gombra.**

1. Kattintson **az Új eszközcsoport létrehozása gombra.** Hozzon létre egy új eszközcsoportot a következő beállításokkal:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név    | Olajszivattyúk |
    | Mező   | Címkék.IsOilPump |
    | Művelet | = Egyenlő |
    | Érték    | I |
    | Típus     | Szöveg |

    [![Eszközcsoport létrehozása](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Kattintson a **Mentés** gombra.

Az IoT Edge eszköz mostantól az **OilPumps** csoporttagja.

### <a name="install-the-edge-runtime"></a>Az Edge futási idő telepítése

Az Edge-eszköz telepítéséhez telepíteni kell az Edge-futási időt. Ebben az oktatóanyagban telepíti az Edge futásidejű egy Azure Linux virtuális gép a forgatókönyv teszteléséhez. A következő lépések az Azure felhőbeli rendszerhéjat használják a virtuális gép telepítéséhez és konfigurálásához:

1. Linuxos virtuális gép létrehozásához az Azure-ban futtassa a következő parancsokat. A tartózkodási helyhez közeli helyet is használhat:

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

1. Ha az Edge futási időt az eszköz kapcsolati karakterláncával szeretné konfigurálni, futtassa a következő parancsot a korábban feljegyzett eszközkapcsolati karakterlánc használatával:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Ügyeljen arra, hogy a kapcsolati karakterláncot idézőjelek közé ágyazza.

Most telepítette és konfigurálta az IoT Edge futásidejű egy Linux-eszközön. Az oktatóanyag későbbi részében a távoli figyelési megoldás használatával telepíti az IoT Edge-modulokat erre az eszközre.

## <a name="create-an-edge-manifest"></a>Szegélyjegyzék létrehozása

Az olajemelő szivattyú eszköz ének szimulálásához a következő modulokat kell hozzáadnia az Edge eszközhöz:

* Hőmérséklet-szimulációs modul.
* Az Azure Stream Analytics anomáliadetektálása.

A következő lépések bemutatják, hogyan hozhat létre egy Edge központi telepítési jegyzékfájl, amely tartalmazza ezeket a modulokat. Az oktatóanyag későbbi részében ezt a jegyzékfájlt csomagként importálja a Távoli figyelési megoldásgyorsítóban.

### <a name="create-the-azure-stream-analytics-job"></a>Az Azure Stream Analytics-feladat létrehozása

A Stream Analytics-feladatot a portálon határozza meg, mielőtt edge-modulként csomagolna.

1. Az Azure Portalon hozzon létre egy Azure storage-fiókot az **IoTEdgeDevices** erőforráscsoport alapértelmezett beállításaival. Jegyezze fel a választott nevet.

1. Az Azure Portalon hozzon létre egy **Stream Analytics-feladatot** az **IoTEdgeDevices** erőforráscsoportban. Használja a következő konfigurációs értékeket:

    | Beállítás | Érték |
    | ------ | ----- |
    | Feladat neve | EdgeDeviceJob |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | IoTEdgeDevices |
    | Hely | USA keleti régiója |
    | Üzemeltetési környezet | Edge |
    | Streamelési egységek | 1 |

1. Nyissa meg az **EdgeDeviceJob** Stream Analytics feladatot a portálon, kattintson a bemenetek elemre, és adjon hozzá egy **Telemetria**nevű Edge Hub-adatfolyam-bemenetet. **Edge Hub**

1. Az **EdgeDeviceJob** Stream Analytics feladatban a portálon kattintson a **Kimenetek** elemre, és adjon hozzá egy **kimenetnek** nevezett Edge Hub **kimenetet.**

1. Az **EdgeDeviceJob** Stream Analytics feladata a portálon, kattintson **a kimenetek** és adjunk hozzá egy második **Edge Hub** kimenet nevű **riasztást.**

1. Az **EdgeDeviceJob** Stream Analytics feladatban kattintson a **Lekérdezés** elemre, és adja hozzá a következő **select** utasítást:

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

1. Az **EdgeDeviceJob** Stream Analytics feladatban a portálon kattintson a **Tárfiók beállításai**elemre. Adja hozzá az **IoTEdgeDevices** erőforráscsoporthoz hozzáadott tárfiókot a szakasz kezdeteként. Hozzon létre egy új tárolót **edgeconfig**néven.

A következő képernyőképen látható a mentett Stream Analytics-feladat:

[![Stream Analytics-feladat](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Most már definiált egy Stream Analytics-feladatot a peremhálózati eszközön való futtatáshoz. A feladat kiszámítja az átlaghőmérsékletet egy 5 másodperces ablakban. A feladat akkor is riasztást küld, ha az átlagos hőmérséklet egy 3 másodperces ablakban 400 fölé megy.

### <a name="create-the-iot-edge-deployment"></a>Az IoT Edge-telepítés létrehozása

Ezután hozzon létre egy IoT Edge-telepítési jegyzékfájl, amely meghatározza a modulokfuttatásához az Edge-eszközön. A következő szakaszban importálja ezt a jegyzékfájlt csomagként a távoli figyelési megoldásban.

1. Az Azure Portalon keresse meg az IoT-központot a távoli figyelési megoldásban. Az IoT hub az erőforráscsoportban, amely ugyanazt a nevet, mint a távoli figyelési megoldás.

1. Az IoT-központban kattintson az **IoT Edge** az **Automatikus eszközkezelés** szakaszban. Kattintson **az IoT Edge-telepítés hozzáadása gombra.**

1. A **Telepítés létrehozása > név és címke** lapon adja meg az **olajszivattyú-eszköz**nevet. Kattintson a **Tovább** gombra.

1. A **Telepítés létrehozása > Modulok hozzáadása** lapon kattintson a + **Hozzáadás gombra.** Válassza **az IoT edge module lehetőséget.**

1. Az **IoT Edge egyéni modulok** panelen adja meg **a temperatureSensor** nevet, és **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64,** mint a rendszerkép URI. Kattintson a **Mentés** gombra.

1. A **Telepítés létrehozása > Modulok hozzáadása** lapon kattintson a + **Hozzáadás** gombra egy második modul hozzáadásához. Válassza az **Azure Stream Analytics-modul lehetőséget.**

1. Az **Edge központi telepítési** panelen válassza ki az előfizetést és az **edgeDeviceJob** az előző szakaszban létrehozott. Kattintson a **Mentés** gombra.

1. A **Telepítés létrehozása > Modulok hozzáadása** lapon kattintson a **Tovább**gombra.

1. A **Központi telepítés létrehozása > Útvonalak megadása** lapon adja hozzá a következő kódot:

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

1. A **Telepítés létrehozása > Metrikák megadása** lapon kattintson a **Tovább**gombra.

1. A **Központi telepítés létrehozása > céleszközök** lapon adja meg prioritásként a 10 értéket. Kattintson a **Tovább** gombra.

1. A **Központi telepítés létrehozása > ellenőrzés e-ellenőrzés** lapon kattintson a **Küldés**gombra:

    [![Telepítés áttekintése](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. A fő **IoT Edge** lapon kattintson az **IoT Edge-telepítések**elemre. Láthatjuk **olaj-szivattyú-eszköz** a telepítési listában.

1. Kattintson az **olajszivattyú-eszköz** központi telepítésére, majd az **IoT Edge-jegyzékfájl letöltése parancsra.** Mentse a fájlt **olaj-szivattyú-device.json** egy megfelelő helyre a helyi gépen. Szüksége van erre a fájlra az oktatóanyag következő részében.

Most létrehozott egy IoT Edge-jegyzékfájlt a távoli figyelési megoldáscsomagként történő importálásához. Általában egy fejlesztő létrehozza az IoT Edge-modulokat és a jegyzékfájl.

## <a name="import-a-package"></a>Csomag importálása

Ebben a szakaszban importálja az Edge jegyzékfájl csomagként a távoli figyelési megoldás.

1. A Távoli figyelés webes felhasználói felületén keresse meg a **Csomagok** lapot, és kattintson **a + Új csomag gombra:**

    [![Új csomag](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Az **Új csomag** panelen válassza az **Edge Manifest** lehetőséget a csomagtípusként, kattintson a **Tallózás** gombra az **olajszivattyú-device.json** fájl helyi számítógépen való megkereséséhez, majd kattintson a **Feltöltés gombra:**

    [![Csomag feltöltése](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    A csomagok listája most már tartalmazza az **olaj-szivattyú-device.json** csomagot.

A következő szakaszban hozzon létre egy központi telepítést, amely alkalmazza a csomagot az Edge-eszközre.

## <a name="deploy-a-package"></a>Csomag telepítése

Most már készen áll a csomag üzembe helyezésére az eszközre.

1. A Távoli figyelés webes felhasználói felületén keresse meg a **Központi telepítések** lapot, és kattintson a **+ Új telepítés**elemre:

    [![Új telepítés](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Az **Új telepítés** panelen hozzon létre egy központi telepítést a következő beállításokkal:

    | Beállítás | Érték |
    | ------ | ----- |
    | Név   | Olajszivattyúeszközök |
    | Csomag típusa | Szegélyjegyzék |
    | Csomag | olaj-szivattyú-device.json |
    | Eszközcsoport | Olajszivattyúk |
    | Prioritás | 10 |

    [![Üzemelő példány létrehozása](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

Meg kell várnia néhány percet, amíg a csomag üzembe helyezése az eszközre, és a telemetriai adatok az eszközről való áramlás megkezdéséhez.

[![Telepítés aktív](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

A **Központi telepítések** lap a következő mutatókat jeleníti meg:

* **A Célzott** eszközcsoportban lévő eszközök számát jeleníti meg.
* **Az alkalmazott** a központi telepítési tartalom malmára vitt eszközök számát mutatja.
* A sikeres azt **mutatja,** hogy az IoT Edge-ügyfél futásidejű telepítési jelentéskészítési sikeres ségében az Edge-eszközök száma látható.
* **Sikertelen az** IoT Edge-ügyfél futásidejű, a központi telepítési jelentési hiba az Edge-eszközök számát mutatja.

## <a name="monitor-the-device"></a>Az eszköz figyelése

A hőmérséklet-telemetriaaz olajszivattyú-eszközről a Távoli figyelés webes felhasználói felületén tekinthető meg:

1. Nyissa meg az **Eszközkezelő** lapot, és válassza ki az olajszivattyú-eszközt.
1. Az **Eszköz részletei** panel **Telemetriai** szakaszában kattintson a **Hőmérséklet**:

    [![Telemetria megtekintése](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Láthatja, hogyan emelkedik a hőmérséklet, amíg el nem éri a küszöbértéket. A Stream Analytics Edge modul észleli, ha a hőmérséklet eléri ezt a küszöbértéket, és küld egy parancsot az eszköznek, hogy azonnal csökkentse a hőmérsékletet. Ez a feldolgozás az eszközön történik anélkül, hogy kommunikálna a felhővel.

Ha értesíteni szeretné az operátorokat a küszöbérték elérésekor, létrehozhat egy szabályt a távoli figyelés webes felhasználói felületén:

1. Nyissa meg a **Szabályok** lapot, és kattintson **a + Új szabály gombra.**
1. Hozzon létre egy új szabályt a következő beállításokkal:

    | Beállítás | Érték |
    | ------ | ----- |
    | Szabály neve | Olajszivattyú hőmérséklete |
    | Leírás | Az olajszivattyú hőmérséklete meghaladta a 300-at |
    | Eszközcsoport | Olajszivattyúk |
    | Számítás | Azonnali |
    | Mező | hőmérséklet |
    | Művelet | > |
    | Érték | 300 |
    | Súlyossági szint | Információ |

    [![Szabály létrehozása](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Kattintson az **Alkalmaz** gombra.

1. Nyissa meg az **Irányítópult** lapot. Egy riasztás azt mutatja, a **riasztások panelen,** amikor a hőmérséklet az **olaj-szivattyú** eszköz megy több mint 300.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és konfigurálhat egy IoT Edge-eszközt a távoli figyelési megoldásgyorsítóban. Ha többet szeretne tudni az IoT Edge-csomagok távfigyelési megoldásban való kezeléséről, olvassa el az alábbi útmutatót:

> [!div class="nextstepaction"]
> [IoT Edge-csomag importálása a távoli figyelési megoldás gyorsítójába](iot-accelerators-remote-monitoring-import-edge-package.md)

Az IoT Edge-futásidő telepítéséről az [Azure IoT Edge futásidő telepítése Linuxon (x64) című](../iot-edge/how-to-install-iot-edge-linux.md)témakörben olvashat bővebben.

Ha többet szeretne megtudni az Azure Stream Analytics edge-eszközökön, [tekintse meg az Azure Stream Analytics üzembe helyezése IoT Edge-modulként.](../iot-edge/tutorial-deploy-stream-analytics.md)
