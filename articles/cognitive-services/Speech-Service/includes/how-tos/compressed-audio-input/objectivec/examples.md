---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943830"
---
Ha tömörített hangformátumban szeretne streamelni a `SPXPullAudioInputStream` `SPXPushAudioInputStream`beszédfelismerési szolgáltatásba, hozzon létre egy vagy .

A következő kódrészlet bemutatja, `SPXAudioConfiguration` hogyan lehet `SPXPushAudioInputStream`létrehozni egy példányt, amely mp3-at ad meg az adatfolyam tömörítési formátumaként.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

A következő kódrészlet bemutatja, hogyan lehet a tömörített hangadatokat olvasni egy fájlból, és hogyan kell a `SPXPushAudioInputStream`rendszerbe pumpálni.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
