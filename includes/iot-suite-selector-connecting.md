---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67179555"
---
> [!div class="op_single_selector"]
> * [C Windows rendszeren](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C Linuxon](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C Raspberry Pi-on](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (általános)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js Raspberry Pi-on](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

Ebben az oktatóanyagban egy olyan **hűtő** eszközt fog megvalósítani, amely a következő telemetria küldi el a távoli figyelési [megoldás-gyorsító](../articles/iot-accelerators/about-iot-accelerators.md)számára:

* Hőmérséklet
* Nyomás
* Páratartalom

Az egyszerűség kedvéért a kód minta telemetria értékeket hoz létre a **hűtőhöz**. Kiterjesztheti a mintát úgy, hogy valódi érzékelőkkel csatlakozik az eszközhöz, és valós telemetria küld.

A minta eszköz is:

* Metaadatokat küld a megoldásnak a képességeinek leírásához.
* Válaszol a megoldás **eszközök** lapjáról indított műveletekre.
* Válaszol a megoldás **eszközök** lapjáról küldött konfigurációs változásokra.

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Előkészületek

Mielőtt bármilyen kódot írsz az eszközhöz, telepítse a távoli figyelési megoldás gyorsító eszközét, és adjon hozzá egy új valódi eszközt a megoldáshoz.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldás-gyorsító üzembe helyezése

Az oktatóanyagban létrehozott **Chiller** -eszköz adatokat küld a [távoli figyelési](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) megoldás-gyorsító egy példányára. Ha még nem telepítette a távoli figyelési megoldás gyorsító az Azure-fiókjában, tekintse meg [a távoli figyelési megoldás központi telepítése](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) című témakört.

Ha a távoli figyelési megoldás üzembe helyezési folyamata befejeződik, kattintson a **Launch (indítás** ) gombra a megoldás irányítópultjának a böngészőben való megnyitásához.

![A megoldás irányítópultja](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Eszköz hozzáadása a távoli figyelési megoldáshoz

> [!NOTE]
> Ha már hozzáadott egy eszközt a megoldásban, kihagyhatja ezt a lépést. A következő lépésben azonban szükség van az eszköz kapcsolati karakterláncára. Lekérheti az eszközhöz tartozó kapcsolatok karakterláncát a [Azure Portal](https://portal.azure.com) vagy az az [IOT](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI eszköz használatával.

Ahhoz, hogy egy eszköz csatlakozni tudjanak a megoldás-gyorsító szolgáltatáshoz, meg kell határoznia magát, hogy érvényes hitelesítő adatokkal IoT Hub. Lehetősége van arra, hogy mentse a hitelesítő adatokat tartalmazó eszköz-kapcsolódási karakterláncot, amikor hozzáadja az eszközt a megoldáshoz. Az oktatóanyag későbbi részében az alkalmazáshoz tartozó eszköz-csatlakoztatási karakterláncot kell megadnia az ügyfélalkalmazás számára.

Ha eszközt szeretne hozzáadni a távoli figyelési megoldáshoz, hajtsa végre a következő lépéseket a megoldás **Device Explorer** oldalán:

1. Válassza az **+ új eszköz**lehetőséget, majd válassza az **igazi** lehetőséget az **eszköz típusaként**:

    ![Valós eszköz hozzáadása](media/iot-suite-selector-connecting/devicesprovision.png)

1. Adja meg a **fizikai-hűtőt** az eszköz azonosítójaként. Válassza a **szimmetrikus kulcs** és az **automatikus kulcs generálása** lehetőséget:

    ![Eszközbeállítások kiválasztása](media/iot-suite-selector-connecting/devicesoptions.png)

1. Válassza az **Alkalmaz** lehetőséget. Ezután jegyezze fel az **eszköz azonosítóját**, az **elsődleges kulcsot**és a **kapcsolatok karakterláncának elsődleges kulcsának** értékét:

    ![Hitelesítő adatok beolvasása](media/iot-suite-selector-connecting/credentials.png)

Mostantól hozzáadta a távoli figyelési megoldáshoz tartozó gyorsított eszközt, és feljegyezte az eszköz kapcsolati karakterláncát. A következő részekben azt az ügyfélalkalmazás implementálja, amely az eszköz kapcsolati karakterláncát használja a megoldáshoz való kapcsolódáshoz.

Az ügyfélalkalmazás implementálja a beépített **hűtő** eszköz modelljét. A megoldás-gyorsító eszköz modellje a következőket határozza meg az eszközről:

* Az eszköz által a megoldásnak jelentett tulajdonságok. A **Chiller** -eszköz például a belső vezérlőprogram és a hely adatait jelenti.
* Az eszköz által a megoldásnak küldött telemetria típusai. A **hűtő** eszköz például hőmérséklet-, páratartalom-és nyomáskülönbség-értékeket küld.
* A megoldásról az eszközön való futtatásra ütemezett metódusok. A **hűtő** eszköznek például a **reboot**, a **FirmwareUpdate**, a **EmergencyValveRelease**és az **IncreasePressure** metódust kell végrehajtania.
