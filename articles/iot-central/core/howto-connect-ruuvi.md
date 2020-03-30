---
title: RuuviTag csatlakoztatása az Azure IoT Central szolgáltatásban | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathat egy RuuviTag környezetérzékelőt az IoT Central alkalmazáshoz.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158238"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>RuuviTag-érzékelő csatlakoztatása az Azure IoT Central alkalmazáshoz

Ez a cikk bemutatja, hogyan csatlakoztathatja a RuuviTag-érzékelőt a Microsoft Azure IoT Central alkalmazáshoz megoldáskészítőként.

Mi az a Ruuvi címke?

A RuuviTag egy fejlett, nyílt forráskódú érzékelőjel platform, amelyet az üzleti ügyfelek, fejlesztők, döntéshozók, diákok és amatőrök igényeinek kielégítésére terveztek. Az eszköz úgy van beállítva, hogy azonnal működjön, amint kiveszi a dobozából, és készen áll arra, hogy ott telepítse, ahol szüksége van rá. Ez egy Bluetooth LE jeladó, beépített környezetérzékelővel és gyorsulásmérővel.

A RuuviTag BLE -n (Bluetooth Low Energy) keresztül kommunikál, és az Azure IoT Central szolgáltatással való beszélgetéshez átjáróeszközre van szükség. Győződjön meg arról, hogy rendelkezik egy átjáró eszközzel, például a Rigado Cascade 500 beállítással, amely lehetővé teszi a RuuviTag számára az IoT Centralhoz való csatlakozást.

Kérjük, kövesse az [itt található utasításokat,](./howto-connect-rigado-cascade-500.md) ha egy Rigado Cascade 500 átjáróeszközt szeretne létrehozni.

## <a name="prerequisites"></a>Előfeltételek

A RuuviTag érzékelők csatlakoztatásához a következő erőforrásokra van szükség:

* Egy RuuviTag érzékelő. További információkért kérjük, látogasson el [RuuviTag](https://ruuvi.com/).
* Egy Rigado Cascade 500-as eszköz vagy egy másik BLE átjáró. További információkért kérjük, látogasson el [Rigado](https://www.rigado.com/).
* Egy Azure IoT Central alkalmazás. További információt az [Új alkalmazás létrehozása című](./quick-deploy-iot-central.md)témakörben talál.

## <a name="add-a-ruuvitag-device-template"></a>RuuviTag eszközsablon hozzáadása

A RuuviTag-érzékelő az Azure IoT Central alkalmazáspéldányba való alaplapához konfigurálnia kell egy megfelelő eszközsablont az alkalmazáson belül.

RuuviTag eszközsablon hozzáadása:

1. Keresse meg a bal oldali ablaktábla ***Eszközsablonok*** ![lapját,](./media/howto-connect-ruuvi/devicetemplate-new.png) válassza a **+ Új**: Új eszközsablon létrehozása Lehetőséget A lap lehetőséget ad ***egyéni sablon létrehozására*** vagy ***előre konfigurált eszközsablon használatára***
1. Válassza ki a RuuviTag eszközsablont az előre konfigurált ![eszközsablonok listájából az alábbi módon: Válassza a RuuviTag eszközsablont](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Válassza a ***Tovább: Testreszabás lehetőséget*** a következő lépés hez.
1. A következő képernyőn válassza a ***Létrehozás*** a C500 eszközsablon nak az IoT Central alkalmazásba való bedeszkázására lehetőséget.

## <a name="connect-a-ruuvitag-sensor"></a>RuuviTag érzékelő csatlakoztatása

Mint korábban említettük, a RuuviTag és az IoT Central alkalmazás csatlakoztatásához be kell állítania egy átjáróeszközt. Az alábbi lépések feltételezik, hogy beállított egy Rigado Cascade 500 átjáróeszközt.  

1. Kapcsolja be a Rigado Cascade 500 készüléket, és csatlakoztassa a hálózati kapcsolathoz (Etherneten vagy vezeték nélkül)
1. Pop a fedelet le a RuuviTag és húzza ki a műanyag fülön, hogy biztosítsa a kapcsolatot az akkumulátorral.
1. Helyezze a RuuviTag-ot egy Rigado Cascade 500-as átjáró közelébe, amely már konfigurálva van az IoT Central alkalmazásban.
1. Néhány másodperc en belül a RuuviTag nak meg kell jelennie az IoT Central eszközlistájában.  
    ![RuuviTag eszközlista](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Most már használhatja ezt a RuuviTag az IoT Central alkalmazáson belül.  

## <a name="create-a-simulated-ruuvitag"></a>Szimulált RuuviTag létrehozása

Ha nem rendelkezik fizikai RuuviTag-eszközzel, létrehozhat egy szimulált RuuviTag-érzékelőt, amelyet az Azure IoT Central alkalmazáson belüli teszteléshez használhat.

Szimulált RuuviTag létrehozása:

1. Válassza **az Eszközök > a RuuviTag lehetőséget.**
1. Válassza **a + Új**lehetőséget.
1. Adjon meg egy egyedi **eszközazonosítót** és egy rövid **eszköznevet.**  
1. Engedélyezze a **Szimulált** beállítást.
1. Kattintson a **Létrehozás** gombra.  

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan csatlakoztathat egy RuuviTag-ot az Azure IoT Central alkalmazáshoz, a javasolt következő lépés az [IoT Central alkalmazás testreszabása](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) egy végponttól végpontig tartó megoldás létrehozásához.
