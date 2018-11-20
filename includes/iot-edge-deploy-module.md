---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 536857a5fe3ec3cc037f21835a4152f93197bcb8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977397"
---
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Az IoT Edge-modul egy tárolóként megvalósított végrehajtható csomag. Ebben a szakaszban üzembe helyez egy modult, amely telemetriát hoz létre a szimulált eszköz számára.

1. Az Azure Portalon keresse meg az IoT-központot.

2. Lépjen az **IoT Edge** részhez az **Automatikus eszközkezelés** alatt, és válassza ki az IoT Edge-eszközt.

3. Válassza a **Modulok beállítása** lehetőséget. Egy három lépéses varázsló megnyílik a portálon, amely végigvezeti a modulok hozzáadása, útvonalak megadása és az üzembe helyezés áttekintése. 

4. Az a **modulok hozzáadása** lépés keresse meg a varázsló a **üzembe helyezési modulok** szakaszban. Kattintson a **Hozzáadás** majd **IoT Edge-modul**.

   ![Adjon hozzá egy új IoT Edge-modul](./media/iot-edge-deploy-module/add-module.png)

5. A **Név** mezőbe írja a következőt: `tempSensor`.

6. A **Rendszerkép URI** mezőbe írja be a következőt: `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.

7. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.

   ![IoT Edge-modul mentése a név és a rendszerkép URI megadása után](./media/iot-edge-deploy-module/name-image.png)

8. Vissza a varázsló első lépése, jelölje ki **tovább**.

9. Az a **útvonalak megadása** lépés a varázsló rendelkeznie kell egy alapértelmezett útvonalat, amely az összes üzenetet küld az összes modulok az IoT hubnak. Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

10. Az a **áttekintése telepítési** . lépés a varázslóban válassza a **küldés**.

11. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. A szolgáltatás indításakor létrehozott edgeAgent modul mellett a listában szerepelnie kell egy másik futtatókörnyezeti modulnak is **edgeHub** néven, valamint a **tempSensor** modulnak.

   Eltarthat néhány percig, az új modulokat jelenik meg. Az IoT Edge-eszköz rendelkezik az új központi telepítési adatok lekérését a felhőben, indítsa el a tárolók és jelentést készít a új állapotának – vissza az IoT hubnak. 

   ![A tempSensor megtekintése az üzembe helyezett modulok listájában](./media/iot-edge-deploy-module/deployed-modules.png)
