---
title: A Custom Vision Service prediction végpont – Azure Cognitive Services használata |} A Microsoft Docs
description: Megtudhatja, hogyan használható az API programozott módon a lemezképek, amelyek a Custom Vision Service osztályozó-teszteléséhez.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d7f9b90db06811e16cd0cd6ad2b32a27912cfee5
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341793"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Az előrejelzési végpont használatával programozott módon a Custom Vision Service besorolás rendszerképek tesztelése

Miután a modell betanításához tesztelheti lemezképek programozott módon az előrejelzési API-nak elküldésével. 

> [!NOTE]
> Ez a dokumentum bemutatja a C# használatával való elküldéséhez a képet, az előrejelzési API-hoz. További információk és az API-t használó példákért tekintse meg a [előrejelzési API-referencia](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Az URL-cím és az előrejelzési kulcs lekérése

Az a [Custom Vision weblap](https://customvision.ai), jelölje ki a projektet, és válassza ki a __teljesítmény__ fülre. Az előrejelzési API-val kapcsolatos információk megjelenítéséhez többek között a __előrejelzés-kulcs__válassza __előrejelzési URL-cím__. Projektek az Azure-erőforrás csatlakoztatva a __előrejelzés-kulcs__ is megtalálható a [az Azure Portal](https://portal.azure.com) társított Azure-erőforrásra vonatkozó lap __kulcsok__. Használja az alábbi adatokat másolja az alkalmazásban:

* __URL-cím__ használatának egy __képfájl__.
* __Előrejelzés-kulcs__ értéket.

> [!TIP]
> Ha több ismétlések, szabályozhatja, melyiket használja az alapértelmezett értékre. Válassza ki a ismétléseinek a __ismétléseinek__ területen, majd válassza ki __alapértelmezett__ az oldal tetején.

![A Teljesítmény lapon megjelenő egy vörös téglalappal az előrejelzési URL-címet.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új C# konzolalkalmazást.

2. A következő kód használatával törzseként a __Program.cs__ fájlt.

    > [!IMPORTANT]
    > Módosítsa a következő információkat:
    >
    > * Állítsa be a __névtér__ a projekt nevére.
    > * Állítsa be a __előrejelzés-kulcs__ kezdődő sort, kapott érték `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Állítsa be a __URL-cím__ kezdődő sort, kapott érték `string url =`.

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

Az alkalmazás futtatásakor adja meg az elérési út képfájlra mutató. A kép elküldésekor az API-hoz, és a rendszer visszairányítja az eredményeket JSON-dokumentumként. A következő JSON-ja egy példa a válasz

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
