---
title: Rigado Cascade 500 csatlakoztatása az Azure IoT Central szolgáltatásban | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathat egy Rigado Cascade 500 átjáróeszközt az IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158356"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Rigado Cascade 500 átjáróeszköz csatlakoztatása az Azure IoT Central alkalmazáshoz


Ez a cikk bemutatja, hogyan csatlakoztathatja a Rigado Cascade 500 átjáróeszközt a Microsoft Azure IoT Central alkalmazáshoz, megoldáskészítőként. 

## <a name="what-is-cascade-500"></a>Mi az a Cascade 500?

A Cascade 500 IoT átjáró a Rigado hardverajánlata, amely a Cascade Edge-as-a-Service megoldás részét képezi. Ez biztosítja a kereskedelmi IoT projekt és a termék csapatok rugalmas peremhálózati számítási teljesítmény, robusztus konténeres alkalmazás környezet, és számos vezeték nélküli eszköz csatlakozási lehetőségek, beleértve a Bluetooth 5, LTE, & Wi-Fi.

A Cascade 500 előre hitelesített az Azure IoT Plug and Play (előzetes verzió) számára, amely lehetővé teszi megoldásépítőink számára, hogy könnyedén beépíthessék az eszközt a végponttól végpontig történő megoldásaikba. A Cascade átjáró lehetővé teszi, hogy vezeték nélkül csatlakozzon a különböző állapotfigyelő érzékelőkhöz, amelyek az átjáróeszköz közelében vannak. Ezek az érzékelők az átjáróeszközön keresztül beszállhatnak az IoT Centralba.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató segítségével a következő forrásokra van szükség:

* Egy Rigado Cascade 500-as szerkezet. További információkért kérjük, látogasson el [Rigado](https://www.rigado.com/).
* Egy Azure IoT Central alkalmazás. További információt az [Új alkalmazás létrehozása című](./quick-deploy-iot-central.md)témakörben talál.

## <a name="add-a-device-template"></a>Eszközsablon hozzáadása

Annak érdekében, hogy egy Cascade 500 átjáróeszköz az Azure IoT Central alkalmazáspéldányba, konfigurálnia kell egy megfelelő eszközsablont az alkalmazáson belül.

Cascade 500 eszközsablon hozzáadása: 

1. Keresse meg a bal oldali ablaktábla ***Eszközsablonok*** ![lapját, és válassza a + **Új**: Új eszközsablon létrehozása lehetőséget](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. A lap lehetőséget ad ***egyéni sablon létrehozására*** vagy ***előre konfigurált eszközsablon használatára***
1. Válassza ki a C500 eszközsablont az előre konfigurált ![eszközsablonok listájából az alábbi módon: Válassza a C500 eszközsablont.](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Válassza a ***Tovább: Testreszabás lehetőséget*** a következő lépés hez. 
1. A következő képernyőn válassza a ***Létrehozás*** a C500 eszközsablon nak az IoT Central alkalmazásba való bedeszkázására lehetőséget.

## <a name="retrieve-application-connection-details"></a>Alkalmazáskapcsolat részleteinek beolvasása

Most le kell kérnie a **scope id** és **az elsődleges kulcsot** az Azure IoT Central alkalmazáshoz a Cascade 500-as eszköz csatlakoztatásához. 

1. Keresse meg a bal oldali ablaktáblában a **Felügyelet** elemet, és kattintson az **Eszközkapcsolat**elemre. 
2. Jegyezze fel az IoT Central-alkalmazás **hatókör-azonosítóját.**
![Alkalmazáshatókör azonosítója](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Most kattintson a **Keys megtekintése gombra,** és jegyezze fel az **elsődleges kulcs**
![elsődleges kulcsát](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Lépjen kapcsolatba rigado-val az átjáró csatlakoztatásához 

A Cascade 500 eszköz ioT Central alkalmazáshoz való csatlakoztatásához kapcsolatba kell lépnie a Rigado-val, és meg kell adnia nekik az alkalmazás kapcsolatának részleteit a fenti lépésekben. 

Miután az eszköz csatlakozik az internethez, a Rigado képes lesz lenyomni egy konfigurációs frissítést a Cascade 500 átjáró eszközre egy biztonságos csatornán keresztül. 

Ez a frissítés az IoT Central kapcsolat részleteit a Cascade 500 eszközön alkalmazza, és megjelenik az eszközök listájában. 

![Elsődleges kulcs](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Most már készen áll a C500-as eszköz használatára az IoT Central alkalmazásban!

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztathat egy Rigado Cascade 500-at az Azure IoT Central alkalmazáshoz, a javasolt következő lépés az, hogy megtanulják, hogyan [hozhat létre egy üzleten belüli elemzési alkalmazást](../retail/tutorial-in-store-analytics-create-app-pnp.md) egy végponttól végpontig tartó megoldás létrehozásához. 