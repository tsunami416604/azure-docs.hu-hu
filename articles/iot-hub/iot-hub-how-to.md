---
title: Az Azure IoT Hub hogyan |} Microsoft Docs
description: "Fejlesztői hogyan használható a különböző IoT-központ szolgáltatásai?"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: b451ea2d42f0ff2f64746bcb296fb9128472fea8
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="how-to-use-azure-iot-hub"></a>Azure IoT Hub használata

Megtudhatja, hogyan fejleszthet az IoT-központ szolgáltatás számos lehetőség közül választhat:

* Olvassa el a fogalmi IoT-központ részeit, részletes ismertető cikkeket.
* Kövesse a cikk egy IoT-központ különböző funkcióhoz valamelyikét.

## <a name="developer-guide"></a>Fejlesztői útmutató

Fejlesztői, olvassa el az IoT-központ fogalmi részletes útmutatást a [– útmutató fejlesztőknek][lnk-devguide]. Ez az útmutató ismerteti:

* Segítségével megtudhatja, hogyan használhatja ezeket az IoT Hub-szolgáltatások részletes leírása.
* Útmutatás válassza ki, amikor több lehetőség is elérhető.

## <a name="tutorials"></a>Oktatóanyagok

Információ a funkciók az IoT-központ feldolgozása révén gyakorlati gyakorlatok szeretne használni, ha nincsenek több oktatóanyagok választhat. Ezek az oktatóanyagok számos több programozási nyelven is elérhető. Ezek az oktatóanyagok a következők:

- [Az IoT-központ eszközről a felhőbe üzenetek útvonalak használatával][lnk-routes-tutorial]. Ez az oktatóanyag bemutatja, hogyan átirányítani az eszköz a felhőbe küldött üzeneteket a könnyen és konfigurációs-alapú útválasztási szabályokat az IoT-központ használandó.

- [IoT Hub-felhő eszközre üzenetek][lnk-c2d-tutorial]. Az oktatóanyag bemutatja, hogyan felhő eszközre üzeneteket egy eszközön keresztül az IoT-központ felhő eszközre üzenetek küldjön és fogadjon.

- [A felhőhöz, az IoT-központ eszközökről fájlok feltöltése][lnk-upload-tutorial]. Az oktatóanyag bemutatja, hogyan használható az IoT-központ fájl feltöltése képességeit.

- [Ismerkedés az eszköz twins][lnk-twin-tutorial]. Ez az oktatóanyag bemutatja a eszköz twins, jelentett tulajdonságok, kívánt tulajdonságokkal és címkék. Eszköz twins értékek szinkronizálni az eszközöket használhatja.

- [Közvetlen módszerekkel][lnk-methods-tutorial]. Az oktatóanyag bemutatja, hogyan használhatja a közvetlen módszer. Vegye fel a kezelő egy közvetlen metódus a szimulált eszköz, és az IoT-központ közvetlen metódus indításához.

- [Ismerkedés az eszközkezelés][lnk-dm-tutorial]. Ez az oktatóanyag bemutatja, hogyan például twins és a közvetlen módszer főbb eszköz felügyeleti funkcióinak használatát. Ezek a szolgáltatások segítségével távolról indítsa újra a szimulált eszköz.

- [Eszközök konfigurálása kívánt tulajdonságokat használni][lnk-properties-tutorial]. Ez az oktatóanyag bemutatja, hogyan használhatják az eszközt iker meg szükséges jelentett tulajdonságai, a távoli konfigurálja az eszközt.

- [Eszköz feladatok használja vezérlőprogram-frissítés indításához][lnk-jobs-tutorial]. Ez az oktatóanyag bemutatja, hogyan például twins és a közvetlen módszer főbb eszköz felügyeleti funkcióinak használatát. Megtanulhatja a szolgáltatások használatára az eszköz belső vezérlőprogram távoli frissítéséhez.

- [Ütemezés és a feladatok][lnk-schedule-tutorial]. Az oktatóanyag bemutatja, hogyan használható a kívánt tulajdonságai és metódusai közvetlen együttműködhet több eszközre az ütemezett időpontban.

## <a name="next-steps"></a>Következő lépések

Az IoT-központ szolgáltatás kapcsolatos további tudnivalókért tekintse meg a [– útmutató fejlesztőknek][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md