---
title: A Modbus üzembe helyezése az Azure IoT Edge-ben | Microsoft Docs
description: Modbus TCP-t használó eszközök Azure IoT Hubbal való kommunikációjának engedélyezése egy IoT Edge-átjáróeszköz létrehozásával
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: kgremban
ms.openlocfilehash: d8675042874ffaa824ccd1bf3b0017f0219268b8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566397"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-eszközök csatlakoztatása az IoT Edge-eszközátjárón keresztül

Ha egy Azure IoT Hubhoz Modbus TCP vagy RTU protokollt használó IoT-eszközöket kíván csatlakoztatni, használjon egy IoT Edge-eszközt átjáróként. Az átjáróeszköz beolvassa az adatokat a Modbus-eszközökből, majd elküldi ezeket az adatokat a felhőbe egy támogatott protokoll használatával. 

![Az IoT Hubhoz Edge-átjárón keresztül csatlakozó Modbus-eszközök](./media/deploy-modbus-gateway/diagram.png)

Ez a cikk bemutatja, hogyan hozható létre saját tárolórendszerkép egy Modbus-modulhoz (vagy használhat előre létrehozott mintát), és ezután ez hogyan helyezhető üzembe az átjáróként szolgáló IoT Edge-eszközön. 

A cikk azt feltételezi, hogy Modbus TCP protokollt használ. A modul Modbus RTU támogató konfigurálásával kapcsolatos további információkért lásd: a [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projektet a Githubon. 

## <a name="prerequisites"></a>Előfeltételek
* Azure IoT Edge-eszköz. Az eszköz beállítását bemutató cikkek: [Azure IoT Edge üzembe helyezése szimulált eszközön Windows](quickstart.md) vagy [Linux rendszeren](quickstart-linux.md). 
* Az IoT Edge-eszköz elsődleges kulcsának kapcsolati sztringje.
* A Modbus TCP-t támogató fizikai vagy szimulált Modbus-eszköz.

## <a name="prepare-a-modbus-container"></a>Modbus-tároló előkészítése

Ha tesztelni kívánja a Modbus-átjáró funkcionalitását, a Microsoft rendelkezik egy mintamodullal, amelyet használhat. A mintamodul használatához lépjen [A megoldás futtatása](#run-the-solution) szakaszhoz, és adja meg az alábbiakat a Rendszerkép URI-ja értékeként: 

```URL
mcr.microsoft.com/azureiotedge/modbus:1.0
```

Ha azt szeretné, hozzon létre saját modult, és testre szabni a környezethez, nincs-e egy nyílt forráskódú [Azure IoT Edge Modbus-modul](https://github.com/Azure/iot-edge-modbus) projektet a Githubon. Kövesse a projektben található útmutatót saját tárolórendszerkép létrehozásához. Ha saját tárolórendszerképet hoz létre, a tárolórendszerképek beállításjegyzékbe való közzétételének módjáról és az egyéni modulok az eszközén való üzembe helyezéséről a [C# IoT Edge-modul fejlesztését és üzembe helyezését](tutorial-csharp-module.md) ismertető részben talál útmutatást. 


## <a name="run-the-solution"></a>A megoldás futtatása
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
7. A **Specify Routes** (Útvonalak megadása) lépésben másolja a következő JSON-t a szövegmezőbe. Ez az útvonal a Modbus-modul által gyűjtött üzeneteket küldi el az IoT Hubnak. Ebben az útvonalban a „modbusOutput” az a végpont, amelyet a Modbus-modul használ az adatok kimenetének küldéséhez, az „upstream” pedig egy speciális cél, amely alapján az Edge Hub az IoT Hubnak küldi az üzeneteket. 
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
docker logs -f modbus
```

Az eszköz által küldött telemetriát is megtekintheti a [Visual Studio Code Azure IoT Toolkit bővítményével](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="next-steps"></a>További lépések

- További információk az IoT Edge-eszközök átjáróként való működéséről: [Transzparens átjáróként működő IoT Edge-eszköz létrehozása](./how-to-create-transparent-gateway.md).
- További információk az IoT Edge-modulok működéséről: [Az Azure IoT Edge-modulok megismerése](iot-edge-modules.md)
