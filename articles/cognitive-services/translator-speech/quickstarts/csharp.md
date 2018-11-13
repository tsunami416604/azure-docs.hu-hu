---
title: 'Rövid útmutató: Translator Speech API – C#'
titlesuffix: Azure Cognitive Services
description: Információ és kódminták segítségével ismerkedhet meg a Translator Speech API használatának alapjaival.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: a05ab716c8aea958e13ebba0dc9ceb09bf3d7cce
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913529"
---
# <a name="quickstart-translator-speech-api-with-c"></a>Rövid útmutató: Translator Speech API és C# 
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ez a cikk bemutatja, hogyan használhatja a Translator Speech API-t egy .wav fájlban elhangzó beszéd lefordításához.

## <a name="prerequisites"></a>Előfeltételek

A kód Windowson történő futtatásához [Visual Studio 2017](https://www.visualstudio.com/downloads/) szükséges. (Az ingyenes Community Edition is elegendő.) Ha Mac OS-t vagy Linuxot használ, a [Visual Studio Code] (https://code.visualstudio.com/Download) szövegszerkesztőt is használhatja alternatív megoldásként.

A „speak.wav” fájlnak ugyanabban a mappában kell lennie, mint az alábbi kódból fordított végrehajtható fájlnak. Ennek a .wav fájlnak standard PCM, 16 bites, 16 kHz-es, mono formátumúnak kell lennie.

Rendelkeznie kell egy [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) és a **Microsoft Translator Speech API-val**. Egy fizetős előfizetői kulcsra van szüksége az [Azure-irányítópultról](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Beszéd fordítása

A következő kód beszédet fordít le egyik nyelvről a másikra.

1. Hozzon létre egy új C#-projektet a kedvenc IDE-jében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[32000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            System.Threading.Thread.Sleep(3000);
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**Beszéd fordításának válasza**

A „speak2.wav” fájl létrejötte sikeres eredményt jelent. Ez a fájl tartalmazza a „speak.wav” fájlban elhangzó szavak fordítását.

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Translator Speech – oktatóanyag](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Lásd még 

[A Translator Speech áttekintése](../overview.md)
[API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
