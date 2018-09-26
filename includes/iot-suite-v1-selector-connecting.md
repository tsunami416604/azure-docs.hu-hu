---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 55920b6c147626f68f51b9e0479949330c71a748
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47102987"
---
> [!div class="op_single_selector"]
> * [C Windowson](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [C Linuxon](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Forgatókönyv áttekintése
Ebben az esetben olyan eszközt hoz létre, amely a következő telemetriát küldi az [előre konfigurált][lnk-what-are-preconfig-solutions] távoli figyelési megoldásnak:

* Külső hőmérséklet
* Belső hőmérséklet
* Páratartalom

Az egyszerűség kedvéért az eszközön lévő kód mintaértékeket hoz létre, de javasoljuk, hogy terjessze ki a mintát valós érzékelők az eszközhöz csatlakoztatása és valós telemetria küldése révén.

Az eszköz a megoldás irányítópultjáról indított metódusokra és a megoldás irányítópultján beállított tulajdonságértékekre is tud válaszolni.

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

## <a name="before-you-start"></a>Előkészületek
Mielőtt bármilyen kódot írna az eszközhöz, ki kell építenie az előre konfigurált távoli figyelési megoldást, és meg kell adnia egy új egyéni eszközt ebben a megoldásban.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Az előre konfigurált távoli figyelési megoldás kiépítése
Az ebben az oktatóanyagban létrehozott eszköz adatokat küld az előre konfigurált [távoli figyelési][lnk-remote-monitoring] megoldásnak. Ha még nem építette ki az előre konfigurált távoli figyelési megoldást az Azure-fiókban, használja a következő lépéseket:

1. Az a <https://www.azureiotsolutions.com/> kattintson **+** megoldást hozhat létre.
2. Kattintson a **Kiválasztás** elemre a **Távoli figyelés** panelen a megoldás létrehozásához.
3. A **Távoli figyelési megoldás létrehozása** oldalon írjon be egy **megoldásnevet**, válassza ki a **Régiót**, ahová üzembe szeretné helyezni azt, majd válassza ki a használni kívánt Azure-előfizetést. Ezután kattintson a **Megoldás létrehozása** parancsra.
4. Várja meg, amíg befejeződik a kiépítési folyamat.

> [!WARNING]
> Az előre konfigurált megoldások számlázható Azure-szolgáltatásokat használnak. A felesleges költségek elkerülése érdekében ügyeljen arra, hogy eltávolítsa az előre konfigurált megoldást az előfizetésből, amikor végzett annak használatával. Teljes mértékben eltávolíthatja előre konfigurált megoldást az előfizetésből meglátogatják a <https://www.azureiotsolutions.com/> lapot.
> 
> 

A távoli figyelési megoldás kiépítésének befejezte után kattintson az **Indítás** gombra a megoldás irányítópultjának a böngészőben történő megnyitásához.

![A megoldás irányítópultja][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Az eszköz kiépítése a távoli figyelési megoldásban
> [!NOTE]
> Ha már kiépített egy eszközt a megoldásában, kihagyhatja ezt a lépést. Ismernie kell az eszköz hitelesítő adatait az ügyfélalkalmazás létrehozásakor.
> 
> 

Ahhoz, hogy egy eszköz előre konfigurált megoldáshoz csatlakozhasson, érvényes hitelesítő adatokkal kell azonosítania magát az IoT Hubon. A megoldás irányítópultjáról kérheti le az eszköz hitelesítő adatait. Az oktatóanyagban később megadja az eszköz hitelesítő adatait az ügyfélalkalmazásban.

Ha eszközt szeretne hozzáadni a távoli figyelési megoldáshoz, végezze el a következő lépéseket a megoldás irányítópultján:

1. Az irányítópult bal alsó részén kattintson az **Eszköz hozzáadása** parancsra.
   
   ![Eszköz hozzáadása][1]
2. Az **Egyéni eszköz** panelen kattintson az **Új hozzáadása** parancsra.
   
   ![Egyéni eszköz hozzáadása][2]
3. Válassza a **Meghatározom a saját eszközazonosítómat** elemet. Írjon be egy eszközazonosítót, például a **sajáteszköz** kifejezést, kattintson az **Azonosító ellenőrzése** parancsra, hogy meggyőződhessen arról, hogy a név még nincs használatban, majd kattintson a **Létrehozás** parancsra az eszköz kiépítéséhez.
   
   ![Eszközazonosító hozzáadása][3]
4. Jegyezze fel az eszköz hitelesítő adatait (az eszköz azonosítóját, az IoT Hub-eszköznevet és az eszköz kulcsát). Az ügyfélalkalmazásnak szüksége van ezekre az értékekre, hogy a távoli figyelési megoldáshoz csatlakozhasson. Ezután kattintson a **Done** (Kész) gombra.
   
    ![Eszköz hitelesítő adatainak megtekintése][4]
5. Válassza ki az eszközt a megoldás irányítópultján lévő eszközlistából. Ezután az **Eszköz részletei** panelen kattintson az **Eszköz engedélyezése** parancsra. Az eszköz mostantól **Fut** állapotú. A távoli figyelési megoldás ettől kezdve telemetriát fogadhat az eszközről és metódusokat hívhat meg az eszközön.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/