---
title: Az átjárókkal – Azure IoT Edge modbus protokollok fordítása |} A Microsoft Docs
description: Modbus TCP-t használó eszközök Azure IoT Hubbal való kommunikációjának engedélyezése egy IoT Edge-átjáróeszköz létrehozásával
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457220"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-eszközök csatlakoztatása az IoT Edge-eszközátjárón keresztül

Ha egy Azure IoT Hubhoz Modbus TCP vagy RTU protokollt használó IoT-eszközöket kíván csatlakoztatni, használjon egy IoT Edge-eszközt átjáróként. Az átjáróeszköz beolvassa az adatokat a Modbus-eszközökből, majd elküldi ezeket az adatokat a felhőbe egy támogatott protokoll használatával.

![Modbus-eszközök csatlakoztatása IoT Hub IoT Edge átjárón keresztül](./media/deploy-modbus-gateway/diagram.png)

Ez a cikk bemutatja, hogyan hozható létre saját tárolórendszerkép egy Modbus-modulhoz (vagy használhat előre létrehozott mintát), és ezután ez hogyan helyezhető üzembe az átjáróként szolgáló IoT Edge-eszközön.

A cikk azt feltételezi, hogy Modbus TCP protokollt használ. További információ arról, hogyan konfigurálható a modul a Modbus RTU támogatásához: a GitHubon a [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projektje.

## <a name="prerequisites"></a>Előfeltételek
* Azure IoT Edge-eszköz. A beállításával kapcsolatban lásd: [Azure IoT Edge telepítése Windows](quickstart.md) vagy [Linux](quickstart-linux.md)rendszeren.
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.
* A Modbus TCP-t támogató fizikai vagy szimulált Modbus-eszköz.

## <a name="prepare-a-modbus-container"></a>Modbus-tároló előkészítése

Ha tesztelni kívánja a Modbus-átjáró funkcionalitását, a Microsoft rendelkezik egy mintamodullal, amelyet használhat. A modult az Azure Marketplace-en, a [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)-ban vagy a **MCR.microsoft.com/azureiotedge/Modbus:1.0**található rendszerkép-URI-n keresztül érheti el.

Ha saját maga szeretné létrehozni a modult, és testre szeretné szabni a környezetében, akkor a GitHubon van egy nyílt forráskódú [Azure IoT Edge Modbus modul](https://github.com/Azure/iot-edge-modbus) -projekt. Kövesse a projektben található útmutatót saját tárolórendszerkép létrehozásához. A tárolók rendszerképének létrehozásával kapcsolatban [lásd C# : modulok fejlesztése a Visual Studióban](how-to-visual-studio-develop-csharp-module.md) , illetve [modulok fejlesztése a Visual Studio Code-ban](how-to-vs-code-develop-module.md). Ezek a cikkek útmutatást nyújtanak az új modulok létrehozásához és a tároló-lemezképek beállításjegyzékbe való közzétételéhez.

## <a name="try-the-solution"></a>Próbálja ki a megoldást

Ez a szakasz végigvezeti a Microsoft minta-Modbus moduljának a IoT Edge eszközön való üzembe helyezésén.

1. Az [Azure Portalon](https://portal.azure.com/) lépjen az IoT hubhoz.

2. Lépjen a **IoT Edgera** , és kattintson a IoT Edge eszközre.

3. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

4. Adja hozzá a Modbus-modult:

   1. Kattintson a **Hozzáadás** gombra, és válassza ki **IoT Edge modult**.

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
                  "StartAddress":"40001",
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

7. A **Specify Routes** (Útvonalak megadása) lépésben másolja a következő JSON-t a szövegmezőbe. Ez az útvonal a Modbus-modul által gyűjtött üzeneteket küldi el az IoT Hubnak. Ebben az útvonalban a **modbusOutput** az a végpont, amelyet a Modbus-modul az adatkimeneti adatokat használ, és a **$upstream** egy speciális cél, amely megadja, hogy IoT Edge hub üzeneteket küldjön a IoT hubnak.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Kattintson a **Tovább** gombra.

9. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.

10. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. A IoT Edge futtatókörnyezettel együtt futó új **Modbus** modulnak kell megjelennie.

## <a name="view-data"></a>Adatok megtekintése
Tekintse meg a Modbus-modulon keresztül érkező adatokat:
```cmd/sh
iotedge logs modbus
```

Megtekintheti azt is, hogy az eszköz telemetria a [Visual Studio Code-hoz készült azure IoT hub Toolkit-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (korábban Azure IoT Toolkit Extension) használatával küldi el.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni arról, hogy IoT Edge eszközök hogyan működhetnek átjáróként, tekintse meg [az olyan IoT Edge-eszköz létrehozását, amely transzparens átjáróként működik](./how-to-create-transparent-gateway.md).
- További információ a IoT Edge moduljainak működéséről: [Azure IoT Edge modulok megismerése](iot-edge-modules.md).
