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
ms.openlocfilehash: 43acf33ec7787378595ad62540a868100bf587f7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
> [!div class="op_single_selector"]
> * [C Windowson](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C Linuxon](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (általános)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js Raspberry Pi-on](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C Raspberry Pi-on](../articles/iot-suite/iot-suite-connecting-pi-c.md)

Az oktatóanyag végrehajtása egy **hűtő** eszköz, amely a következő telemetriai adatokat küld a távoli megfigyelési [megoldásgyorsító](../articles/iot-suite/iot-suite-what-are-solution-accelerators.md):

* Hőmérséklet
* nyomás
* Páratartalom

Az egyszerűség, a kódot állít elő, a telemetriai adatok mintaértékek a **hűtő**. A minta lehetett bővíteni a valódi érzékelők csatlakozik az eszközt, és valós telemetriai adatok küldését.

A minta eszköz is:

* A megoldás platformképességei leírására elküldi a metaadatokat.
* Válaszol-e a műveletek állapotváltozás aktiválja a **eszközök** lap a megoldásban.
* A konfigurációs módosítások megválaszolja küldjön a **eszközök** lap a megoldásban.

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Előkészületek

Kód írása az eszköz, mielőtt a távoli felügyeleti megoldásgyorsító központi telepítése, és a fizikai eszköz hozzáadása a megoldáshoz.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>A távoli felügyeleti megoldásgyorsító központi telepítése

A **hűtő** eszköz ebben az oktatóanyagban létrehozhat adatokat küld egy példányát a [távoli megfigyelési](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) megoldásgyorsító. Ha a távoli felügyeleti megoldásgyorsító még nem már megtörtént, az Azure-fiókjába, lásd: [a távoli felügyeleti megoldásgyorsító központi telepítése](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Ha a telepítési folyamat befejezte a távoli felügyeleti megoldás, gombra kattintva **indítása** a megoldás irányítópult megnyitása a böngészőben.

![A megoldás irányítópultja](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Az eszköz hozzáadása a távoli felügyeleti megoldás

> [!NOTE]
> Ha már van egy eszközt a megoldásban, kihagyhatja ezt a lépést. Azonban a következő lépésre van szükség az eszköz kapcsolati karakterlánc. Egy eszköz kapcsolati karakterláncnak a következőről kérheti le a [Azure-portálon](https://portal.azure.com) vagy a [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) parancssori eszköz.

Egy eszköz csatlakozni a megoldásgyorsító azt kell azonosítja magát az IoT-központ érvényes hitelesítő adatok használatával. Hogy az eszköz kapcsolati karakterlánc, amely tartalmazza ezeket a hitelesítő adatokat, az eszköz a megoldás mentéséhez lehetőséget. Az eszköz kapcsolati karakterlánc szerepel az ügyfélalkalmazás az oktatóanyag későbbi részében.

Hozzáad egy eszközt a távoli felügyeleti megoldás, végezze el az alábbi lépéseket a **eszközök** lap a megoldásban:

1. Válasszon **+ új eszköz**, és válassza a **fizikai** , a **eszköztípus**:

    ![Fizikai eszköz hozzáadása](media/iot-suite-selector-connecting/devicesprovision.png)

1. Adja meg **fizikai-hűtő** , az eszközazonosító. Válassza ki a **szimmetrikus kulcs** és **automatikus kulcsok létrehozása** beállítások:

    ![Válassza ki az eszköz beállítások](media/iot-suite-selector-connecting/devicesoptions.png)

1. Válasszon **alkalmazása**. Majd jegyezze fel a **Eszközazonosító**, **elsődleges kulcs**, és **kapcsolati karakterlánc elsődleges kulcs** értékeket:

    ![Olvashatók be hitelesítő adatok](media/iot-suite-selector-connecting/credentials.png)

Hogy most már hozzáadott egy fizikai eszköz a távoli felügyeleti megoldásgyorsító és feljegyzett eszköz kapcsolati karakterláncában. A következő szakaszokban, amely az eszköz kapcsolati karakterláncát a megoldás való kapcsolódáshoz használ az ügyfélalkalmazás megvalósítása.

Az ügyfélalkalmazás valósítja meg a beépített **hűtő** eszközmodell. A megoldás gyorsító eszköz típusa határozza meg az alábbiakat az eszköz:

* Az eszköz jelentés készít a megoldás tulajdonságait. Például egy **hűtő** eszköz a belső vezérlőprogram és helyére vonatkozó jelent adatokat.
* A megoldás küld az eszköz a telemetriai adatok típusú. Például egy **hűtő** eszköz küld hőmérséklet, páratartalom és érték.
* A módszerek is ütemezheti a megoldásban való futtatható az eszközön. Például egy **hűtő** meg kell valósítania az eszköz **újraindítás**, **FirmwareUpdate**, **EmergencyValveRelease**, és  **IncreasePressure** módszerek.