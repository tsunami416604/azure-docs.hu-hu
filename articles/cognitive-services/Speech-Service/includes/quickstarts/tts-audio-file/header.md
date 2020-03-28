---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961365"
---
Ebben a rövid útmutatóban a [Beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) segítségével konvertálhatja a szöveget hangfájlban lévő szintetizált beszédté. A szövegfelolvasó szolgáltatás számos lehetőséget kínál a szintetizált hangokszámára a [szövegfelolvasó nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel teljesítése után a beszéd fájllá történő szintetizálása csak öt lépést tesz:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Hozzon létre egy hangkonfigurációs objektumot, amely megadja a. WAV fájlnév.
> * Objektum `SpeechSynthesizer` létrehozása felülről származó konfigurációs objektumokkal.
> * Az `SpeechSynthesizer` objektum használatával konvertálja a szöveget szintetizált beszédté, és mentse a megadott hangfájlba.
> * Vizsgálja `SpeechSynthesizer` meg a visszaadott hibákat.
