---
title: 'Rövid útmutató: Képek keresése a Javához készült Bing Image Search SDK használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Java-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 36f59e1c405ef9e5cf69a19e49d69a3adfdc4636
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298183"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Rövid útmutató: Képek keresése a Java és a Bing Image Search SDK használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Java-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart).

## <a name="prerequisites"></a>Előfeltételek

A [Java fejlesztői készlet (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) legfrissebb verziója

Telepítse a Bing Image Search SDK függőségeit a Maven, a Gradle vagy más függőségkezelési rendszer segítségével. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat az osztályok implementációjába:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. A fő metódusban hozza létre az előfizetési kulcs és a keresőkifejezés változóit. Ezután példányosítsa a Bing Image Search-ügyfelet.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Keresési kérelem küldése a Bing Image Search API-nak

1. A `bingImages().search()` használatával küldjön el egy HTTP-kérést a keresési lekérdezéssel. A választ mentse `ImagesModel` néven.
    ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Az eredmények elemzése és megjelenítése

Elemezze a válaszban visszaadott képtalálatokat.
Ha a válasz tartalmaz keresési eredményeket, tárolja az első találatot, és jelenítse meg annak részleteit, például a miniatűr URL-címét és az eredeti URL-címet a visszaadott képek teljes számával együtt.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Java-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
