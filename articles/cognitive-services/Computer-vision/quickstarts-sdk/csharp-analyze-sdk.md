---
title: 'Gyors útmutató: Kép – SDK-t és elemzéseC#'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy képet fog elemezni a Computer Vision Windows C#-ügyfélkódtárával.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5bb11d77a6bdca3ef328ad24ac18ade8df9f65ec
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583375"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Gyors útmutató: A számítógép Látástechnológiai SDK-val kép elemzése ésC#

Ebben a rövid útmutatóban egy helyi és egy távoli képet fog elemezni vizuális jellemzők kinyeréséhez a Computer Vision Windows-ügyfélkódtárával.

## <a name="prerequisites"></a>Előfeltételek

* A Computer Vision használatához előfizetési kulcsra van szüksége, lásd az [előfizetési kulcsok beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.
* A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) ügyfélkódtár NuGet-csomagja. A csomag letöltése nem szükséges. A telepítési utasításokat az alábbiakban találja.

## <a name="analyzeimageasync-method"></a>AnalyzeImageAsync metódus

> [!TIP]
> Szerezze be a legújabb kódot Visual Studio-megoldásként a [GitHubról](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

Az `AnalyzeImageAsync` és `AnalyzeImageInStreamAsync` metódus a [képelemző API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) burkolja távoli, illetve helyi képekhez. Ezekkel a metódusokkal vizuális jellemzőket nyerhet ki a képek tartalma alapján, és kiválaszthatja, hogy mely jellemzőket adják vissza, többek között a következők közül:

* a kép tartalmához kapcsolódó címkék részletes listája;
* a kép tartalmának leírása teljes mondatban;
* a képen szereplő arcok koordinátái, neme és kora;
* a kép típusa (ClipArt vagy vonalrajz);
* a domináns és a kiegészítő színek, vagy hogy a kép fekete-fehér;
* az ebben a [taxonómiában](../Category-Taxonomy.md) meghatározott kategória;
* tartalmaz-e a kép felnőtteknek szóló és szexuális tartalmat.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást.
1. Telepítse a Computer Vision-ügyfélkódtár NuGet-csomagját.
    1. A menüben kattintson a **Tools** (Eszközök) elemre, és válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.
    1. Kattintson a **Browse** (Tallózás) lapra, majd írja be a **keresőmezőbe** a „Microsoft.Azure.CognitiveServices.Vision.ComputerVision” kifejezést.
    1. Válassza a megjelenő **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lehetőséget, majd jelölje be a projektnév melletti jelölőnégyzetet, és kattintson az **Install** (Telepítés) gombra.
1. Írja felül a `Program.cs` értékét az alábbi kóddal.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Ha szükséges, módosítsa a `computerVision.Endpoint` elem értékét arra az Azure-régióra, amelyhez az előfizetési kulcsai társítva vannak.
1. A `<LocalImage>` helyére írja be a helyi kép elérési útját és fájlnevét.
1. A `remoteImageUrl` értékét beállíthatja egy másik képre.
1. Futtassa a programot.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace ImageAnalyze
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

        // Specify the features to return
        private static readonly List<VisualFeatureTypes> features =
            new List<VisualFeatureTypes>()
        {
            VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
            VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
            VisualFeatureTypes.Tags
        };

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the "westus"
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
            var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Analyze a remote image
        private static async Task AnalyzeRemoteAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            ImageAnalysis analysis =
                await computerVision.AnalyzeImageAsync(imageUrl, features);
            DisplayResults(analysis, imageUrl);
        }

        // Analyze a local image
        private static async Task AnalyzeLocalAsync(
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
                ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                    imageStream, features);
                DisplayResults(analysis, imagePath);
            }
        }

        // Display the most relevant caption for the image
        private static void DisplayResults(ImageAnalysis analysis, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
        }
    }
}
```

## <a name="analyzeimageasync-response"></a>Az AnalyzeImageAsync válasza

A sikeres válasz a legrelevánsabb feliratot jeleníti meg az egyes képekhez.

Lásd: [API rövid útmutatók: Elemezheti a helyi rendszerképet a C# ](../QuickStarts/CSharp-analyze.md#examine-the-response) példa nyers JSON-kimenetét.

```
http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük.

> [!div class="nextstepaction"]
> [Ismerkedjen meg a Computer Vision API-k működésével](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
