---
title: Translate modbus protocols with gateways - Azure IoT Edge | Microsoft Docs
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
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connect Modbus TCP devices through an IoT Edge device gateway

Ha egy Azure IoT Hubhoz Modbus TCP vagy RTU protokollt használó IoT-eszközöket kíván csatlakoztatni, használjon egy IoT Edge-eszközt átjáróként. Az átjáróeszköz beolvassa az adatokat a Modbus-eszközökből, majd elküldi ezeket az adatokat a felhőbe egy támogatott protokoll használatával.

![Modbus devices connect to IoT Hub through IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Ez a cikk bemutatja, hogyan hozható létre saját tárolórendszerkép egy Modbus-modulhoz (vagy használhat előre létrehozott mintát), és ezután ez hogyan helyezhető üzembe az átjáróként szolgáló IoT Edge-eszközön.

A cikk azt feltételezi, hogy Modbus TCP protokollt használ. For more information about how to configure the module to support Modbus RTU, see the [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub.

## <a name="prerequisites"></a>Előfeltételek
* Azure IoT Edge-eszköz. For a walkthrough on how to set up one, see [Deploy Azure IoT Edge on Windows](quickstart.md) or [Linux](quickstart-linux.md).
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.
* A Modbus TCP-t támogató fizikai vagy szimulált Modbus-eszköz.

## <a name="prepare-a-modbus-container"></a>Modbus-tároló előkészítése

Ha tesztelni kívánja a Modbus-átjáró funkcionalitását, a Microsoft rendelkezik egy mintamodullal, amelyet használhat. You can access the module from the Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), or with the image URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

If you want to create your own module and customize it for your environment, there is an open-source [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub. Kövesse a projektben található útmutatót saját tárolórendszerkép létrehozásához. To create a container image, refer to [Develop C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md) or [Develop modules in Visual Studio Code](how-to-vs-code-develop-module.md). Those articles provide instructions on creating new modules and publishing container images to a registry.

## <a name="try-the-solution"></a>Try the solution

This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

1. Az [Azure Portalon](https://portal.azure.com/) lépjen az IoT hubhoz.

2. Go to **IoT Edge** and click on your IoT Edge device.

3. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

4. Adja hozzá a Modbus-modult:

   1. Click **Add** and select **IoT Edge module**.

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

7. A **Specify Routes** (Útvonalak megadása) lépésben másolja a következő JSON-t a szövegmezőbe. Ez az útvonal a Modbus-modul által gyűjtött üzeneteket küldi el az IoT Hubnak. In this route, **modbusOutput** is the endpoint that Modbus module uses to output data and **$upstream** is a special destination that tells IoT Edge hub to send messages to IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Kattintson a **Tovább** gombra.

9. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.

10. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. You should see the new **modbus** module running along with the IoT Edge runtime.

## <a name="view-data"></a>Adatok megtekintése
Tekintse meg a Modbus-modulon keresztül érkező adatokat:
```cmd/sh
iotedge logs modbus
```

You can also view the telemetry the device is sending by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

## <a name="next-steps"></a>Következő lépések

- To learn more about how IoT Edge devices can act as gateways, see [Create an IoT Edge device that acts as a transparent gateway](./how-to-create-transparent-gateway.md).
- For more information about how IoT Edge modules work, see [Understand Azure IoT Edge modules](iot-edge-modules.md).
