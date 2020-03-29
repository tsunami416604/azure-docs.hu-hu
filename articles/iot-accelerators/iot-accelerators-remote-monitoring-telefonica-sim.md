---
title: SIM-adatok integrálása a távoli figyelési megoldásba – Azure| Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan integrálható a Telefónica SIM-adatai a távfigyelési megoldásba.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442239"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>SIM-adatok integrálása a távfelügyeleti megoldásba

Az IoT-eszközök gyakran egy SIM-kártyával csatlakoznak a felhőhöz, amely lehetővé teszi számukra, hogy bárhonnan adatfolyamokat küldjenek. Az Azure IoT remote monitoring megoldás lehetővé teszi az IoT felügyelt kapcsolati adatok integrálását, így az operátorok az IoT-SIM által biztosított adatokon keresztül is nyomon követhetik az eszköz állapotát.

A távfigyelés a Telefónica IoT-kapcsolattal való automatikus integrációt biztosít, lehetővé téve az IoT connectivity platformot használó ügyfelek számára, hogy szinkronizálják az eszköz SIMs-kapcsolati adatait a megoldásaikkal. Ez a megoldás kiterjeszthető más IoT-kapcsolatszolgáltatók támogatására a [GitHub-tárházon](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)keresztül.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* A Telefónica IoT SIM-adatok integrálása a távfelügyeleti megoldásba
* Valós idejű telemetriai adatok megtekintése
* SIM-adatok megtekintése

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT integráció beállítása

### <a name="prerequisites"></a>Előfeltételek

Ez a további távoli figyelési funkció jelenleg előzetes verzióban érhető el. A kapcsolódási adatok Azure Remote Monitoring Solution szolgáltatásba való szinkronizálásához kövesse az alábbi lépéseket:

1. Töltse ki a kérelmet a [Telefónica webhelyén,](https://iot.telefonica.com/contact)válassza ki az **Azure Remote Monitoring**lehetőséget, beleértve a kapcsolattartási adatait is.
2. A Telefónica aktiválja a fiókját.
3. Ha Ön még nem Telefónica ügyfél, és szeretné élvezni ezt vagy más IoT Connectivity Cloud Ready szolgáltatásokat, látogasson el a [Telefónica webhelyére,](https://iot.telefonica.com/) és válassza a **Connectivity**lehetőséget.

### <a name="telefnica-sim-setup"></a>Telefónica SIM beállítás
A Telefónica SIM & Az Azure Twin eszközazonosító-társítás a Telefónica IoT SIM "alias" tulajdonságon alapul. 

Keresse meg a [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM-leltárt, > válassza ki a SIM-kártyát, és frissítse az egyes SIM-kártyákat a kívánt Twin deviceID azonosítóval. Ez a feladat tömeges módban is elvégezhető (lásd a Telefónica IoT Connectivity Platform felhasználói kézikönyveit).

Ez a feladat tömeges módban is elvégezhető (lásd a Telefónica IoT Connectivity Platform felhasználói kézikönyveit)

![Telefónica frissítés](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Ha az eszközt a távoli figyeléshez szeretné csatlakoztatni, kövesse ezeket az útmutatókat a [C](iot-accelerators-connecting-devices-linux.md) vagy [a Node](iot-accelerators-connecting-devices-node.md)használatával. 

## <a name="view-device-telemetry-and-sim-properties"></a>Eszköztelemetria és SIM-tulajdonságok megtekintése

Miután a Telefónica-fiók megfelelően van konfigurálva, és a készülék csatlakoztatva van, megtekintheti az eszköz adatait és a SIM-adatokat.

A következő kapcsolódási paraméterek kerülnek közzétételre:

* ICCID
* IP
* Hálózati jelenlét
* SIM-kártya állapota
* Hálózatalapú hely
* Felhasznált adatforgalom

![Irányítópult](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>További lépések

Most, hogy áttekintést kap aSIM-adatok Azure IoT Remote Monitoring szolgáltatásba való integrálásáról, az alábbiakban a következő lépéseket ismerteti a megoldásgyorsítók számára:

* [Az Azure IoT távfigyelési megoldás működtetése](quickstart-remote-monitoring-deploy.md)
* [Speciális monitorozás végrehajtása](iot-accelerators-remote-monitoring-monitor.md)
* [Eszközök kezelése](iot-accelerators-remote-monitoring-manage.md)
* [Eszközökkel kapcsolatos problémák elhárítása](iot-accelerators-remote-monitoring-maintain.md)

