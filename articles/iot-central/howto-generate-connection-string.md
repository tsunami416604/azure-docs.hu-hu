---
title: Eszköz-kapcsolatok karakterláncának létrehozása az Azure IoT Centralhoz | Microsoft Docs
description: Hogyan lehet létrehozni egy IoT Central alkalmazáshoz csatlakozni kívánó eszközhöz tartozó kapcsolati sztringet?
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019780"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Eszköz kapcsolati karakterlánc létrehozása Azure IoT Central-alkalmazáshoz való kapcsolódáshoz

Ez a cikk azt ismerteti, hogyan lehet egy eszköz fejlesztőként létrehozni egy kapcsolati karakterláncot olyan eszközhöz, amelynek IoT Central alkalmazáshoz kell csatlakoznia. A cikkben ismertetett eljárás azt mutatja be, hogyan lehet gyorsan összekapcsolást egyetlen eszközt közös hozzáférésű aláírással (SAS). Ez a megközelítés akkor lehet hasznos, ha IoT Central vagy tesztelési eszközökkel kísérletezik. Az éles környezetben használható alternatív módszerekért lásd: [az eszközök csatlakoztatása az Azure IoT Centralban](concepts-connectivity.md).

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

- Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
- A [Node. js](https://nodejs.org/) 8.0.0 vagy újabb verzióját futtató fejlesztői gép. A parancssorban `node --version` futtatva ellenőrizhető a verzió. A Node.js az operációs rendszerek széles körében elérhető.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

A következő lépések bemutatják, hogyan kérheti le a SAS-kapcsolódási karakterlánc létrehozásához szükséges információkat az eszközhöz:

1. A **Device Explorer**keresse meg a valódi eszközt, amelyhez csatlakozni szeretne az alkalmazáshoz:

    ![Valódi eszköz kiválasztása](media/howto-generate-connection-string/real-devices.png)

1. Az **eszköz** lapon válassza a **Csatlakoztatás**lehetőséget:

    ![Válassza a kapcsolat lehetőséget.](media/howto-generate-connection-string/connect.png)

1. Jegyezze fel a kapcsolat részleteit, a **hatókör azonosítóját**, az **eszköz azonosítóját**és az **eszköz elsődleges kulcsát**a következő lépésekben való használatra:

    ![Kapcsolat részletei](media/howto-generate-connection-string/device-connect.png)

    Az ezen a lapon található értékeket a mentéshez másolhatja.

## <a name="generate-the-connection-string"></a>A kapcsolatok karakterláncának létrehozása

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta a kapcsolati karakterláncot egy valódi eszközhöz az Azure IoT Central-alkalmazáshoz való kapcsolódáshoz, a következő lépések javasoltak:

* [Fejlesztői készlet-eszköz előkészítése és csatlakoztatása (C)](howto-connect-devkit.md)
* [Málna PI (Python) előkészítése és összekötése](howto-connect-raspberry-pi-python.md)
* [Málna PI (C#) előkészítése és összekapcsolása](howto-connect-raspberry-pi-csharp.md)
* [Windows 10 IoT Core-eszköz előkészítése és csatlakoztatásaC#()](howto-connect-windowsiotcore.md)
* [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)