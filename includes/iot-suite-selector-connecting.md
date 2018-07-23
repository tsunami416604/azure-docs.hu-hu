---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 780a215b66fec845bc1df639fedda870881b4027
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39189405"
---
> [!div class="op_single_selector"]
> * [C Windowson](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C Linuxon](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js (általános)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js Raspberry Pi-on](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [C Raspberry Pi-on](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

Ez az oktatóanyag végrehajtása egy **hűtő** eszköz, amely a következő telemetriát küldi a távoli figyelési [megoldásgyorsító](../articles/iot-accelerators/about-iot-accelerators.md):

* Hőmérséklet
* Pressure
* Páratartalom

Az egyszerűség kedvéért a kódot állít elő, a minta telemetriaértékeket az **hűtő**. Az eszköz valódi érzékelők csatlakozik, és valós telemetria küldése sikerült terjessze ki a mintát.

A mintául szolgáló eszköz is:

* Elküldi a metaadatokat a megoldás írja le annak képességeit.
* Reagáljon a indított műveleteket az **eszközök** lap a megoldásban.
* Konfigurációs módosítások megválaszolja küldött a **eszközök** lap a megoldásban.

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Előkészületek

Mielőtt bármilyen kódot írna az eszközhöz, a távoli figyelési megoldásgyorsító üzembe helyezése, és adjon hozzá egy új fizikai eszköz a megoldáshoz.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító üzembe helyezése

A **hűtő** ebben az oktatóanyagban létrehozott eszköz adatokat küld egy példányát a [távoli megfigyelés](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) megoldásgyorsító. Ha még nem építette a távoli figyelési megoldásgyorsító az Azure-fiókjával, [a távoli figyelési megoldásgyorsító üzembe helyezése](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

Ha a távoli figyelési megoldáshoz a telepítési folyamat befejezését követően kattintson **indítsa el a** a megoldás irányítópultjának megnyitásához a böngészőben.

![A megoldás irányítópultja](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Az eszköz a távoli figyelési megoldás hozzáadása

> [!NOTE]
> Ha már felvett egy eszközt a megoldásában, kihagyhatja ezt a lépést. Azonban a következő lépésben szükség van az eszköz kapcsolati karakterláncát. Kérheti, hogy az eszköz kapcsolati karakterláncot a [az Azure portal](https://portal.azure.com) vagy a [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) parancssori eszköz.

A megoldásgyorsító csatlakozni egy eszközhöz, kell azonosítania magát az IoT Hub érvényes hitelesítő adatokkal. A gyakorlati kipróbálásra menteni az eszköz kapcsolati karakterláncának, amely tartalmazza ezeket a hitelesítő adatokat, amikor hozzáadja az eszközt, a megoldás. Az eszköz kapcsolati karakterláncának felvétel az ügyfélalkalmazás az oktatóanyag későbbi részében.

A távoli figyelési megoldáshoz egy eszköz hozzáadásához kövesse az alábbi lépéseket a **eszközök** lap a megoldásban:

1. Válasszon **+ új eszköz**, és válassza a **fizikai** , a **eszköztípus**:

    ![Fizikai eszköz hozzáadása](media/iot-suite-selector-connecting/devicesprovision.png)

1. Adja meg **fizikai-hűtő** az eszköz azonosítójával. Válassza ki a **szimmetrikus kulcs** és **kulcsok automatikus létrehozása** beállítások:

    ![Válassza ki az eszköz beállításai](media/iot-suite-selector-connecting/devicesoptions.png)

1. Válasszon **alkalmazása**. Majd jegyezze fel a **Eszközazonosító**, **elsődleges kulcs**, és **kapcsolati karakterlánc – elsődleges kulcs** értékeket:

    ![Olvashatók be hitelesítő adatok](media/iot-suite-selector-connecting/credentials.png)

Sikeresen hozzáadott egy fizikai eszköz a távoli figyelési megoldásgyorsító és feljegyzett az eszköz kapcsolati karakterláncát. A következő szakaszokban az ügyfélalkalmazás, amely az eszköz kapcsolati karakterláncát használja a megoldáshoz történő csatlakoztatásáról valósíthatja meg.

Az ügyfélalkalmazás valósítja meg a beépített **hűtő** eszköz modellje. Egy megoldás gyorsító eszközmodell megadja egy eszközről a következőket:

* A tulajdonságok, jelentett a megoldáshoz. Ha például egy **hűtő** jelentett információ a belső vezérlőprogram és helyét.
* A típusú telemetriát az eszköz küld a megoldásnak. Ha például egy **hűtő** hőmérsékletet, a páratartalmat és a pressure értékeit.
* A módszerek ütemezheti a megoldásból származó futtatható az eszközön. Ha például egy **hűtő** eszköz musí implementovat **újraindítás**, **FirmwareUpdate**, **EmergencyValveRelease**, és  **IncreasePressure** módszereket.