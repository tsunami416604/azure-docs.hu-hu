---
title: Eszköz szimulálása a távoli figyelési megoldás – Azure |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan a készülékszimulátort a távoli figyelési megoldásgyorsító használandó.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/15/2018
ms.topic: conceptual
ms.openlocfilehash: 8d8835bd97b489a730a040e86748c668963c7196
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187723"
---
# <a name="create-a-new-simulated-device"></a>Új szimulált eszköz létrehozása

Ez az oktatóanyag bemutatja, hogyan szabhatja testre az eszköz szimulátor mikroszolgáltatás a távoli figyelési megoldásgyorsító. Ez az oktatóanyag két forgatókönyvet használja az Contoso IoT alkalmazás megjelenítése az készülékszimulátort képességeit.

Az alábbi videó az eszköz szimulátor mikroszolgáltatás testreszabása a lehetőségeket mutatja be:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

Az első esetben a Contoso biztosítani szeretné egy új intelligens villanykörte eszköz teszteléséhez. A teszteket hajt végre, létrehozhat új szimulált eszköz a következő jellemzőkkel:

*Tulajdonságok*

| Name (Név)                     | Értékek                      |
| ------------------------ | --------------------------- |
| Szín                    | Fehér, a vörös, kék            |
| Brightness               | 0 – 100                    |
| Becsült hátralévő élettartamának | Visszaszámlálás 10 000 óra |

*Telemetria*

Az alábbi táblázat mutatja az adatok a villanykörte a felhőbe, mint egy adatfolyam-jelentések:

| Name (Név)   | Értékek      |
| ------ | ----------- |
| status | "on" "off" |
| Hőmérséklet | F fok |
| online | IGAZ, hamis |

> [!NOTE]
> A **online** telemetriaérték minden szimulált típusok megadása kötelező.

*Módszerek*

Az alábbi táblázat a műveletek az új eszköz támogatja:

| Name (Név)        |
| ----------- |
| Váltás   |
| Kikapcsolás  |

*Kezdeti állapot*

Az alábbi táblázat az eszköz kezdeti állapotát jeleníti meg:

| Name (Név)                     | Értékek |
| ------------------------ | -------|
| Kezdeti színe            | Fehér  |
| Kezdeti fényereje       | 75     |
| Kezdeti fennmaradó élettartama   | 10,000 |
| Kezdeti telemetriai állapota | "a"   |
| Kezdeti telemetriai hőmérséklet | 200   |

A második esetben hozzáadhat egy új telemetriatípus contoso meglévő **hűtő** eszköz.

Az oktatóanyag bemutatja, hogyan használható a távoli figyelési megoldásgyorsító a készülékszimulátort:

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Hozzon létre egy új eszköztípus
> * Egyéni eszköz viselkedésének szimulálása
> * Egy új eszköztípus hozzáadása az irányítópulthoz
> * Egyéni telemetriát küld egy meglévő eszköz típusa

A következő videó bemutatja egy valódi, és szimulált eszközök csatlakoztatása a távoli figyelési megoldás bemutatója:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az alábbiak szükségesek:

* A távoli figyelési megoldás az Azure-előfizetésben üzembe helyezett példánya. Ha még nem telepítette a távoli figyelési megoldás, még, hajtsa végre a [a távoli figyelési megoldásgyorsító üzembe helyezése](../iot-accelerators/quickstart-remote-monitoring-deploy.md) oktatóanyag.

* Visual Studio 2017. Ha még nincs telepítve a Visual Studio 2017, akkor letöltheti az ingyenes [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) edition.

* [Cloud Explorer Pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) Visual Studio-bővítmény.

* A fiók [Docker Hub](https://hub.docker.com/). Iratkozzon fel az ingyenes kezdéshez.

* [A Git](https://git-scm.com/downloads) az asztali gépekre telepíthető.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

Hajtsa végre a következő feladatokat, hogyan adhat hozzá a távoli figyelési megoldás új szimulált eszköz a fejlesztőkörnyezet előkészítése:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>A megoldás virtuális géphez SSH-hozzáférés konfigurálása az Azure-ban

A távoli figyelési megoldást a következő létrehozásakor [www.azureiotsolutions.com](https://www.azureiotsolutions.com), választotta, a megoldás nevét. A megoldás neve lesz az Azure-erőforráscsoportot, amely tartalmazza a különféle üzembe helyezett erőforrások, amelyek a megoldás nevét. A következő parancsokat használja egy nevű erőforráscsoportot **Contoso-01**, kell cserélni, **Contoso-01** az erőforráscsoport nevét.

A következő parancsokat használja a `az` parancsot a [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Az Azure CLI 2.0 telepítése a fejlesztői gépen, vagy használja a [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) a a [az Azure portal](http://portal.azure.com). Az Azure CLI 2.0 használatával a Cloud shellben előre telepítve.

1. Ellenőrizze a távoli figyelési erőforrásokat tartalmazó erőforráscsoport nevét, futtassa a következő parancsot:

    ```sh
    az group list | grep "name"
    ```

    Ez a parancs felsorolja az előfizetés összes erőforráscsoportjában. A listában szerepelnie kell egy erőforráscsoportot a neve megegyezik a távoli figyelési megoldás.

1. Ahhoz, hogy az erőforrás-csoport az alapértelmezett csoport további parancsokhoz, futtassa a következő parancsot, az erőforráscsoport neve helyére használatával **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. A listában az erőforráscsoportban lévő erőforrásokat, futtassa a következő parancsot:

    ```sh
    az resource list -o table
    ```

    Jegyezze fel a virtuális gép és a hálózati biztonsági csoport nevét. A későbbi lépésekben használhatja ezeket az értékeket.

1. A virtuális gép SSH-hozzáférés engedélyezéséhez futtassa a következő parancsot az előző lépésben, a hálózati biztonsági csoport neve:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    A hálózat bejövő szabályok listájának megtekintéséhez futtassa a következő parancsot:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. Virtuális gép jelszómódosítás ismeri a jelszóra, futtassa a következő parancsot. Használja a korábban feljegyzett virtuális gép nevét és a egy tetszőleges jelszót:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. A virtuális gép IP-cím megkereséséhez használja a következő parancsot, és jegyezze fel a nyilvános IP-cím:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. SSH használatával csatlakozhat a virtuális gép most. A `ssh` parancsot a Cloud shellben előre telepítve. Az előző lépésben szereplő nyilvános IP-címet használja, és amikor a rendszer kéri, a jelszó konfigurálta a virtuális gép számára:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Most már rendelkezik hozzáféréssel a rendszerhéj a virtuális gépet, amely a Docker-tárolókat a távoli figyelési megoldásban. A futó tárolók megtekintéséhez használja a következő parancsot:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Keresse meg a szolgáltatás-kapcsolati karakterláncok

Az oktatóanyagban dolgozik, Visual Studio-megoldás, amely a megoldás Cosmos DB és az IoT Hub-szolgáltatásokhoz kapcsolódik. A következő lépések bemutatják, keresse meg a kapcsolati karakterlánc-értékeket kell egyik módja:

1. A Cosmos DB kapcsolati sztring megkereséséhez futtassa a következő parancsot az SSH-munkamenetből a virtuális gép csatlakozik:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Jegyezze fel a kapcsolati karakterlánc. Ezt az értéket az oktatóanyag későbbi részében még használni fogja.

1. Keresse meg az IoT Hub kapcsolati karakterláncot, csatlakozik a virtuális gép SSH-munkamenetben futtassa a következő parancsot:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Jegyezze fel a kapcsolati karakterlánc. Ezt az értéket az oktatóanyag későbbi részében még használni fogja.

> [!NOTE]
> Is megtalálhatja a kapcsolati karakterláncok az Azure Portalon vagy a használatával a `az` parancsot.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Az eszköz szimuláció szolgáltatás a virtuális gép leállítása

Amikor módosítja az eszköz szimulálása szolgáltatást, futtathatja helyileg, tesztelheti a módosításokat. Ahhoz, hogy az eszköz szimulálása szolgáltatás helyileg futtatni, le kell állítania a következők szerint a virtuális gépen futó példány:

1. Keresése a **Tárolóazonosító** , a **eszköz-szimuláció-dotnet** szolgáltatás, az alábbi parancsot az SSH-munkamenetből a virtuális gép csatlakozik:

    ```sh
    docker ps
    ```

    Jegyezze fel a tároló azonosítója a **eszköz-szimuláció-dotnet** szolgáltatás.

1. Leállítja a **eszköz-szimuláció-dotnet** tárolót, futtassa a következő parancsot:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Klónozza a GitHub-adattárak

Ebben az oktatóanyagban, akivel együttműködik a **eszközszimuláció** és **tárolóadaptert** Visual Studio-projektek. A Githubról forráskódtárházak klónozhat. Végezze el az ebben a lépésben a helyi fejlesztői gépen, amelyen telepítve a Visual Studio:

1. Nyisson meg egy parancssort, és lépjen abba a mappába, ahová menteni a **eszközszimuláció** és **tárolóadaptert** GitHub-adattárak.

1. .NET verziója klónozásához a **eszközszimuláció** tárházban, futtassa a következő parancsot:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    A távoli figyelési megoldásban az eszköz szimuláció szolgáltatás lehetővé teszi, hogy módosítja a beépített szimulált eszköz típusa, és létrehozhat új szimulált eszköz típusa. Egyéni eszköztípus használatával tesztelje a távoli figyelési megoldás működését, mielőtt a fizikai eszközök csatlakoztatása.

1. .NET verziója klónozásához a **tárolóadaptert** tárházban, futtassa a következő parancsot:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Az eszköz szimulálása szolgáltatás a társzolgáltatás adapter a Cosmos DB szolgáltatást az Azure-ban való kapcsolódáshoz használ. A távoli figyelési megoldás a szimulált eszköz konfigurációs adatokat tárol egy Cosmos DB-adatbázisban.

### <a name="run-the-storage-adapter-service-locally"></a>A társzolgáltatás adapter helyileg történő futtatása

Az eszköz szimulálása szolgáltatás a társzolgáltatás adapter a megoldás Cosmos DB-adatbázishoz való kapcsolódáshoz használ. Ha helyileg futtatja az eszköz szimulálása szolgáltatás, is futtatnia kell a társzolgáltatás adapter helyileg. A következő lépések bemutatják, hogyan a társzolgáltatás adapter futtatni a Visual Studióból:

1. A Visual Studióban nyissa meg a **számítógépek-storage-adapter.sln** megoldásfájlt a helyszíni klónjának a **tárolóadaptert** tárház.

1. A Megoldáskezelőben kattintson a jobb gombbal a **webszolgáltatás** projektre, válassza a **tulajdonságok**, és válassza a **Debug**.

1. Az a **környezeti változók** részen szerkessze az értékét a **számítógépek\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** változó legyen a Cosmos DB-kapcsolat a karakterlánc a korábban feljegyzett. Mentse a módosításokat.

1. A Megoldáskezelőben kattintson a jobb gombbal a **webszolgáltatás** projektre, válassza a **Debug**, és válassza a **új példány indítása**.

1. A szolgáltatás elindul a helyileg futtatott, és megnyitja `http://localhost:9022/v1/status` az alapértelmezett böngészőben. Ellenőrizze, hogy a **állapot** értéke "OK: tartási és jól."

1. Hagyja meg a tároló adapter szolgáltatás helyben fut, amíg el nem végezte az oktatóanyagot.

Most már minden helyen, és kezdje el hozzáadni a távoli figyelési megoldás új szimulált eszköz típus készen áll.

## <a name="create-a-simulated-device-type"></a>Hozzon létre egy szimulált eszköz típusa

A legegyszerűbb módja az eszköz szimulálása szolgáltatásban hozzon létre egy új eszköztípus, hogy másolja ki és a egy már meglévő típus módosítása. A következő lépések bemutatják, hogyan másolhat át a beépített **hűtő** hozzon létre egy új eszköz **villanykörte** eszköz:

1. A Megoldáskezelőben kattintson a jobb gombbal a **webszolgáltatás** projektre, válassza a **tulajdonságok**, és válassza a **Debug**.

1. Az a **környezeti változók** részen szerkessze az értékét a **számítógépek\_IOTHUB\_CONNSTRING** változót az IoT Hub kapcsolati karakterláncra kell korábban feljegyzett. Mentse a módosításokat.

1. A Megoldáskezelőben kattintson a jobb gombbal a **eszközszimuláció** megoldást, és válassza a **indítási projektek beállítása**. Válasszon **egyetlen kezdőprojekt** válassza **webszolgáltatás**. Ezután kattintson az **OK** gombra.

1. Minden eszköz rendelkezik modell JSON-fájlt, és a kapcsolódó parancsprogramokat a **szolgáltatások/data/devicemodels** mappát. A Megoldáskezelőben, másolja a **hűtő** fájlok létrehozásához a **villanykörte** fájlok az alábbi táblázatban látható módon:

    | Forrás                      | Cél                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Az új eszköztípus jellemzőinek definiálása

A **villanykörte-01.json** fájl határozza meg a típus jellemzői, például a telemetriai adatokat állít elő, és a módszereket támogatja. Az alábbi lépések a frissítés a **villanykörte-01.json** fájl meghatározásához a **villanykörte** eszköz:

1. Az a **villanykörte-01.json** fájlt, frissítse az eszköz metaadatait, az alábbi kódrészletben látható módon:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Az a **villanykörte-01.json** fájlt, frissítse a szimuláció definíciója az alábbi kódrészletben látható módon:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Interval": "00:00:20",
      "Scripts": [
        {
          "Type": "javascript",
          "Path": "lightbulb-01-state.js"
        }
      ]
    },
    ```

1. Az a **villanykörte-01.json** fájlt, frissítse a eszköz tulajdonságait az alábbi kódrészletben látható módon:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Az a **villanykörte-01.json** fájlt, az eszköz telemetriai típusdefiníciók frissítse az alábbi kódrészletben látható módon:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. Az a **villanykörte-01.json** fájlt, az eszköz adattípusú metódusok frissítse az alábbi kódrészletben látható módon:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Mentse a **villanykörte-01.json** fájlt.

### <a name="simulate-custom-device-behavior"></a>Egyéni eszköz viselkedésének szimulálása

A **parancsfájlok/villanykörte-01-state.js** fájl határozza meg a szimuláció viselkedését a **villanykörte** típusa. Az alábbi lépések a frissítés a **parancsfájlok/villanykörte-01-state.js** viselkedésének megadása a fájl a **villanykörte** eszköz:

1. Az állapot definíciójának szerkesztése a **parancsfájlok/villanykörte-01-state.js** fájlt az alábbi kódrészletben látható módon:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Adjon hozzá egy **tükrözés** függvény után a **eltérőek lehetnek** függvényt a következő definíciót:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Szerkessze a **fő** függvény viselkedésének megvalósítása az alábbi kódrészletben látható módon:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global device properties and the global state before
        // generating the new telemetry, so that the telemetry can apply changes
        // using the previous function state.
        restoreSimulation(previousState, previousProperties);

        state.temperature = vary(200, 5, 150, 250);

        // Make this flip every so often
        state.status = flip(state.status);

        updateState(state);

        return state;
    }
    ```

1. Mentse a **parancsfájlok/villanykörte-01-state.js** fájlt.

A **parancsfájlok/SwitchOn-method.js** valósítja meg a fájl a **kapcsoló a** metódus az egy **villanykörte** eszköz. Az alábbi lépések a frissítés a **parancsfájlok/SwitchOn-method.js** fájlt:

1. Az állapot definíciójának szerkesztése a **parancsfájlok/SwitchOn-method.js** fájlt az alábbi kódrészletben látható módon:

    ```js
    var state = {
       status: "on"
    };
    ```

1. A villanykörte váltani, szerkesztheti a **fő** függvényt az alábbiak szerint:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Mentse a **parancsfájlok/SwitchOn-method.js** fájlt.

1. Készítsen másolatot a **parancsfájlok/SwitchOn-method.js** nevű **parancsfájlok/SwitchOff-method.js**.

1. Kapcsolja ki a villanykörtére, szerkessze a **fő** működni a **parancsfájlok/SwitchOff-method.js** fájlt az alábbiak szerint:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Mentse a **parancsfájlok/SwitchOff-method.js** fájlt.

1. A Megoldáskezelőben válassza ki a négy új fájlok mindegyike viszont. Az a **tulajdonságok** minden ablakot, ellenőrizze, hogy **Másolás a kimeneti könyvtár** értékre van állítva **másolás, ha újabb**.

### <a name="configure-the-device-simulation-service"></a>Az eszköz szimulálása szolgáltatás konfigurálása

Ahhoz, hogy a tesztelés során a megoldáshoz történő csatlakoztatásáról szimulált eszközök számának korlátozásához konfigurálnia a egyetlen hűtő és a egy egyetlen villanykörte eszköz futtatásához. A konfigurációs adatokat a Cosmos DB-példányra, a megoldás az erőforráscsoportban van tárolva. A konfigurációs adatok szerkesztéséhez használja a **Cloud Explorer** megtekintése a Visual Studióban:

1. Megnyitásához a **Cloud Explorer** megtekintése a Visual Studióban, válassza a **nézet** , majd **Cloud Explorer**.

1. A szimuláció konfigurációs dokumentum lévő keresése **erőforrás keresése** adja meg **simualtions.1**.

1. Kattintson duplán a **simulations.1** a dokumentumot, nyissa meg szerkesztésre.

1. Értéke **adatok**, keresse meg a **DeviceModels** tömb, amely a következő kódrészletre hasonlít:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Egyetlen hűtő és a egy egyetlen villanykörte szimulált eszköz megadásához cserélje le a **DeviceModels** tömb az alábbi kódra:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    A változtatás mentése a **simulations.1** dokumentumot.

> [!NOTE]
> Használhatja a Cosmos DB adatkezelő az Azure Portalon szerkesztheti az **simulations.1** dokumentumot.

### <a name="test-the-lightbulb-device-type-locally"></a>A villanykörte eszköztípus helyi tesztelése

Most már készen áll az új szimulált villanykörte típusát tesztelheti az eszköz szimulálása projekt helyi futtatásával.

1. A Megoldáskezelőben kattintson a jobb gombbal **webszolgáltatás**, válassza a **Debug** majd **új példány indítása**.

1. Ellenőrizze, hogy a két szimulált eszköz csatlakozik az IoT Hub, a böngészőben nyissa meg az Azure Portalon.

1. Keresse meg az IoT hubot az erőforráscsoportban, amely tartalmazza a távoli figyelési megoldás.

1. Az a **figyelés** válassza **metrikák**. Ellenőrizze, hogy hány **csatlakoztatott eszközök** kettőt:

    ![Csatlakoztatott eszközök száma](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. A böngészőben navigáljon a **irányítópult** a távoli figyelési megoldáshoz. A telemetriai adatok panelen a a **irányítópult**válassza **hőmérséklet**. Az összes szimulált eszközt a hőmérséklet a diagramon jeleníti meg:

    ![Hőmérsékleti telemetria](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Most már a villanykörte eszköz szimuláció futtatása helyileg történik. A következő lépés, hogy a frissített szimulátor kód üzembe helyezése a virtuális gépet, amely a távoli figyelési mikroszolgáltatásokat az Azure-ban.

A folytatás előtt állítsa le az eszközszimuláció és a tárolási adapter projekteket a Visual Studio-hibakeresés.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>A frissített szimulátor üzembe helyezése a felhőben

A távoli figyelési megoldásban a mikroszolgáltatások docker-tárolókat futtathat. A tárolók a megoldás virtuális gépen az Azure-ban üzemel. Ebben a szakaszban:

* Hozzon létre egy új eszköz szimulálása docker-rendszerképet.
* Töltse fel a rendszerképet a docker hub adattárából.
* A kép importálása a megoldás virtuális gépet.

A következő lépések feltételezik, hogy rendelkezik-e egy tárház nevű **villanykörte** a Docker Hub-fiókban.

1. A Visual Studióban az a **eszközszimuláció** projekt, nyissa meg a fájlt **solution\scripts\docker\build.cmd**.

1. Módosítsa a sort, amely beállítja a **DOCKER_IMAGE** környezeti változót, a Docker Hub-adattár neve:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    A módosítás mentéséhez.

1. A Visual Studióban az a **eszközszimuláció** projekt, nyissa meg a fájlt **solution\scripts\docker\publish.cmd**.

1. Módosítsa a sort, amely beállítja a **DOCKER_IMAGE** környezeti változót, a Docker Hub-adattár neve:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    A módosítás mentéséhez.

1. Nyisson meg egy parancssort rendszergazdaként. Majd lépjen abba a mappába **scripts\docker** , a Klónozás a **eszközszimuláció** GitHub-adattárban.

1. A docker-rendszerkép létrehozásához futtassa a következő parancsot:

    ```cmd
    build.cmd
    ```

1. Jelentkezzen be a Docker Hub-fiókjához, futtassa a következő parancsot:

    ```cmd
    docker login
    ```

1. Töltse fel az új rendszerképet a Docker Hub-fiókjába, futtassa a következő parancsot:

    ```cmd
    publish.cmd
    ```

1. A feltöltés ellenőrzéséhez lépjen [ https://hub.docker.com/ ](https://hub.docker.com/). Keresse meg a **villanykörte** tárházat, és válassza a **részletek**. Válassza a **címkék**:

    ![A docker hub](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    A parancsfájlok hozzáadva a **tesztelés** címke a rendszerkép.

1. Az SSH használatával csatlakozhat a megoldás virtuális géphez az Azure-ban. Keresse meg a **alkalmazás** mappa és a Szerkesztés a **docker-compose.yml** fájlt:

    ```sh
    cd /app
    sudo nano docker-compose.yml
    ```

1. A bejegyzés a docker-rendszerkép használata az eszköz szimulálása szolgáltatás szerkesztése:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Mentse a módosításokat.

1. Az új beállítások az összes szolgáltatás újraindításához futtassa a következő parancsot:

    ```sh
    sudo ./start.sh
    ```

1. A naplófájlban talál az új eszköz szimulálása a tárolóból, futtassa a következő parancsot a tároló-azonosító található:

    ```sh
    docker ps
    ```

    Ezután futtassa a következő parancsot a tároló azonosítója alapján:

    ```sh
    docker logs {container ID}
    ```

Ezennel befejezte a lépéseket az eszköz szimulálása szolgáltatás frissített verziójának telepítése a távoli figyelési megoldáshoz.

A böngészőben navigáljon a **irányítópult** a távoli figyelési megoldáshoz. A telemetriai adatok panelen a a **irányítópult**válassza **hőmérséklet**. A két szimulált eszköz hőmérséklete a diagramon jeleníti meg:

![Hőmérsékleti telemetria](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Az a **eszközök** oldalon telepíthet az új típusú példányok:

![Rendelkezésre álló szimulációk listájának megtekintése](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

A szimulált eszközről érkező telemetriai adatok tekintheti meg:

![Villanykörte telemetria megtekintése](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

Hívása a **SwitchOn** és **SwitchOff** módszerek az eszközön:

![Villanykörte metódusok meghívása](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Adjon hozzá egy új telemetria típusa

Ez a szakasz ismerteti, hogyan módosíthatja egy meglévő szimulált eszköz típusa támogatásához új telemetriai típus.

### <a name="locate-the-chiller-device-type-files"></a>Keresse meg a hűtő eszköz típusú fájlok

A következő lépések bemutatják, hogyan találhatja meg a fájlokat, amelyek meghatározzák a beépített **hűtő** eszköz:

1. Ha még nem tette meg, a következő paranccsal klónozásához a **eszköz-szimuláció-dotnet** GitHub-adattár helyi számítógépre:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Minden eszköz rendelkezik modell JSON-fájlt, és a kapcsolódó parancsprogramokat a `data/devicemodels` mappát. A fájlokat, amelyek meghatározzák a szimulált **hűtő** eszköz típusa:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Adja meg az új telemetria típusa

A következő lépések bemutatják, hogyan adhat hozzá egy új **belső hőmérséklet** írja be a **hűtő** eszköz típusa:

1. Nyissa meg a **hűtő-01.json** fájlt.

1. Frissítés a **sémaverzióval** értékét az alábbiak szerint:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Az a **InitialState** területen adja hozzá a feloldását két kérte definíciókat:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Az a **Telemetriai** tömböt, adja hozzá a következő-definíciót:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Mentse a **hűtő-01.json** fájlt.

1. Nyissa meg a **parancsfájlok/hűtő-01-state.js** fájlt.

1. Adja hozzá a következő mezőket a **állapot** változó:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Adja hozzá a következő sort a **fő** függvény:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Mentse a **parancsfájlok/hűtő-01-state.js** fájlt.

### <a name="test-the-chiller-device-type"></a>Tesztelje a hűtő eszköz típusa

A frissített teszteléséhez **hűtő** eszköztípus, először futtassa a helyi példányának a **eszköz-szimuláció-dotnet** szolgáltatást, hogy az eszköz típusának vizsgálati várt módon viselkedik. Ha tesztelése és hibakeresése helyileg a frissített eszköz típusa, a tároló újraépítése és ismételt üzembe helyezése a **eszköz-szimuláció-dotnet** szolgáltatást az Azure-bA.

Ha futtatja a **eszköz-szimuláció-dotnet** szolgáltatás helyi, telemetriai adatokat küld a távoli figyelési megoldáshoz. Az a **eszközök** lapon is üzembe helyezi a frissített típusú példányok.

Tesztelése és hibakeresése helyileg a módosításokat, lásd az előző szakaszban [tesztelje helyben a villanykörte eszköztípus](#test-the-lightbulb-device-type-locally).

A frissített eszköz szimulálása szolgáltatáshoz a megoldás virtuális géphez az Azure-beli üzembe helyezéséhez lásd az előző szakaszban [a felhőben üzembe helyezni a frissített szimulátor](#deploy-the-updated-simulator-to-the-cloud).

Az a **eszközök** lapon is üzembe helyezi a frissített típusú példányok:

![Adja hozzá a frissített hűtő](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

Megtekintheti, hogy az új **belső hőmérséklet** szimulált eszközről érkező telemetriai adatok.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan való:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Hozzon létre egy új eszköztípus
> * Egyéni eszköz viselkedésének szimulálása
> * Egy új eszköztípus hozzáadása az irányítópulthoz
> * Egyéni telemetriát küld egy meglévő eszköz típusa

Most már megtanulhatta, hogyan szabhatja testre az eszköz szimulálása szolgáltatás. A javasolt következő lépésre az, hogy ismerje meg, hogyan [egy fizikai eszköz csatlakoztatása a távoli figyelési megoldás](iot-accelerators-connecting-devices-node.md).

A távoli figyelési megoldás fejlesztői ismertetését lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
