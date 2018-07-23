---
title: A távoli figyelési megoldás – Azure SIM-adatok integrálása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan integrálhatja a távoli figyelési megoldás Telefónica SIM-adatokat.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: c453998eea2a747b2cb608482f0ef9c1ee197ee0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185431"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>A távoli figyelési megoldásban SIM-adatok integrálása

IoT-eszközök csatlakoztatása a felhőhöz, a SIM-kártya, amely lehetővé teszi, hogy bárhonnan küldeni a data-adatfolyamok használatával gyakran. Az Azure IoT távoli figyelési megoldás lehetővé teszi az IoT felügyelt csatlakozási adatok, így operátorok is nyomon követheti az adatokat az IoT-SIM által biztosított keresztül az eszköz állapotát.

Távoli figyelés kívül a szolgáltatással való Telefónica IoT-kapcsolatot, biztosít, lehetővé teszi az IoT-kapcsolat Platform segítségével ügyfeleink szinkronizálása eszközüket SIM-kártya csatlakozási adatok megoldásaikat. Ez a megoldás támogatja a Githubon keresztül más IoT kapcsolatszolgáltatók kiterjeszthető [tárház](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* A távoli figyelési megoldás Telefónica IoT SIM-adatok integrálása
* Valós idejű telemetriai adatok megtekintése
* SIM-adatok megtekintése

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT-integráció beállítása

### <a name="prerequisites"></a>Előfeltételek

Ez további, a távoli figyelési funkció jelenleg előzetes verzióban érhető el. A csatlakozási adatok távoli figyelési megoldás az Azure-ba való szinkronizálásához, kövesse az alábbi lépéseket:

1. Töltse be kérelmet [Telefónica a hely](https://iot.telefonica.com/contact), válassza a **Azure távoli megfigyelés**, beleértve a kapcsolattartási adatokat.
2. Telefónica aktiválja a fiókhoz.
3. Ha még nem regisztrált Telefónica ügyfél és a jelen vagy egyéb IoT kapcsolat Felhőbeli kész szolgáltatások igénybevételéhez, keresse fel szeretné [Telefónica a hely](https://iot.telefonica.com/) , és jelölje be **kapcsolat**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM-telepítő
Telefónica SIM & Azure Ikereszköz azonosító eszköztársítás Telefónica IoT SIM "alias" tulajdonság alapján történik. 

Navigáljon a [Telefónica IoT kapcsolat Platform portál](https://m2m-movistar-es.telefonica.com/) > SIM-készlet > Válassza ki a SIM, és frissítse az Ikereszköz kívánt eszközazonosítót az egyes SIM "másodlagos". Ez a feladat is elvégezhető tömegesen módban (lásd a Telefónica IoT kapcsolat Platform felhasználói kézikönyv).

Ez a feladat is elvégezhető tömegesen módban (lásd a Telefónica IoT kapcsolat Platform felhasználói kézikönyv)

![Telefónica Update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Az eszköz csatlakoztatása a távoli megfigyelés, kövesse ezekben az oktatóanyagokban használja [C](iot-accelerators-connecting-devices-linux.md) vagy [csomópont](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Eszköztelemetria megtekintése és SIM tulajdonságai

Miután a Telefónica fiók megfelelően van konfigurálva, és az eszköz csatlakoztatva van, megtekintheti a eszközadatok és a SIM-adatok.

A következő kapcsolódási paraméterek közzétett:

* ICCID
* IP
* Hálózati jelenlét
* SIM állapota
* Hálózati hely
* Felhasznált adatok forgalom

![Irányítópult](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>További lépések

Most, hogy hogyan integrálhatók a SIM-adatok az Azure IoT távoli figyelési áttekintését, az alábbiakban javasolt következő lépések az megoldások megoldásgyorsítók:

* [Az Azure IoT távoli figyelési megoldás üzemeltetése](quickstart-remote-monitoring-deploy.md)
* [Speciális monitorozás végrehajtása](iot-accelerators-remote-monitoring-monitor.md)
* [Saját eszközök kezelése](iot-accelerators-remote-monitoring-manage.md)
* [Eszközökkel kapcsolatos problémák elhárítása](iot-accelerators-remote-monitoring-maintain.md)

