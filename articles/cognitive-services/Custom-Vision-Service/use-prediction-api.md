---
title: 'Példa: Előrejelzési végpont programozott módon tesztelheti a rendszerképeket az osztályozó által igénybe vett – Custom Vision használata'
titlesuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható az API képek programozott tesztelésére a Custom Vision Service osztályozóval.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472725"
---
#  <a name="use-your-model-with-the-prediction-api"></a>A modell használata az előrejelzési API

Miután betanította a modellt, programozott módon tesztelhet képeket úgy, hogy elküldi őket az előrejelzési API számára.

> [!NOTE]
> Ez a dokumentum C# használatával mutatja be a kép elküldését az előrejelzési API-hoz. Az API használatával kapcsolatos bővebb információkért és példákért tekintse meg az [Előrejelzési API-referenciát](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>A betanított iteráció közzététele

A [Custom Vision weblapon](https://customvision.ai) jelölje ki a projektet, majd válassza ki a __Teljesítmény__ fület.

Elküldeni a képek az előrejelzési API-hoz, létre kell közzétenni az előrejelzéshez, amely választásával teheti meg az iteráció __közzététel__ , és adjon meg egy közzétett ismétléseinek. Ez lehetővé teszi a modell az előrejelzési API-hoz a Custom Vision Azure-erőforrás érhető el. 

![A Teljesítmény lapon megjelenő, egy vörös téglalappal a Közzététel gombra.](./media/use-prediction-api/unpublished-iteration.png)

Miután sikeresen közzétette a modell, megjelenik egy "Közzétéve" címkét, az iteráció a bal oldali oldalsávon, valamint a közzétett verzió továbbfejlesztésében az iteráció leírásában neve mellett jelenik meg.

![A Teljesítmény lap jelenik meg, egy vörös téglalappal, a közzétett címke és a közzétett iteráció nevével együtt.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Az URL és az előrejelzési kulcs megszerzése

Miután közzétette a modell, lekérheti az előrejelzési API-val kiválasztásával kapcsolatos információk __előrejelzési URL-cím__. Ekkor megnyílik egy párbeszédpanel, például az alábbi képen látható a adatokkal, az előrejelzési API-val többek között a __előrejelzési URL-cím__ és __előrejelzés-kulcs__.

![A Teljesítmény lapon megjelenő egy vörös téglalappal az előrejelzési URL-cím gombra.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A Teljesítmény lapon megjelenő egy vörös téglalappal képfájl és az előrejelzési-kulcs-érték segítségével előrejelzési URL-cím értékét.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> A __előrejelzés-kulcs__ is megtalálható a [az Azure Portal](https://portal.azure.com) lapon a projekthez, a társított a Custom Vision Azure Resource __kulcsok__. 

A párbeszédpanelről másolja az alkalmazás használja a következő információkat:

* __Előrejelzési URL-cím__ használatának egy __képfájl__.
* __Előrejelzés-kulcs__ értéket.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A Visual Studio-ban hozzon létre új C# konzolalkalmazást.

1. A __Program.cs__ fájl törzsében használja a következő kódot.

    > [!IMPORTANT]
    > Írja át a következő információkat:
    >
    > * A __névteret__ állítsa a saját projektje nevére.
    > * Állítsa be a __előrejelzés-kulcs__ kezdődő sort korábban lekért érték `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Állítsa be a __előrejelzési URL-cím__ kezdődő sort korábban lekért érték `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Az alkalmazás használata

Az alkalmazás futtatásakor kell megadnia az elérési utat a konzolon képfájlra mutató. A kép elküldésekor az előrejelzési API-hoz, és a rendszer visszairányítja az előrejelzési eredményeket JSON-dokumentumként. A következő JSON-ra, amelyek a válasz.

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

[A modell exportálása mobil használatra](export-your-model.md)

[Ismerkedés a .NET SDK-k](csharp-tutorial.md)

[Python SDK-k használatának első lépései](python-tutorial.md)

[A Java SDK-k használatának első lépései](java-tutorial.md)

[Node SDK-k használatának első lépései](node-tutorial.md)

[Go SDK-k használatának első lépései](go-tutorial.md)
