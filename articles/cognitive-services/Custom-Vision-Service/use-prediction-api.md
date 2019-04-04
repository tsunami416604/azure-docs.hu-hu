---
title: Előrejelzési végpont programozott módon tesztelheti a rendszerképeket az osztályozó által igénybe vett – Custom Vision használata
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható az API képek programozott tesztelésére a Custom Vision Service osztályozóval.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 78ca1d7ceb9086e0d589f904b24b967d36b079a0
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895613"
---
# <a name="use-your-model-with-the-prediction-api"></a>A modell használata az előrejelzési API

Miután a modell betanításához már tesztelheti lemezképek programozott módon az előrejelzési API-végpont való elküldésével.

> [!NOTE]
> Ez a dokumentum C# használatával mutatja be a kép elküldését az előrejelzési API-hoz. További információért és példákért lásd: a [előrejelzési API-referencia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>A betanított iteráció közzététele

A [Custom Vision weblapon](https://customvision.ai) jelölje ki a projektet, majd válassza ki a __Teljesítmény__ fület.

Elküldeni a képek az előrejelzési API-hoz, létre kell közzétenni az előrejelzéshez, amely választásával teheti meg az iteráció __közzététel__ , és adjon meg egy közzétett ismétléseinek. Ez fog tenni a modellt, az előrejelzési API-t a Custom Vision Azure-erőforrás számára is elérhető.

![A Teljesítmény lapon megjelenő, egy vörös téglalappal a Közzététel gombra.](./media/use-prediction-api/unpublished-iteration.png)

Miután sikeresen közzétette a modell, látni fog egy "Közzétéve" felirat jelenik meg a bal oldali oldalsávon az iteráció mellett, és a név fog megjelenni az iteráció leírását.

![A Teljesítmény lap jelenik meg, egy vörös téglalappal, a közzétett címke és a közzétett iteráció nevével együtt.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Az URL és az előrejelzési kulcs megszerzése

A modell közzététele után képes-e a szükséges információk beszerzéséhez kiválasztásával __előrejelzési URL-cím__. Ekkor megnyílik egy párbeszédpanel a adatokkal, az előrejelzési API-val többek között a __előrejelzési URL-cím__ és __előrejelzés-kulcs__.

![A Teljesítmény lapon megjelenő egy vörös téglalappal az előrejelzési URL-cím gombra.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A Teljesítmény lapon megjelenő egy vörös téglalappal képfájl és az előrejelzési-kulcs-érték segítségével előrejelzési URL-cím értékét.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> A __előrejelzés-kulcs__ is megtalálható a [az Azure Portal](https://portal.azure.com) oldala a Custom Vision Azure-erőforrás a társított a projekthez a __kulcsok__ panelen.

Ebben az útmutatóban azt fogja használni a helyi rendszerképet, ezért másolja az URL-CÍMÉT a **Ha képfájl** egy ideiglenes helyre. Másolja be a megfelelő __előrejelzés-kulcs__ értéket is.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új C# Konzolalkalmazás.

1. A __Program.cs__ fájl törzsében használja a következő kódot.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Írja át a következő információkat:
   * Állítsa be a `namespace` mezőt a projekt nevét.
   * Cserélje le a helyőrző `<Your prediction key>` a korábban kapott a kulcs értékét.
   * Cserélje le a helyőrző `<Your prediction URL>` a korábban kapott URL-címet.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásakor kéri a konzol képfájlra mutató elérési utat meg. A lemezkép ezután elküldésekor az előrejelzési API-hoz, és előrejelzési eredményeket visszaadott JSON-formátumú karakterlánc formájában. Az alábbiakban látható egy példa választ.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan lehet elküldeni az egyéni lemezképek lemezkép osztályozó/detector használatával, és programozott módon választ kapnak a C# SDK-t. Ezután megtudhatja, hogyan végezze el a végpontok közötti forgatókönyvek C#, vagy egy másik nyelvet SDK használatának első lépései.

* [Gyors útmutató: .NET SDK-val](csharp-tutorial.md)
* [Gyors útmutató: Python SDK](python-tutorial.md)
* [Gyors útmutató: Java SDK](java-tutorial.md)
* [Gyors útmutató: Node SDK](node-tutorial.md)
* [Gyors útmutató: Go SDK](go-tutorial.md)
