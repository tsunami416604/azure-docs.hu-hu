---
title: A Rigado Cascade 500-es verziójának összekötése az Azure IoT Centralban | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható a Rigado Cascade 500-átjáró eszköz a IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: b07ce135d9efec6ecad369b6333d0a3bf2c3aab1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468245"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Rigado Cascade 500-átjáró eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz


Ez a cikk azt ismerteti, hogyan lehet megoldás-szerkesztőként csatlakozni a Rigado Cascade 500 Gateway-eszközhöz a Microsoft Azure IoT Central alkalmazáshoz. 

## <a name="what-is-cascade-500"></a>Mi az a Cascade 500?

A Cascade 500 IoT-átjáró egy olyan Rigado, amely a saját lépcsőzetes peremhálózati megoldás részeként szerepel. Kereskedelmi IoT-projekteket és-termékeket biztosít rugalmas peremhálózati számítási teljesítménnyel, robusztus tárolós alkalmazási környezettel és számos vezeték nélküli eszköz csatlakozási lehetőséggel, beleértve a Bluetooth 5, HTH, & Wi-Fi-t.

A Cascade 500 előzetes tanúsítvánnyal rendelkezik az Azure IoT Plug and Play (PnP) számára, amely lehetővé teszi a megoldás-építők számára, hogy a teljes körű megoldásokhoz egyszerűen beépítsék az eszközt. A lépcsőzetes átjáró lehetővé teszi, hogy vezeték nélkül kapcsolódjon az átjáró-eszköz közelében lévő különböző feltételi figyelő érzékelőkhöz. Ezek az érzékelők IoT Centralba helyezhetők az átjáró-eszközön keresztül.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következő erőforrásokra van szükség:

* Egy Rigado lépcsőzetes 500-eszköz. További információért látogasson el a [Rigado](https://www.rigado.com/)webhelyre.
* Azure IoT Central-alkalmazás, amely az egyik előzetes verziójú alkalmazás-sablonból lett létrehozva. További információt az [új alkalmazás létrehozása](https://docs.microsoft.com/azure/iot-central/core/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)című témakörben talál.

## <a name="add-a-device-template"></a>Eszköz sablonjának hozzáadása

A Cascade 500 Gateway-eszköz Azure IoT Central Application-példányba való beléptetéséhez konfigurálnia kell egy megfelelő sablont az alkalmazáson belül.

Lépcsőzetes 500-eszköz sablonjának hozzáadása: 

1. Navigáljon a bal oldali ablaktáblán található ***eszközök*** lapra, válassza az **+ új**: ![új sablon létrehozása](./media/howto-connect-rigado-cascade500-pnp/device-template-new.png)
1. Az oldalon lehetőség van ***egyéni sablon létrehozására*** vagy ***egy előre konfigurált sablon használatára***
1. Válassza ki a C500 az előre konfigurált eszközök listájából az alább látható módon: ![válassza a C500-eszköz sablonja](./media/howto-connect-rigado-cascade500-pnp/device-template-preconfigured.png)
1. A ***Tovább gombra*** kattintva folytassa a következő lépéssel. 
1. A következő képernyőn válassza a ***Létrehozás*** elemet a C500-eszköz sablonjának a IoT Central alkalmazásba való beléptetéséhez.

## <a name="retrieve-application-connection-details"></a>Alkalmazás-kapcsolat részleteinek beolvasása

Most le kell kérnie az Azure IoT Central-alkalmazás **hatókör-azonosítóját** és **elsődleges kulcsát** a lépcsőzetes 500-eszköz csatlakoztatásához. 

1. Navigáljon a **felügyelet** elemre a bal oldali ablaktáblán, és kattintson az **eszköz csatlakoztatása**elemre. 
2. Jegyezze fel IoT Central alkalmazás **hatókör-azonosítóját** .
![alkalmazás hatókör-azonosítója](./media/howto-connect-rigado-cascade500-pnp/app-scope-id.png)
3. Most kattintson a **kulcsok megtekintése** elemre, és jegyezze fel az **elsődleges kulcs**
![elsődleges kulcsát](./media/howto-connect-rigado-cascade500-pnp/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Az átjáróhoz való kapcsolódáshoz forduljon a Rigado-hez 

Ahhoz, hogy a Cascade 500 eszközt csatlakoztatni lehessen a IoT Central alkalmazáshoz, kapcsolatba kell lépnie a Rigado, és meg kell adnia a fenti lépések alkalmazás-csatlakozási részleteit. 

Miután az eszköz csatlakozik az internethez, a Rigado leküldheti a konfigurációs frissítést a lépcsőzetes 500 Gateway eszközre egy biztonságos csatornán keresztül. 

Ez a frissítés alkalmazza a IoT Central kapcsolat részleteit a lépcsőzetes 500 eszközön, és megjelenik az eszközök listájában. 

![Elsődleges kulcs](./media/howto-connect-rigado-cascade500-pnp/devices-list-c500.png)  

Most már készen áll a C500-eszköz használatára a IoT Central alkalmazásban!

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan csatlakoztatható a Rigado Cascade 500 az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, hogyan [hozhat létre egy beépített elemzési alkalmazást](../retail/tutorial-in-store-analytics-create-app-pnp.md) a teljes körű megoldás létrehozásához. 