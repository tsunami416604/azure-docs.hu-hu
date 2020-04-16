---
title: Beszédeszközök SDK letöltése
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatás számos eszközzel és hangforrással működik. Mostantól a beszédfelismerési alkalmazásokat a következő szintre lépheti az egyező hardverrel és szoftverrel. Ebből a cikkből megtudhatja, hogyan érheti el a beszédeszközök SDK-t, és hogyan kezdheti meg a fejlesztést.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 756ec976c4643c1cd80552b7fa552b70fd1f5d24
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391273"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>A Cognitive Services beszédeszközeinek SDK beolvasása

A Beszédeszközök SDK egy előre hangolt könyvtár, amelyet a célirányos fejlesztői készletekkel és a különböző mikrofontömb-konfigurációkkal való együttműködésre terveztek.

## <a name="choose-a-development-kit"></a>Fejlesztői készlet kiválasztása

|Eszközök|Specifikáció|Leírás|Forgatókönyvek|
|--|--|--|--|
|[URbetter T11 Fejlesztési Tanács](https://www.alibaba.com/product-detail/URBETTER-Dev-Kits-for-Microsoft-Speech_62358746402.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mikrofon tömb, ARM SOC, WIFI, Ethernet, HDMI, USB kamera. <br>Linux|Iparági szintű beszédeszközök SDK, amely alkalmazkodik a Microsoft Mic tömbhöz, és támogatja a kiterjesztett I/O-t, például a HDMI/Ethernet-et és több USB-perifériát|Beszélgetés átirat, oktatás, kórház, robotok, OTT Box, Voice Agent, Drive Thru|
|[Roobo Intelligens Audio Dev Kit](https://ddk.roobo.com)<br>[Gyorsútmutató](speech-devices-sdk-roobo-v1.md) / [Quickstart](speech-devices-sdk-android-quickstart.md)![Roobo intelligens hangfejlesztői készlete](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mikrofon tömb, ARM SOC, WIFI, Audio Out, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|Az első beszédeszközök SDK-ja a Microsoft Mic Array és az sdk először történő feldolgozásához a kiváló minőségű átírási és beszédforgatókönyvek fejlesztéséhez|Beszélgetés átírása, okos hangszóró, hangügynök, hordható|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Gyorsindítás](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / az Azure Kinect DK-ban[Quickstart](speech-devices-sdk-windows-quickstart.md)![](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic Array RGB és Mélység kamerák. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Fejlesztői készlet fejlett mesterséges intelligencia (AI) érzékelőkkel kifinomult számítógépes látás- és beszédmodellek készítéséhez. A kategóriájában legjobb térbeli mikrofontömböt és mélységmérő kamerát kombinálja a videokamerával és a tájolásérzékelővel – mindezt egyetlen kis eszközben, több móddal, opcióval és SDK-val, hogy számos számítási típust befogadjon.|Beszélgetés átirat, Robotika, Smart Building|
|Roobo Intelligens Audio Dev Kit 2<br>[Telepítés](speech-devices-sdk-roobo-v2.md)<br>![Roobo Intelligens Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mikrofon tömb, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|Generációs beszédeszközök SDK, amely alternatív operációs rendszert és több funkciót biztosít egy költséghatékony referencia-kialakításban.|Beszélgetés átírása, okos hangszóró, hangügynök, hordható|


## <a name="download-the-speech-devices-sdk"></a>A beszédfelismerési eszközök SDK letöltése

Töltse le a [beszédeszközök SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a beszédfelismerési eszközök SDK-val](https://aka.ms/sdsdk-quickstart)
