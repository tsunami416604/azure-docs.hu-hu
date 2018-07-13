---
title: Face API a C# oktatóanyag |} A Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Hozzon létre egy egyszerű Windows-alkalmazást, amely a Cognitive Services Face API segítségével funkcióit arcok észlelése a képet.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: b51760f889db27aa25e54582070ee7d3adcf66f8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665240"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Csatlakozás a Visual Studio csatlakoztatott szolgáltatásai segítségével a Cognitive Services Face API

A Cognitive Services Face API segítségével észlelheti, elemzése, rendszerezése és megjelölése fényképeken.

Ez a cikk és a kiegészítő cikkek adja meg adatait a Visual Studio csatlakoztatott szolgáltatása szolgáltatás Cognitive Services Face API segítségével. A funkció a mindkét Visual Studio 2017 15.7 vagy későbbi, a Cognitive Services-bővítmény telepítve.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- **A Visual Studio 2017 verzió 15.7** együtt a **webfejlesztés** számítási feladattal. [Töltse le most](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Hozzon létre egy projektet, és a Cognitive Services Face API támogatása

1. Hozzon létre egy új ASP.NET Core web projektet. A projekt üres sablon segítségével. 

1. A **Megoldáskezelőben**, válassza a **Hozzáadás** > **csatlakoztatott szolgáltatás**.
   A projekthez is hozzáadhat szolgáltatásokat a csatlakoztatott szolgáltatás oldalon jelenik meg.

   ![Adja hozzá a csatlakoztatott szolgáltatás menüpont](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Elérhető szolgáltatások menüjében válassza **Cognitive Services Face API**.

   ![Válassza ki a szolgáltatáshoz való csatlakozáshoz](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Ha már bejelentkezett a Visual Studióban, és a fiókhoz társított Azure-előfizetéssel rendelkezik, megjelenik egy oldal, és a egy legördülő lista az összes előfizetés.

   ![Válassza ki előfizetését.](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Válassza ki az előfizetést szeretné használni, és válassza a Face API nevét, vagy válassza a Szerkesztés hivatkozásra módosíthatja is az automatikusan létrehozott nevet, válassza ki az erőforráscsoportot és a Tarifacsomagot.

   ![Csatlakoztatott szolgáltatás részleteinek szerkesztése](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Kövesse a hivatkozást a tarifacsomagok.

1. Támogatott a csatlakoztatott szolgáltatás hozzáadása a Hozzáadás gombra.
   A Visual Studio módosítja a NuGet-csomagok, konfigurációs bejegyzéseket és egyéb módosításokat egy kapcsolatot a Face API hozzáadása a projekthez.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>A Face API segítségével a képet attribútumai arcok észlelése

1. Adja hozzá a következő using utasításokat a Startup.cs fájlban.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adjon hozzá egy konfigurációs mező, és adja hozzá a konstruktort, amely az indítási osztályt konfigurációs ahhoz, hogy a program konfigurációs mezője inicializálja.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. A projekt wwwroot mappája, a képek mappa hozzáadása, és a képfájl hozzáadása a wwwroot mappája. Tegyük fel, használhatja a rendszerképeket a [Face API oldal](https://azure.microsoft.com/services/cognitive-services/face/). A jobb gombbal a rendszerképeket, mentse a helyi merevlemezről, majd a Megoldáskezelőben kattintson a jobb gombbal a képek mappa, és choosee **Hozzáadás** > **meglévő elem** , adja hozzá a projekthez. A projekt kell kinéznie a Megoldáskezelőben:
 
   ![Képek mappa képfájl](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Kattintson a jobb gombbal a képfájlt, válassza a Tulajdonságok elemet, és válassza **másolás, ha újabb**.

   ![Másolás, ha újabb](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. A konfigurálás módszert cserélje le a következő kódot a Face API eléréséhez, és a egy rendszerkép tesztelése. Módosítsa a imagePath karakterláncot a helyes elérési útját és fájlnevét, a arcképét.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Ebben a lépésben a kód létrehoz egy HTTP-kérést a csatlakoztatott szolgáltatás hozzáadásakor hozzáadott kulccsal a Face REST API-hívással.

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

1. A webalkalmazás futtatásához, és tekintse meg, milyen Face API a lemezképben található.
 
   ![A Face API-rendszerképet és a formázott eredmények](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a cognitive Services-szolgáltatás és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További tudnivalók a Face API olvassa el a [Face API-dokumentáció](Overview.md).
