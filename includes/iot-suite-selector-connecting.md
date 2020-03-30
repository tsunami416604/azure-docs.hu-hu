---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179555"
---
> [!div class="op_single_selector"]
> * [C a Windows rendszeren](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C Linuxon](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C Raspberry Pi-on](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (általános)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js Raspberry Pi-on](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

Ebben az oktatóanyagban egy **hűtőeszközt** valósít meg, amely a következő telemetriát küldi a távoli figyelési [megoldás gyorsítójának:](../articles/iot-accelerators/about-iot-accelerators.md)

* Hőmérséklet
* Nyomás
* Páratartalom

Az egyszerűség kedvéért a kód minta telemetriai értékeket hoz létre a **hűtőhez.** Kiterjesztheti a mintát, ha valódi érzékelőket csatlakoztat az eszközhöz, és valódi telemetriát küld.

A mintaeszköz:

* Metaadatokat küld a megoldásnak a képességeinek leírására.
* Válaszol a megoldás **Eszközök** lapjáról indított műveletekre.
* Válaszol a megoldás **Eszközök** lapjáról küldött konfigurációs módosításokra.

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Előkészületek

Mielőtt bármilyen kódot írna az eszközhöz, telepítse a távoli figyelési megoldásgyorsítót, és adjon hozzá egy új valódi eszközt a megoldáshoz.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>A távfigyelési megoldás gyorsítójának üzembe helyezése

Az oktatóanyagban létrehozott **Hűtő** eszköz adatokat küld a [távoli figyelési](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) megoldás gyorsító egy példányának. Ha még nem kiépítette a távfigyelési megoldásgyorsítót az Azure-fiókjában, [olvassa el a Távfigyelési megoldásgyorsító üzembe helyezése című témakört.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Amikor a távoli figyelési megoldás telepítési folyamata befejeződik, az **Indítás** gombra kattintva nyissa meg a megoldás irányítópultját a böngészőben.

![A megoldás irányítópultja](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Eszköz hozzáadása a távfigyelési megoldáshoz

> [!NOTE]
> Ha már hozzáadott egy eszközt a megoldásban, kihagyhatja ezt a lépést. A következő lépéshez azonban szükség van az eszköz kapcsolati karakterláncára. Lekérheti az eszköz kapcsolati karakterláncát az [Azure Portalon](https://portal.azure.com) vagy az [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI eszközzel.

Ahhoz, hogy egy eszköz csatlakozzon a megoldásgyorsítóhoz, érvényes hitelesítő adatokkal azonosítania kell magát az IoT Hubhoz. Lehetősége van arra, hogy mentse az eszköz kapcsolati karakterláncot, amely tartalmazza ezeket a hitelesítő adatokat, amikor hozzáadja az eszközt a megoldáshoz. Az oktatóanyag későbbi részében az eszközkapcsolati karakterláncot is beilleszti az ügyfélalkalmazásba.

Ha eszközt szeretne hozzáadni a távoli figyelési megoldáshoz, hajtsa végre az alábbi lépéseket a megoldás **Eszközkezelő** lapján:

1. Válassza **a + Új eszköz**lehetőséget , majd a **Real** lehetőséget **eszköztípusként:**

    ![Valós eszköz hozzáadása](media/iot-suite-selector-connecting/devicesprovision.png)

1. Adja meg **a fizikai-hűtőt** eszközazonosítóként. Válassza a **Szimmetrikus billentyű** és az **Automatikus létrehozás i akta** beállításokat:

    ![Eszközbeállítások megadása](media/iot-suite-selector-connecting/devicesoptions.png)

1. Válassza az **Alkalmaz** lehetőséget. Ezután jegyezze fel az **eszközazonosítót,** az **elsődleges kulcsot**és a **Kapcsolati karakterlánc elsődleges** kulcsértékeit:

    ![Hitelesítő adatok beolvasása](media/iot-suite-selector-connecting/credentials.png)

Most már hozzáadott egy valódi eszközt a távoli figyelési megoldás gyorsítóhoz, és megjegyezte az eszköz kapcsolati karakterláncát. A következő szakaszokban valósítja meg az ügyfélalkalmazást, amely az eszköz kapcsolati karakterláncot használja a megoldáshoz való csatlakozáshoz.

Az ügyfélalkalmazás megvalósítja a **Chiller** beépített hűtőeszköz-modellt. A megoldásgyorsító eszközmodellje a következőket határozza meg az eszközről:

* Az eszköz által a megoldásnak jelentendő tulajdonságok. A **Hűtőeszköz** például a belső vezérlőprogramjával és helyével kapcsolatos információkat jelent.
* Az eszköz által a megoldásnak küldött telemetriai adatok típusai. A **Hűtőeszköz** például hőmérsékleti, páratartalom- és nyomásértékeket küld.
* A megoldásból az eszközön való futtatásra ütemezhető módszerek. Például egy **hűtőeszköznek** végre kell hajtania **az Újraindítás**, A **FirmwareUpdate**, a **EmergencyValveRelease**és **a IncreasePressure** metódusokat.
