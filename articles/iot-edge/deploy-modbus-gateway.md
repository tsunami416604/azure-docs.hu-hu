---
title: Modbus protokollok fordítása átjárókkal - Azure IoT Edge | Microsoft dokumentumok
description: Modbus TCP-t használó eszközök Azure IoT Hubbal való kommunikációjának engedélyezése egy IoT Edge-átjáróeszköz létrehozásával
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511144"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-eszközök csatlakoztatása IoT Edge-eszközátjárón keresztül

Ha Modbus TCP vagy RTU protokollokat használó IoT-eszközöket szeretne csatlakoztatni egy Azure IoT-központhoz, használhatja az IoT Edge-eszközt átjáróként. Az átjáróeszköz beolvassa az adatokat a Modbus-eszközökből, majd elküldi ezeket az adatokat a felhőbe egy támogatott protokoll használatával.

![Modbus-eszközök az IoT Edge-átjárón keresztül csatlakoznak az IoT Hubhoz](./media/deploy-modbus-gateway/diagram.png)

Ez a cikk bemutatja, hogyan hozható létre saját tárolórendszerkép egy Modbus-modulhoz (vagy használhat előre létrehozott mintát), és ezután ez hogyan helyezhető üzembe az átjáróként szolgáló IoT Edge-eszközön.

A cikk azt feltételezi, hogy Modbus TCP protokollt használ. A Modbus RTU támogatásához a modul konfigurálásáról az [Azure IoT Edge Modbus modulprojekt](https://github.com/Azure/iot-edge-modbus) a GitHubon.

## <a name="prerequisites"></a>Előfeltételek

* Azure IoT Edge-eszköz. Az Azure [IoT Edge](quickstart.md) telepítése Windows vagy [Linux](quickstart-linux.md)rendszeren.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.
* A Modbus TCP-t támogató fizikai vagy szimulált Modbus-eszköz. Ismernie kell az IPv4-címét.

## <a name="prepare-a-modbus-container"></a>Modbus-tároló előkészítése

Ha tesztelni kívánja a Modbus-átjáró funkcionalitását, a Microsoft rendelkezik egy mintamodullal, amelyet használhat. A modul az Azure Marketplace-en, [modbus,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)vagy `mcr.microsoft.com/azureiotedge/modbus:1.0`a lemezkép URI, .

Ha saját modult szeretne létrehozni, és a környezetéhez szeretné testreszabni, van egy nyílt forráskódú [Azure IoT Edge Modbus modulprojekt](https://github.com/Azure/iot-edge-modbus) a GitHubon. Kövesse a projektben található útmutatót saját tárolórendszerkép létrehozásához. Tárolólemezkép létrehozásához olvassa el a [C# modulok fejlesztése a Visual Studio alkalmazásban](how-to-visual-studio-develop-csharp-module.md) vagy a Modulok fejlesztése a Visual [Studio-kódban](how-to-vs-code-develop-module.md)című részt. Ezek a cikkek utasításokat adnak az új modulok létrehozásához és a tárolórendszerképek rendszerleíró adatbázisba való közzétételéhez.

## <a name="try-the-solution"></a>Próbálja ki a megoldást

Ez a szakasz végigvezeti a Microsoft minta Modbus modul üzembe helyezését az IoT Edge-eszközre.

1. Az [Azure Portalon](https://portal.azure.com/) lépjen az IoT hubhoz.

2. Nyissa meg az **IoT Edge-et,** és kattintson az IoT Edge-eszközre.

3. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

4. Az **IoT Edge-modulok** szakaszban adja hozzá a Modbus modult:

   1. Kattintson a **Hozzáadás** legördülő menüre, és válassza a **Piactérmodul lehetőséget.**
   2. Keresse `Modbus` meg és jelölje ki a Microsoft **Modbus TCP modulját.**
   3. A modul automatikusan konfigurálva van az IoT Hubhoz, és megjelenik az IoT Edge-modulok listájában. Az útvonalak is automatikusan konfigurálva vannak. Válassza az **Áttekintés + létrehozás** lehetőséget.
   4. Tekintse át a központi telepítési jegyzékfájlt, és válassza **a Létrehozás lehetőséget.**

5. Válassza ki a `ModbusTCPModule`Modbus modult a listában, és válassza a **Modul ikerbeállítások** lapot. A modul iker kívánt tulajdonságaihoz szükséges JSON az automatikus feltöltés.

6. Keresse meg a **SlaveConnection** tulajdonságot a JSON-ban, és állítsa az értékét a Modbus-eszköz IPv4-címére.

7. Válassza a **Frissítés** lehetőséget.

8. Válassza **a Véleményezés + létrehozás**lehetőséget, tekintse át a központi telepítést, majd válassza a **Létrehozás lehetőséget.**

9. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Az új `ModbusTCPModule` modulnak az IoT Edge futtatóidővel együtt kell futnia.

## <a name="view-data"></a>Adatok megtekintése

Tekintse meg a Modbus modulon keresztül érkező adatokat:

```cmd/sh
iotedge logs modbus
```

Megtekintheti az eszköz által küldött telemetriai adatokat is az [Azure IoT Hub-bővítmény visual studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábbi néven Azure IoT Toolkit bővítmény) használatával.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni arról, hogy az IoT Edge-eszközök hogyan működhetnek átjáróként, [olvassa el az Átlátszó átjáróként használható IoT Edge-eszköz létrehozása című témakört.](./how-to-create-transparent-gateway.md)
* Az IoT Edge-modulok működéséről az [Azure IoT Edge-modulok megismerése](iot-edge-modules.md)című témakörben talál további információt.
