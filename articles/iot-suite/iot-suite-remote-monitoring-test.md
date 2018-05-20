---
title: Eszköz szimulálása a távoli felügyeleti megoldás - Azure |} Microsoft Docs
description: Az oktatóanyag bemutatja, az eszköz szimulátor használata a távoli felügyeleti megoldásgyorsító.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 9b4f7f9a9c501204d48b738089dc3cbd015a744c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="create-a-new-simulated-device"></a>Új szimulált eszköz létrehozása

Az oktatóanyag bemutatja, hogyan szabhatja testre az eszköz szimulátor mikroszolgáltatási a távoli felügyeleti megoldásgyorsító a. Ez az oktatóanyag két esetben használja a Contoso IoT alkalmazásban megjelenítése az eszköz szimulátor képességeit.

A következő videó bemutatja az eszköz szimulátor mikroszolgáltatási testreszabására szolgáló beállítások áttekintése:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

Az első esetben Contoso szeretné egy új intelligens villanykörte eszközt. A tesztek kerülnek végrehajtásra, létrehozhat egy új szimulált eszköz a következő jellemzőkkel:

*Tulajdonságok*

| Name (Név)                     | Értékek                      |
| ------------------------ | --------------------------- |
| Szín                    | Fehér, a piros, kék            |
| Fényerő               | 0 – 100                    |
| Becsült hátralévő élettartamát | 10 000 óra visszaszámlálási |

*Telemetria*

A következő táblázat a villanykörte a felhőbe, mint egy adatfolyam jelentések adatainak megjelenítése:

| Name (Név)   | Értékek      |
| ------ | ----------- |
| status | "a" "off" |
| Hőmérséklet | F fok |
| online | IGAZ, hamis |

> [!NOTE]
> A **online** telemetriai értéke összes szimulált esetében kötelező.

*Módszerek*

Az alábbi táblázat a műveletek az új eszköz támogatja:

| Name (Név)        |
| ----------- |
| Kapcsoló   |
| Kikapcsolás  |

*Kezdeti állapot*

A következő táblázat az eszköz kezdeti állapotának megjelenítése:

| Name (Név)                     | Értékek |
| ------------------------ | -------|
| Kezdeti szín            | Fehér  |
| Kezdeti fényerő       | 75     |
| Kezdeti fennmaradó élettartama   | 10,000 |
| Kezdeti telemetriai állapota | "a"   |
| Kezdeti telemetriai hőmérséklet | 200   |

A második forgatókönyvben hozzáadhat egy új telemetriai típus contoso meglévő **hűtő** eszköz.

Az oktatóanyag bemutatja, az eszköz szimulátor használata a távoli felügyeleti megoldásgyorsító:

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Hozzon létre egy új eszköz típusa
> * Egyéni eszközviselkedés szimulálása
> * Új eszköz-típus hozzáadása az irányítópulton
> * Egyéni telemetriai adatokat küldhet egy meglévő eszközt típusból

A következő videó bemutatja, a forgatókönyv szimulált és valós eszközök kapcsolódni a távoli figyelési megoldást:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az alábbiak szükségesek:

* A távoli felügyeleti megoldás az Azure-előfizetéshez telepített példányát. Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [telepíteni a távoli felügyeleti megoldásgyorsító](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) oktatóanyag.

* Visual Studio 2017. Ha nincs telepítve a Visual Studio 2017, érdemes letöltenie a szabad [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) kiadását.

* [Cloud Explorerben a Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) Visual Studio bővítmény.

* A fiók [Docker Hub](https://hub.docker.com/). Regisztrálhat az ingyenes a kezdéshez.

* [Git](https://git-scm.com/downloads) a asztali gépen telepítve van.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

Fejezze be a fejlesztési környezetet az új szimulált eszköz hozzáadása a távoli felügyeleti megoldás előkészítéséhez a következő feladatokat:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>A megoldás virtuális gép SSH elérésének konfigurálása az Azure-ban

A távoli figyelési megoldást a következő létrehozásakor [www.azureiotsuite.com](https://www.azureiotsuite.com), úgy döntött, hogy a megoldás neve. A megoldás neve, amely a különböző telepített olyan erőforrásokat tartalmaz, a megoldás az Azure erőforráscsoport neve lesz. Az alábbi parancsokat használja nevű erőforráscsoport **Contoso-01**, le kell cserélni **Contoso-01** az erőforráscsoport nevét.

Az alábbi parancsokat használja a `az` parancsot [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Az Azure CLI 2.0 telepítése a fejlesztői számítógépén, vagy használja a [felhő rendszerhéj](https://docs.microsoft.com/azure/cloud-shell/overview) a a [Azure-portálon](http://portal.azure.com). Az Azure CLI 2.0 előre telepített, a felhő rendszerhéj.

1. Ha ellenőrizni szeretné a távoli felügyeleti erőforrásokat tartalmazó erőforráscsoport nevét, a következő parancsot:

    ```sh
    az group list | grep "name"
    ```

    Ez a parancs felsorolja az előfizetésében szereplő összes erőforráscsoport. A lista tartalmazza a távoli felügyeleti megoldásként ilyen nevű erőforráscsoport.

1. Ahhoz, hogy a csoport alapértelmezett csoport a következő parancsok erőforrás, a következő parancsot az erőforráscsoport neve helyett használatával **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Az erőforrások az erőforráscsoportban listájának, futtassa a következő parancsot:

    ```sh
    az resource list -o table
    ```

    Jegyezze fel a virtuális gép és a hálózati biztonsági csoport nevét. A későbbi lépésekben használhatja ezeket az értékeket.

1. A virtuális gép SSH-hozzáférés engedélyezéséhez a következő parancsot az előző lépésben a hálózati biztonsági csoport neve:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    A hálózat bejövő szabályok listájának megtekintéséhez futtassa a következő parancsot:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. A következő parancsot a virtuális gép jelszavát tudja jelszó módosításához. A korábban feljegyzett virtuális gép nevét, és egy tetszőleges jelszót használja:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Az IP-címet a virtuális gép található, használja a következő parancsot, és jegyezze fel a nyilvános IP-cím:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. A virtuális géphez történő csatlakozáshoz SSH használhatja. A `ssh` parancs a felhő rendszerhéj előre telepített. Az előző lépésben a nyilvános IP-címet, és amikor a rendszer kéri, a jelszót, a virtuális géphez konfigurált:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Most már rendelkezik hozzáféréssel a rendszerhéj a távoli felügyeleti megoldás a Docker-tároló futó virtuális gépen. A futó tárolók megtekintéséhez használja a következő parancsot:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>A szolgáltatás kapcsolati karakterláncok keresése

Az oktatóanyag használata Visual Studio megoldás, amely a megoldás Cosmos DB és az IoT-központ szolgáltatáshoz csatlakozik. Az alábbi lépéseket a kapcsolat egyik módja kell karakterlánc-értékek megjelenítése:

1. Keresse meg a Cosmos DB kapcsolati karakterláncot, a következő parancsot a kapcsolódik a virtuális gép SSH-munkamenetet:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Jegyezze fel a kapcsolati karakterlánc. Használja ezt az értéket az oktatóanyag későbbi részében.

1. Keresse meg az IoT-központ kapcsolati karakterláncot, a következő parancsot a kapcsolódik a virtuális gép SSH-munkamenetet:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Jegyezze fel a kapcsolati karakterlánc. Használja ezt az értéket az oktatóanyag későbbi részében.

> [!NOTE]
> Is található a kapcsolati karakterláncok az Azure portálon vagy a `az` parancsot.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Állítsa le a virtuális gép az eszköz szimuláció szolgáltatást

Ha módosítja az szimuláció szolgáltatást, futtathatja úgy, hogy helyben tesztelheti a módosításokat. Mielőtt az eszköz szimuláció szolgáltatás helyileg futtatta, le kell állítania a következőképpen a virtuális gépen futó példány:

1. Található a **TÁROLÓAZONOSÍTÓ:** , a **eszköz-szimuláció** szolgáltatás, a következő parancsot az SSH-munkamenetet, a virtuális gép csatlakozik:

    ```sh
    docker ps
    ```

    Jegyezze fel a tároló Azonosítóját a **eszköz-szimuláció** szolgáltatás.

1. Leállítja a **eszköz-szimuláció** tároló, a következő parancsot:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>A GitHub-adattárak klónozása

Ebben az oktatóanyagban, akivel együttműködik a **eszköz-szimuláció** és **tárolóadapter** Visual Studio-projektek. A forráskódú adattárakban a Githubból is klónozhatja. Ez a lépés végrehajtásához a helyi fejlesztési számítógépén, ahol telepítve a Visual Studio:

1. Nyisson meg egy parancssort, és lépjen abba a mappába, ahová menteni a **eszköz-szimuláció** és **tárolóadapter** GitHub-adattárak.

1. A .NET-verziója, a Klónozás a **eszköz-szimuláció** -tárházban, futtassa a következő parancsot:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Az eszköz szimuláció szolgáltatás a távoli felügyeleti megoldás lehetővé teszi, hogy módosíthassa a beépített szimulált eszköz típusa, és új szimulált eszköz típusa. Egyéni eszköztípus segítségével tesztelje a működését a távoli felügyeleti megoldás, mielőtt a fizikai eszközök csatlakoztatása.

1. A .NET-verziója, a Klónozás a **tárolóadapter** -tárházban, futtassa a következő parancsot:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Az eszköz szimuláció szolgáltatás a társzolgáltatás adapter használja az Azure-ban a Cosmos DB szolgáltatásához. A távoli felügyeleti megoldás a szimulált eszköz konfigurációs adatok Cosmos DB adatbázisban tárolja.

### <a name="run-the-storage-adapter-service-locally"></a>Futtassa helyben a társzolgáltatás adapter

Az eszköz szimuláció szolgáltatás a társzolgáltatás adapter használja, a megoldás Cosmos DB adatbázishoz való kapcsolódáshoz. Ha az eszköz szimuláció szolgáltatás helyileg futtatta, is futtatnia kell a társzolgáltatás adapter helyileg. A következő lépések bemutatják a Visual Studio-ről futtatva a társzolgáltatás adapter:

1. A Visual Studióban nyissa meg a **számítógépek-storage-adapter.sln** a helyi klónja a megoldásfájlt a **tárolóadapter** tárház.

1. A Megoldáskezelőben kattintson a jobb gombbal a **WebService** projektre, válassza a **tulajdonságok**, és válassza a **Debug**.

1. Az a **környezeti változók** területen értékét a **számítógépek\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** változó a Cosmos DB kapcsolat legyen a karakterlánc korábban feljegyzett. Mentse a módosításokat.

1. A Megoldáskezelőben kattintson a jobb gombbal a **WebService** projektre, válassza a **Debug**, és válassza a **Start új példány**.

1. A szolgáltatás elindul a helyben fut, és megnyitja `http://localhost:9022/v1/status` az alapértelmezett böngészőben. Ellenőrizze, hogy a **állapot** értéke "OK: Alive és jól."

1. Hagyja meg a tároló adapter szolgáltatás helyben fut, az oktatóanyag befejezése után.

Most már rendelkezik minden helyen, és készen áll a távoli figyelési megoldást szeretne hozzáadni a egy új szimulált eszköz típusa.

## <a name="create-a-simulated-device-type"></a>Hozzon létre egy szimulált eszköz típusa

A legegyszerűbben úgy, hogy az eszköz szimuláció szolgáltatásban hozzon létre egy új eszköz típusa, másolása és egy már meglevő típus módosítása. A következő lépések bemutatják, hogyan másolhatja a beépített **hűtő** hozzon létre egy új eszköz **villanykörte** eszköz:

1. A Visual Studióban nyissa meg a **eszköz-simulation.sln** a helyi klónja a megoldásfájlt a **eszköz-szimuláció** tárház.

1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulationAgent** projektre, válassza a **tulajdonságok**, és válassza a **Debug**.

1. Az a **környezeti változók** területen értékét a **számítógépek\_IOT HUBBAL\_CONNSTRING** változó az IoT-központ kapcsolati karakterláncot kell azt korábban említettük. Mentse a módosításokat.

1. A Megoldáskezelőben kattintson a jobb gombbal a **WebService** projektre, válassza a **tulajdonságok**, és válassza a **Debug**.

1. Az a **környezeti változók** területen értékét a **számítógépek\_IOT HUBBAL\_CONNSTRING** változó az IoT-központ kapcsolati karakterláncot kell azt korábban említettük. Mentse a módosításokat.

1. A Megoldáskezelőben kattintson a jobb gombbal a **eszköz-szimuláció** megoldás válassza **indítási projektek beállítása**. Válasszon **egyetlen kezdőprojekt** válassza **WebService**. Ezután kattintson az **OK** gombra.

1. Minden eszköz rendelkezik a modell JSON-fájl és a kapcsolódó parancsprogramokat a **szolgáltatások/data/devicemodels** mappa. A Megoldáskezelőben, másolja a **hűtő** fájlokat, hogy a **villanykörte** fájlok az alábbi táblázatban látható módon:

    | Forrás                      | Cél                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Adja meg az új eszköztípus jellemzői

A **villanykörte-01.json** fájl határozza meg a típus jellemzői, például a telemetriai adatokat hoz létre, és a módszereket támogatja. Az alábbi lépéseket a frissítés a **villanykörte-01.json** fájlt adja meg a **villanykörte** eszköz:

1. Az a **villanykörte-01.json** fájlt, az eszköz metaadatainak frissítéséhez, ahogy az a következő kódrészletet:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Az a **villanykörte-01.json** fájl, a szimuláció definition frissítése, ahogy az a következő kódrészletet:

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

1. Az a **villanykörte-01.json** fájlt, az eszköz típus tulajdonságainak frissítéséhez látható módon a következő kódrészletet:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Az a **villanykörte-01.json** fájl, a eszköz típusa telemetriai definíciók frissítése, ahogy az a következő kódrészletet:

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

1. Az a **villanykörte-01.json** fájlt, az eszköz metódusai frissítése, ahogy az a következő kódrészletet:

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

### <a name="simulate-custom-device-behavior"></a>Egyéni eszközviselkedés szimulálása

A **parancsfájlok/villanykörte-01-state.js** fájl szimuláció viselkedését határozza meg a **villanykörte** típusa. Az alábbi lépéseket a frissítés a **parancsfájlok/villanykörte-01-state.js** viselkedésének meghatározása a fájl a **villanykörte** eszköz:

1. Az állapot definíciójának szerkesztése a **parancsfájlok/villanykörte-01-state.js** fájl, ahogy az a következő kódrészletet:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Adja hozzá a **tükrözés** után működik a **eltérő** függvény a következő definícióját:

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

1. Szerkessze a **fő** funkció viselkedését végrehajtásához, ahogy az az alábbi kódrészletet:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Mentse a **parancsfájlok/villanykörte-01-state.js** fájlt.

A **parancsfájlok/SwitchOn-method.js** valósít meg fájlt a **kapcsoló a** metódust egy **villanykörte** eszköz. Az alábbi lépéseket a frissítés a **parancsfájlok/SwitchOn-method.js** fájlt:

1. Az állapot definíciójának szerkesztése a **parancsfájlok/SwitchOn-method.js** fájl, ahogy az a következő kódrészletet:

    ```js
    var state = {
       status: "on"
    };
    ```

1. A villanykörte váltani, szerkesztheti a **fő** működéséhez az alábbiak szerint:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Mentse a **parancsfájlok/SwitchOn-method.js** fájlt.

1. Készítsen másolatot a **parancsfájlok/SwitchOn-method.js** nevű **parancsfájlok/SwitchOff-method.js**.

1. Kapcsolja ki a villanykörte, szerkessze a **fő** működni a **parancsfájlok/SwitchOff-method.js** fájlt az alábbiak szerint:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Mentse a **parancsfájlok/SwitchOff-method.js** fájlt.

1. A Megoldáskezelőben válassza ki a négy új fájlokat pedig. Az a **tulajdonságok** minden ablakot, ellenőrizze, hogy **másolása a kimeneti könyvtárba** értéke **másolhatja, ha újabb**.

### <a name="configure-the-device-simulation-service"></a>Az eszköz szimuláció szolgáltatás konfigurálása

A szimulált tesztelés során a megoldás csatlakozó eszközök számának korlátozásához, konfigurálja a szolgáltatás futtatásához egy egyetlen hűtő és egy egyetlen villanykörte eszköz. A konfigurációs adatok tárolódik a Cosmos DB példány a megoldás erőforráscsoportban. A konfigurációs adatok szerkesztéséhez használja a **Cloud Explorer** a Visual Studio megtekintése:

1. Lehetőségre a **Cloud Explorer** Visual Studio megtekintéséhez válasszon **nézet** , majd **Cloud Explorer**.

1. A szimuláció konfigurációs dokumentum, a található **erőforrás keresése** meg **simualtions.1**.

1. Kattintson duplán a **simulations.1** dokumentum nyissa meg szerkesztésre.

1. Értékének **adatok**, keresse meg a **DeviceModels** tömb, amely hasonlít a következő kódrészletet:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Egyetlen hűtő és egy egyetlen villanykörte szimulált eszköz megadásához cserélje le a **DeviceModels** tömb a következő kóddal:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    A változtatás mentése a **simulations.1** dokumentum.

> [!NOTE]
> Is használhatja a Cosmos DB adatkezelő az Azure portálon szerkesztése a **simulations.1** dokumentum.

### <a name="test-the-lightbulb-device-type-locally"></a>A villanykörte eszköztípus helyi tesztelése

Most már készen áll az új szimulált villanykörte típusának tesztelése eszköz szimuláció-projektek futtatásával foglalkozik helyileg.

1. A Megoldáskezelőben kattintson a jobb gombbal **WebService**, válassza a **Debug** majd **Start új példányt**.

1. Ellenőrizze, hogy a két szimulált eszköz csatlakozik az IoT Hub, az Azure-portál megnyitása a böngészőben.

1. Nyissa meg az IoT-központ, amely tartalmazza a távoli felügyeleti megoldás az erőforráscsoportban.

1. Az a **figyelés** területen válasszon **metrikák**. Ellenőrizze, hogy hány **csatlakoztatott eszközök** kettő:

    ![Csatlakoztatott eszközök száma](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. A böngészőben navigáljon a **irányítópult** távoli figyelési megoldást. A telemetriai adatok panelen a **irányítópult**, jelölje be **hőmérséklet**. A diagramon az összes a szimulált eszköz hőmérséklete jeleníti meg:

    ![Hőmérséklet-telemetria](media/iot-suite-remote-monitoring-test/telemetry.png)

A villanykörte eszköz szimuláció helyileg futó most már rendelkezik. A következő lépés, hogy telepítse a frissített szimulátor kódját a virtuális géphez, futtatja a távoli felügyeleti mikroszolgáltatások létrehozására az Azure-ban.

A folytatás előtt állítsa le a szimuláció eszköz és a tárolási adapter projekteket a Visual Studio hibakeresési.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Telepítse a frissített szimulátor a felhőben

A távoli felügyeleti megoldás mikroszolgáltatások docker-tárolókban lévő futtatásához. A tárolók a megoldás virtuális gépet az Azure-ban tárolt. Ebben a szakaszban:

* Hozzon létre egy új eszköz szimuláció docker-lemezképet.
* A docker hub tárházhoz feltölti a lemezképet.
* A kép importálnia kell a megoldás virtuális gépet.

A következő lépések azt feltételezik, hogy rendelkezik-e a tárház nevű **villanykörte** Docker Hub-fiókjában.

1. A Visual Studio a a **eszköz-szimuláció** projektre, nyissa meg a fájlt **solution\scripts\docker\build.cmd**.

1. Módosítsa a sort, amely beállítja a **DOCKER_IMAGE** környezeti változót a Docker központ tárház nevét:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    A módosítás mentéséhez.

1. A Visual Studio a a **eszköz-szimuláció** projektre, nyissa meg a fájlt **solution\scripts\docker\publish.cmd**.

1. Módosítsa a sort, amely beállítja a **DOCKER_IMAGE** környezeti változót a Docker központ tárház nevét:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    A módosítás mentéséhez.

1. Nyisson meg egy parancssort rendszergazdaként. Ezután lépjen abba a mappába **scripts\docker** , a Klónozás a **eszköz-szimuláció** GitHub-tárházban.

1. A docker-lemezkép létrehozásához a következő parancsot:

    ```cmd
    build.cmd
    ```

1. Jelentkezzen be a Docker központ fiókjához, futtassa a következő parancsot:

    ```cmd
    docker login
    ```

1. Töltse fel az új lemezképet a Docker központ fiókjához, futtassa a következő parancsot:

    ```cmd
    publish.cmd
    ```

1. A feltöltés ellenőrzéséhez navigáljon [ https://hub.docker.com/ ](https://hub.docker.com/). Keresse meg a **villanykörte** tárház válassza **részletek**. Válassza a **címkék**:

    ![Docker központ](media/iot-suite-remote-monitoring-test/dockerhub.png)

    A hozzáadott parancsfájlok a **tesztelés** címkén belül, hogy a lemezképet.

1. Az SSH használata a megoldás az Azure virtuális géphez való kapcsolódáshoz. Keresse meg a **App** mappa és szerkesztése a **docker-compose.yaml** fájlt:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Az eszköz szimuláció szolgáltatást a docker-lemezkép a bejegyzés szerkesztése:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Mentse a módosításokat.

1. Az új beállítások az összes szolgáltatás újraindításához futtassa a következő parancsot:

    ```sh
    sudo ./start.sh
    ```

1. Tekintse meg a naplófájlt, az új eszköz szimuláció tárolóból, futtassa a tárolóhely-azonosító található a következő parancsot:

    ```sh
    docker ps
    ```

    Ezután futtassa a következő parancs használatával a tárolóhely-azonosító:

    ```sh
    docker logs {container ID}
    ```

Ezzel befejezte az eszköz szimuláció szolgáltatás frissített verziójának telepítése a távoli felügyeleti megoldás lépéseit.

A böngészőben navigáljon a **irányítópult** távoli figyelési megoldást. A telemetriai adatok panelen a **irányítópult**, jelölje be **hőmérséklet**. A két szimulált eszköz hőmérséklete a diagram jelenik meg:

![Hőmérséklet-telemetria](media/iot-suite-remote-monitoring-test/telemetry.png)

Az a **eszközök** lapon megadhat az új típusú példányok:

![Rendelkezésre álló szimulációja listájának megtekintése](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

A szimulált eszköz telemetriai tekintheti meg:

![Nézet villanykörte telemetriai adat](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Hívása a **SwitchOn** és **SwitchOff** módszerek az eszközön:

![Hívás villanykörte módszerek](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Új telemetria-típus hozzáadása

Ez a szakasz ismerteti, hogyan lehet módosítani egy meglévő szimulált eszköz típusa egy új telemetriai típus támogatásához.

### <a name="locate-the-chiller-device-type-files"></a>Keresse meg a hűtő eszköz típusú fájlok

A következő lépések bemutatják, hol találhatók a fájlok, amelyek meghatározzák a beépített **hűtő** eszköz:

1. Ha még nem tette meg, a következő paranccsal klónozásához a **eszköz-szimuláció** GitHub-tárházban a helyi számítógépen:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Minden eszköz rendelkezik a modell JSON-fájl és a kapcsolódó parancsprogramokat a `data/devicemodels` mappát. A fájlokat, amelyek meghatározzák a szimulált **hűtő** eszköz típusa:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Adja meg azt az új telemetriai adat

A következő lépések bemutatják a adjon hozzá egy új **belső hőmérséklet** típus a **hűtő** eszköz típusa:

1. Nyissa meg a **hűtő-01.json** fájlt.

1. Frissítés a **sémaverzióval** érték az alábbiak szerint:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Az a **InitialState** területen írja be a következő két definíciók:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Az a **Telemetriai** tömb, adja hozzá a következő definícióját:

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

A frissített tesztelése **hűtő** eszköztípus, előbb futtassa az egy helyi példányát a **eszköz-szimuláció** szolgáltatást, hogy az eszköz típusának tesztelése viselkedik a várt módon. Ha tesztelni, és helyileg a frissített eszköztípus indítja, építse újra a tárolót, és telepítse újra a **eszköz-szimuláció** szolgáltatás az Azure-bA.

Amikor futtatja a **eszköz-szimuláció** szolgáltatás helyileg küld telemetriai adatokat a távoli felügyeleti megoldás. Az a **eszközök** lap, a frissített típusú példányok létesíthet.

Tesztelése és hibakeresése a módosítások helyileg, tekintse meg az előző szakaszban [helyileg a villanykörte eszköztípus tesztelése](#test-the-lightbulb-device-type-locally).

A frissített eszköz szimuláció szolgáltatás telepítése a megoldás virtuális gépet az Azure-ban, tekintse meg az előző szakaszban [telepíti a felhőbe a frissített szimulátor](#deploy-the-updated-simulator-to-the-cloud).

Az a **eszközök** lapon létesíthet a frissített típusú példányok:

![Vegye fel a frissített hűtő](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Megtekintheti az új **belső hőmérséklet** a szimulált eszköz telemetriai adatokat.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Hozzon létre egy új eszköz típusa
> * Egyéni eszközviselkedés szimulálása
> * Új eszköz-típus hozzáadása az irányítópulton
> * Egyéni telemetriai adatokat küldhet egy meglévő eszközt típusból

Most már rendelkezik megtanulta, hogyan szabhatja testre az szimuláció szolgáltatást. A javasolt következő lépésre megtudhatja, hogyan [egy fizikai eszköz csatlakozni a távoli felügyeleti megoldás](iot-suite-connecting-devices-node.md).

Fejlesztői kapcsolatos további információkért a távoli felügyeleti megoldás az alábbi témakörben talál:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
