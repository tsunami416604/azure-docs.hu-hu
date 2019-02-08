---
title: Visual Studio csatlakoztatott szolgáltatása – Computer Vision
titleSuffix: Azure Cognitive Services
description: Csatlakozhat a Computer Vision API egy ASP.NET Core-webalkalmazás a Visual Studio csatlakoztatott szolgáltatása funkció használatát.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: Tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: a67b4b7eee30d4bdcabee4fdcfe2e4a37ed6c718
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876113"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Connected Services használata a Visual Studióban a Computer Vision API-hoz való csatlakozáshoz

A Cognitive Services Computer Vision API használatával részletes információkat nyerhet ki a vizuális adatok kategorizálásához és feldolgozásához, és képek gépi támogatású moderálása segíthet a szolgáltatások összeválogatásában.

Ez a cikk és a kísérő cikkek részletesen ismertetik, hogyan használható a Visual Studio Connected Service funkciója a Cognitive Services Computer Vision API-hoz. Ez a funkció a Visual Studio 2017 15.7-es vagy újabb verzióiban érhető el, ha a Cognitive Services bővítmény telepítve van.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- A **Visual Studio 2017 15.7-es verziója**, telepített **webfejlesztési** számítási feladattal. [Ezt innen töltheti le](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>A Cognitive Services Computer Vision API támogatásának hozzáadása a projekthez

1. Hozzon létre egy új ASP.NET Core-webprojektet. Használja az Üres projekt sablont. 

1. A **Megoldáskezelőben** válassza az **Add** > **Connected Service** (Hozzáadás, Csatlakoztatott szolgáltatás) lehetőséget.
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.

   ![Képernyőfelvétel a Visual Studio-projektet a helyi menüben: Adjon hozzá > csatlakoztatott szolgáltatás](../media/vs-common/Connected-Service-Menu.PNG)

1. Az elérhető szolgáltatások menüjében válassza a **Cognitive Services Computer Vision API** lehetőséget.

   ![a csatlakoztatott szolgáltatás menü lemezképek elemezheti a Computer Vision kiemelése](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Ha bejelentkezett a Visual Studióba, és rendelkezik a fiókjához társított Azure-előfizetéssel, egy lap jelenik meg, amely az előfizetéseit tartalmazza egy legördülő listában.

   ![Egy "A Computer Vision API" a Visual Studio-ablakot a legördülő listában vannak kiemelve](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Válassza ki a használni kívánt előfizetést, majd válasszon nevet a Computer Vision API számára, vagy a szerkesztési hivatkozást választva módosítsa az automatikusan létrehozott nevet, és válassza ki az erőforráscsoportot és a tarifacsomagot.

   ![A csatlakoztatott szolgáltatás részleteinek szerkesztése](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   A tarifacsomagok részleteiért kövesse a hivatkozást.

1. Válassza az Add (Hozzáadás) gombot a csatlakoztatott szolgáltatás támogatásának hozzáadásához.
   A Visual Studio módosítja a projektet, és hozzáadja a NuGet-csomagokat, a konfigurációs fájlbejegyzéseket, és a Computer Vision API-kapcsolat támogatása érdekében végrehajtott egyéb módosításokat. A kimeneti ablak a projektben történt események naplóját mutatja. Az alábbihoz hasonló kimenet jelenik meg:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>A Computer Vision API használata egy kép attribútumainak észleléséhez

1. Adja hozzá a következő „using” utasításokat a Startup.cs fájlhoz.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adjon hozzá egy konfigurációs mezőt, és adja hozzá a konstruktort, amely inicializálja a konfigurációs mezőt a `Startup` osztályban, hogy engedélyezze a konfigurációt a programban.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. A projekt wwwroot mappájában hozzon létre egy képek mappát, és adjon egy képfájlt a wwwroot mappához. Példaként használhat egy képet erről a [Computer Vision API-oldalról](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Kattintson a jobb gombbal az egyik képre, mentse a helyi merevlemezre, majd a Megoldáskezelőben kattintson a jobb gombbal a képek mappára, és válassza az **Add** > **Existing Item** (Hozzáadás, Meglévő elem) lehetőséget, hogy hozzáadja a projekthez. A projektnek ehhez hasonlóan kell kinéznie a Megoldáskezelőben: 
  
   ![A kiválasztott képfájllal megoldáskezelő-nézet képernyőképe](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Kattintson jobb gombbal a képfájlra, válassza a Properties (Tulajdonságok) elemet, majd a **Copy if newer** (Másolás, ha újabb) lehetőséget. 

   ![Egy lemezkép tulajdonságai ablakban; Másolás a kimeneti könyvtár másolási értékre van állítva, ha újabb](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. A Configure metódust cserélje a következő kódra a Computer Vision API eléréséhez és a kép teszteléséhez.

   ```csharp
        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site. 
            string imagePath = @"images/subway.png";

            // Enable static files such as image files. 
            app.UseStaticFiles();

            string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
            string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

            // Assemble the URI for the REST API Call.
            string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts an image you've added to your site's images folder. 
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentString = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentString = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
                await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentString));
                await context.Response.WriteAsync("<p>");
            });
        }

   ```
    A kód létrehoz egy HTTP-kérelmet az URI-val és a bináris tartalomként szereplő képpel a Computer Vision REST API felé irányuló híváshoz.

1. Adja hozzá a GetImageAsByteArray és a JsonPrettyPrint segítő függvényt.

   ```csharp
        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            string INDENT_STRING = "    ";
            var indent = 0;
            var quoted = false;
            var sb = new StringBuilder();
            for (var i = 0; i < json.Length; i++)
            {
                var ch = json[i];
                switch (ch)
                {
                    case '{':
                    case '[':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case '}':
                    case ']':
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        sb.Append(ch);
                        break;
                    case '"':
                        sb.Append(ch);
                        bool escaped = false;
                        var index = i;
                        while (index > 0 && json[--index] == '\\')
                            escaped = !escaped;
                        if (!escaped)
                            quoted = !quoted;
                        break;
                    case ',':
                        sb.Append(ch);
                        if (!quoted)
                        {
                            sb.AppendLine();
                        }
                        break;
                    case ':':
                        sb.Append(ch);
                        if (!quoted)
                            sb.Append(" ");
                        break;
                    default:
                        sb.Append(ch);
                        break;
                }
            }
            return sb.ToString();
        }
   ```

1. Futtassa a webalkalmazást, és tekintse át, hogy mit talált a Computer Vision API a képen.

   ![A Computer Vision API képe és a formázott eredmények](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a Cognitive Services-szolgáltatást és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

A Computer Vision API-ról a [Computer Vision API dokumentációjában](Home.md) olvashat további információkat.
