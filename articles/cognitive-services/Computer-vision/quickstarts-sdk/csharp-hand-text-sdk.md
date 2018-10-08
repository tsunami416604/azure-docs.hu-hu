---
title: 'Rövid útmutató: Szöveg kinyerése - SDK, C# - Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban szöveget fog kinyerni egy képből a Computer Vision Windows C#-ügyfélkódtárával.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: nolachar
ms.openlocfilehash: 86808756721b2dc983df6eaf8a9e643a12d73969
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409016"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>Rövid útmutató: Szöveg kinyerése a Computer Vision SDK és a C# használatával

Ebben a rövid útmutatóban kézzel írt vagy nyomtatott szöveget fog kinyerni egy képből a Computer Vision Windows-ügyfélkódtárával.

A minta forráskódja elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

## <a name="prerequisites"></a>Előfeltételek

* A Computer Vision használatához előfizetési kulcsra van szüksége, lásd az [előfizetési kulcsok beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.
* A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) ügyfélkódtár NuGet-csomagja. A csomag letöltése nem szükséges. A telepítési utasításokat az alábbiakban találja.

## <a name="recognizetextasync-method"></a>RecognizeTextAsync metódus

A `RecognizeTextAsync` és `RecognizeTextInStreamAsync` metódus a [szövegfelismerő API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) burkolja a távoli, illetve helyi képekhez. A `GetTextOperationResultAsync` metódus a [szövegfelismerési művelet eredményének beszerzése API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) burkolja.  Ezekkel a metódusokkal szöveget ismerhet fel egy képben, és géppel olvasható karakterfolyamba nyerheti ki a felismert karaktereket.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást.
1. Telepítse a Computer Vision-ügyfélkódtár NuGet-csomagját.
    1. A menüben kattintson a **Tools** (Eszközök) elemre, és válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.
    1. Kattintson a **Browse** (Tallózás) lapra, majd írja be a **keresőmezőbe** a „Microsoft.Azure.CognitiveServices.Vision.ComputerVision” kifejezést.
    1. Válassza a megjelenő **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lehetőséget, majd jelölje be a projektnév melletti jelölőnégyzetet, és kattintson az **Install** (Telepítés) gombra.
1. Írja felül a `Program.cs` értékét az alábbi kóddal.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Szükség esetén váltsa a `computerVision.Endpoint` beállítást az előfizetési kulcsaihoz társított Azure-régióra.
1. Igény szerint beállíthatja a `textRecognitionMode` beállítást a `TextRecognitionMode.Printed` értékre.
1. A `<LocalImage>` helyére írja be a helyi kép elérési útját és fájlnevét.
1. A `remoteImageUrl` értékét beállíthatja egy másik képre.
1. Futtassa a programot.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ExtractText
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // For printed text, change to TextRecognitionMode.Printed
        private const TextRecognitionMode textRecognitionMode =
            TextRecognitionMode.Handwritten;

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
            "Cursive_Writing_on_Notebook_paper.jpg/" +
            "800px-Cursive_Writing_on_Notebook_paper.jpg";

        private const int numberOfCharsInOperationId = 36;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
            var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Recognize text from a remote image
        private static async Task ExtractRemoteTextAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            // Start the async process to recognize the text
            RecognizeTextHeaders textHeaders =
                await computerVision.RecognizeTextAsync(
                    imageUrl, textRecognitionMode);

            await GetTextAsync(computerVision, textHeaders.OperationLocation);
        }

        // Recognize text from a local image
        private static async Task ExtractLocalTextAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                // Start the async process to recognize the text
                RecognizeTextInStreamHeaders textHeaders =
                    await computerVision.RecognizeTextInStreamAsync(
                        imageStream, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }
        }

        // Retrieve the recognized text
        private static async Task GetTextAsync(
            ComputerVisionClient computerVision, string operationLocation)
        {
            // Retrieve the URI where the recognized text will be
            // stored from the Operation-Location header
            string operationId = operationLocation.Substring(
                operationLocation.Length - numberOfCharsInOperationId);

            Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
            TextOperationResult result =
                await computerVision.GetTextOperationResultAsync(operationId);

            // Wait for the operation to complete
            int i = 0;
            int maxRetries = 10;
            while ((result.Status == TextOperationStatusCodes.Running ||
                    result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
            {
                Console.WriteLine(
                    "Server status: {0}, waiting {1} seconds...", result.Status, i);
                await Task.Delay(1000);

                result = await computerVision.GetTextOperationResultAsync(operationId);
            }

            // Display the results
            Console.WriteLine();
            var lines = result.RecognitionResult.Lines;
            foreach (Line line in lines)
            {
                Console.WriteLine(line.Text);
            }
            Console.WriteLine();
        }
    }
}
```

## <a name="recognizetextasync-response"></a>RecognizeTextAsync válasz

A sikeres válasz a képek felismert szövegsorait jeleníti meg.

A nyers JSON-kimenetre itt láthat egy példát: [Rövid útmutató: Kézzel írt szöveg kinyerése – REST, C#](../QuickStarts/CSharp-hand-text.md#examine-the-response).

```cmd
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük.

> [!div class="nextstepaction"]
> [Ismerkedjen meg a Computer Vision API-k működésével](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
