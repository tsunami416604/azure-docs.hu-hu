---
title: SIM adatokat a távoli figyelésére szolgáló megoldás - Azure integrálása |} Microsoft Docs
description: A cikkből megtudhatja, hogyan integrálják a távoli figyelésére szolgáló megoldás a Telefonica SIM-adatokat.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>A távoli figyelésére szolgáló megoldás SIM adatok integrálása

## <a name="overview"></a>Áttekintés
Az IoT-eszközök gyakran csatlakozni a felhőhöz, amely lehetővé teszi, hogy az adatfolyamok küldjön bárhonnan SIM kártyával. Az Azure IoT távoli figyelésére szolgáló megoldás lehetővé teszi a SIM-felügyeleti adatokat, így operátorok is nyomon követheti a SIM által szolgáltatott adatokat keresztül az eszköz állapotát. Távoli figyelés kívül Telefonica IoT be integrálva az IoT-kapcsolat platformot használnak az ügyfelek szinkronizálása az eszköz lehetővé SIM-kártya csatlakozási adatokat biztosít a megoldások. Ez a megoldás kiterjeszthető más telefon vállalati szolgáltatók GitHub-tárházban keresztül támogatja.
Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
* A távoli figyelésére szolgáló megoldás integrálja a SIM adatait
* Nézet valós idejű telemetriai adat
* SIM-adatok megtekintése 

## <a name="telefonica-iot-integration-setup"></a>Telefonica IoT-integráció telepítője

### <a name="prerequisites"></a>Előfeltételek
A kapcsolati adatokat szinkronizálni az Azure távoli figyelésére szolgáló megoldás, kövesse az alábbi lépéseket:

1.  Töltse ki a kérelem [Telefonica tartozó hely](https://iot.telefonica.com/contact), a beállításnak a **Azure távoli megfigyelési**, a kapcsolattartási adatokat is beleértve.
2.  Telefonica aktiválja a fiókját. 
3.  Ha még nem Telefónica ügyfelet, és ez vagy más IoT kapcsolat felhő készen áll a szolgáltatások, látogasson el szeretné [Telefonica tartozó hely](https://iot.telefonica.com/contact) válassza ki, **kapcsolat**.

### <a name="telefonica-sim-setup"></a>Telefonica SIM-telepítő
Telefónica SIM & Azure a két eszköz azonosítója társítása Telefónica IoT SIM "alias" tulajdonság alapul. 

Navigáljon a [Telefónica IoT kapcsolat Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM készlet > Válassza ki a SIM, és frissítse az egyes SIM "alias" a kívánt iker deviceID. 

Ez a feladat is elvégezhető tömeges módban (Telefónica IoT kapcsolat Platform felhasználói laborútmutatókkal vonatkozik)

![Telefonica frissítés](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Csatlakoztassa az eszközt a távoli megfigyelési, ezek az oktatóanyagok használatával is végrehajthatja [C](iot-suite-connecting-devices-linux.md) vagy [csomópont](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Nézet telemetriát és SIM tulajdonságai
Miután a Telefonica fiók helyesen van konfigurálva, és az eszköz csatlakoztatva van, eszköz adatai és a SIM-adatok tekintheti meg.
A következő kapcsolat paraméterek közzé kell tenni:
* ICCID
* IP
* Hálózati jelenléte
* SIM állapota
* Hálózati hely
* Felhasznált az adatforgalmat

![Irányítópult](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>További lépések

Most, hogy hogyan integrálja a SIM-adatok Azure IoT távoli figyelés, az alábbiakban megoldások gyorsítók javasolt lépéseket:

* [Az Azure IoT távoli figyelésére szolgáló megoldás működik](iot-suite-remote-monitoring-explore.md)
* [Speciális monitorozás végrehajtása](iot-suite-remote-monitoring-monitor.md)
* [Saját eszközök kezelése](iot-suite-remote-monitoring-manage.md)
* [Eszközökkel kapcsolatos problémák elhárítása](iot-suite-remote-monitoring-maintain.md)

