---
title: 'Példa: Előrejelzési végpont programozott módon tesztelheti a rendszerképeket az osztályozó által igénybe vett – Custom Vision használata'
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható az API képek programozott tesztelésére a Custom Vision Service osztályozóval.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: sample
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 0758670f4b20df7a3147dd7ecbc21b92209c148f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869653"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Az előrejelzési végpont használatával tesztelje a képeket programból a Custom Vision Service osztályozóval

Miután betanította a modellt, programozott módon tesztelhet képeket úgy, hogy elküldi őket az előrejelzési API számára. 

> [!NOTE]
> Ez a dokumentum C# használatával mutatja be a kép elküldését az előrejelzési API-hoz. Az API használatával kapcsolatos bővebb információkért és példákért tekintse meg az [Előrejelzési API-referenciát](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Az URL és az előrejelzési kulcs megszerzése

A [Custom Vision weblapon](https://customvision.ai) jelölje ki a projektet, majd válassza ki a __Teljesítmény__ fület. Az előrejelzési API használatával kapcsolatos információk megjelenítéséhez, az __előrejelzés-kulcsot__ is beleértve, válassza az __Előrejelzési URL-címet__. Azure-erőforráshoz csatolt projekteknél az __Előrejelzés-kulcs__ a társított Azure-erőforrás [Azure Portal](https://portal.azure.com) lapján is megtalálható a __Kulcsok__ alatt. Másolja ki a következő információkat az alkalmazásban történő használatra:

* __URL__ a használt __képfájlhoz__.
* __Előrejelzés-kulcs__ értéke.

> [!TIP]
> Ha több iteráció is van, akkor az alapértelmezett beállításával szabályozhatja, hogy melyiket használja. Jelölj ki az iterációt az __Iterációk__ területen, majd a lap tetején válassza ki a __Legyen alapértelmezett__ lehetőséget.

![Megjelenik a teljesítmény lap, rajta az előrejelzési URL-címet piros téglalap veszi körbe.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A Visual Studio-ban hozzon létre új C# konzolalkalmazást.

2. A __Program.cs__ fájl törzsében használja a következő kódot.

    > [!IMPORTANT]
    > Írja át a következő információkat:
    >
    > * A __névteret__ állítsa a saját projektje nevére.
    > * A korábban kapott __Előrejelzés-kulcs__ értéket állítsa be a `client.DefaultRequestHeaders.Add("Prediction-Key",` kezdetű sorban.
    > * A korábban kapott __URL__ értéket állítsa be a `string url =` kezdetű sorban.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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
        }
    }
    ```

## <a name="use-the-application"></a>Az alkalmazás használata

Az alkalmazás futtatásakor a képfájl elérési útvonalát kell megadni. A képet elküldi az API-hoz, az eredményeket JSON-dokumentumban kapja vissza. A következő JSON példa egy ilyen válaszra

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>További lépések

[A modell exportálása mobil használatra](export-your-model.md)
