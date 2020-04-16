---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 870dce55e79bf0169f19d31dfec6689c65fce9cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400360"
---
Ebben a rövid útmutatóban a [Beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) segítségével konvertálhatja a szöveget hangfájlban lévő szintetizált beszédté. A szövegfelolvasó szolgáltatás számos lehetőséget kínál a szintetizált hangokszámára a [szövegfelolvasó nyelvi támogatás](../../../language-support.md#text-to-speech)alatt. Néhány előfeltétel teljesítése után a beszéd fájllá történő szintetizálása csak öt lépést tesz:
> [!div class="checklist"]
> * Hozzon `SpeechConfig` létre egy objektumot az előfizetési kulcsból és a régióból.
> * Hozzon létre egy hangkonfigurációs objektumot, amely megadja a. WAV fájlnév.
> * Objektum `SpeechSynthesizer` létrehozása felülről származó konfigurációs objektumokkal.
> * Az `SpeechSynthesizer` objektum használatával konvertálja a szöveget szintetizált beszédté, és mentse a megadott hangfájlba.
> * Vizsgálja `SpeechSynthesizer` meg a visszaadott hibákat.
