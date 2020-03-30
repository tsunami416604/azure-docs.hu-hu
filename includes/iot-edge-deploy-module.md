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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564820"
---
Az Azure IoT Edge egyik legfontosabb képessége, hogy képes kódot telepíteni az IoT Edge-eszközök a felhőből. **Az IoT Edge-modulok** tárolóként megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre elkészített modult telepít [az Azure Piactér IoT Edge-modulok szakaszából](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül az Azure IoT Hubból.

Az ebben a szakaszban üzembe helyezett modul egy érzékelőt szimulál, és generált adatokat küld. Ez a modul egy hasznos kód, amikor az IoT Edge, mert a szimulált adatok fejlesztési és tesztelési használhatja. Ha azt szeretné, hogy pontosan mit csinál ez a modul, megtekintheti a [szimulált hőmérséklet-érzékelő forráskódját](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Az első modul azure-piactérről történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT-központot.

1. A bal oldali ablaktábla **Automatikus eszközkezelés**területén válasszuk az **IoT Edge**lehetőséget.

1. Kattintson a céleszköz eszközazonosítójára az eszközök listájából.

1. A felső sávon válassza a **Modulok beállítása**lehetőséget.

1. A lap **IoT Edge Modulok** szakaszában kattintson a **Hozzáadás**gombra.

1. A legördülő menüben válassza a **Piactérmodul lehetőséget.**

   ![Simulated Temperature Sensor az Azure Portal-keresésben](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. Az **IoT Edge module marketplace-en**keressen a "Szimulált hőmérséklet-érzékelő" kifejezésre, és válassza ki azt a modult.

1. Figyelje meg, hogy a SimulatedTemperatureSensor modul automatikusan ki van töltve. Az oktatóanyagokban ezen a lapon további modulokat adhat hozzá a központi telepítéshez. Ehhez a rövid útmutatóhoz csak ezt az egy modult telepítse. Nincs szükség hitelesítő adatokra, mert nyilvános.

   ![Modulok beállítása az eszközön](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Válassza a **Tovább: Útvonalak lehetőséget** a varázsló következő lépéséhez.

1. A varázsló **Útvonalak** lapján megadhatja, hogy a modulok és az IoT Hub hogyan továbbítsa az üzeneteket. Az üzenetek név-érték párok használatával készülnek. A rövid útmutató, azt szeretné, hogy az összes üzenet`$upstream`az összes modult, hogy az IoT Hub ( ). Ha nincs automatikusan feltöltve, adja hozzá a következő kódot a **Név értékéhez:** `upstream` **Value**

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Válassza a **Tovább: Véleményezés + létrehozás** lehetőséget a varázsló következő lépéséhez.

1. A **varázsló Véleményezés + létrehozás** lapján megtekintheti a JSON-fájlt, amely meghatározza az IoT Edge-eszközre üzembe helyezett összes modult. Figyelje meg, hogy a **SimulatedTemperatureSensor** modul is megtalálható, és két további rendszermodul, az **edgeAgent** és **az edgeHub.** Válassza a **Létrehozás lehetőséget,** ha végzett a véleményezésével.

   Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissítettüzembe helyezési jegyzéket talál, felhasználja az új üzemelő példányra vonatkozó információt a modul lemezképeinek felhőből történő adatkiszolgálásához, majd elindítja a modulok helyi futtatását. Ez a folyamat eltarthat néhány percig.

1. A modul üzembe helyezésének részleteinek létrehozása után a varázsló visszaadja az **IoT Edge-lap** az IoT hub. A részletek megtekintéséhez válassza ki az eszközt a IoT Edge-eszközök listájából.

1. Az eszköz részleteit tartalmazó lapon görgessen le a **Modulok** lapra. Három modult kell felsorolni: $edgeAgent, $edgeHub és SimulatedTemperatureSensor. Ha egy vagy több modul szerepel a központi telepítésben megadott, de az eszköz által nem jelentett, az IoT Edge-eszköz továbbra is elindítja őket. Várjon néhány percet, és válassza a **frissítés** lehetőséget a lap tetején.

   ![A SimulatedTemperatureSensor megtekintése a telepített modulok listájában](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
