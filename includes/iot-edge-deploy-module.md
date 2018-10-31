---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 983c65ba6e8b87f1dd66fcfdb50eac088ffab5d0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49960239"
---
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Az IoT Edge-modul egy tárolóként megvalósított végrehajtható csomag. Ebben a szakaszban üzembe helyez egy modult, amely telemetriát hoz létre a szimulált eszköz számára.

1. Az Azure Portalon keresse meg az IoT-központot.
1. Lépjen az **IoT Edge** részhez az **Automatikus eszközkezelés** alatt, és válassza ki az IoT Edge-eszközt.
1. Válassza a **Modulok beállítása** lehetőséget.
1. A **Modulok hozzáadása** lépés **Üzembehelyezési modulok** szakaszában kattintson a **Hozzáadás** gombra, majd válassza az **IoT Edge-modul** elemet.
1. A **Név** mezőbe írja a következőt: `tempSensor`.
1. A **Rendszerkép URI** mezőbe írja be a következőt: `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.

   ![IoT Edge-modul mentése a név és a rendszerkép URI megadása után](./media/iot-edge-deploy-module/name-image.png)

1. Az **Add Modules** (Modulok hozzáadása) lépésben kattintson a **Next** (Tovább) gombra.
1. Az **Útvonalak megadása** lépésben egy alapértelmezett útvonallal kell rendelkeznie, amely az összes modul összes üzenetét az IoT Hubba irányítja. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. Az **Üzembe helyezés áttekintése** lépésben kattintson a **Küldés** elemre.
1. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. A szolgáltatás indításakor létrehozott edgeAgent modul mellett a listában szerepelnie kell egy másik futtatókörnyezeti modulnak is **edgeHub** néven, valamint a **tempSensor** modulnak.

   ![A tempSensor megtekintése az üzembe helyezett modulok listájában](./media/iot-edge-deploy-module/deployed-modules.png)
