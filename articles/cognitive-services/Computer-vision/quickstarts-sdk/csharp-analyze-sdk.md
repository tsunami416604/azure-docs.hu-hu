---
title: 'Gyors útmutató: Rendszerkép elemzése – SDK,C#'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy képet fog elemezni a Computer Vision Windows C#-ügyfélkódtárával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 59641973a953d1db501f7b660b04df900bc55cb0
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141022"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Gyors útmutató: Rendszerkép elemzése a Computer Vision SDK használatával ésC#

Ebben a rövid útmutatóban egy helyi és egy távoli rendszerképet is elemez, hogy kinyerje a vizualizációs funkciókat a C#Computer Vision ügyféloldali kódtár használatával. Ha szeretné, letöltheti az útmutatóban szereplő kódot teljes minta alkalmazásként a GitHubon elérhető [Cognitive Services csharp](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) -jövőképből.

## <a name="prerequisites"></a>Előfeltételek

* Egy Computer Vision előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs-és szolgáltatás végponti `COMPUTER_VISION_SUBSCRIPTION_KEY` karakterláncához, a nevet és `COMPUTER_VISION_ENDPOINT`a-t.
* A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) ügyfélkódtár NuGet-csomagja. A csomag letöltése nem szükséges. A telepítési utasításokat az alábbiakban találja.

## <a name="create-and-run-the-sample-application"></a>A mintaalkalmazás létrehozása és futtatása

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást.
1. Telepítse a Computer Vision-ügyfélkódtár NuGet-csomagját.
    1. A menüben kattintson a **Tools** (Eszközök) elemre, és válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.
    1. Kattintson a **Browse** (Tallózás) lapra, majd írja be a **keresőmezőbe** a „Microsoft.Azure.CognitiveServices.Vision.ComputerVision” kifejezést.
    1. Válassza a megjelenő **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** lehetőséget, majd jelölje be a projektnév melletti jelölőnégyzetet, és kattintson az **Install** (Telepítés) gombra.
1. Cserélje le a *program.cs* tartalmát a következő kódra. A `AnalyzeImageAsync` és`AnalyzeImageInStreamAsync` metódusok betakarják az [elemzés lemezképét REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) távoli és helyi lemezképekhez.

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
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

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
                if (analysis.Description.Captions.Count != 0)
                {
                    Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
                }
                else
                {
                    Console.WriteLine("No description generated.");
                }
            }
        }
    }
    ```

1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Ha szükséges, módosítsa a `computerVision.Endpoint` elem értékét arra az Azure-régióra, amelyhez az előfizetési kulcsai társítva vannak.
1. A `<LocalImage>` helyére írja be a helyi kép elérési útját és fájlnevét.
1. Opcionálisan egy másik `remoteImageUrl` rendszerkép URL-címére is beállíthat.
1. Futtassa a programot.

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz a legrelevánsabb feliratot jeleníti meg az egyes képekhez. A `DisplayResults` metódust módosíthatja különböző képadatok kinyomtatásához. További információért tekintse meg a [AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet) metódust.

Lásd [az API-gyors útmutatókat: Egy helyi rendszerkép elemzése, C# ](../QuickStarts/CSharp-analyze.md#examine-the-response) amely egy nyers JSON-kimenetre mutat példát.

```console
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük.

> [!div class="nextstepaction"]
> [Ismerkedjen meg a Computer Vision API-k működésével](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
