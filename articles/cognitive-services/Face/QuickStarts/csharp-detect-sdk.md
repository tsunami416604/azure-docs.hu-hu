---
title: 'Rövid útmutató: Arcfelismerés képen – SDK, C#'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban arcokat fog felismerni egy képen a Face Windows C# ügyfélkódtár használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: a9a7efd89f8e7462812064615d07acf12acbc3a3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364112"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>Rövid útmutató: Arcok felismerése képen C&#35 használatával – Face

Ebben a rövid útmutatóban emberi arcokat fog felismerni egy képen a Face Windows ügyfélkódtár segítségével.

A minta forráskódja elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face).

## <a name="prerequisites"></a>Előfeltételek

* A minta futtatásához előfizetési kulcs szükséges. Ingyenes próba előfizetési kulcsot itt szerezhet: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) ügyfélkódtár NuGet-csomagja. A csomag letöltése nem szükséges. A telepítési utasításokat az alábbiakban találja.

## <a name="detectwithurlasync-method"></a>DetectWithUrlAsync metódus

Az `DetectWithUrlAsync` és `DetectWithStreamAsync` metódus rendre az [Arcfelismerő API-t](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) csomagolja be távoli, illetve helyi képekhez. A metódusok képeken arcok felismerésére használhatók, a visszakapott arc attribútumok a következők lehetnek:

* Arcazonosító: Számos Face API-forgatókönyvben használt egyedi azonosító.
* Arcot jelölő téglalap: A képen az arc helyét jelző „bal oldalon, felül, szélesség és hosszúság” érték.
* Jellegzetes pontok: 27 pontból álló jellegzetes pontok egy tömbje, amely az arcösszetevők fontos részeire mutat.
* Az arcattribútumok közé tartozik az életkor, a nem, a mosoly intenzitása, a fejtartás és az arcszőrzet.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást.
1. Telepítse a Face ügyfélkódtár NuGet-csomagját.
    1. A felső menüben kattintson a **Eszközök** elemre, és válassza a **NuGet-csomagkezelő**, majd a **NuGet-csomagok kezelése a megoldáshoz** lehetőséget.
    1. Kattintson a **Tallózás** fülre, majd válassza az **Előzetes verzióval** lehetőséget.
    1. A **Keresés** mezőbe írja be: „Microsoft.Azure.CognitiveServices.Vision.Face”.
    1. Válassza a megjelenő **Microsoft.Azure.CognitiveServices.Vision.Face** lehetőséget, majd jelölje be a projektnév melletti jelölőnégyzetet, és kattintson az **Telepítés** gombra.
1. A *Program.cs* fájl tartalmát cserélje le a következő kódra.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Ha szükséges, módosítsa a `faceEndpoint` elem értékét arra az Azure-régióra, amelyhez az előfizetési kulcsai társítva vannak.
1. A <`LocalImage>` helyére beírhatja a helyi kép elérési útját és fájlnevét (ha nincs megadva, a rendszer figyelmen kívül hagyja).
1. A `remoteImageUrl` értékét beállíthatja egy másik képre.
1. Futtassa a programot.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>DetectWithUrlAsync válasz

A sikeres válasz a képen látható minden arcra megjeleníti a nemét és korát.

Nyers JSON-kimenet példákat lásd: [API rövid útmutató: Arcok felismerése képen C# használatával](CSharp.md).

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>További lépések

Megismerheti, hogyan lehet képen történő arcfelismerésre Face szolgáltatást használó WPF Windows-alkalmazást létrehozni. Az alkalmazás keretet rajzol mindegyik arc köré és leírást jelenít meg az arcról az állapotsávon.

> [!div class="nextstepaction"]
> [Oktatóanyag: WPF-alkalmazás létrehozása képeken lévő arcok észleléséhez és bekeretezéséhez](../Tutorials/FaceAPIinCSharpTutorial.md)
