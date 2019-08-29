---
title: 'Oktatóanyag: Metaadatok készítése az Azure-rendszerképekhez'
titleSuffix: Azure Cognitive Services
description: Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Azure Computer Vision szolgáltatást egy webalkalmazásba a rendszerképekhez tartozó metaadatok létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: c125941414c73b14138f2b2fc15e470d9ed2d905
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137542"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Oktatóanyag: Rendszerkép-metaadatok előállítása az Azure Storage-ban Computer Vision használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Azure Computer Vision szolgáltatást egy webalkalmazásba a feltöltött lemezképek metaadatainak létrehozásához. Az Azure Storage-ban és a GitHubon [Cognitive Services laborban](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) teljes körű útmutatót talál, és ez az oktatóanyag alapvetően a labor 5. gyakorlatát fedi le. Előfordulhat, hogy minden lépés után létre kívánja hozni a végpontok közötti alkalmazást, de ha szeretné megtekinteni, hogyan integrálható a Computer Vision egy meglévő webalkalmazásba, olvassa el itt.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Computer Vision-erőforrás létrehozása az Azure-ban
> * Rendszerkép-elemzés végrehajtása az Azure Storage-lemezképeken
> * Metaadatok csatolása az Azure Storage-lemezképekhez
> * Képmetaadatok keresése Azure Storage Explorer használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) vagy újabb verziója a "ASP.net and Web Development" és az "Azure Development" számítási feladatokkal.
- Egy Azure Storage-fiók lemezképek számára lefoglalt blob-tárolóval ( [Az Azure Storage Lab 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) . gyakorlatának követése, ha segítségre van szüksége ehhez a lépéshez).
- Az Azure Storage Explorer eszköz ( [Az Azure Storage Lab 2](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) . gyakorlatának követése, ha segítségre van szüksége ehhez a lépéshez).
- Egy ASP.NET webalkalmazás, amely hozzáfér az Azure Storage-hoz ( [Az Azure Storage Lab 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) . gyakorlatának követésével gyorsan létrehoz egy ilyen alkalmazást).

## <a name="create-a-computer-vision-resource"></a>Computer Vision erőforrás létrehozása

Létre kell hoznia egy Computer Vision erőforrást az Azure-fiókjához; Ez az erőforrás felügyeli az Azure Computer Vision szolgáltatáshoz való hozzáférését. 

1. Computer Vision erőforrás létrehozásához kövesse az [Azure Cognitive Services-erőforrás létrehozása](../../cognitive-services-apis-create-account.md#single-service-resource) című témakör utasításait.

1. Ezután lépjen az erőforráscsoport menüjére, és kattintson az imént létrehozott Computer Vision API-előfizetésre. Másolja az URL-címet a **végpont** alatt, hogy valahol egyszerűen lekérheti azt egy pillanat alatt. Ezután kattintson a **hozzáférési kulcsok megjelenítése**lehetőségre.

    ![Azure Portal oldalon a végpont URL-címe és a hozzáférési kulcsok hivatkozása](../Images/copy-vision-endpoint.png)[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. A következő ablakban másolja az **1. kulcs** értékét a vágólapra.

    ![A kulcsok kezelése párbeszédpanel, amely a másolás gombot ismerteti](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Computer Vision hitelesítő adatok hozzáadása

Ezután hozzá kell adnia a szükséges hitelesítő adatokat az alkalmazáshoz, hogy hozzáférhessen Computer Vision erőforrásaihoz

Nyissa meg a ASP.NET-webalkalmazást a Visual Studióban, és navigáljon a **web. config** fájlhoz a projekt gyökerében. Adja hozzá a következő utasításokat `<appSettings>` a fájl szakaszához, és cserélje `VISION_KEY` le az előző lépésben lemásolt kulcsra, valamint `VISION_ENDPOINT` az előző lépésben mentett URL-címet.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Ezután a Megoldáskezelő kattintson a jobb gombbal a projektre, és a **NuGet-csomagok kezelése** parancs használatával telepítse a **Microsoft. Azure. CognitiveServices. vízió. ComputerVision**csomagot. Ez a csomag a Computer Vision API meghívásához szükséges típusokat tartalmazza.

## <a name="add-metadata-generation-code"></a>Metaadatok létrehozási kódjának hozzáadása

Ezután adja hozzá a kódot, amely ténylegesen kihasználja a Computer Vision szolgáltatást a rendszerképekhez tartozó metaadatok létrehozásához. Ezek a lépések érvényesek lesznek a ASP.NET alkalmazásra a laborban, de a saját alkalmazásra is adaptálható. Fontos, hogy ezen a ponton van egy ASP.NET webalkalmazása, amely képeket tölthet fel egy Azure Storage-tárolóba, beolvashatja a képeket, és megjelenítheti őket a nézetben. Ha nem biztos benne, hogy [Az Azure Storage Lab 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). gyakorlatát követi. 

1. Nyissa meg a *HomeController.cs* fájlt a projekt- **vezérlők** mappában, és adja `using` hozzá a következő utasításokat a fájl elejéhez:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Ezután nyissa meg a **feltöltési** metódust; Ez a metódus átalakítja és feltölti a képeket a blob Storage-ba. Adja hozzá a következő kódot közvetlenül a `// Generate a thumbnail` (vagy a rendszerkép-létrehozási folyamat végén) kezdetű blokk után. Ez a kód a rendszerképet tartalmazó blobot`photo`() használja, és a Computer Vision használatával készít leírást ehhez a képhez. A Computer Vision API a képre vonatkozó kulcsszavak listáját is létrehozhatja. A generált Leírás és a kulcsszavak a blob metaadataiban tárolódnak, hogy később is beolvashatók legyenek.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Ezután nyissa meg az **index** metódust ugyanebben a fájlban; Ez a metódus enumerálja a tárolt képblobokat a célként megadott blob-tárolóban ( **IListBlobItem** -példányként), és átadja azokat az alkalmazás nézetnek. Cserélje le `foreach` a metódus blokkját a következő kódra. Ez a kód a **CloudBlockBlob. FetchAttributes** metódust hívja meg az egyes Blobok csatolt metaadatainak lekéréséhez. Kibontja a számítógép által generált leírást`caption`() a metaadatokból, és hozzáadja azt a **BlobInfo** objektumhoz, amely átkerül a nézetbe.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Az alkalmazás tesztelése

Mentse a módosításokat a Visual Studióban, és nyomja le a **CTRL + F5** billentyűkombinációt az alkalmazás a böngészőben történő elindításához. Az alkalmazás használatával tölthet fel néhány képet a labor erőforrásainak "fényképek" mappájából vagy a saját mappájából. Amikor a nézet egyik képére viszi a kurzort, megjelenik egy elemleírás ablak, amely megjeleníti a lemezkép számítógép által generált feliratát.

![A számítógép által generált felirat](../Images/thumbnail-with-tooltip.png)

Az összes csatolt metaadat megtekintéséhez a Azure Storage Explorer segítségével megtekintheti a lemezképekhez használt tárolót. Kattintson a jobb gombbal a tároló egyik blobára, és válassza a **Tulajdonságok**lehetőséget. A párbeszédpanelen megjelenik a kulcs-érték párok listája. A számítógép által generált rendszerkép leírását a "Caption" elem tárolja, a keresési kulcsszavakat pedig a "Tag0", "Tag1" és így tovább. Ha elkészült, kattintson a **Mégse** gombra a párbeszédpanel bezárásához.

![Rendszerkép tulajdonságai párbeszédpanel, metaadatokat tartalmazó címkékkel](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbbra is szeretne dolgozni a webalkalmazáson, tekintse meg a [következő lépések](#next-steps) szakaszt. Ha nem tervezi az alkalmazás használatának folytatását, törölje az összes alkalmazásspecifikus erőforrást. Ehhez egyszerűen törölheti az Azure Storage-előfizetést és Computer Vision erőforrást tartalmazó erőforráscsoportot. Ezzel eltávolítja a Storage-fiókot, a hozzá feltöltött blobokat, valamint a ASP.NET-webalkalmazáshoz való kapcsolódáshoz szükséges App Service erőforrást. 

Az erőforráscsoport törléséhez nyissa meg az **erőforráscsoportok** panelt a portálon, navigáljon a projekthez használt erőforráscsoporthoz, és kattintson a nézet tetején található **erőforráscsoport törlése** elemre. A rendszer felszólítja az erőforráscsoport nevének megadására, hogy erősítse meg, hogy törölni kívánja-e, mert a törlés után egy erőforráscsoport nem állítható helyre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban integrálta az Azure Computer Vision szolgáltatását egy meglévő webalkalmazásba, hogy automatikusan előállítson feliratokat és kulcsszavakat a blob-képekhez, amikor feltöltik őket. Ezután tekintse meg az Azure Storage Lab, 6. gyakorlatát, hogy megtudja, hogyan adhat hozzá keresési funkciókat a webalkalmazáshoz. Ez kihasználja a Computer Vision szolgáltatás által generált keresési kulcsszavakat.

> [!div class="nextstepaction"]
> [Keresés hozzáadása az alkalmazáshoz](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
