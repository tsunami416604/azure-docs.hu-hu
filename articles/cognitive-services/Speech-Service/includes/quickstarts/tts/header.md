---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400377"
---
Ebben a rövid útmutatóban a [Beszéd SDK-val](~/articles/cognitive-services/speech-service/speech-sdk.md) konvertálhatja a szöveget szintetizált beszédmé. A szövegfelolvasó szolgáltatás számos lehetőséget kínál a szintetizált hangokszámára a [szövegfelolvasó nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel teljesítése után a szintetizált beszéd megjelenítése az alapértelmezett hangszórókba csak négy lépést tesz meg:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `SpeechSynthesizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `SpeechSynthesizer` objektum használata a szöveg kimondásához.
> * Ellenőrizze, `SpeechSynthesisResult` hogy nem tartalmaznak-e hibákat a visszaküldött ek.
