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
ms.openlocfilehash: 12661c77c6a950b482187b09e4465c964e6d6652
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494055"
---
Azure IoT Edge egyik fő funkciója, hogy a felhőből üzembe helyezhet egy kódot a IoT Edge-eszközökön. **IoT Edge modulok** tárolóként megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre összeállított modult helyezünk üzembe az [Azure Marketplace IoT Edge-modulok szakaszában](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). 

Az ebben a szakaszban üzembe helyezett modul szimulál egy érzékelőt, és a generált adatokat küldi el. Ez a modul hasznos kódrészlet, ha első lépések a IoT Edgehoz, mivel a szimulált adat a fejlesztéshez és a teszteléshez használható. Ha pontosan látni szeretné a modult, akkor megtekintheti a [szimulált hőmérséklet-érzékelő forráskódját](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs). 

Az első modul Azure piactéren való üzembe helyezéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)írja be a **szimulált hőmérséklet-érzékelőt** a keresésbe, és nyissa meg a Piactéri eredményt.

   ![Szimulált hőmérséklet-érzékelő Azure Portal keresésben](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Válassza ki IoT Edge eszközt a modul fogadásához. A **IoT Edge modult tároló eszközök** lapon adja meg a következő információkat:

   1. **Előfizetés**: válassza ki azt az előfizetést, amely az Ön által használt IoT hub-t tartalmazza.

   2. **IoT hub**: válassza ki az Ön által használt IoT hub nevét.

   3. **IoT Edge eszköznév**: Ha a rövid útmutatóban korábban használta a javasolt eszköznév, írja be a **myEdgeDevice**nevet. Vagy válassza az **eszköz keresése** lehetőséget az IoT hub IoT Edge eszközeinek listájából. 
   
   4. Kattintson a **Létrehozás** gombra.

3. Most, hogy kiválasztott egy IoT Edge modult az Azure Marketplace-ről, és kiválasztott egy IoT Edge eszközt a modul fogadásához, egy három lépésből álló varázsló segítségével határozhatja meg, hogy pontosan hogyan kell telepíteni a modult. A varázsló **modulok hozzáadása** lépésében figyelje meg, hogy a **SimulatedTemperatureSensor** modul automatikusan fel van töltve. Az oktatóanyagokban ezen a lapon további modulokat adhat hozzá az üzembe helyezéshez. Ebben a rövid útmutatóban csak egy modult telepítsen. A tovább **gombra kattintva folytassa** a varázsló következő lépésével.

4. A varázsló **útvonalak megadása** lépésében megadhatja, hogyan adja át az üzeneteket a modulok és a IoT hub között. A gyors üzembe helyezéshez az összes modul összes üzenetét szeretné IoT Hub (`$upstream`). Ha nincs automatikusan feltöltve, adja hozzá a következő kódot:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```
   Ezután kattintson a **Tovább** gombra.

5. A varázsló **központi telepítés áttekintése** lépésében megtekintheti a IoT Edge eszközre telepített összes modult meghatározó JSON-fájlt. Figyelje meg, hogy a **SimulatedTemperatureSensor** modult tartalmazza, valamint két további, **edgeAgent** és **edgeHub**nevű rendszermodult. Ha végzett a megtekintéssel, válassza a **Submit (Küldés** ) lehetőséget.

   Amikor új központi telepítést küld egy IoT Edge eszközre, a rendszer semmilyen hibát nem küld az eszközre. Ehelyett az eszköz lekérdezi az új utasításokhoz IoT Hub rendszeresen. Ha az eszköz egy frissített központi telepítési jegyzéket talál, az az új központi telepítés információit használja a modul rendszerképeinek a felhőből való lekéréséhez, majd elindítja a modulok helyi futtatását. Ez a folyamat néhány percet is igénybe vehet. 

6. Miután elküldte a modul központi telepítésének részleteit, a varázsló visszaadja az IoT hub **IoT Edge** lapjára. A részletek megtekintéséhez válassza ki az eszközt a IoT Edge eszközök listájából. 

7. Az eszköz adatai lapon görgessen le a **modulok** szakaszhoz. Három modulnak kell szerepelnie: $edgeAgent, $edgeHub és SimulatedTemperatureSensor. Ha egy vagy több modul a központi telepítésben megadott módon van felsorolva, de az eszköz nem jelentett, akkor a IoT Edge eszköz továbbra is el lesz indítva. Várjon néhány percet, és válassza a **frissítés** lehetőséget az oldal tetején. 

   ![SimulatedTemperatureSensor megtekintése az üzembe helyezett modulok listájában](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
