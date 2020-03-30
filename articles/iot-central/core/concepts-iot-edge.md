---
title: Azure IoT Edge és Azure IoT Central | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure IoT Edge-et egy IoT Central alkalmazással.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027068"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge-eszközök csatlakoztatása egy Azure IoT Central alkalmazáshoz

Az IoT Edge három összetevőből áll:

* **Az IoT Edge-modulok olyan tárolók,** amelyek Azure-szolgáltatásokat, partnerszolgáltatásokat vagy saját kódot futtatnak. A modulok az IoT Edge-eszközökre vannak telepítve, és azokon az eszközökön helyileg futnak.
* Az **IoT Edge-futásidejű fut** minden IoT Edge-eszközön, és kezeli az egyes eszközökre telepített modulokat.
* A felhőalapú felület lehetővé teszi az IoT **Edge-eszközök** távoli figyelése és kezelése. Az IoT Central a felhőfelület.

Az **Azure IoT Edge-eszköz** lehet egy átjáró eszköz, az IoT Edge-eszközhöz csatlakozó alsóbb rétegbeli eszközökkel. Ez a cikk további információkat oszt meg az alsóbb rétegbeli eszközkapcsolati mintákról.

Az **eszközsablon** az eszköz és az IoT Edge-modulok képességeit határozza meg. A képességek közé tartozik a modul által küldött telemetriai adatok, a modul tulajdonságai és a parancsok, amelyekre a modul válaszol.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Alsóbb rétegbeli eszközkapcsolatok átjáróval és modulokkal

Az alsóbb rétegbeli eszközök a modulon keresztül csatlakozhatnak egy `$edgeHub` IoT Edge-átjáróeszközhöz. Ez az IoT Edge-eszköz lesz egy átlátszó átjáró ebben a forgatókönyvben.

![Átlátszó átjáró diagramja](./media/concepts-iot-edge/gateway-transparent.png)

Az alsóbb rétegbeli eszközök egy Egyéni modulon keresztül is csatlakozhatnak egy IoT Edge-átjáróeszközhöz. A következő esetben az alsóbb rétegbeli eszközök egy Modbus egyéni modulon keresztül csatlakoznak.

![Egyéni modulkapcsolat diagramja](./media/concepts-iot-edge/gateway-module.png)

Az alábbi ábrán az IoT Edge átjáróeszközhöz való csatlakozás `$edgeHub`látható mindkét típusú modulon (egyéni és ) keresztül.  

![A két csatlakozómodulon keresztüli csatlakozás diagramja](./media/concepts-iot-edge/gateway-module-transparent.png)

Végül az alsóbb rétegbeli eszközök több egyéni modulon keresztül csatlakozhatnak egy IoT Edge-átjáróeszközhöz. Az alábbi ábrán egy Modbus egyéni modulon, egy BLE-es egyéni modulon és a `$edgeHub` modulon keresztül csatlakozó alsóbb rétegbeli eszközök láthatók. 

![Több egyéni modulon keresztüli csatlakozás diagramja](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Telepítési jegyzékek és eszközsablonok

Az IoT Edge-ben üzembe helyezheti és kezelheti az üzleti logikát modulok formájában. Az IoT Edge-modulok az IoT Edge által kezelt legkisebb számítási egység, és tartalmazhatnak Azure-szolgáltatásokat (például az Azure Stream Analytics) vagy a saját megoldásspecifikus kódot. A modulok fejlesztésének, üzembe helyezésének és karbantartásának megértéséhez lásd: [IoT Edge-modulok.](../../iot-edge/iot-edge-modules.md)

Magas szinten a központi telepítési jegyzékfájl a modul ikreik listáját, amelyek a kívánt tulajdonságokkal vannak konfigurálva. A központi telepítési jegyzékfájl lemondja az IoT Edge-eszköz (vagy eszközök egy csoportja), amely modulok telepítése, és hogyan konfigurálja őket. A központi telepítési jegyzékek tartalmazzák az egyes ikermodulok kívánt tulajdonságait. Az IoT Edge-eszközök jelentés vissza az egyes modulok jelentett tulajdonságait.

A Visual Studio-kód használatával hozzon létre egy központi telepítési jegyzékfájlt. További információ: [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Az Azure IoT Central ban importálhat egy központi telepítési jegyzékfájlt egy eszközsablon létrehozásához. Az alábbi folyamatábra egy telepítési jegyzékfájl életciklusát mutatja az IoT Centralban.

![A telepítési jegyzék életciklusának folyamatábrája](./media/concepts-iot-edge/dmflow.png)

Az IoT Plug and Play (előzetes verzió) az alábbiak szerint modellezi az IoT Edge-eszközt:

* Minden IoT Edge-eszközsablon rendelkezik egy eszközképességi modellel.
* A központi telepítési jegyzékben felsorolt minden egyéni modulhoz létrejön egy modulképességi modell.
* Az egyes modulképességi modellek és az eszközképességi modell között kapcsolat jön létre.
* A modulképességi modell modulinterfészeket valósít meg.
* Minden modulfelület telemetriai adatokat, tulajdonságokat és parancsokat tartalmaz.

![IoT Edge modellezés diagramja](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge átjáróeszközök

Ha egy IoT Edge-eszközt választott átjáróeszköznek, hozzáadhat alsóbb rétegbeli kapcsolatokat az átjáróeszközhöz csatlakoztatni kívánt eszközök eszközképesség-modelljeihez.

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, mik az IoT Central alkalmazássablonok, az [IoT Központi alkalmazás létrehozásával](quick-deploy-iot-central.md)első lépések.