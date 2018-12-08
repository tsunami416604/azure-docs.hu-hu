---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108022"
---
Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Az IoT Edge-modul egy tárolóként megvalósított végrehajtható csomag. Ebben a szakaszban azt fogja telepítését végzi az előre elkészített modul a [IoT Edge-modulok szakaszában az Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Ez a modul a szimulált eszköz telemetriai adatokat hoz létre.

1. Adja meg az Azure Portalon `Simulated Temperature Sensor` a keresést, és nyissa meg a Marketplace-en eredményt.

   ![Az Azure portál keresési szimulált hőmérséklet-érzékelő](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Az a **előfizetés** mezőben válassza ki az előfizetést és az IoT Hub használata esetén, ha még nem szerepel.

3. Az a **az IoT Hub** mezőben válassza ki az IoT Hub használata esetén a nevét, ha még nem szerepel.

4. Kattintson a **található eszköz**, válassza ki az IoT Edge-eszköz (nevű `myEdgeDevice`), majd **létrehozás**.

5. Az a **modulok hozzáadása** . lépés a varázslóban, kattintson a a **SimulatedTemperatureSensor** modult, ellenőrizze a konfigurációs beállításokat, kattintson a **mentése** Válassza**Tovább**.

6. Az a **útvonalak megadása** lépés a varázsló az útvonalak megfelelő beállítása az alapértelmezett útvonal, amely az összes modulok összes üzeneteket küld az IoT Hub-ellenőrzése (`$upstream`). Amennyiben nem így lenne, adja hozzá a következő kódot, és kattintson a **Tovább** gombra.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. Az a **áttekintése telepítési** . lépés a varázslóban válassza a **küldés**.

8. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Mellett a edgeAgent modul, amely a szolgáltatás első indításakor lett létrehozva, megjelenik egy másik futásidőben hívható modulra nevű **edgeHub** és a **SimulatedTemperatureSensor** felsorolt modul.

   Eltarthat néhány percig, az új modulokat jelenik meg. Az IoT Edge-eszköz rendelkezik az új központi telepítési adatok lekérését a felhőben, indítsa el a tárolók és jelentést készít a új állapotának – vissza az IoT hubnak. 

   ![A telepített modulok listájának megtekintése SimulatedTemperatureSensor](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
