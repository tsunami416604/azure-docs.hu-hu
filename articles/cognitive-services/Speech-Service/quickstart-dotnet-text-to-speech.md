---
title: 'Gyors útmutató: Szöveg-beszéd átalakítás, .NET Core - beszédszolgáltatások konvertálása'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutatóban megismerheti, hogyan átalakítandó szöveg-hang transzformációs szöveg-hang transzformációs REST API-val fog. A jelen útmutatóban szereplő minta szöveg van felépítve, beszéd összefoglaló Markup Language (SSML). Ez lehetővé teszi, hogy válassza ki a beszédfelismerési és nyelvi beszédfelismerési válasz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 656ed50b9104fec48ff3eddb3c291608f8f87294
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534037"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Gyors útmutató: Átalakítás szöveg-hang transzformációs .NET Core használatával

Ebből a gyorsútmutatóból megtudhatja, hogyan átalakítandó szöveg-hang transzformációs .NET Core és a szöveg-hang transzformációs REST API használatával. A jelen útmutatóban szereplő minta szöveg van strukturálva, [Speech összefoglaló Markup Language (SSML)](speech-synthesis-markup.md), amely lehetővé teszi, hogy a válasz nyelv és válassza ki.

Ez a rövid útmutatóhoz egy [Azure Cognitive Services-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beszédszolgáltatás erőforrással. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcsa

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o tts-sample
cd tts-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `tts-sample`. A második parancs módosítja a projekt számára a könyvtárba.

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi változatát

Ez a rövid útmutatóhoz C# 7.1-es vagy újabb verziója. Néhány módon módosítsa a C# verzió a projekthez. Ebben az útmutatóban bemutatjuk, hogyan módosíthatja a `tts-sample.csproj` fájlt. Az összes rendelkezésre álló beállításokat, például a Visual Studióban, a nyelv módosítását lásd: [válassza ki a C# beállított nyelvi verzióhoz](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Nyissa meg a projektet, majd nyissa meg a `tts-sample.csproj`. Győződjön meg arról, hogy `LangVersion` 7.1-es vagy újabb verziójára van beállítva. Ha nincs a tulajdonságcsoport a beállított nyelvi verzióhoz, adja hozzá ezeket a sorokat:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Győződjön meg arról, hogy a módosítások mentéséhez.

## <a name="add-required-namespaces-to-your-project"></a>Adja hozzá a projekthez szükséges névterek

A `dotnet new console` parancsot, amely futtatta korábban létrehozott egy projektet, beleértve a `Program.cs`. Ez a fájl meg, ahová az alkalmazás kódjában fog. Nyissa meg `Program.cs`, és cserélje le a meglévő using utasítások. Ezek az utasítások győződjön meg arról, hogy a minta-alkalmazás létrehozásához és futtatásához szükséges összes típusú hozzáférést.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Hozzon létre egy osztályt token Exchange-hez

A szöveg-hang transzformációs REST API egy hozzáférési jogkivonatot a hitelesítéshez szükséges. A hozzáférési jogkivonatot kapjon egy exchange szükség. Ez a minta kicserél egy hozzáférési jogkivonatot a Speech Service előfizetői azonosítóját a `issueToken` végpont.

Ez a példa feltételezi, hogy a beszédfelismerési szolgáltatás előfizetését az USA nyugati régiójában. Ha egy másik régiót használ, módosítsa a `FetchTokenUri`. A teljes listát lásd: [régiók](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> A hitelesítés további információkért lásd: [hitelesítés hozzáférési jogkivonatot a](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Hozzáférési jogkivonat lekérése és a gazdagép URL-Címének beállítása

Keresse meg `static void Main(string[] args)` , és cserélje le a következőre `static async Task Main(string[] args)`.

Ezután másolja ezt a kódot a fő módszert. Néhány dolgot, azonban ennél is fontosabb, tart a szöveget egy bemeneti, valamint a hívások a `Authentication` függvényt, hogy az exchange-hozzáférési jogkivonat előfizetői azonosítóját. Ha hiba lép fel, a hiba nyomtatása a konzolhoz.

Ellenőrizze, hogy az előfizetési kulcs hozzáadása az alkalmazás futtatása előtt.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Majd adja meg a gazdagép és a szöveg-hang transzformációs útvonala:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>A SSML kérelem létrehozása

One-time passcode törzseként egy `POST` kérelmet. SSML, a nyelv és is megadhat. Ez a rövid útmutatóban a nyelvi beállítás fogjuk használni SSML `en-US` állítja be a hang és `ZiraRUS`. Most hozza létre a kérés a SSML:

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> Ebben a példában a `ZiraRUS` hangtípusú. A Microsoft teljes listáját adott beszédhangot/nyelvek, lásd: [nyelvi támogatás](language-support.md). Ha érdekli a saját márkáját egy egyedi, könnyen felismerhető névre hangalapú létrehozása, [létrehozása egyéni hangtípust](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Hozza létre az ügyfél, a kérés és a szintetizált hang mentése fájlba

Nincs a kódmintában számos művelet. Gyorsan vizsgáljuk meg mi történik:

* Az ügyfél és a kérés példányosítása.
* A HTTP-metódus be van állítva `POST`.
* A kérelem szükséges fejlécek kerülnek.
* A kérelem elküldésekor, és az állapotkódot be van jelölve.
* A válasz olvasása aszinkron módon történik, és sample.wav nevű fájlba írni.

Ez a kód másolása a projektbe a Finderből. Értékét cserélje le a `User-Agent` az Azure Portalról az erőforrás nevét a fejlécet.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a szöveg-hang transzformációs alkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

Ha ez sikeres, a beszéd fájl kerül, a projektmappa fájllistájának. A kedvenc media player használatával lejátszani.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem változtatható módon adta meg az előfizetői azonosítót a programban, a rövid útmutató befejezése után mindenképpen távolítsa el az előfizetői azonosítót.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a .NET-minták a Githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Lásd még

* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Egyéni hangtípust létrehozása](how-to-customize-voice-font.md)
* [Hozzon létre egy egyéni beszédfelismerési rekord hangalapú minták](record-custom-voice-samples.md)
