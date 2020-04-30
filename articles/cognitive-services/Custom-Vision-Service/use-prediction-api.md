---
title: Előrejelzési végpont használata a lemezképek programozott teszteléséhez osztályozó-Custom Vision
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
ms.openlocfilehash: 88aaa3d54ea44a15f7900aba093bf28c70c19695
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82129855"
---
# <a name="use-your-model-with-the-prediction-api"></a>Modell használata az előrejelzési API-val

A modell betanítása után programozott módon tesztelheti a képeket az előrejelzési API-végpontra való küldéssel.

> [!NOTE]
> Ez a dokumentum C# használatával mutatja be a kép elküldését az előrejelzési API-hoz. További információért és Példákért lásd az [előrejelzési API-referenciát](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>A betanított iteráció közzététele

A [Custom Vision weblapon](https://customvision.ai) jelölje ki a projektet, majd válassza ki a __Teljesítmény__ fület.

Ha képeket szeretne küldeni az előrejelzési API-nak, először közzé kell tennie az iterációt, amelyet a __Közzététel__ lehetőségre kattintva, majd a közzétett iteráció nevének megadásával lehet elvégezni. Így a modell elérhetővé válik a Custom Vision Azure-erőforrás előrejelzési API-jával.

![Megjelenik a teljesítmény lap, amely a közzététel gombot körülvevő piros téglalapot jeleníti meg.](./media/use-prediction-api/unpublished-iteration.png)

Miután a modell sikeresen közzé lett téve, megjelenik egy "közzétett" felirat az iteráció mellett a bal oldali oldalsávon, és a neve az iteráció leírásában fog megjelenni.

![Megjelenik a teljesítmény lap, amely a közzétett címkét és a közzétett iteráció nevét körülvevő piros téglalapot jeleníti meg.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Az URL és az előrejelzési kulcs megszerzése

Ha a modell közzé lett téve, az __előrejelzési URL-cím__kiválasztásával kérheti le a szükséges információkat. Ekkor megnyílik egy párbeszédpanel, amely az előrejelzési API használatára vonatkozó információkat tartalmazza, beleértve az __előrejelzési URL-címet__ és az __előrejelzési kulcsot__.

![A teljesítmény lap az előrejelzési URL-cím gombját körülvevő piros négyszögtel jelenik meg.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A Performance (teljesítmény) lap a prediktív URL-címet körülvevő piros négyszöget jeleníti meg a képfájl és az előrejelzési kulcs értékének használatával.](./media/use-prediction-api/prediction-api-info.png)


Ebben az útmutatóban egy helyi rendszerképet fog használni, ezért másolja ki az URL-címet, ha egy ideiglenes helyre mutató **képfájl van** . Másolja a megfelelő __előrejelzési kulcs__ értékét is.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Hozzon létre egy új C#-konzol alkalmazást a Visual Studióban.

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
   * Állítsa a `namespace` mezőt a projekt nevére.
   * Cserélje le a `<Your prediction key>` helyőrzőt a korábban lekért kulcs értékére.
   * Cserélje le a `<Your prediction URL>` helyőrzőt a korábban lekért URL-címre.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásakor a rendszer felszólítja egy képfájl elérési útjának megadására a konzolon. A rendszer ezután elküldi a rendszerképet az előrejelzési API-nak, és az előrejelzési eredményeket JSON-formátumú karakterláncként adja vissza. A következő egy példa erre a válaszra.

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

Ebben az útmutatóban megtanulta, hogyan küldhet képeket az egyéni rendszerkép-osztályozó/detektorba, és hogyan kaphat választ programozott módon a C# SDK-val. Következő lépésként megtudhatja, hogyan végezheti el a teljes körű forgatókönyvek megvalósítását a C# használatával, vagy egy másik nyelvi SDK használatának megkezdését.

* [Rövid útmutató: Custom Vision SDK](quickstarts/image-classification.md)
