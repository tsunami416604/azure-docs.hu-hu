---
title: Az egyéni stratégiai szolgáltatás előrejelzés végpont - kognitív Azure-szolgáltatások használata |} Microsoft Docs
description: 'Útmutató: az API segítségével programozott módon ellenőrizze, az egyéni stratégiai szolgáltatás osztályozó képeket.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 54f9d9fec1f40c167341dec6a8699b6a558419da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348154"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Az előrejelzés végpont segítségével ellenőrizze, programozott módon képekkel egyéni stratégiai szolgáltatás besorolás

Után modellje betanításához képek programozott módon tesztelheti, ha azokat az előrejelzés API elküldése. 

> [!NOTE]
> Ez a dokumentum bemutatja C# elküldeni a képet, hogy az előrejelzés API használatával. További információt és példákat az API-val, tekintse meg a [előrejelzés API-referencia](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Az URL-cím és előrejelzés kulcs beszerzése

Az a [egyéni stratégiai weblap](https://customvision.ai), válassza ki a projektet, és válassza ki a __teljesítmény__ fülre. Az előrejelzés API-val kapcsolatos információk megjelenítése, válassza ki a __előrejelzés URL-cím__. Másolja az alábbi használatra az alkalmazásban:

* __URL-cím__ használatára vonatkozó egy __képfájl__.
* __Előrejelzés-kulcs__ érték.

> [!TIP]
> Ha többszöri, azt is szabályozhatja, melyiket használja az alapértelmezett beállítás. Válassza ki az ismétlés a __ismétlési__ területen, majd válassza ki __alapértelmezett__ az oldal tetején.

![A Teljesítmény lapon megjelenő egy vörös téglalappal előrejelzés URL-CÍMÉT.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Hozzon létre egy új C# konzolalkalmazást a Visual Studio.

2. A következő kódot használja szövegeként a __Program.cs__ fájlt.

    > [!IMPORTANT]
    > Módosítsa a következő információkat:
    >
    > * Állítsa be a __névtér__ a projekt nevét.
    > * Állítsa be a __előrejelzés-kulcs__ kezdődő sort a korábbi kapott érték `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Állítsa be a __URL-cím__ kezdődő sort a korábbi kapott érték `string url =`.

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

## <a name="use-the-application"></a>Az alkalmazás használatára

Ha az alkalmazás fut, adja meg az elérési út képfájlra mutató. A kép elküldve az API-t a, és az eredmény akkor minősül JSON-dokumentumként. A válasz egy példát a következő JSON-ja

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

[A modell mobil használatra exportálása](export-your-model.md)