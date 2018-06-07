---
title: SIM adatokat a távoli figyelésére szolgáló megoldás - Azure integrálása |} Microsoft Docs
description: A cikkből megtudhatja, hogyan integrálják a távoli figyelésére szolgáló megoldás a Telefónica SIM-adatokat.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: ae8751f429cb4b11199bd8da9d8c2e08c0b98b35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628283"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>A távoli figyelésére szolgáló megoldás SIM adatok integrálása

Az IoT-eszközök gyakran csatlakozni a felhőhöz, amely lehetővé teszi, hogy az adatfolyamok küldjön bárhonnan SIM kártyával. Az Azure IoT távoli figyelésére szolgáló megoldás lehetővé teszi az IoT felügyelt kapcsolati adatokat, így operátorok is nyomon követheti az eszközt az IoT-SIM által szolgáltatott adatokat keresztül állapotát.

Távoli figyelés kívül a Telefónica IoT kapcsolatban, a mezőben integrációs biztosít, lehetővé téve az IoT-kapcsolat platformot használnak az ügyfelek szinkronizálása az eszköz SIM-kártya kapcsolati adatokat a megoldásokhoz. Ez a megoldás támogatja a Githubon keresztül más IoT kapcsolat szolgáltatók kiterjeszthető [tárház](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* A távoli figyelésére szolgáló megoldás integrálja a Telefónica IoT SIM adatait
* Nézet valós idejű telemetriai adat
* SIM-adatok megtekintése

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT-integráció telepítője

### <a name="prerequisites"></a>Előfeltételek

A további távoli figyelési funkció jelenleg előzetes verzió. A kapcsolati adatokat szinkronizálni az Azure távoli figyelésére szolgáló megoldás, kövesse az alábbi lépéseket:

1. Töltse ki a kérelem [Telefónica tartozó hely](https://iot.Telefónica.com/contact), a beállításnak a **Azure távoli megfigyelési**, a kapcsolattartási adatokat is beleértve.
2. Telefónica aktiválja a fiókját.
3. Ha még nem Telefónica ügyfelet, és ez vagy más IoT kapcsolat felhő készen áll a szolgáltatások, látogasson el szeretné [Telefónica tartozó hely](https://iot.Telefónica.com/contact) válassza ki, **kapcsolat**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM-telepítő
Telefónica SIM & Azure a két eszköz azonosítója társítása Telefónica IoT SIM "alias" tulajdonság alapul. 

Navigáljon a [Telefónica IoT kapcsolat Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM készlet > Válassza ki a SIM, és frissítse az egyes SIM "alias" a kívánt iker deviceID. Ez a feladat is elvégezhető tömeges módban (lásd Telefónica IoT kapcsolat Platform felhasználói kézikönyv).

Ez a feladat is elvégezhető tömeges módban (Telefónica IoT kapcsolat Platform felhasználói laborútmutatókkal vonatkozik)

![Telefónica frissítés](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Csatlakoztassa az eszközt a távoli megfigyelési, ezek az oktatóanyagok használatával is végrehajthatja [C](iot-accelerators-connecting-devices-linux.md) vagy [csomópont](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Nézet telemetriát és SIM tulajdonságai

Miután a Telefónica fiók helyesen van konfigurálva, és az eszköz csatlakoztatva van, eszköz adatai és a SIM-adatok tekintheti meg.

A következő kapcsolat paraméterek közzétett:

* ICCID
* IP
* Hálózati jelenléte
* SIM állapota
* Hálózati hely
* Felhasznált az adatforgalmat

![Irányítópult](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>További lépések

Most, hogy hogyan integrálja a SIM-adatok Azure IoT távoli figyelés, az alábbiakban megoldások gyorsítók javasolt lépéseket:

* [Az Azure IoT távoli figyelésére szolgáló megoldás működik](iot-accelerators-remote-monitoring-explore.md)
* [Speciális monitorozás végrehajtása](iot-accelerators-remote-monitoring-monitor.md)
* [Saját eszközök kezelése](iot-accelerators-remote-monitoring-manage.md)
* [Eszközökkel kapcsolatos problémák elhárítása](iot-accelerators-remote-monitoring-maintain.md)

