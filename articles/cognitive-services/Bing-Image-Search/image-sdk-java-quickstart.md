---
title: 'Gyors útmutató: képek keresése – Bing Image Search Javához készült SDK'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Java-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cb25f6be358ae8aed18bf392ad933eab87332dcb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930616"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Rövid útmutató: Képek keresése a Javához készült Bing Image Search SDK használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Java-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart).

## <a name="prerequisites"></a>Előfeltételek

A [Java fejlesztői készlet (JDK)](https://aka.ms/azure-jdks) legfrissebb verziója

Telepítse a Bing Image Search SDK függőségeit a Maven, a Gradle vagy más függőségkezelési rendszer segítségével. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálásokat az osztály implementációhoz:

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

## <a name="send-a-search-request-to-the-api"></a>Keresési kérelem küldése az API-nak

1. A `bingImages().search()` használatával küldjön el egy HTTP-kérést a keresési lekérdezéssel. A választ mentse `ImagesModel` néven.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Az eredmények elemzése és megjelenítése

Elemezze a válaszban visszaadott képtalálatokat.
Ha a válasz keresési eredményeket tartalmaz, tárolja az első eredményt, és nyomtassa ki a részleteket, például egy miniatűr URL-címet, az eredeti URL-címet, valamint a visszaadott lemezképek teljes számát.  

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

```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még:

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Java-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referenciaanyag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
