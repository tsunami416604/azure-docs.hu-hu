---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564820"
---
Azure IoT Edge egyik fő funkciója, hogy a felhőből üzembe helyezhet egy kódot a IoT Edge-eszközökön. **IoT Edge modulok** tárolóként megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre összeállított modult helyezünk üzembe az [Azure Marketplace IoT Edge-modulok szakaszával](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül az azure-IoT hub.

Az ebben a szakaszban üzembe helyezett modul szimulál egy érzékelőt, és a generált adatokat küldi el. Ez a modul hasznos kódrészlet, ha első lépések a IoT Edgehoz, mivel a szimulált adat a fejlesztéshez és a teszteléshez használható. Ha pontosan látni szeretné a modult, akkor megtekintheti a [szimulált hőmérséklet-érzékelő forráskódját](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Az első modul Azure piactéren való üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

1. A bal oldali ablaktábla menüjének **automatikus eszközkezelés**területén válassza a **IoT Edge**lehetőséget.

1. Kattintson a céleszköz eszközének AZONOSÍTÓJÁRA az eszközök listájából.

1. A felső sávon válassza a **modulok beállítása**lehetőséget.

1. A lap **IoT Edge modulok** szakaszában kattintson a **Hozzáadás**gombra.

1. A legördülő menüben válassza a **piactér modul**elemet.

   ![Simulated Temperature Sensor az Azure Portal-keresésben](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. A **IoT Edge modul Marketplace**-en keressen rá a "szimulált hőmérséklet-érzékelő" kifejezésre, és válassza ki a modult.

1. Figyelje meg, hogy a SimulatedTemperatureSensor modul automatikusan ki van töltve. Az oktatóanyagokban ezen a lapon további modulokat adhat hozzá az üzembe helyezéshez. Ebben a rövid útmutatóban csak egy modult telepítsen. Nem szükségesek hitelesítő adatok, mert nyilvánosak.

   ![Modulok beállítása az eszközön](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Válassza a **tovább lehetőséget: útvonalakat** a varázsló következő lépéséhez.

1. A varázsló **útvonalak** lapján megadhatja, hogyan adja át az üzeneteket a modulok és a IoT hub között. Az üzenetek név/érték párokkal vannak kiépítve. A gyors üzembe helyezéshez az összes modul összes üzenetét szeretné IoT Hub (`$upstream`). Ha nincs automatikusan feltöltve, adja hozzá a következő kódot a **név** **értékéhez** `upstream`:

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Válassza a **Next (tovább): felülvizsgálat + létrehozás** lehetőséget a varázsló következő lépésének folytatásához.

1. A varázsló **Áttekintés + létrehozás** lapján megtekintheti a IoT Edge eszközre telepített összes modult meghatározó JSON-fájlt. Figyelje meg, hogy a **SimulatedTemperatureSensor** modult tartalmazza, valamint két további, **edgeAgent** és **edgeHub**nevű rendszermodult. Ha végzett a megtekintéssel, válassza a **Létrehozás** lehetőséget.

   Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissített központi telepítési jegyzéket talál, az az új központi telepítés információit használja a modul rendszerképeinek a felhőből való lekéréséhez, majd elindítja a modulok helyi futtatását. Ez a folyamat néhány percet is igénybe vehet.

1. Miután létrehozta a modul központi telepítésének részleteit, a varázsló visszaadja az IoT hub **IoT Edge** lapjára. A részletek megtekintéséhez válassza ki az eszközt a IoT Edge-eszközök listájából.

1. Az eszköz adatai lapon görgessen le a **modulok** lapra. Három modulnak kell szerepelnie: $edgeAgent, $edgeHub és SimulatedTemperatureSensor. Ha egy vagy több modul a központi telepítésben megadott módon van felsorolva, de az eszköz nem jelentett, akkor a IoT Edge eszköz továbbra is el lesz indítva. Várjon néhány percet, és válassza a **frissítés** lehetőséget az oldal tetején.

   ![SimulatedTemperatureSensor megtekintése az üzembe helyezett modulok listájában](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
