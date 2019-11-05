---
title: RuuviTag összekötése az Azure IoT Centralban | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható RuuviTag környezeti érzékelő a IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: f1d152c921d38931f8c67396fc5769cfd2dfcf58
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468235"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>RuuviTag-érzékelő összekötése az Azure IoT Central-alkalmazással

Ez a cikk azt ismerteti, hogyan csatlakozhat egy RuuviTag-érzékelőt a Microsoft Azure IoT Central alkalmazáshoz.

Mi az a ruuvi címke?

A RuuviTag egy fejlett, nyílt forráskódú érzékelő-irányjelző platform, amely az üzleti ügyfelek, a fejlesztők, a döntéshozók és a tanulók igényeinek kielégítésére szolgál, és akár saját maga is felhasználható. Az eszköz úgy van beállítva, hogy a saját mezőjéből kiindulva azonnal működjön, és készen áll a telepítésre, ahol szüksége van rá. A Bluetooth LE Beacon egy beépített környezeti érzékelővel és gyorsulásmérővel rendelkezik. 

A RuuviTag a kapcsolaton keresztül kommunikál (Bluetooth alacsony energiafogyasztású), ezért az Azure IoT Central-vel való kommunikációhoz átjáró-eszközre van szükség. Győződjön meg arról, hogy rendelkezik egy átjáróval, például a Rigado Cascade 500-as, a telepítővel, hogy csatlakozni tudjon a RuuviTag IoT Centralhoz. 

Kövesse az [itt található utasításokat](./howto-connect-rigado-cascade-500-pnp.md) , ha Rigado Cascade 500 Gateway-eszközt szeretne beállítani.

## <a name="prerequisites"></a>Előfeltételek
A RuuviTag-érzékelők összekapcsolásához a következő erőforrásokra van szükség:

* Egy RuuviTag-érzékelő. További információért látogasson el a [RuuviTag](https://ruuvi.com/)webhelyre. 
* Egy Rigado lépcsőzetes 500-eszköz vagy egy másik-átjáró. További információért látogasson el a [Rigado](https://www.rigado.com/)webhelyre.
* Azure IoT Central-alkalmazás, amely az egyik előzetes verziójú alkalmazás-sablonból lett létrehozva. További információt az [új alkalmazás létrehozása](https://docs.microsoft.com/azure/iot-central/core/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)című témakörben talál.

## <a name="add-a-ruuvitag-device-template"></a>RuuviTag-eszköz sablonjának hozzáadása

Ahhoz, hogy egy RuuviTag-érzékelőt beépítse az Azure IoT Central Application instanceba, konfigurálnia kell egy megfelelő sablont az alkalmazáson belül.

RuuviTag-sablon hozzáadása: 

1. Navigáljon a bal oldali ablaktáblán található ***eszközök*** panelre, majd válassza az **+ új**: ![új sablon létrehozása](./media/howto-connect-ruuvi/devicetemplate-new.png) a lapon lehetőség van ***egyéni sablon létrehozására*** vagy ***egy előre konfigurált sablon használatára***
1. Válassza ki a RuuviTag az előre konfigurált eszközök listájából az alább látható módon: ![válassza a RuuviTag-eszköz sablonja](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. A ***Tovább gombra*** kattintva folytassa a következő lépéssel. 
1. A következő képernyőn válassza a ***Létrehozás*** elemet a C500-eszköz sablonjának a IoT Central alkalmazásba való beléptetéséhez.

## <a name="connect-a-ruuvitag-sensor"></a>RuuviTag-érzékelő összekötése

Ahogy fent említettük, a RuuviTag a IoT Central alkalmazással való csatlakoztatásához be kell állítania egy átjáró-eszközt. Az alábbi lépésekben feltételezzük, hogy a Rigado Cascade 500 Gateway eszközt telepítette.  

1. Kapcsolja be a Rigado lépcsőzetes 500-eszközét, és csatlakoztatja a hálózati kapcsolathoz (Etherneten vagy vezeték nélküli hálózaton keresztül)
1. A fedél kikapcsolásával kikapcsolhatja a RuuviTag, és lekérheti a műanyag lapot az akkumulátorral való kapcsolódás biztonságossá tételéhez. 
1. Helyezze a RuuviTag a Rigado Cascade 500-átjáró közelében, amely korábban már konfigurálva van a IoT Central alkalmazással. 
1. Néhány másodperc elteltével a RuuviTag meg kell jelennie az eszközök listájában a IoT Centralon belül.  
![RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png) mostantól használhatja ezt a RuuviTag a IoT Central alkalmazásban.  

## <a name="create-a-simulated-ruuvitag"></a>Szimulált RuuviTag létrehozása
Ha nem rendelkezik a fizikai RuuviTag, létrehozhat egy szimulált RuuviTag-érzékelőt, amelyet az Azure IoT Central alkalmazásban való teszteléshez használhat.

Szimulált RuuviTag létrehozása:

1. Válassza az **eszközök > RuuviTag**elemet. 
1. Válassza az **+ új**lehetőséget. 
1. Adjon meg egy egyedi **azonosítót** és egy felhasználóbarát **eszköznév**.  
1. Engedélyezze a **szimulált** beállítást.
1. Kattintson a **Létrehozás** gombra.  

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakozhat egy RuuviTag az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés annak megismerése, hogyan [szabhatja testre a IoT Central alkalmazást](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) teljes körű megoldás létrehozásához. 