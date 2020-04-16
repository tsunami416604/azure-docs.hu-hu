---
title: 'Oktatóanyag: Metaadatok létrehozása Azure-lemezképekhez'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Azure Computer Vision szolgáltatást egy webalkalmazásba a képek metaadatainak létrehozásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 43172cb08bb1e31c8cff891628ca6ef85cb8c864
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404415"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Oktatóanyag: A Computer Vision használata lemezkép-metaadatok létrehozásához az Azure Storage-ban

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Azure Computer Vision szolgáltatást egy webalkalmazásba a feltöltött képek metaadatainak létrehozásához. Ez digitális [eszközkezelési (DAM)](../Home.md#computer-vision-for-digital-asset-management) forgatókönyvek esetén hasznos, például ha egy vállalat gyorsan szeretne leíró feliratokat vagy kereshető kulcsszavakat létrehozni az összes képéhez.

A teljes alkalmazás-útmutató megtalálható az [Azure Storage és a Cognitive Services Labor](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) a GitHubon, és ez az oktatóanyag lényegében a labor 5. Érdemes lehet létrehozni a teljes alkalmazás a következő minden lépést, de ha csak azt szeretné, hogy megtanulják, hogyan kell integrálni Computer Vision egy meglévő webes alkalmazás, olvassa el itt.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Számítógép-vision-erőforrás létrehozása az Azure-ban
> * Lemezképelemzés végrehajtása Azure Storage-lemezképeken
> * Metaadatok csatolása Azure Storage-lemezképekhez
> * A kép metaadatainak ellenőrzése az Azure Storage Explorer használatával

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017 community edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) vagy újabb, a "ASP.NET és a webfejlesztés" és az "Azure-fejlesztés" számítási feladatok telepítve.
- Egy Azure Storage-fiók egy blob tároló rendszerkép-tároláshoz beállított (kövesse [az Azure Storage Lab 1. gyakorlatát,](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) ha segítségre van szüksége ezzel a lépéssel kapcsolatban).
- Az Azure Storage Explorer eszköz (kövesse [az Azure Storage Lab 2. gyakorlatát,](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) ha segítségre van szüksége ezzel a lépéssel kapcsolatban).
- Egy ASP.NET webalkalmazás az Azure Storage-hoz való hozzáféréssel (kövesse [az Azure Storage Lab 3. gyakorlatát egy](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) ilyen alkalmazás gyors létrehozásához).

## <a name="create-a-computer-vision-resource"></a>Számítógép-látomás erőforrás létrehozása

Létre kell hoznia egy Computer Vision erőforrást az Azure-fiókjához; ez az erőforrás kezeli az Azure Computer Vision szolgáltatásához való hozzáférést. 

1. Kövesse az [Azure Cognitive Services-erőforrás létrehozása](../../cognitive-services-apis-create-account.md) című útmutató utasításait a Computer Vision erőforrás létrehozásához.

1. Ezután nyissa meg az erőforráscsoport menüjét, és kattintson az imént létrehozott Computer Vision API-előfizetésre. Másolja az URL-címet az **Endpoint** alá olyan helyre, ahová egy pillanat alatt könnyen lekérheti. Ezután kattintson **a Hozzáférési kulcsok megjelenítése gombra.**

    ![Az Azure Portal lap a végpont URL-címét és a hozzáférési kulcsok hivatkozását vázolt](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. A következő ablakban másolja a **KEY 1** értékét a vágólapra.

    ![Billentyűk kezelése párbeszédpanel a körvonalazott másolásgombbal](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Computer Vision hitelesítő adatok hozzáadása

Ezután adja hozzá a szükséges hitelesítő adatokat az alkalmazáshoz, hogy hozzáférhessen a Computer Vision erőforrásaihoz

Nyissa meg a ASP.NET webalkalmazást a Visual Studióban, és keresse meg a **web.config** fájlt a projekt gyökerében. Adja hozzá a következő `<appSettings>` állításokat a `VISION_KEY` fájl szakaszához, helyettesítve az előző `VISION_ENDPOINT` lépésben másolt kulccsal, valamint az előző lépésben mentett URL-címmel.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Ezután a Megoldáskezelőben kattintson a jobb gombbal a projektre, és a **NuGet csomagok kezelése** paranccsal telepítse a **Microsoft.Azure.CognitiveServices.Vision.Computer.** csomagot. Ez a csomag tartalmazza a Computer Vision API hívásához szükséges típusokat.

## <a name="add-metadata-generation-code"></a>Metaadat-generálási kód hozzáadása

Ezután adja hozzá azt a kódot, amely ténylegesen kihasználja a Computer Vision szolgáltatást a képek metaadatainak létrehozásához. Ezek a lépések a ASP.NET alkalmazásra vonatkoznak a laborban, de a saját alkalmazásához igazíthatja őket. A fontos, hogy ezen a ponton van egy ASP.NET webalkalmazás, amely képeket tölthet fel egy Azure Storage-tárolóba, képeket olvashat belőle, és megjelenítheti őket a nézetben. Ha nem biztos ebben a lépésben, a legjobb, ha az [Azure Storage Lab 3.](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) 

1. Nyissa meg a *HomeController.cs* fájlt a projekt **Vezérlők** `using` mappájában, és adja hozzá a következő állításokat a fájl tetejére:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Ezután folytassa a **Feltöltési** módszert; ez a módszer konvertálja és feltölti a képeket blob storage.this method converts and uploads images to blob storage. Adja hozzá a következő kódot közvetlenül `// Generate a thumbnail` a blokk után, amely kezdődik (vagy a kép-blob-létrehozási folyamat végén). Ez a kód a lemezképet`photo`( ) tartalmazó blobot veszi fel, és a Computer Vision segítségével leírást készít a lemezképről. A Computer Vision API a képre vonatkozó kulcsszavak listáját is létrehozza. A létrehozott leírás és a kulcsszavak a blob metaadatai között tárolódnak, így később leolvashatók lesznek.

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

1. Ezután nyissa meg az **Index** metódust ugyanabban a fájlban. Ez a módszer számba veszi a tárolt lemezkép-blobokat a megcélzott blobtárolóban **(IListBlobItem-példányként),** és átadja azokat az alkalmazásnézetnek. Cserélje `foreach` le a blokkot ebben a módszerben a következő kódra. Ez a kód meghívja **a CloudBlockBlob.FetchAttributes metódust,** hogy megkapja az egyes blobok csatolt metaadatait. Kinyeri a számítógép által létrehozott`caption`leírást ( ) a metaadatokból, és hozzáadja a **BlobInfo** objektumhoz, amely átkerül a nézetbe.
    
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

Mentse a módosításokat a Visual Studióban, és nyomja le a **Ctrl+F5 billentyűkombinációt** az alkalmazás böngészőben való elindításához. Az alkalmazás segítségével feltölthet néhány képet, akár a labor erőforrásainak "fényképek" mappájából, akár a saját mappájából. Amikor a mutatót a nézet egyik képe fölé viszi, elemleírásablak jelenik meg, és megjeleníti a kép számítógép által létrehozott feliratát.

![A számítógép által generált felirat](../Images/thumbnail-with-tooltip.png)

Az összes csatolt metaadat megtekintéséhez az Azure Storage Explorer segítségével tekintse meg a rendszerképekhez használt tárolótárolót. Kattintson a jobb gombbal a tárolóban lévő blobok bármelyikére, és válassza a **Tulajdonságok parancsot.** A párbeszédablakban megjelenik a kulcsérték-párok listája. A számítógép által létrehozott képleírás a "Felirat" elemben, a keresési kulcsszavak pedig a "Címke0", "Tag1" és így tovább tárolódnak. Ha végzett, a párbeszédpanel bezárásához kattintson a **Mégse** gombra.

![A Kép tulajdonságai párbeszédpanel ablak, a felsorolt metaadat-címkékkel](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbbra is szeretne dolgozni a webalkalmazáson, olvassa el a Következő lépések című [szakaszt.](#next-steps) Ha nem tervezi, hogy továbbra is használja ezt az alkalmazást, törölje az összes alkalmazás-specifikus erőforrásokat. Erőforrások törléséhez törölheti az Azure Storage-előfizetést és a Computer Vision erőforrást tartalmazó erőforráscsoportot. Ez eltávolítja a tárfiókot, a blobok feltöltött, és az App Service-erőforrás szükséges a ASP.NET webalkalmazáshoz való csatlakozáshoz. 

Az erőforráscsoport törléséhez nyissa meg az **Erőforráscsoportok** lapot a portálon, keresse meg a projekthez használt erőforráscsoportot, és kattintson a nézet tetején az **Erőforráscsoport törlése** gombra. A rendszer megkéri, hogy írja be az erőforráscsoport nevét, hogy erősítse meg, hogy törölni szeretné, mert a törlés után az erőforráscsoport nem lehet visszasegíteni.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Computer Vision szolgáltatását egy meglévő webalkalmazásban állíthatja be, hogy automatikusan generálja a blobképek feliratait és kulcsszavait a feltöltéssorán. Ezután tekintse meg az Azure Storage Lab, 6. Ez kihasználja a Computer Vision szolgáltatás által létrehozott keresési kulcsszavakat.

> [!div class="nextstepaction"]
> [Keresés hozzáadása az alkalmazáshoz](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
