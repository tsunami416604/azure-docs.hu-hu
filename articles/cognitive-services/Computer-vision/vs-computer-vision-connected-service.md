---
title: Számítógép Vision C# oktatóanyag |} A Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services – Látástechnológiai csatlakozhat egy ASP.NET Core-webalkalmazás.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 76ca1215144a5caa40971e1eda23f6462f7bf27b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665254"
---
# <a name="connecting-to-cognitive-services-computer-vision-api-by-using-connected-services-in-visual-studio"></a>Csatlakozás a Visual Studio csatlakoztatott szolgáltatásai segítségével a Cognitive Services Computer Vision API

A Cognitive Services Computer Vision API használatával a vizuális adatok kategorizálásához és feldolgozásához részletes információt nyerhet ki, és hajtsa végre a gépi támogatású képmoderálás segítheti a szolgáltatások összeválogatásában.

Ez a cikk és a kiegészítő cikkek adja meg adatait a Visual Studio csatlakoztatott szolgáltatása szolgáltatás a Cognitive Services Computer Vision API segítségével. A funkció a mindkét Visual Studio 2017 15.7 vagy későbbi, a Cognitive Services-bővítmény telepítve.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- **A Visual Studio 2017 verzió 15.7** együtt a **webfejlesztés** számítási feladattal. [Töltse le most](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Támogatás hozzáadása a projekthez a Cognitive Services Computer Vision API

1. Hozzon létre egy új ASP.NET Core web projektet. A projekt üres sablon segítségével. 

1. A **Megoldáskezelőben**, válassza a **Hozzáadás** > **csatlakoztatott szolgáltatás**.
   A projekthez is hozzáadhat szolgáltatásokat a csatlakoztatott szolgáltatás oldalon jelenik meg.

   ![Adja hozzá a csatlakoztatott szolgáltatás menüpont](../media/vs-common/Connected-Service-Menu.PNG)

1. Elérhető szolgáltatások menüjében válassza **Cognitive Services Computer Vision API**.

   ![Válassza ki a szolgáltatáshoz való csatlakozáshoz](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Ha már bejelentkezett a Visual Studióban, és a fiókhoz társított Azure-előfizetéssel rendelkezik, megjelenik egy oldal, és a egy legördülő lista az összes előfizetés.

   ![Válassza ki előfizetését.](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Válassza ki az előfizetést szeretné használni, és válassza a Computer Vision API egy nevet, vagy válassza a Szerkesztés hivatkozásra módosíthatja is az automatikusan létrehozott nevet, válassza ki az erőforráscsoportot és a Tarifacsomagot.

   ![Csatlakoztatott szolgáltatás részleteinek szerkesztése](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Kövesse a hivatkozást a tarifacsomagok.

1. Támogatott a csatlakoztatott szolgáltatás hozzáadása a Hozzáadás gombra.
   A Visual Studio módosítja a NuGet-csomagok, konfigurációs bejegyzéseket és egyéb módosításokat egy kapcsolatot a Computer Vision API hozzáadása a projekthez. A kimeneti ablak megjeleníti a naplót, hogy mi történik a projekthez. A következőhöz hasonló kell megjelennie:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 1.0.2-preview.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Kép attribútumai észleléséhez használni a Computer Vision API

1. Adja hozzá a következő using utasításokat a Startup.cs fájlban.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adjon hozzá egy konfigurációs mező, és adja hozzá a konstruktort, amely a konfigurációs mező inicializálja a `Startup` ahhoz, hogy a program konfigurációs osztályt.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. A projekt wwwroot mappája, a képek mappa hozzáadása, és a képfájl hozzáadása a wwwroot mappája. Tegyük fel, használhatja a rendszerképeket a [Computer Vision API oldal](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Kattintson a jobb gombbal a rendszerképeket, mentse a helyi merevlemezről, majd a Megoldáskezelőben kattintson a jobb gombbal a képek mappa, és choosee **Hozzáadás** > **meglévő elem** , adja hozzá a projekthez. A projekt kell kinéznie a Megoldáskezelőben: 
  
   ![Képek mappa képfájl](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Kattintson a jobb gombbal a képfájlt, válassza a Tulajdonságok elemet, és válassza **másolás, ha újabb**. 

   ![Másolás, ha újabb](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. A konfigurálás módszert cserélje le a következő kódot a Computer Vision API eléréséhez, és a egy rendszerkép tesztelése.

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
    A kód itt az URI-t és a egy HTTP-kérést a Computer Vision REST API-hívások bináris tartalomként hoz létre.

1. Adja hozzá a segédfüggvények GetImageAsByteArray és JsonPrettyPrint.

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

1. A webalkalmazás futtatásához, és tekintse meg, milyen Computer Vision API a lemezképben található.

   ![Lemezképének Vision API és a formázott eredmények](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a cognitive Services-szolgáltatás és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További tudnivalók a Computer Vision API olvassa el a [számítógépes Látástechnológiai API-dokumentáció](Home.md).
