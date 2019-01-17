---
title: 'Oktatóanyag: Metaadatok Azure Storage-rendszerképek létrehozása'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban megtudhatja, hogyan integrálható az Azure Computer Vision service webalkalmazásba metaadatok rendszerképek létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: pafarley
ms.openlocfilehash: d720314e190c217857b026c25c85f5b8cf1dc626
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355432"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Oktatóanyag: A képek metaadatai létrehozni az Azure Storage használata a Computer Vision

Ebben az oktatóanyagban megtudhatja, hogyan integrálható az Azure Computer Vision service webalkalmazásba a feltöltött képek metaadatai létrehozásához. Egy teljes körű útmutatót megtalálhatók a [Azure Storage és a Cognitive Services labor](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) a GitHub és az oktatóprogramból a lényegében fedezik gyakorolja a labor létrehozása 5. Előfordulhat, hogy a létre kívánja hozni a végpontok közötti alkalmazás minden lépést követve, de ha, csak szeretné látni, hogyan számítógépes Látástechnológiai integrálható egy meglévő web Apps, olvassa el itt mentén.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Számítógépes Látástechnológia erőforrás létrehozása az Azure-ban
> * Kép elemzéseket végezhet az Azure Storage-lemezképek
> * Metaadatok csatolása az Azure Storage-lemezképek
> * Ellenőrizze a képek metaadatai Azure Storage Explorer használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- [A Visual Studio 2017 Community edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) vagy újabb, az "ASP.NET és webfejlesztési" és "Azure-fejlesztési" számítási feladatokat telepíteni.
- Azure Storage-fiókot, és egy blobtárolót a rendszerképek számára lefoglalt (hajtsa végre a [gyakorlatok 1 az Azure Storage labor](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) Ha ehhez a lépéshez segítségre van szüksége).
- Az Azure Storage Explorer eszköz (hajtsa végre a [az Azure Storage-labor létrehozása a gyakorlatban 2](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) Ha ehhez a lépéshez segítségre van szüksége).
- ASP.NET-webalkalmazás hozzáférést az Azure Storage (hajtsa végre a [gyakorlat 3 az Azure Storage labor](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) ilyen alkalmazások gyors létrehozása).

## <a name="create-a-computer-vision-resource"></a>Számítógépes Látástechnológia erőforrás létrehozása

Szüksége lesz az Azure-fiókjával; a Computer Vision erőforrás létrehozása Ezt az erőforrást a hozzáférést az Azure Computer Vision Service kezeli.

1. Jelentkezzen be a [az Azure portal](https://ms.portal.azure.com) kattintson **erőforrás létrehozása**, utána pedig **mesterséges Intelligencia és Machine Learning** és **Computer Vision**.

    ![Computer Vision API új előfizetés létrehozása](../Images/new-vision-api.png)

1. A párbeszédpanelen adja meg az "látás-api-key" az a **neve** mezőbe, majd válasszon **F0** , a **tarifacsomag**. Válassza ki ugyanazt **hely** az Azure Storage-fiók beállításakor kiválasztott. Alatt **erőforráscsoport**válassza **meglévő** , és válassza ki ugyanazt az erőforráscsoportot is. Ellenőrizze a **megerősítem, hogy** mezőbe, majd kattintson a **létrehozás**.

    ![A Computer Vision API, Subcribing](../Images/create-vision-api.png)

1. Térjen vissza az erőforráscsoport a menüben, majd kattintson az imént létrehozott Computer Vision API-előfizetés. Másolja az URL-CÍMÉT a **végpont** valahol, hogy könnyen hozzáférhet kis idő múlva. Kattintson a **hozzáférési kulcsok megjelenítése**.

    ![A hozzáférési kulcsok megtekintése](../Images/copy-vision-endpoint.png)

1. A következő ablakban másolja a értékét **kulcs 1** a vágólapra.

    ![A hozzáférési kulcs másolása](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Számítógépes Látástechnológia hitelesítő adatainak hozzáadása

Ezután fel fog venni szükséges hitelesítő adatok alkalmazását úgy, hogy hozzá tudjon férni a Computer Vision erőforrások

Az ASP.NET-alkalmazás megnyitása a Visual Studióban, és keresse meg a **Web.config** fájlt a projekt gyökerében. Adja hozzá az alábbi utasításokat, a `<appSettings>` szakasz a fájl cseréje `VISION_KEY` az előző lépésben kimásolt kulccsal és `VISION_ENDPOINT` , amely előtt az lépésben mentett URL-címét.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Ezután a megoldáskezelőben kattintson a jobb gombbal a projektre, és használhatja a **NuGet-csomagok kezelése** parancsot a csomag telepítéséhez **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Ez a csomag tartalmazza a Computer Vision API meghívásához szükséges típusokat.

## <a name="add-metadata-generation-code"></a>Adja hozzá a metaadatokat kódnak

Ezután hozzáadja a kódot, amely ténylegesen a Computer Vision service lemezképek metaadatokat létrehozni. Az ASP.NET alkalmazás a tesztkörnyezetben alkalmazza ezeket a lépéseket, de a saját alkalmazásba módosíthatja őket. Fontos, hogy ezen a ponton rendelkezik egy ASP.NET-alkalmazás, tölthet fel képeket a az Azure Storage-tárolókat, lemezképek olvasni, és meg tudja őket jeleníteni a nézetben. Ha ez tesztadatkészletén, célszerű kövesse [gyakorlat 3 az Azure Storage labor](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Nyissa meg a *HomeController.cs* fájlt a projektben **tartományvezérlők** mappát, és adja hozzá a következő `using` utasítást a fájl elejéhez:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Ezután nyissa meg a **feltöltése** metódus; a metódus konvertálja és feltöltések képeket a blobtárolóba. Adja hozzá a következő kódot közvetlenül a után kezdődő kódblokkot `// Generate a thumbnail` (vagy a rendszerkép-blob-létrehozási folyamat végén található). Ez a kód szükséges a lemezképet tartalmazó blob (`photo`), és használja a Computer Vision létrehozni a lemezkép leírását. A Computer Vision API is alkalmazását a lemezképre kulcsszavak álló listát hoz létre. A létrehozott leírást és a kulcsszavak vannak tárolva a blob metaadatai, hogy ezek később lekérhető.

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

1. Folytassa a **Index** metódust ugyanebbe a fájlba; ez a módszer a célként megadott blob-tárolóban tárolt rendszerkép blobok enumerálása (mint **IListBlobItem** példányok), és átadja őket az alkalmazás megtekintése. Cserélje le a `foreach` letiltása ennél a módszernél a következő kóddal. Ez a kód meghívja **CloudBlockBlob.FetchAttributes** minden egyes blob megszerezni a csatolt metaadatokat. Kibontja a számítógép által generált leírása (`caption`) a metaadatok, és hozzáadja azt, hogy a **BlobInfo** objektum, amely a nézet át.
    
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

Mentse a módosításokat a Visual Studio, és nyomja le az **Ctrl + F5** , indítsa el az alkalmazást a böngészőben. Az alkalmazás használatával tölthet fel néhány képeket, vagy a labor-erőforrásokat a "photos" mappájából, vagy a saját mappájában. Fölé a kurzort a nézetben a rendszerképeket, amikor a elemleírás ablak kell jelenik meg, és a számítógép által generált felirat a kép megjelenítéséhez.

![A számítógép által generált felirat](../Images/thumbnail-with-tooltip.png)

Az összes csatolt metaadatok megtekintéséhez használja az Azure Storage Explorer a storage-tárolót használ a lemezképek zobrazíte. Kattintson a jobb gombbal a blobokat a tárolóba, és válassza ki valamelyik **tulajdonságok**. A párbeszédpanelen látni fogja a kulcs-érték párok listáját. A számítógép által generált Képleírás tárolja a "Caption" elemet, és a keresési kulcsszavak "Tag0", "Tag1," tárolja, és így tovább. Ha elkészült, kattintson a **Mégse** a párbeszédpanel bezárásához.

![Blob metaadatai](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné a webes alkalmazás folyamatosan dolgozik, tekintse meg a [további lépések](#next-steps) szakaszban. Ha nem szeretné használni ezt az alkalmazást, minden alkalmazásspecifikus erőforrás törölni kell. Ehhez egyszerűen törölheti az erőforráscsoportot, amely tartalmazza az Azure Storage-előfizetés és a Computer Vision erőforrás. Ezzel eltávolítja a tárfiókot, a feltöltött blobok és az App Service-erőforrást az ASP.NET-webalkalmazás a kapcsolódáshoz szükséges. 

Törölje az erőforráscsoportot, nyissa meg a **erőforráscsoportok** panel a portálon keresse meg az ehhez a projekthez használt erőforráscsoportot, és kattintson a **erőforráscsoport törlése** a nézet tetején. Meg kell adnia az erőforráscsoport nevét, ellenőrizze, hogy szeretne törölni, mert a törölt, az erőforráscsoport nem állítható helyre.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Computer Vision service automatikusan létrehozni a feliratokat és a kulcsszavak blob rendszerképeket, mivel azok már feltöltött egy meglévő webes alkalmazásba integrált. Ezután tekintse meg az Azure Storage Lab, 6. gyakorlatban, keresési funkciók hozzáadása a webalkalmazáshoz. Ez a keresési kulcsszavakat a Computer Vision service által létrehozott kihasználja.

> [!div class="nextstepaction"]
> [Keresés hozzáadása az alkalmazáshoz](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
