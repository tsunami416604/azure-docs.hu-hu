---
title: Egy eszköz kapcsolati karakterláncának létrehozása az Azure IoT Central |} A Microsoft Docs
description: Eszköz a fejlesztők hogyan do I létre eszköz, amelyet szeretne csatlakozni az IoT Central alkalmazáshoz tartozó kapcsolati karakterláncot?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615761"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Csatlakozhat egy Azure IoT Central alkalmazáshoz eszköz kapcsolati karakterláncának létrehozása

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők, eszköz, amelyet szeretne csatlakozni az IoT Central alkalmazáshoz tartozó kapcsolati karakterláncot létrehozni. Az ebben a cikkben leírt eljárás bemutatja, hogyan gyorsan csatlakozhat egy közös hozzáférésű jogosultságkód (SAS) használatával egyetlen eszközhöz. Ez a megközelítés akkor hasznos, amikor kísérleteztek az IoT-központ vagy a tesztelési eszközök. Alternatív módszerek az éles környezetben használni, lásd: [eszköz csatlakoztatása az Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

- Azure IoT Central alkalmazáshoz. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
- A fejlesztői gépen való [Node.js](https://nodejs.org/) 8.0.0 verzió vagy újabb verziója szükséges. Futtathat `node --version` a parancssorban a verzió ellenőrzéséhez. A Node.js az operációs rendszerek széles körében elérhető.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Az alábbi lépések bemutatják, hogyan tehet szert az adatokat, létre kell hoznia egy SAS-kapcsolati karakterláncot egy eszközhöz:

1. Az a **Device Explorer**, keresse meg az igazi eszközön, amelyhez csatlakozni szeretne az alkalmazáshoz:

    ![Valós eszközök kiválasztása](media/howto-generate-connection-string/real-devices.png)

1. Az a **eszköz** lapon jelölje be **Connect**:

    ![Válassza ki a csatlakozáshoz](media/howto-generate-connection-string/connect.png)

1. Jegyezze fel a kapcsolati adatok **hatókör azonosítója**, **Eszközazonosító**, és **eszköz elsődleges kulcs**ahhoz, hogy a következő lépésekben:

    ![Kapcsolat adatai](media/howto-generate-connection-string/device-connect.png)

    Ezen az oldalon mentéséhez másolhatja az értékeket.

## <a name="generate-the-connection-string"></a>A kapcsolati karakterlánc létrehozása

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>További lépések

Most, hogy már létrehozott egy valós eszköz csatlakozhat az Azure IoT Central alkalmazáshoz tartozó kapcsolati karakterláncot, Íme a javasolt következő lépések:

* [Készítse elő és (C) eszköz DevKit csatlakoztatása](howto-connect-devkit.md)
* [Készítse elő, és csatlakozzon a Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Készítse elő és a Raspberry Pi csatlakoztatása (C#)](howto-connect-raspberry-pi-csharp.md)
* [Készítse elő és a egy Windows 10 IoT core-eszköz csatlakoztatása (C#)](howto-connect-windowsiotcore.md)
* [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)