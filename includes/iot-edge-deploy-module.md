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
ms.openlocfilehash: c20a14ef2bc74d73b93ab39ee52fe1be8a5f984f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192160"
---
Az Azure IoT Edge főbb képességei egyik folyamatban van, helyezhet üzembe a kódot az IoT Edge-eszközökön a felhőből. **IoT Edge-modulok** tárolókként megvalósított végrehajtható-csomagokat. Ebben a szakaszban egy előre elkészített modulnak a központi telepítése a [IoT Edge-modulok szakaszában az Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). 

A modul úgy, hogy ebben a szakaszban egy érzékelőt szimulál, és elküldi a létrehozott adatokat. Ez a modul hasznos kódrészleteket, ha most ismerkedik az IoT Edge segítségével, mert a szimulált adatokat használhatja fejlesztési és tesztelési célra. Ha meg szeretné tekinteni a pontosan ez a modul működésével, megtekintheti a [hőmérséklet-érzékelő forráskód szimulált](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs). 

Az első modul az Azure Marketplace-ről üzembe helyezéséhez használja az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), adja meg **szimulált hőmérséklet-érzékelő** a keresést, és nyissa meg a Marketplace-en eredményt.

   ![Az Azure portál keresési szimulált hőmérséklet-érzékelő](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Válassza ki az IoT Edge-eszköz, ez a modul fogadásához. Az a **IoT Edge-modul a Céleszközök** lap, adja meg a következő információkat:

   1. **Előfizetés**: válassza ki az előfizetést, amely tartalmazza az IoT hub használata esetén.

   2. **Az IoT Hub**: válassza ki az IoT hub használata a nevét.

   3. **IoT Edge-eszköz neve**: Ha a javasolt eszköznév ebben a rövid útmutatóban a korábban használt, adja meg a **myEdgeDevice**. Vagy válassza **található eszköz** választhat az IoT hub IoT Edge-eszközök listáját. 
   
   4. Kattintson a **Létrehozás** gombra.

3. Most, hogy egy IoT Edge-modul az Azure Marketplace-ről úgy döntött, és ki az IoT Edge-eszköz, a modul fogadásához, ekkor átkerül egy három lépéses varázsló, amely segítséget nyújt a pontosan hogyan helyezheti üzembe a modul adja meg. Az a **modulok hozzáadása** lépés figyelje meg, hogy a varázsló a **SimulatedTemperatureSensor** modul töltve. Az oktatóanyagok ezen a lapon a további modulok hozzáadása a környezethez. Ebben a rövid útmutatóban csak telepíteni ezt egy modult. Válassza ki **tovább** a varázsló a következő lépéssel folytatja.

4. Az a **útvonalak megadása** lépés a varázsló meghatározhatja, hogyan továbbított üzenetek modulok között, és az IoT hubnak. A rövid útmutatóhoz szeretné azokat az üzeneteket az összes modulok nyissa meg az IoT Hub (`$upstream`). Ha nem így töltve, adja hozzá a következő kódot, majd válassza **tovább**:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. Az a **áttekintése telepítési** lépés a varázsló a JSON-fájlt, amely meghatározza a modulokat, amely az IoT Edge-eszköz üzembe lesznek helyezve megtekintheti. Figyelje meg, hogy a **SimulatedTemperatureSensor** modul részét képezi, és a két rendszer további modulok nevű **edgeAgent** és **edgeHub**. Válassza ki **küldés** Ha már áttekintette.

   Amikor küld az IoT Edge-eszköz új központi telepítést, semmi nem leküldéssel az eszközre. Ehelyett az eszköz lekérdezi az IoT Hub rendszeresen új utasításokat. Ha az eszköz talál egy frissített telepítési jegyzékfájlt, az új központi telepítés adatai alapján, hogy a modul rendszerképeket a felhőből, majd elindítja a helyileg futó modulok. Ez a folyamat pár percet is igénybe vehet. 

6. Miután elküldött az modul üzembe helyezés részleteiről, a varázsló adja vissza, hogy a **IoT Edge** az IoT hub oldalon. A részletek megtekintéséhez IoT Edge-eszközök listájából válassza ki az eszközt. 

7. Az eszköz részletei oldalon görgessen le a **modulok** szakaszban. Három modult kell szerepelnie: $edgeAgent $edgeHub és SimulatedTemperatureSensor. Ha egy vagy több modul jelennek meg a megadott központi telepítésben lévő, de nem eszköz, az IoT Edge által jelentett eszköz továbbra is indítása folyamatban van őket. Várjon néhány pillanatot, és válassza ki **frissítése** az oldal tetején. 

   ![A telepített modulok listájának megtekintése SimulatedTemperatureSensor](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
