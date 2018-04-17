---
title: Szimulált málna Pi felhőbe (Node.js) - csatlakozás málna Pi webes szimulátor több Azure IoT Hub |} Microsoft Docs
description: Csatlakozás málna Pi webes szimulátor Azure IoT-központ málna Pi Azure felhőben történő adatküldéshez.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timtl
tags: ''
keywords: Raspberry pi szimulátor, az azure iot raspberry pi, raspberry pi iot-központ, raspberry pi adatokat küldött a felhőben, raspberry pi felhőbe
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: fe953b56e0a4dbbf7aaaa81d5b8ac6e31bb6bc4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Csatlakozás málna Pi online szimulátor Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Ebben az oktatóanyagban akkor először tanulás alapjainak málna Pi online szimulátor használata. Majd megtudhatja, hogyan zökkenőmentesen használatával történő kapcsolódást a Pi szimulátor a felhőbe [Azure IoT Hub](iot-hub-what-is-iot-hub.md). 

Ha a fizikai eszköz van, látogassa meg [málna Pi csatlakozzon az Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) a kezdéshez. 

<p>
<div id="diag" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/3_banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6_button_default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5_button_click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6_button_default.png';">
</div>

## <a name="what-you-do"></a>Mit

* Alapismeretei málna Pi online szimulátor.
* Létrehoz egy IoT-központot.
* Eszköz regisztrálása az IoT hub a pi.
* Futtassa a mintaalkalmazást szimulált érzékelő adatokat küldeni az IoT hub Pi.

Az IoT-központ az Ön által létrehozott szimulált málna Pi csatlakozni. Majd futtassa a mintaalkalmazást a szimulátor érzékelőadatait létrehozásához a. Végezetül az érzékelő adatokat küldött az IoT hub.

## <a name="what-you-learn"></a>Ismertetett témák

* Megtudhatja, hogyan hozzon létre egy Azure IoT-központot, és az új eszköz kapcsolati karakterláncot. Ha az Azure-fiók nem rendelkezik [hozzon létre egy Azure próbafiókot](https://azure.microsoft.com/free/) csak néhány perc múlva.
* Hogyan használható az málna Pi online szimulátor.
* Hogyan érzékelő adatokat küldeni az IoT hub.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Málna Pi webes szimulátor áttekintése

Kattintson a gombra málna Pi online szimulátorban történő elindításához.

> [!div class="button"]
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Indítsa el a Raspberry Pi szimulátor</a>

A webes szimulátor nincsenek háromféleképpen használhatók.
1. Összeállítási terület - az alapértelmezett kapcsolatcsoport, a Pi kapcsolódik BME280 érzékelő és LED. A terület zárolva van az előzetes verzióra jelenleg így testreszabási nem hajtható végre.
2. Kódolás a terület - málna Pi kódot egy online kód szerkesztővel meg. Az alapértelmezett mintaalkalmazás segít BME280 érzékelő érzékelő adatokat gyűjteni, és az Azure IoT Hub küld. Az alkalmazásra az teljes mértékben kompatibilis a valódi Pi eszközök. 
3. Integrált Windows - azt mutatja, a kód. Ez az ablak tetején nincsenek három gomb.
   * **Futtatás** -futtassa az alkalmazást a kódolási területen.
   * **Alaphelyzetbe** -kódolási terület visszaállítása az alapértelmezett mintaalkalmazást.
   * **Modellrészek/kibontott** -jobb oldalán van ahhoz, hogy a konzolablakban modellrészek kibontása gomb.

> [!NOTE] 
A málna Pi webes szimulátor jelenleg előzetes verzióban érhető el. Szeretnénk hallani a hangot a a [Gitter Chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). A forráskód a nyilvános [Github](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![A Pi online szimulátor áttekintése](media/iot-hub-raspberry-pi-web-simulator/0_overview.png)

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]


## <a name="run-a-sample-application-on-pi-web-simulator"></a>A Pi webes szimulátor mintaalkalmazás futtatása

1. Kódolási terület, győződjön meg arról, az alapértelmezett mintaalkalmazás dolgoznak. A sor 15 a helyőrzőt cserélje le az Azure IoT hub eszköz kapcsolati karakterláncot.
   ![Cserélje le az eszköz kapcsolati karakterlánc](media/iot-hub-raspberry-pi-web-simulator/1_connectionstring.png)

2. Kattintson a **futtatása** vagy típus `npm start` az alkalmazás futtatásához.


Bemutatja az érzékelő adatokat és az IoT hub küldött üzenetek a következő kimenetet kell látnia ![kimeneti - érzékelő adatokat küld az IoT hub málna Pi](media/iot-hub-raspberry-pi-web-simulator/2_run_application.png)


## <a name="next-steps"></a>További lépések

Egy mintaalkalmazás érzékelő adatokat gyűjteni, és küldje el az IoT hub futtatását.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
