---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422168"
---
Ha tömörített hangformátumban szeretne streamelni a `SPXPullAudioInputStream` `SPXPushAudioInputStream`beszédfelismerési szolgáltatásba, hozzon létre egy vagy .

A következő kódrészlet bemutatja, `SPXAudioConfiguration` hogyan lehet `SPXPushAudioInputStream`létrehozni egy példányt, amely mp3-at ad meg az adatfolyam tömörítési formátumaként.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

A következő kódrészlet bemutatja, hogyan lehet a tömörített hangadatokat olvasni egy fájlból, és hogyan kell a `SPXPushAudioInputStream`rendszerbe pumpálni.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
