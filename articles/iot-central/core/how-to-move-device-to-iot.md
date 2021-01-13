---
title: Eszköz áthelyezése az Azure IoT Centralba IoT Hub
description: Eszköz áthelyezése az Azure IoT Centralba IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146453"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Eszköz átvitele az Azure IoT Centralba IoT Hub

*Ez a cikk az operátorokra és az eszközök fejlesztőire vonatkozik.*  

Ez a cikk azt ismerteti, hogyan vihető át egy eszköz egy Azure IoT Central-alkalmazásba egy IoT Hubból. 

Az eszköz először csatlakozik egy DPS-végponthoz, hogy lekérje az alkalmazáshoz való csatlakozáshoz szükséges információkat. Belsőleg a IoT Central-alkalmazás egy IoT hub használatával kezeli az eszköz kapcsolatát.  

Az eszközök közvetlenül a kapcsolati sztring vagy a DPS használatával csatlakoztathatók egy IoT hubhoz. Az [Azure IoT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) a IoT Central útvonala.

## <a name="to-move-the-device"></a>Az eszköz áthelyezése

Eszköz csatlakoztatása az IOT hub-IoT Central az eszközt a következővel kell frissíteni:

* A IoT Central alkalmazás [hatókör-azonosítója](../../iot-dps/concepts-service.md) .
* Az SAS-kulcs vagy [az X. 509-tanúsítvány](../../iot-hub/iot-hub-x509ca-overview.md) [csoportból](concepts-get-connected.md) származtatott kulcs

A IoT Central való interakcióhoz az eszköz által megvalósított tulajdonságokat/telemetria/parancsokat modellező eszköznek kell lennie. További információ: [Csatlakozás a IoT Centralhoz](concepts-get-connected.md) és [Mik azok az eszközök sablonjai?](concepts-device-templates.md)

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, néhány javasolt lépés a következő:

- Tekintse át a következő kódrészletet, amely bemutatja, hogyan használhatók az SAS-tokenek az [oktatóanyagban: ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással](tutorial-connect-device.md)
- Ismerje meg, hogyan [csatlakoztathatók az X. 509 tanúsítvánnyal rendelkező eszközök a IoT Central alkalmazáshoz készült Node.js ESZKÖZOLDALI SDK-val](how-to-connect-devices-x509.md)
- Ismerje meg, hogyan [figyelheti az eszközök kapcsolatát az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
- Ismerje meg, hogyan [határozhat meg új IoT-típust az Azure IoT Central-alkalmazásban](./howto-set-up-template.md)
- További információ a [Azure IoT Edge eszközökről és az Azure IoT Central](./concepts-iot-edge.md)