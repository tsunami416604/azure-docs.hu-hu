---
title: SIM-alkalmazások integrálása a távoli figyelési megoldásba – Azure | Microsoft Docs
description: Ez a cikk bemutatja, hogyan integrálhatja a Telefónica SIM-információkat a távoli figyelési megoldásba.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "61442239"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>SIM-alkalmazások integrálása a távoli figyelési megoldásba

A IoT-eszközök gyakran csatlakoznak a felhőhöz egy SIM-kártyával, amely lehetővé teszi az adatstreamek bárhonnan történő küldését. Az Azure IoT-alapú távoli figyelési megoldás lehetővé teszi a IoT által felügyelt kapcsolati információk integrálását, így az operátorok a IoT SIM által biztosított információkon keresztül is nyomon követhetik az eszköz állapotát.

A távoli figyelés lehetővé teszi a Telefónica IoT-kapcsolattal való integrációt, lehetővé téve az ügyfelek számára, hogy a IoT-kapcsolati platformját szinkronizálják az eszközük SIMs-kapcsolati adatait a megoldásaikkal. Ez a megoldás bővíthető a GitHub- [tárházon](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)keresztül más IoT-kapcsolati szolgáltatók támogatásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* A Telefónica IoT SIM-adatbázisának integrálása a távoli figyelési megoldásba
* Valós idejű telemetriai adatok megtekintése
* SIM-adattárolás megtekintése

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT-integráció beállítása

### <a name="prerequisites"></a>Előfeltételek

Ez a további távoli figyelési funkció jelenleg előzetes verzióban érhető el. Az alábbi lépéseket követve szinkronizálhatja a kapcsolati adatait az Azure távoli figyelési megoldásával:

1. Töltsön ki egy kérelmet a [Telefónica's webhelyen](https://iot.telefonica.com/contact), és válassza az **Azure távoli figyelés**lehetőséget, beleértve a kapcsolattartási adatait is.
2. A Telefónica aktiválja a fiókját.
3. Ha még nem Telefónica-ügyfél, és szeretné használni ezt vagy más IoT-kapcsolati Felhőbeli használatra kész szolgáltatásokat, látogasson el a [Telefónica's webhelyére](https://iot.telefonica.com/) , és válassza a **kapcsolat**lehetőséget.

### <a name="telefnica-sim-setup"></a>Telefónica SIM-telepítő
A Telefónica SIM & Azure Twin Device ID társítás a Telefónica IoT SIM "alias" tulajdonságán alapul. 

Navigáljon a [Telefónica IoT connectivity platform portál](https://m2m-movistar-es.telefonica.com/) > sim Inventory > válassza ki a SIM-t, és frissítse az egyes SIM-aliasokat a kívánt Twin deviceID használatával. Ez a feladat tömeges módban is elvégezhető (lásd: Telefónica IoT connectivity platform felhasználói kézikönyvek).

Ez a feladat tömeges módban is elvégezhető (lásd: Telefónica IoT connectivity platform felhasználói kézikönyvek)

![Telefónica frissítése](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Ha az eszközt a távoli figyeléshez szeretné kapcsolni, a [C](iot-accelerators-connecting-devices-linux.md) vagy a [csomópont](iot-accelerators-connecting-devices-node.md)használatával követheti ezeket az oktatóanyagokat. 

## <a name="view-device-telemetry-and-sim-properties"></a>Az eszköz telemetria és SIM-tulajdonságainak megtekintése

Ha a Telefónica-fiókja megfelelően van konfigurálva, és az eszköz csatlakoztatva van, megtekintheti az eszköz adatait és a SIM-adatokat.

A következő kapcsolódási paraméterek vannak közzétéve:

* ICCID
* IP
* Hálózati jelenlét
* SIM-állapot
* Hálózat alapú hely
* Adatforgalom felhasználva

![Irányítópult](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>További lépések

Most, hogy áttekinti, hogyan integrálhatja a SIM-információkat az Azure IoT távoli monitorozási szolgáltatásba, a következő lépéseket javasoljuk a megoldás-gyorsítók:

* [Az Azure IoT távoli figyelési megoldásának üzemeltetése](quickstart-remote-monitoring-deploy.md)
* [Speciális monitorozás végrehajtása](iot-accelerators-remote-monitoring-monitor.md)
* [Eszközök kezelése](iot-accelerators-remote-monitoring-manage.md)
* [Eszközökkel kapcsolatos problémák elhárítása](iot-accelerators-remote-monitoring-maintain.md)

