---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422168"
---
Ha tömörített hangformátumot szeretne továbbítani a beszédfelismerési szolgáltatásba, hozzon létre egy `SPXPullAudioInputStream` vagy `SPXPushAudioInputStream`.

Az alábbi kódrészletből megtudhatja, `SPXAudioConfiguration` hogyan hozhat létre egy példányt `SPXPushAudioInputStream`az a-példányból, és hogyan lehet MP3-ként megadnia az adatfolyam tömörítési formátumát.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

A következő kódrészlet azt mutatja be, hogyan olvashatók be a tömörített hangadatok egy fájlból, `SPXPushAudioInputStream`és hogyan tölthetők be a alkalmazásba.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
