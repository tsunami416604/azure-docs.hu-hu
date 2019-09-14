---
title: Adatátviteli adatfolyamok összedarabolása | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A többlemezes trasfer használata az Bing Speech szolgáltatásba való hangstream küldéséhez
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966487"
---
# <a name="chunked-transfer-encoding"></a>Darabolásos átvitel kódolása

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A beszéd szövegbe való átírásához a Microsoft Speech Recognition API lehetővé teszi, hogy a hangot egyetlen egész számként küldje el, vagy a hanganyagot apró darabokra vágja. A hatékony hangátvitel és az átiratok késésének csökkentése érdekében ajánlott a [hangsűrűn továbbított átviteli kódolást](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) használni a hang a szolgáltatásba való továbbításához. Más megvalósítások esetében a felhasználó által észlelt késés is magasabb lehet. További információkért lásd a [hangadatfolyamok](../concepts.md#audio-streams) lapot.

> [!NOTE]
> Legfeljebb 10 másodperces hangot tölthet fel egyetlen kérelemben, és a kérelem teljes időtartama legfeljebb 14 másodperc lehet.
> [!NOTE]
> Csak akkor kell megadnia a darabolásos átvitel kódolását, ha a [REST API-kat](../GetStarted/GetStartedREST.md) használja a beszédfelismerési szolgáltatás meghívásához. Az [ügyféloldali kódtárakat](../GetStarted/GetStartedClientLibraries.md) használó alkalmazásoknak nem kell konfigurálniuk a darabolásos átvitel kódolását.

A következő kód bemutatja, hogyan állíthatja be a darabolásos átvitel kódolását, és hogyan küldhet el egy hangfájlt a 1024 bájtos adattömbökbe.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
