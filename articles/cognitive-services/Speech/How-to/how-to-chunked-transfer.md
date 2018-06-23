---
title: Darabolásos átviteli hangadatfolyam hogyan |} Microsoft Docs
description: Darabolásos trasfer hangadatfolyam küldeni a beszédfelismerés szolgáltatás használata
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347398"
---
# <a name="chunked-transfer-encoding"></a>Darabolásos átviteli kódolás

Beszédfelismerés szöveg átírni, Microsoft beszédfelismerés API segítségével egy teljes adatrészlet elküldeni a hang, vagy a hang részeket kis adattömbökbe. A hatékony hang streaming, és csökkenti a késéseket írjanak elő, javasoljuk, hogy használjon [darabolásos átviteli kódolás](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) a szolgáltatás hang adatfolyamként történő küldéséhez. Más felhasználói által érzékelt késés nagyobb eredményezhet. További információkért lásd: a [hang adatfolyamok](../concepts.md#audio-streams) lap.

> [!NOTE]
> Nem lehetséges, hogy feltölti a több mint 10 másodpercnyi egy kérésének, és az összes kérelem időtartama nem lehet hosszabb 14 másodperc.
> [!NOTE]
> Meg kell adnia a darabolásos átviteli kódolás a csak akkor, ha használja a [REST API-k](../GetStarted/GetStartedREST.md) a beszédfelismerés szolgáltatás hívásához. Használó alkalmazások [klienskódtárak](../GetStarted/GetStartedClientLibraries.md) nem kell konfigurálni a darabolásos átviteli kódolás.

A következő kód bemutatja, hogyan a darabolásos átviteli kódolás és 1024 bájt méretű adattömbökbe alatt darabolásos hangfájl küldeni.

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
