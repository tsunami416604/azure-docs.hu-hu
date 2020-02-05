---
title: Azure IoT Edge eszköz hozzáadása az Azure IoT Centralhoz | Microsoft Docs
description: Operátorként vegyen fel egy Azure IoT Edge eszközt az Azure IoT Central alkalmazásba
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: bf74784998de6bbad6310c48c24d6353512bff13
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027733"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Azure IoT Edge-eszköz hozzáadása az Azure IoT Central-alkalmazáshoz

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat és adhat hozzá egy Azure IoT Edge-eszközt az Azure IoT Central-alkalmazáshoz. Az oktatóanyag egy IoT Edge-kompatibilis linuxos virtuális gépet (VM) használ az Azure Marketplace-en egy IoT Edge-eszköz szimulálásához. A IoT Edge eszköz szimulált környezeti telemetria létrehozó modult használ. A telemetria a IoT Central alkalmazásban található irányítópulton tekintheti meg.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszköz sablonjának létrehozása IoT Edge eszközhöz
> * IoT Edge-eszköz létrehozása IoT Central
> * Szimulált IoT Edge eszköz üzembe helyezése Linux rendszerű virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central-alkalmazás létrehozásához az **egyéni alkalmazás > egyéni alkalmazásspecifikus** sablon használatával.

Az oktatóanyag lépéseinek elvégzéséhez aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Töltse le a IoT Edge manifest-fájlt a GitHubról. Kattintson a jobb gombbal a következő hivatkozásra, majd válassza a **hivatkozás mentése másként**: [EnvironmentalSensorManifest. JSON lehetőséget.](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Eszköz sablonjának létrehozása

Ebben a szakaszban egy IoT Edge eszközhöz tartozó sablont hoz létre, amely a IoT Central alkalmazáshoz csatlakozik. Egy IoT Edge jegyzékfájlt importálhat az első lépésekhez, majd a sablon módosításával telemetria-definíciókat és-nézeteket adhat hozzá:

### <a name="import-manifest-to-create-template"></a>Jegyzékfájl importálása sablon létrehozásához

Eszköz sablonjának létrehozása IoT Edge jegyzékfájlból:

1. A IoT Central alkalmazásban navigáljon az **eszközök sablonjaihoz** , és válassza az **+ új**lehetőséget.

1. A **sablon típusának kiválasztása** lapon válassza a **Azure IoT Edge** csempét. Ezután válassza a **Tovább: testreszabás**lehetőséget.

1. Az **Azure IoT Edge üzembe helyezési jegyzék feltöltése** lapon válassza a **Tallózás** lehetőséget a korábban letöltött **EnvironmentalSensorManifest. JSON** feltöltéséhez. Ezután válassza a **Tovább: felülvizsgálat**lehetőséget.

1. Az **Áttekintés** lapon válassza a **Létrehozás**lehetőséget.

1. A sablon létrehozása után módosítsa a nevét a *környezeti érzékelő peremhálózati eszközére*.

1. Válassza a **kezelés** felületet a **SimulatedTemperatureSensor** modulban a jegyzékfájlban definiált két tulajdonság megtekintéséhez:

![IoT Edge jegyzékfájlból létrehozott eszköz sablonja](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Telemetria hozzáadása a jegyzékfájlhoz

Egy IoT Edge jegyzékfájl nem határozza meg a telemetria által küldött adatokat. A telemetria-definíciókat hozzá kell adnia az eszköz sablonhoz. A **SimulatedTemperatureSensor** modul a következő JSON-hoz hasonló telemetria-üzeneteket küld:

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

A telemetria-definíciók hozzáadása az eszköz sablonhoz:

1. Válassza a **kezelés** felületet a **környezeti érzékelő Edge-eszköz** sablonjában.

1. Válassza a **+ képesség hozzáadása**lehetőséget. Adja meg a gép **megjelenítendő nevét** , és győződjön meg arról, hogy a **telemetria** **típusa** .

1. Válassza az **objektum** lehetőséget a séma típusaként, majd válassza a **Definiálás**lehetőséget. Az objektum definíciója lapon adja hozzá a *hőmérsékletet* és a *nyomást* a **Double** típusú attribútumokként, majd válassza az **alkalmaz**lehetőséget.

1. Válassza a **+ képesség hozzáadása**lehetőséget. A **megjelenítendő név** mezőben adja meg a *környezeti* értéket, és győződjön meg arról, hogy a **képesség típusa** **telemetria**.

1. Válassza az **objektum** lehetőséget a séma típusaként, majd válassza a **Definiálás**lehetőséget. Az objektum definíciója lapon adja hozzá a *hőmérséklet* és a *páratartalom* értéket a **Double** típusú attribútumoknál, majd válassza az **alkalmaz**lehetőséget.

1. Válassza a **+ képesség hozzáadása**lehetőséget. Írja be a timeCreated **nevet a megjelenítendő név** értékre, és győződjön meg arról, hogy a **képesség típusa** **telemetria**.

1. Válassza a **datetime** értéket a séma típusaként.

1. A sablon frissítéséhez válassza a **Mentés** lehetőséget.

A **kezelés** felület mostantól tartalmazza a **gép**-, a **környezeti**és a **timeCreated** telemetria-típusokat:

![Felület gépi és környezeti telemetria-típusokkal](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Nézetek hozzáadása a sablonhoz

Az eszköz sablonja még nem rendelkezik olyan nézettel, amely lehetővé teszi, hogy az operátor megtekintse a telemetria a IoT Edge eszközről. Nézet hozzáadása az eszköz sablonhoz:

1. Válassza a **views (nézetek** ) lehetőséget a **környezeti érzékelő Edge-eszköz** sablonjában.

1. Az **új nézet hozzáadása** lapon válassza az **eszköz megjelenítése** csempét.

1. Módosítsa a nézet nevét *IoT Edge eszköz telemetria megtekintéséhez*.

1. Válassza ki a **környezeti** és a **gépi** telemetria típusait. Ezután válassza a **csempe hozzáadása**lehetőséget.

1. Válassza a **Mentés** lehetőséget a **nézet mentéséhez IoT Edge eszköz telemetria** nézetét.

![Eszköz sablonja telemetria nézettel](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>A sablon közzététele

Ahhoz, hogy hozzáadjon egy olyan eszközt, amely a **környezeti érzékelők peremhálózati eszközének** sablonját használja, közzé kell tennie a sablont.

Navigáljon a **környezeti érzékelő Edge-eszköz** sablonhoz, és válassza a **Közzététel**lehetőséget. Ezután válassza a **Közzététel** lehetőséget a sablon közzétételéhez:

![Az eszköz közzétételének közzététele](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>IoT Edge eszköz hozzáadása

Most, hogy közzétette a **környezeti érzékelők peremhálózati eszközének** sablonját, hozzáadhat egy eszközt a IoT Central alkalmazáshoz:

1. A IoT Central alkalmazásban navigáljon az **eszközök** lapra, és válassza a **környezeti érzékelő Edge-eszköz** lehetőséget az elérhető sablonok listájában.

1. Válassza a **+** lehetőséget, ha új eszközt szeretne hozzáadni a sablonból. Az **új eszköz létrehozása** lapon válassza a **Létrehozás**lehetőséget.

Most már rendelkezik egy új, **regisztrált**állapottal rendelkező eszközzel:

![Az eszköz közzétételének közzététele](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Az eszköz hitelesítő adatainak beolvasása

Amikor az oktatóanyag későbbi részében telepíti a IoT Edge eszközt, szüksége lesz a hitelesítő adatokra, amelyek lehetővé teszik, hogy az eszköz csatlakozhasson a IoT Central alkalmazáshoz. Az eszköz hitelesítő adatainak lekérése:

1. Az **eszköz** lapon válassza ki a létrehozott eszközt.

1. Kattintson a **Csatlakozás** gombra.

1. Az **eszköz-kapcsolatok** lapon jegyezze fel az **azonosító hatókörét**, az **eszköz azonosítóját**és az **elsődleges kulcsot**. Ezeket az értékeket később használhatja.

1. Válassza a **Bezárás**lehetőséget.

Ezzel befejezte a IoT Central alkalmazás konfigurálását, hogy lehetővé váljon IoT Edge-eszköz csatlakoztatása.

## <a name="deploy-an-iot-edge-device"></a>IoT Edge eszköz üzembe helyezése

Ebben az oktatóanyagban egy Azure IoT Edge-kompatibilis linuxos virtuális gépet használ, amelyet az Azure-ban hoztak létre egy IoT Edge-eszköz szimulálásához. A IoT Edge-kompatibilis virtuális gép létrehozása:

1. Az Azure Marketplace-en navigáljon [Azure IoT Edge az Ubuntuhoz](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) . Ezután válassza a **Letöltés most**lehetőséget.

1. Az **alkalmazás létrehozása az Azure-ban** lapon válassza a **Folytatás**lehetőséget. Ez a hivatkozás végigvezeti a Azure Portal, ahol előfordulhat, hogy be kell jelentkeznie az Azure-előfizetésbe.

1. A Azure Portal **Ubuntu** lapjának Azure IoT Edge válassza a **Létrehozás**lehetőséget.

1. A **virtuális gép létrehozása > alapjai** lapon:

    - Válassza ki az Azure-előfizetését.
    - Hozzon létre egy új erőforráscsoportot **IOT-Edge-Devices**néven.
    - Használja a virtuális gép nevét: **iotedgevm**.
    - Válassza ki a legközelebbi régiót.
    - Állítsa a hitelesítési típust **jelszó**értékre.
    - Válassza ki a felhasználónevet és a jelszót.
    - A többi beállítást az alapértelmezett értékekre is kihagyhatja.
    - Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Ha az ellenőrzés befejeződött, válassza a **Létrehozás**lehetőséget.

Néhány perc elteltével az üzembe helyezés befejezésekor válassza az **Ugrás erőforráshoz**lehetőséget.

### <a name="provision-vm-as-an-iot-edge-device"></a>Virtuális gép kiépítése IoT Edge eszközként 

Virtuális gép kiépítése IoT Edge eszközként:

1. A **támogatás + hibaelhárítás** szakaszban válassza a **Serial Console**lehetőséget.

1. Nyomja le az **ENTER** billentyűt az `login:` prompt megjelenítéséhez. A bejelentkezéshez adja meg felhasználónevét és jelszavát.

1. Futtassa a következő parancsot a IoT Edge Runtime verziójának vizsgálatához. Az írás időpontjában a verzió 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Nyissa meg a IoT Edge config. YAML fájlt a `nano` Editor használatával:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Görgessen lefelé, amíg meg nem jelenik a `# Manual provisioning configuration`. Tegye megjegyzésbe a következő három sort az alábbi kódrészletben látható módon:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Görgessen lefelé, amíg meg nem jelenik a `# DPS symmetric key provisioning configuration`. A következő nyolc sor megjegyzésének visszalépése az alábbi kódrészletben látható módon:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Cserélje le a `{scope_id}`t az **azonosító hatókörre** , és jegyezze fel korábban.

1. Cserélje le a `{registration_id}`t a korábban létrehozott **eszköz-azonosítóra** .

1. Cserélje le a `{symmetric_key}`t az **elsődleges kulccsal** , amelyet korábban jegyzett készített.

1. Mentse a módosításokat (**CTRL-O**), és zárja be (**CTRL-X**) a `nano` szerkesztőt.

1. Futtassa a következő parancsot a IoT Edge démon újraindításához:

    ```bash
    sudo systemctl restart iotedge
    ```

1. A IoT Edge modulok állapotának megtekintéséhez futtassa a következő parancsot:

    ```bash
    iotedge list
    ```

    A kimenet a következőhöz hasonlóan néz ki:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>A telemetria megtekintése

A szimulált IoT Edge eszköz mostantól fut a virtuális gépen. A IoT Central alkalmazásban az eszköz állapota most az **eszközök** lapon lett **kiépítve** :

![Kiépített eszköz](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

A telemetria a **IoT Edge eszköz telemetria megtekintése** lapon tekintheti meg:

![Eszköz telemetria](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

A **modulok** lap a IoT Edge modulok állapotát jeleníti meg:

![Eszköz telemetria](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan dolgozhat és kezelhet IoT Edge-eszközöket a IoT Centralban, a következő lépés a javasolt lépés:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Transzparens átjáró konfigurálása](../../iot-edge/how-to-create-transparent-gateway.md)
