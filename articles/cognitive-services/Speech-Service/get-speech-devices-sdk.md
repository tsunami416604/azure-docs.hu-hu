---
title: Beszédeszközök SDK letöltése
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás együttműködik számos különböző hang forrásai és eszközei. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. Ebből a cikkből megtudhatja, hogyan érheti el a Speech Devices SDK-t, és megkezdheti a fejlesztést.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b60c8735833c3012523c5cc0d7eea6a50e9d10b6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559690"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A Cognitive Services beszédfelismerő eszközök SDK beszerzése

A Speech Devices SDK egy előre összehangolt könyvtár, amelynek célja, hogy a célnak megfelelően kialakított fejlesztői készletekkel működjön, és a mikrofonos tömbök különböző konfigurációit használják.

## <a name="choose-a-development-kit"></a>Fejlesztői csomag kiválasztása

|Eszközök|Specifikáció|Leírás|Forgatókönyvek|
|--|--|--|--|
|[Roobo Smart audio dev Kit](https://ddk.roobo.com)</br>[A telepítő](speech-devices-sdk-roobo-v1.md) / rövid útmutatója[](speech-devices-sdk-android-quickstart.md)RooboSmartaudio![dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC-tömb, ARM SOC, WIFI, audiokimenet, IO. </br>[Android](speech-devices-sdk-android-quickstart.md)|Az első Speech Devices SDK a Microsoft MIC Array és az első feldolgozó SDK hozzáigazításához a kiváló minőségű átírási és beszédfelismerési forgatókönyvek fejlesztéséhez|Beszélgetés átirata, intelligens hangszóró, hangvezérelt ügynök, hordható|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)</br>[A telepítő](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / rövid útmutatója[](speech-devices-sdk-windows-quickstart.md)Azure![Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC-tömb RGB és mélységmérő kamerák. </br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Fejlett mesterséges intelligencia (AI) érzékelőkkel rendelkező fejlesztői készlet kifinomult számítógépes jövőkép és beszédfelismerési modellek létrehozásához. Kombinálja a legjobb minőségű térbeli mikrofon tömbjét és részletes kameráját egy videokamera és tájolás érzékelővel – mindezt egyetlen kis eszközön, több mód, lehetőség és SDK használatával, amelyek számos számítási típust biztosítanak.|Beszélgetés átirata, robotika, intelligens kialakítás|
|Roobo Smart audio dev Kit 2![Roobo Smart audio dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 MIC-tömb, ARM SOC, WIFI, Bluetooth, IO. </br>Linux|A 2. generációs Speech Devices SDK, amely alternatív operációs rendszert és további funkciókat biztosít a költséghatékony hivatkozási tervben.|Beszélgetés átirata, intelligens hangszóró, hangvezérelt ügynök, hordható|
|URbetter T11 fejlesztési Tanács![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 MIC-tömb, ARM SOC, WIFI, Ethernet, HDMI, USB kamera. </br>Linux|Iparági szintű Speech Devices SDK, amely alkalmazkodik a Microsoft MIC-tömbhöz, és támogatja a kiterjesztett I/O-t, például a HDMI/Ethernet és a több USB-perifériát|Beszélgetések átírása, oktatás, kórház, robotok, OTT Box, hangvezérelt ügynök, Drive Thru|

## <a name="download-the-speech-devices-sdk"></a>A beszédfelismerés eszközök SDK letöltése

Töltse le a [Speech Devices SDK](https://aka.ms/sdsdk-download)-t.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszédfelismerés eszközök SDK használatának első lépései](https://aka.ms/sdsdk-quickstart)
