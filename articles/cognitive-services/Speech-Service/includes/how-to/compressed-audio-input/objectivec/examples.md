---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409636"
---
Ha tömörített hangformátumban szeretne streamelni a `SPXPullAudioInputStream` `SPXPushAudioInputStream`beszédfelismerési szolgáltatásba, hozzon létre egy vagy .

A következő kódrészlet bemutatja, `SPXAudioConfiguration` hogyan lehet `SPXPushAudioInputStream`létrehozni egy példányt, amely mp3-at ad meg az adatfolyam tömörítési formátumaként.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

A következő kódrészlet bemutatja, hogyan lehet a tömörített hangadatokat olvasni egy fájlból, és hogyan kell a `SPXPushAudioInputStream`rendszerbe pumpálni.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
