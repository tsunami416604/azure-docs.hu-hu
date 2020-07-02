---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801715"
---
Azure IoT Edge egyik fő funkciója, hogy a felhőből üzembe helyezhet egy kódot a IoT Edge-eszközökön. *IoT Edge modulok* tárolóként megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre összeállított modult helyezünk üzembe az [Azure Marketplace IoT Edge-modulok szakaszával](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül az azure-IoT hub.

Az ebben a szakaszban üzembe helyezett modul szimulál egy érzékelőt, és a generált adatokat küldi el. Ez a modul hasznos kódrészlet, ha első lépések a IoT Edgehoz, mivel a szimulált adat a fejlesztéshez és a teszteléshez használható. Ha pontosan látni szeretné a modult, akkor megtekintheti a [szimulált hőmérséklet-érzékelő forráskódját](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Az első modul Azure piactéren való üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktábla menüjének **automatikus eszközkezelés**területén válassza a **IoT Edge**lehetőséget.

1. Kattintson a céleszköz eszközének AZONOSÍTÓJÁRA az eszközök listájából.

1. A felső sávon válassza a **modulok beállítása**lehetőséget.

   ![Válassza a modulok beállítása lehetőséget az eszköz adatai lapon.](./media/iot-edge-deploy-module/select-set-modules.png)

1. A lap **IoT Edge modulok** szakaszában kattintson a **Hozzáadás** gombra, és válassza ki a **piactér modult** a legördülő menüből.

   ![Marketplace-modul hozzáadása](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. A **IoT Edge modul Marketplace**-en keressen rá a "szimulált hőmérséklet-érzékelő" kifejezésre, és válassza ki a modult.

1. Figyelje meg, hogy a SimulatedTemperatureSensor modul hozzá lett adva a IoT Edge modulok szakaszhoz, és a kívánt állapotot **futtatja**.

   Válassza a **tovább lehetőséget: útvonalakat** a varázsló következő lépéséhez.

   ![Ugrás a következő lépésre a hőmérséklet-érzékelő modul listázása után](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. A varázsló **útvonalak** lapján megadhatja, hogyan adja át az üzeneteket a modulok és a IoT hub között. Az útvonalak név/érték párok használatával vannak kialakítva. Ezen a lapon két útvonalnak kell megjelennie. Az **útvonal** nevű alapértelmezett útvonal az összes üzenetet elküldi a IoT hubnak (amely neve `$upstream` ). A rendszer automatikusan létrehoz egy **SimulatedTemperatureSensorToIoTHub** nevű második útvonalat, amikor hozzáadta a modult a piactéren. Ez az útvonal kifejezetten a szimulált hőmérséklet modulból a IoT Hubre küldi az összes üzenetet. Az alapértelmezett útvonalat törölheti, mert ebben az esetben redundáns lehet.

   Válassza a **Next (tovább): felülvizsgálat + létrehozás** lehetőséget a varázsló következő lépésének folytatásához.

   ![Törölje az alapértelmezett útvonalat, majd lépjen a következő lépésre.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. A varázsló **Áttekintés + létrehozás** lapján megtekintheti a IoT Edge eszközre telepített összes modult meghatározó JSON-fájlt. Figyelje meg, hogy a **SimulatedTemperatureSensor** modult is tartalmazza, valamint a két futásidejű modult, a **EdgeAgent** és a **edgeHub**. Ha végzett a megtekintéssel, válassza a **Létrehozás** lehetőséget.

   Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissítettüzembe helyezési jegyzéket talál, felhasználja az új üzemelő példányra vonatkozó információt a modul lemezképeinek felhőből történő adatkiszolgálásához, majd elindítja a modulok helyi futtatását. Ez a folyamat néhány percet is igénybe vehet.

1. Miután létrehozta a modul központi telepítésének részleteit, a varázsló visszaadja az eszköz adatai lapot. Az eszköz adatai lapon tekintse meg a telepítés állapotát a **modulok** lapon. Három modulnak kell szerepelnie: $edgeAgent, $edgeHub és SimulatedTemperatureSensor. Ha egy vagy több modul a központi telepítésben megadott módon van felsorolva, de az eszköz nem jelentett, akkor a IoT Edge eszköz továbbra is el lesz indítva. Várjon néhány percet, és válassza a **frissítés** lehetőséget az oldal tetején.

   ![SimulatedTemperatureSensor megtekintése az üzembe helyezett modulok listájában](./media/iot-edge-deploy-module/view-deployed-modules.png)
