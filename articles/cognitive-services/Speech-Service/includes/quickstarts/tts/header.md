---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: e9f02f95693552180a0eed1550cc59d8f975416b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961397"
---
Ebben a rövid útmutatóban a [Beszéd SDK-val](~/articles/cognitive-services/speech-service/speech-sdk.md) konvertálhatja a szöveget szintetizált beszédmé. A szövegfelolvasó szolgáltatás számos lehetőséget kínál a szintetizált hangokszámára a [szövegfelolvasó nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel teljesítése után a szintetizált beszéd megjelenítése az alapértelmezett hangszórókba csak négy lépést tesz meg:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Objektum `SpeechSynthesizer` létrehozása felülről az `SpeechConfig` objektum használatával.
> * Az `SpeechSynthesizer` objektum használata a szöveg kimondásához.
> * Ellenőrizze, `SpeechSynthesisResult` hogy nem tartalmaznak-e hibákat a visszaküldött ek.
