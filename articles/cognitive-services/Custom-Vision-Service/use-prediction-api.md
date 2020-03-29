---
title: Előrejelzési végpont használata programozott tesztképekhez osztályozóval – Egyéni látás
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható az API képek programozott tesztelésére a Custom Vision Service osztályozóval.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169952"
---
# <a name="use-your-model-with-the-prediction-api"></a>A modell használata az előrejelzési API-val

Miután betanítása a modell, a képek et programozott módon tesztelheti, elküldve őket az előrejelzési API-végpontra.

> [!NOTE]
> Ez a dokumentum C# használatával mutatja be a kép elküldését az előrejelzési API-hoz. További információkért és példákért tekintse meg az [Előrejelzés api-referencia](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>A betanított iteráció közzététele

A [Custom Vision weblapon](https://customvision.ai) jelölje ki a projektet, majd válassza ki a __Teljesítmény__ fület.

Az előrejelzési API-ba való képek elküldéséhez először közzé kell tennie az előrejelzéshez szükséges iterációt, amely a __Közzététel__ kiválasztásával és a közzétett iteráció nevének megadásával végezhető el. Ez elérhetővé teszi a modellt a Custom Vision Azure-erőforrás előrejelzési API-ja számára.

![Megjelenik a Teljesítmény lap, a Közzététel gombot egy piros téglalap.](./media/use-prediction-api/unpublished-iteration.png)

A modell sikeres közzététele után megjelenik egy "Közzétett" címke az iteráció mellett a bal oldali oldalsávon, és a neve megjelenik az iteráció leírásában.

![Megjelenik a teljesítmény lap, amelyen egy piros téglalap veszi körül a Közzétett címkét, és a közzétett iteráció neve látható.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Az URL és az előrejelzési kulcs megszerzése

A modell közzététele után az __Előrejelzés URL-címének__kiválasztásával lekérheti a szükséges információkat. Ez megnyit egy párbeszédablakot az Előrejelzés API használatával kapcsolatos információkkal, beleértve az __előrejelzési URL-t__ és az __Előrejelzés-kulcsot.__

![A teljesítmény lapon egy piros téglalap látható az Előrejelzés URL-címe gomb körül.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A teljesítmény lapon egy piros téglalap körül az előrejelzési URL-értéket egy képfájl és az Előrejelzés-kulcs érték.](./media/use-prediction-api/prediction-api-info.png)


Ebben az útmutatóban egy helyi képet fog használni, ezért másolja az URL-címet: **Ha van képfájlja** egy ideiglenes helyre. Másolja a megfelelő __Előrejelzés-kulcs__ értéket is.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A Visual Studio-ban hozzon létre egy új C# konzolalkalmazást.

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
   * Állítsa `namespace` be a mezőt a projekt nevére.
   * Cserélje le `<Your prediction key>` a helyőrzőt a korábban beolvasott kulcsértékre.
   * Cserélje le `<Your prediction URL>` a helyőrzőt a korábban beolvasott URL-re.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásakor a rendszer kéri, hogy adja meg egy képfájl elérési útját a konzolon. A rendszerképet ezután elküldjük az előrejelzési API-t, és az előrejelzési eredmények egy JSON-formázott karakterláncként adják vissza. Az alábbi példaválasz látható.

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

Ebben az útmutatóban megtanulta, hogyan küldhet el képeket az egyéni lemezjátszóba/detektorba, és hogyan fogadhat választ programozott módon a C# SDK-val. Ezután megtudhatja, hogyan végezhet teljes körű forgatókönyveket C#-val, vagy ismerkedhet meg egy másik nyelvű SDK használatával.

* [Rövid útmutató: .NET SDK](csharp-tutorial.md)
* [Rövid útmutató: Python SDK](python-tutorial.md)
* [Rövid útmutató: Java SDK](java-tutorial.md)
* [Rövid útmutató: Node SDK](node-tutorial.md)
* [Rövid útmutató: Ugrás az SDK-ba](go-tutorial.md)
