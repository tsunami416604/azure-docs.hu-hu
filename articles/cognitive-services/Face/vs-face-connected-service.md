---
title: 'Oktatóanyag: szembenéző csatlakoztatott szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy Windows-alkalmazást, amely a Cognitive Services Face szolgáltatást használja az arcok funkcióinak észleléséhez egy képben.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: ghogen
ms.openlocfilehash: e0fe92fc7f19c3c899bcccfa9f9cc18029af049c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170244"
---
# <a name="connect-to-the-face-service-by-using-connected-services-in-visual-studio"></a>Csatlakozás a Face szolgáltatáshoz csatlakoztatott szolgáltatások használatával a Visual Studióban

Az Azure Face szolgáltatás használatával képeket talál, elemezheti, rendszerezheti és címkézheti az arcokat.

Ez a cikk és a hozzá tartozó cikkek részletesen ismertetik a Visual Studio Connected Service szolgáltatás használatát a Face szolgáltatáshoz. A funkció a Visual Studio 2017 15.7 és későbbi változatokban is elérhető, ha a Cognitive Services bővítmény telepítve van.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- A Visual Studio 2017 15,7-es vagy újabb verziója telepítve van a **webes fejlesztési** számítási feladattal. [Ezt innen töltheti le](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-face"></a>Projekt létrehozása és a Face támogatásának hozzáadása

1. Hozzon létre egy új ASP.NET Core-webprojektet. Használja az Üres projekt sablont. 

1. A **Megoldáskezelőben** válassza az **Add** > **Connected Service** (Hozzáadás, Csatlakoztatott szolgáltatás) lehetőséget.
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.

   ![Csatlakoztatott szolgáltatás menüpont hozzáadása](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Az elérhető szolgáltatások menüjében válassza a **Cognitive Services Face API** pontot.

   ![A csatlakoztatni kívánt szolgáltatás kiválasztása](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Ha már bejelentkezett a Visual Stúdióba, és rendelkezik a fiókhoz társított Azure-előfizetéssel, akkor megjelenik egy oldal az előfizetéseit tartalmazó legördülő listával.

   ![Válassza ki előfizetését.](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Válassza ki a használni kívánt előfizetést, majd válasszon egy nevet a Face szolgáltatáshoz, vagy a Szerkesztés hivatkozásra kattintva módosítsa az automatikusan generált nevet, válassza ki az erőforráscsoportot és az árképzési szintet.

   ![A csatlakoztatott szolgáltatás részleteinek szerkesztése](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   A tarifacsomagokkal kapcsolatban kövesse a hivatkozást.

1. Válassza a Hozzáadás gombot a csatlakoztatott szolgáltatás támogatás hozzáadásához.
   A Visual Studio módosítja a projektet, hogy hozzáadja a NuGet-csomagokat, a konfigurációs fájl bejegyzéseit és más módosításokat a Face szolgáltatáshoz való kapcsolódás támogatásához.

## <a name="use-the-face-service-to-detect-attributes-of-faces-in-an-image"></a>A Face szolgáltatás használatával azonosíthatja a képekben lévő arcok attribútumait

1. Adja hozzá a következőket utasításokkal a Startup.cs fájlba.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Adjon hozzá egy konfigurációs mezőt, és adja hozzá a Startup osztályban a konfigurációs mezőt inicializáló konstruktort, hogy engedélyezze a Konfigurációt a programban.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. A projekt wwwroot mappában készítsen egy képek mappát, és adjon egy képfájlt a wwwroot mappába. Példaként használhatja a rendszerképek egyikét a Azure Portal [arc oldalán](https://azure.microsoft.com/services/cognitive-services/face/) . Kattintson a jobb gombbal az egyik képre, mentse a helyi merevlemezre, majd Megoldáskezelő kattintson a jobb gombbal a lemezképek mappára, és válassza a > **meglévő elem** **hozzáadása** lehetőséget a projekthez való hozzáadáshoz. A projektnek ehhez hasonlóan kell kinéznie a Megoldáskezelőben:
 
   ![Képek mappa a képfájllal](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Kattintson jobb gombbal a képfájlra, válassza a Properties (Tulajdonságok) elemet, majd a **Copy if newer** (Másolás, ha újabb) lehetőséget.

   ![Másolás, ha újabb](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Cserélje le a configure metódust a következő kódra a Face szolgáltatás eléréséhez és a rendszerkép teszteléséhez. Az imagePath sztringet változtassa át az arcfelismerés képre mutató útvonalra és fájlnévre.

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
    Az ebben a lépésben szereplő kód egy HTTP-kérelmet hoz létre a Face REST API hívásával, a csatlakoztatott szolgáltatás hozzáadásakor hozzáadott kulccsal.

1. Adja hozzá a GetImageAsByteArray és JsonPrettyPrint segédfüggvényeket.

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

1. Futtassa a webalkalmazást, és tekintse meg a rendszerképben található Arcfelismerés szolgáltatást.
 
   ![Arc-szolgáltatás képe és formázott eredményei](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kognitív szolgáltatást és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a Face szolgáltatásról: a [Face dokumentációjának](Overview.md)olvasása.
