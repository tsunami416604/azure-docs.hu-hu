---
title: Az átjárókkal – Azure IoT Edge modbus protokollok fordítása |} A Microsoft Docs
description: Modbus TCP-t használó eszközök Azure IoT Hubbal való kommunikációjának engedélyezése egy IoT Edge-átjáróeszköz létrehozásával
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873763"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-eszközök csatlakoztatása az IoT Edge-eszközátjárón keresztül

Ha egy Azure IoT Hubhoz Modbus TCP vagy RTU protokollt használó IoT-eszközöket kíván csatlakoztatni, használjon egy IoT Edge-eszközt átjáróként. Az átjáróeszköz beolvassa az adatokat a Modbus-eszközökből, majd elküldi ezeket az adatokat a felhőbe egy támogatott protokoll használatával.

![Az IoT hub, IoT Edge-átjárón keresztül csatlakozó Modbus-eszközök](./media/deploy-modbus-gateway/diagram.png)

Ez a cikk bemutatja, hogyan hozható létre saját tárolórendszerkép egy Modbus-modulhoz (vagy használhat előre létrehozott mintát), és ezután ez hogyan helyezhető üzembe az átjáróként szolgáló IoT Edge-eszközön.

A cikk azt feltételezi, hogy Modbus TCP protokollt használ. A modul Modbus RTU támogató konfigurálásával kapcsolatos további információkért lásd: a [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projektet a Githubon.

## <a name="prerequisites"></a>Előfeltételek
* Azure IoT Edge-eszköz. Hogyan állítható be egy általános bemutató, lásd: [Azure IoT Edge üzembe helyezése a Windows](quickstart.md) vagy [Linux](quickstart-linux.md).
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.
* A Modbus TCP-t támogató fizikai vagy szimulált Modbus-eszköz.

## <a name="prepare-a-modbus-container"></a>Modbus-tároló előkészítése

Ha tesztelni kívánja a Modbus-átjáró funkcionalitását, a Microsoft rendelkezik egy mintamodullal, amelyet használhat. A modul az Azure piactéren érheti el [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), vagy a kép URI-t, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Ha azt szeretné, hozzon létre saját modult, és testre szabni a környezethez, nincs-e egy nyílt forráskódú [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projektet a Githubon. Kövesse a projektben található útmutatót saját tárolórendszerkép létrehozásához. Ha saját tárolórendszerképet hoz létre, tekintse meg [Develop C# modulok Visual Studióban](how-to-visual-studio-develop-csharp-module.md) vagy [fejlesztése a Visual Studio Code modulok](how-to-vs-code-develop-module.md). E cikkekben utasításokat az új modul létrehozása és közzététele a tárolórendszerképeket egy regisztrációs adatbázisba.

## <a name="try-the-solution"></a>Próbálja ki a megoldást

Ez a szakasz végigvezeti a Microsoft mintát Modbus-modul üzembe helyezése az IoT Edge-eszköz.

1. Az [Azure Portalon](https://portal.azure.com/) lépjen az IoT hubhoz.

2. Lépjen a **IoT Edge** , majd kattintson a az IoT Edge-eszköz.

3. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

4. Adja hozzá a Modbus-modult:

   1. Kattintson a **Hozzáadás** válassza **IoT Edge-modul**.

   2. A **Name** (Név) mezőbe írja a következőt: „modbus”.

   3. Az **Image** (Rendszerkép) mezőbe írja be a mintatároló kép URI-ját: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Jelölje be az **Enable** (Engedélyezés) jelölőnégyzetet a moduliker kívánt tulajdonságainak frissítéséhez.

   5. Másolja az alábbi JSON-t a szövegmezőbe. Módosítsa a **SlaveConnection** értékét a Modbus-eszköz IPv4-címére.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Kattintson a **Mentés** gombra.

5. Az **Add Modules** (Modulok hozzáadása) lépésben kattintson a **Next** (Tovább) gombra.

7. A **Specify Routes** (Útvonalak megadása) lépésben másolja a következő JSON-t a szövegmezőbe. Ez az útvonal a Modbus-modul által gyűjtött üzeneteket küldi el az IoT Hubnak. Ebben az útvonalban "modbusOutput" az a végpont, amelyet a Modbus-modul kimeneti adatokat használ, és "upstream" egy speciális cél, amely arra utasítja az IoT Edge hub az IoT Hub üzenetküldés.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Kattintson a **Tovább** gombra.

9. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.

10. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Látható, az új **modbus** modul futtatása az IoT Edge-futtatókörnyezettel együtt.

## <a name="view-data"></a>Adatok megtekintése
Tekintse meg a Modbus-modulon keresztül érkező adatokat:
```cmd/sh
iotedge logs modbus
```

Az eszköz által küldött telemetriát is megtekintheti a [Azure IoT Hub-eszközkészlet bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábbi nevén Azure IoT-eszközkészlet bővítmény).

## <a name="next-steps"></a>További lépések

- Hogyan IoT Edge-eszközök átjáróként való működéséről kapcsolatos további információkért lásd: [hozzon létre egy IoT Edge-eszköz, amely transzparens átjáróként](./how-to-create-transparent-gateway.md).
- További információ az IoT Edge-modulok működéséről: [megismerheti az Azure IoT Edge-modulok](iot-edge-modules.md).
