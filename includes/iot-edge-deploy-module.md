---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/04/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 019d6120f8f3769d2d974270d575278da0370ef0
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057388"
---
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Az IoT Edge-modul egy tárolóként megvalósított végrehajtható csomag. Ebben a szakaszban egy előre elkészített modulnak a üzembe a [IoT Edge-modulok szakaszában az Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Ez a modul az IoT Edge-eszköz szimulált telemetriát hoz létre.

1. Az a [az Azure portal](https://portal.azure.com), adja meg **szimulált hőmérséklet-érzékelő** a keresést, és nyissa meg a Marketplace-en eredményt.

   ![Az Azure portál keresési szimulált hőmérséklet-érzékelő](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Válassza ki az IoT Edge-eszköz, ez a modul fogadásához. Az a **IoT Edge-modul a Céleszközök** lap, adja meg a következő információkat:

   1. **Előfizetés**: válassza ki az előfizetést, amely tartalmazza az IoT hub használata esetén.

   2. **Az IoT Hub**: válassza ki az IoT hub használata a nevét.

   3. **IoT Edge-eszköz neve**: Ha a javasolt eszköznév ebben a rövid útmutatóban a korábban használt, adja meg a **myEdgeDevice**. Vagy válassza **található eszköz** választhat az IoT hub IoT Edge-eszközök listáját. 
   
   4. Kattintson a **Létrehozás** gombra.

3. Most, hogy egy IoT Edge-modul az Azure Marketplace-ről úgy döntött, és ki az IoT Edge-eszköz, a modul fogadásához, ekkor átkerül egy három lépéses varázsló, amellyel meghatározhatja, hogy pontosan milyen a modul telepítve lesz. Az a **modulok hozzáadása** lépés figyelje meg, hogy a varázsló a **SimulatedTemperatureSensor** modul töltve. Az oktatóanyagok további modulok hozzáadása a központi telepítés lesz ezen a lapon. Ebben a rövid útmutatóban csak telepíteni ezt egy modult. Válassza ki **tovább** a varázsló a következő lépéssel folytatja.

4. Az a **útvonalak megadása** lépés a varázsló meghatározhatja, hogyan továbbított üzenetek modulok között, és az IoT hubnak. A rövid útmutatóhoz szeretné azokat az üzeneteket az összes modulok nyissa meg az IoT Hub (`$upstream`). Ha nem így töltve, adja hozzá a következő kódot, majd válassza **tovább**:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. Az a **áttekintése telepítési** lépés a varázsló a JSON-fájlt, amely meghatározza a modulokat, amely az IoT Edge-eszköz üzembe lesznek helyezve megtekintheti. Figyelje meg, hogy a **SimulatedTemperatureSensor** modul részét képezi, valamint két további rendszer modulként nevű **edgeAgent** és **edgeHub**. Válassza ki **küldés** Ha már áttekintette.

   Amikor küld az IoT Edge-eszköz új központi telepítést, semmi nem leküldéssel az eszközre. Ehelyett az eszköz lekérdezi az IoT Hub rendszeresen új utasításokat. Ha azt látja, hogy az új központi telepítési információk, az eszköz használja, hogy a modul rendszerképeket a felhőből, és elindítja a helyileg futó modulok. Ez a folyamat pár percet is igénybe vehet. 

6. Miután elküldött az modul üzembe helyezés részleteiről, a varázsló adja vissza, hogy a **IoT Edge** az IoT hub oldalon. A részletek megtekintéséhez IoT Edge-eszközök listájából válassza ki az eszközt. 

7. Az eszköz részletei oldalon görgessen le a **modulok** szakaszban. Három modult kell szerepelnie: $edgeAgent $edgeHub és SimulatedTemperatureSensor. Ha egy vagy több, a modulok megadott központi telepítésben szereplő, de nem jelentenek, eszköz, amely azt jelenti, hogy az IoT Edge-eszköz továbbra is elindítja őket. Várjon néhány pillanatot, és válassza ki **frissítése** az oldal tetején. 

   ![A telepített modulok listájának megtekintése SimulatedTemperatureSensor](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
