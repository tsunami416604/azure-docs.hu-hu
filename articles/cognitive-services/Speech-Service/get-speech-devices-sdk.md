---
title: Beszédeszközök SDK letöltése
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás számos különböző eszközzel és hangforrással működik. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. Ebből a cikkből megtudhatja, hogyan érheti el a Speech Devices SDK-t, és megkezdheti a fejlesztést.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220533"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A Cognitive Services beszédfelismerő eszközök SDK beszerzése

A Speech Devices SDK egy előre összehangolt könyvtár, amelynek célja, hogy a célnak megfelelően kialakított fejlesztői készletekkel működjön, és a mikrofonos tömbök különböző konfigurációit használják.

## <a name="choose-a-development-kit"></a>Fejlesztői csomag kiválasztása

|Eszközök|Specifikáció|Leírás|Forgatókönyvek|
|--|--|--|--|
|[Roobo Smart audio dev Kit](https://ddk.roobo.com)<br>[](speech-devices-sdk-roobo-v1.md) A telepítő [ / rövid](speech-devices-sdk-android-quickstart.md) útmutató![Roobo Smart audio dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC-tömb, ARM SOC, WIFI, audiokimenet, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|Az első Speech Devices SDK a Microsoft MIC Array és az első feldolgozó SDK hozzáigazításához a kiváló minőségű átírási és beszédfelismerési forgatókönyvek fejlesztéséhez|Beszélgetés átirata, intelligens hangszóró, hangvezérelt ügynök, hordható|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Telepítő](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [rövid](speech-devices-sdk-windows-quickstart.md) útmutató![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC-tömb RGB és mélységmérő kamerák. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Fejlett mesterséges intelligencia (AI) érzékelőkkel rendelkező fejlesztői készlet kifinomult számítógépes jövőkép és beszédfelismerési modellek létrehozásához. Kombinálja a legjobb minőségű térbeli mikrofon tömbjét és részletes kameráját egy videokamera és tájolás érzékelővel – mindezt egyetlen kis eszközön, több mód, lehetőség és SDK használatával, amelyek számos számítási típust biztosítanak.|Beszélgetés átirata, robotika, intelligens kialakítás|
|Roobo Smart audio dev Kit 2<br>[Telepítés](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart audio dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 MIC-tömb, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|A 2. generációs Speech Devices SDK, amely alternatív operációs rendszert és további funkciókat biztosít a költséghatékony hivatkozási tervben.|Beszélgetés átirata, intelligens hangszóró, hangvezérelt ügynök, hordható|
|URbetter T11 fejlesztési Tanács![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 MIC-tömb, ARM SOC, WIFI, Ethernet, HDMI, USB kamera. <br>Linux|Iparági szintű Speech Devices SDK, amely alkalmazkodik a Microsoft MIC-tömbhöz, és támogatja a kiterjesztett I/O-t, például a HDMI/Ethernet és a több USB-perifériát|Beszélgetések átírása, oktatás, kórház, robotok, OTT Box, hangvezérelt ügynök, Drive Thru|

## <a name="download-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK letöltése

Töltse le a [Speech Devices SDK](https://aka.ms/sdsdk-download)-t.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Speech Devices SDK-val](https://aka.ms/sdsdk-quickstart)
