---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943830"
---
Ha tömörített hangformátumot szeretne továbbítani a beszédfelismerési szolgáltatásba, hozzon létre egy `SPXPullAudioInputStream` vagy `SPXPushAudioInputStream`.

Az alábbi kódrészletből megtudhatja, hogyan hozhat létre `SPXAudioConfiguration` egy `SPXPushAudioInputStream`-példányból, és hogyan adható meg az adatfolyam tömörítési formátuma az MP3-ban.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

A következő kódrészlet azt mutatja be, hogyan olvashatók be a tömörített hangadatok egy fájlból, és hogyan tölthetők be a `SPXPushAudioInputStream`ba.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
