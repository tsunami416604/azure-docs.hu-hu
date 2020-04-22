---
title: Azure IoT Edge-eszköz hozzáadása az Azure IoT Centralhoz | Microsoft dokumentumok
description: Operátorként adjon hozzá egy Azure IoT Edge-eszközt az Azure IoT Central alkalmazáshoz
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758161"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Azure IoT Edge-eszköz hozzáadása az Azure IoT Central alkalmazáshoz

*Ez a cikk a megoldáskészítőkre és az eszközfejlesztőkre vonatkozik.*

Ez az oktatóanyag bemutatja, hogyan konfigurálhat és adhat hozzá egy Azure IoT Edge-eszközt az Azure IoT Central-alkalmazáshoz. Az oktatóanyag egy IoT Edge-kompatibilis Linux-alapú virtuális gépet (VM) használ az Azure Marketplace-ről egy IoT Edge-eszköz szimulálására. Az IoT Edge-eszköz egy modult használ, amely szimulált környezeti telemetriai adatokat hoz létre. A telemetriai adatokat az IoT Central-alkalmazás irányítópultján tekintheti meg.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközsablon létrehozása IoT Edge-eszközhöz
> * IoT Edge-eszköz létrehozása az IoT Centralban
> * Szimulált IoT Edge-eszköz üzembe helyezése Linux os virtuális gépre

## <a name="prerequisites"></a>Előfeltételek

Végezze el az [Azure IoT Central alkalmazás](./quick-deploy-iot-central.md) létrehozása rövid útmutatót egy IoT Central alkalmazás létrehozásához az egyéni alkalmazás > egyéni **alkalmazássablon** használatával.

Az oktatóanyag lépései végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Töltse le az IoT Edge-jegyzékfájl a GitHubról. Kattintson a jobb gombbal az alábbi hivatkozásra, majd válassza a **Mentés hivatkozást:** [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Eszközsablon létrehozása

Ebben a szakaszban hozzon létre egy eszközsablont egy IoT Edge-eszközhöz, amely csatlakozik az IoT Central-alkalmazáshoz. Az első lépésekhez importál egy IoT Edge-jegyzékfájlt, majd módosítja a sablont telemetriai definíciók és nézetek hozzáadásához:

### <a name="import-manifest-to-create-template"></a>Jegyzékfájl importálása sablon létrehozásához

Eszközsablon létrehozása IoT Edge-jegyzékfájlból:

1. Az IoT Central alkalmazásban keresse meg az **Eszközsablonok at,** és válassza a **+ Új**lehetőséget.

1. A **Sablon típusának kiválasztása** lapon válassza ki az **Azure IoT Edge** csempét. Ezután válassza **a Tovább: Testreszabás lehetőséget.**

1. Az **Azure IoT Edge központi telepítési jegyzékfájljának feltöltése** lapon válassza a **Tallózás** lehetőséget a korábban letöltött **EnvironmentalSensorManifest.json** feltöltéséhez. Ezután válassza **a Tovább: Véleményezés**lehetőséget.

1. A **Véleményezés** lapon válassza a **Létrehozás gombot.**

1. A sablon létrehozása után módosítsa a nevét *Környezeti érzékelő peremhálózati eszközre.*

1. A jegyzékben definiált két tulajdonság megtekintéséhez válassza a Felület **kezelése** lehetőséget a **SimulatedTemperatureSensor** modulban:

![IoT Edge-jegyzékfájlból létrehozott eszközsablon](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Telemetria hozzáadása a jegyzékhez

Az IoT Edge-jegyzékfájl nem határozza meg a modul által küldött telemetriai adatokat. Hozzá kell adnia a telemetriai definíciókat az eszközsablonhoz. A **SimulatedTemperatureSensor** modul telemetriai üzeneteket küld, amelyek a következő JSON-hoz hasonlóan jelennek meg:

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

A telemetriai definíciók hozzáadása az eszközsablonhoz:

1. Válassza ki a **Felület kezelése** a környezeti **érzékelő peremhálózati eszköz** sablonban.

1. Válassza **a + Add capability**lehetőséget . Adja meg a *gépet* **megjelenítendő névként,** és győződjön meg arról, hogy a **Képesség típusa** **Telemetriai.**

1. Válassza az **Objektum** sématípusként lehetőséget, majd válassza a **Definiálás gombot.** Az objektumdefiníciós lapon adja hozzá a *hőmérsékletet* és a *nyomást* **Dupla** típusú attribútumként, majd válassza **az Alkalmaz**lehetőséget.

1. Válassza **a + Add capability**lehetőséget . Adja meg a *környezeti értéket* **megjelenítendő névként,** és győződjön meg arról, hogy a Képesség **típusa** **Telemetriai.**

1. Válassza az **Objektum** sématípusként lehetőséget, majd válassza a **Definiálás gombot.** Az objektumdefiníciós lapon adja hozzá a *hőmérsékletet* és a *páratartalmat* **Dupla** típusú attribútumként, majd válassza **az Alkalmaz**lehetőséget.

1. Válassza **a + Add capability**lehetőséget . Adja meg az *időlétrehozott* **megjelenítendő névként,** és győződjön meg arról, hogy a **Képesség típusa** **Telemetriai.**

1. Válassza a **DateTime** lehetőséget sématípusként.

1. A sablon frissítéséhez válassza a **Mentés** gombot.

A **Manage** felület most már tartalmazza a **gép,** **környezeti**, és **időLétrehozott** telemetriai típusok:

![Felület gép- és környezeti telemetriai típusokkal](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Nézetek hozzáadása sablonhoz

Az eszközsablon még nem rendelkezik olyan nézettel, amely lehetővé teszi, hogy egy operátor az IoT Edge-eszköz telemetriáját lássa. Nézet hozzáadása az eszközsablonhoz:

1. Válassza a **Nézetek lehetőséget** a Környezeti **érzékelő peremhálózati eszközsablonjában.**

1. A **Kijelölés új nézet hozzáadásához** jelölje ki **az eszköz** megjelenítése csempét.

1. Módosítsa a nézet nevét *Az IoT Edge-eszköz telemetria nézetére.*

1. Válassza ki a **környezeti** és **a gép** telemetriai típusok. Ezután válassza **a Csempe hozzáadása**lehetőséget.

1. Válassza a **Mentés** lehetőséget az **IoT Edge-eszköz telemetriai** nézetének mentéséhez.

![Eszközsablon telemetriai nézetgel](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>A sablon közzététele

Mielőtt hozzáadhatna egy eszközt, amely a **Környezeti érzékelő peremhálózati eszközt** használja, közzé kell tennie a sablont.

Nyissa meg a **Környezeti érzékelő peremhálózati eszköz sablonját,** és válassza **a Közzététel**lehetőséget. Ezután a közzététel **gombra** a sablon közzétételéhez válassza a következőket:

![Az eszközsablon közzététele](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>IoT Edge-eszköz hozzáadása

Most, hogy közzétette a **Környezeti érzékelő peremhálózati eszköz** sablonját, hozzáadhat egy eszközt az IoT Central alkalmazáshoz:

1. Az IoT Central alkalmazásban keresse meg az **Eszközök** lapot, és válassza **a Környezeti érzékelő peremhálózati eszköz** lehetőséget az elérhető sablonok listájában.

1. Jelölje **+** be, ha új eszközt szeretne hozzáadni a sablonból. Az **Új eszköz létrehozása** lapon válassza a **Létrehozás gombot.**

Most már van egy új eszköze, amelynek állapota **Regisztrált:**

![Az eszközsablon közzététele](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Az eszköz hitelesítő adatainak beszereznie

Ha az oktatóanyag későbbi részében telepíti az IoT Edge-eszközt, szüksége van a hitelesítő adatokra, amelyek lehetővé teszik, hogy az eszköz csatlakozzon az IoT Central-alkalmazáshoz. Az eszköz hitelesítő adatainak bekéselése:

1. Az **Eszköz** lapon jelölje ki a létrehozott eszközt.

1. Kattintson a **Csatlakozás** gombra.

1. Az **Eszközkapcsolat** lapon jegyezze fel az **azonosító hatókört,** az **eszközazonosítót**és az **elsődleges kulcsot.** Ezeket az értékeket később használhatja.

1. Kattintson a **Bezárás** gombra.

Ezzel befejezte az IoT Central-alkalmazás konfigurálását, hogy egy IoT Edge-eszköz csatlakozzon.

## <a name="deploy-an-iot-edge-device"></a>IoT Edge-eszköz üzembe helyezése

Ebben az oktatóanyagban egy Azure IoT Edge-kompatibilis Linux virtuális gép, az Azure-ban létrehozott egy IoT Edge-eszköz szimulálása. Az IoT Edge-kompatibilis virtuális gép létrehozása:

1. Keresse meg az [Azure IoT Edge-et az Ubuntun](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) az Azure Marketplace-en. Ezután válassza **a Get it now**lehetőséget.

1. Az **Alkalmazás létrehozása az Azure-ban** lapon válassza a **Continue (Folytatás)** lehetőséget. Ez a hivatkozás az Azure Portalra vezet, ahol előfordulhat, hogy be kell jelentkeznie az Azure-előfizetésbe.

1. Az **Azure IoT Edge ubuntuoldalán** az Azure Portalon válassza a **Létrehozás lehetőséget.**

1. A **Virtuális gép létrehozása > alapjai** lapon:

    - Válassza ki az Azure-előfizetését.
    - Hozzon létre egy **iot-edge-devices**nevű új erőforráscsoportot.
    - Használja a virtuális gép nevét: **iotedgevm**.
    - Válassza ki az Önhöz legközelebbi régiót.
    - Állítsa a hitelesítéstípusát **Jelszó .**
    - Válasszon felhasználónevet és jelszót.
    - A többi beállítást az alapértelmezett értékekre hagyhatja.
    - Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Az ellenőrzés befejezése után válassza a **Létrehozás gombot.**

Néhány perc múlva, amikor a központi telepítés befejeződött, válassza az Ugrás az **erőforrásra**lehetőséget.

### <a name="provision-vm-as-an-iot-edge-device"></a>Virtuális gép kiépítése IoT Edge-eszközként 

Virtuális gép kiépítése IoT Edge-eszközként:

1. A **Támogatás + hibaelhárítás** csoportban válassza a **Soros konzol**lehetőséget.

1. A kérdés megtekintéséhez nyomja **le** az `login:` Enter billentyűt. Adja meg felhasználónevét és jelszavát a bejelentkezéshez.

1. Futtassa a következő parancsot az IoT Edge futásidejű verziójának ellenőrzéséhez. Abban az időben az írás, a változat 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. A `nano` szerkesztő segítségével nyissa meg az IoT Edge config.yaml fájlt:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Görgessen lefelé, amíg meg nem jelenik. `# Manual provisioning configuration` Fűzzön megjegyzést a következő három sorhoz, ahogy az a következő kódrészletben látható:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Görgessen lefelé, amíg meg nem jelenik. `# DPS symmetric key provisioning configuration` A következő nyolc sor megjegyzésének kimegjegyzést iszágítása a következő kódrészletben látható módon:

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

1. Cserélje `{scope_id}` le az **azonosító hatóköraz** Ön készített egy feljegyzést korábban.

1. Cserélje `{registration_id}` le azt az **eszközazonosítót,** amelyről korábban feljegyzést készített.

1. Cserélje `{symmetric_key}` le a korábban feljegyzett **elsődleges kulcsra.**

1. Mentse a módosításokat (**Ctrl-O**) és `nano` lépjen ki **(Ctrl-X**) a szerkesztőből.

1. Az IoT Edge démon újraindításához futtassa a következő parancsot:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Az IoT Edge-modulok állapotának ellenőrzéséhez futtassa a következő parancsot:

    ```bash
    iotedge list
    ```

    A kimenet a következőre néz ki:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

A szimulált IoT Edge-eszköz most fut a virtuális gép. Az IoT Central-alkalmazásban az eszköz állapota most **már ki van építve** az **Eszközök** lapon:

![Kiépített eszköz](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

A telemetriai adatok at az **IoT Edge-eszköz telemetriai** lapján láthatja:

![Eszköztelemetria](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

A **Modulok** lap az IoT Edge modulok állapotát mutatja:

![Eszköztelemetria](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>További lépések

Eszközfejlesztőként most, hogy megtanulta, hogyan dolgozhat az IoT Edge-eszközökkel és hogyan kezelheti az IoT Centralban, a következő javasolt lépés a következő:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Csatlakozzon az Azure IoT Centralhoz](./concepts-get-connected.md)
