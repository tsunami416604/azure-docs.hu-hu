---
title: Azure Kinect DK mikrofon bemeneti adatok elérése
description: Ismerkedjen meg a mikrofon adataival az Azure Kinect DK mikrofon-tömb használatával.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, mikrofon, hozzáférési mikrofon, MIC-információ
ms.openlocfilehash: f2e604d39bc191af095e009c50f3a93e0d695657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277602"
---
# <a name="access-azure-kinect-dk-microphone-input-data"></a>Azure Kinect DK mikrofon bemeneti adatok elérése

A [SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service) gyors útmutatója példákat tartalmaz arra, hogyan használhatja az Azure Kinect DK mikrofon-tömböt különböző programozási nyelveken.
Tekintse meg például a beszédfelismerési **beszédfelismerés a Windowsban** című témakört a Speech SDK rövid útmutatója segítségével. A kód elérhető a [githubról](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp).

A mikrofon tömbje a Windows API-n keresztül is elérhető. A Windows-dokumentációval kapcsolatos részletekért tekintse meg a következő dokumentumokat:

* [Windows audio architektúra](https://docs.microsoft.com/windows-hardware/drivers/audio/windows-audio-architecture)
* [Windows. Media. Capture dokumentáció](https://docs.microsoft.com/uwp/api/Windows.Media.Capture)
* [Oktatóanyag a webkamera rögzítéséhez](https://docs.microsoft.com/windows/uwp/audio-video-camera/basic-photo-video-and-audio-capture-with-mediacapture)
* [USB-hanginformációk](https://docs.microsoft.com/windows-hardware/drivers/audio/usb-2-0-audio-drivers)

A [mikrofon tömb hardverének specifikációját](hardware-specification.md#microphone-array)is áttekintheti.

## <a name="next-steps"></a>További lépések

>[!div class="nextstepaction"]
>[Speech Services SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/)
