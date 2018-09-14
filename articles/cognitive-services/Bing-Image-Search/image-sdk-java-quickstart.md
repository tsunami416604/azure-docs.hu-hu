---
title: 'Gyors útmutató: Keresse meg a képeket, a Bing kép Search SDK és a Java használatával'
description: Ez a rövid útmutató segítségével keresse meg és -rendszerképek keresése a weben a Bing kép Search SDK és a Java használatával.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 12bd6f9a9a0b43b4571a7e0311ffbea54c7b9054
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574061"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-java"></a>Gyors útmutató: Keresse meg a Bing kép Search SDK és a Java-lemezképek

Ez a rövid útmutató segítségével győződjön meg arról, az első képkeresési Bing kép Search SDK használatával, amely egy burkoló a API-hoz, és ugyanazokat a szolgáltatásokat tartalmazza. Az egyszerű Java-alkalmazás-lemezkép keresési lekérdezést küld, elemzi a JSON-válasz és URL-címét adja vissza az első képet jeleníti meg.

Az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) további hibakezelést és jegyzetek. 

## <a name="prerequisites"></a>Előfeltételek 

A legújabb verzióját a [Java Development Kitet](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)

Telepítse a Bing kép Search SDK-függőség Maven, Gradle, vagy egy másik függőségkezelési rendszer. A Maven POM-fájljába igényel az alábbi nyilatkozatot:

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

## <a name="create-and-initialize-the-application"></a>Hozzon létre, és az alkalmazás inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és az osztály implimentation ad hozzá az alábbi importálásokat:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. A fő az előfizetési kulcs változók létrehozása és a keresési kifejezést. Ezután hozza létre a Bing Képkeresés ügyfél.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-bing-image-search-api"></a>Egy keresési kérelmet küld a Bing Image Search API

1. Használatával `bingImages().search()`, a keresési lekérdezést tartalmazó HTTP-kérelem küldése. A válasz való mentése egy `ImagesModel`.
    ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Elemezheti és az eredmény megtekintéséhez

A kép eredményeket, a válasz elemzése.
A válasz tartalmazza a keresési eredmények, ha az első eredmény tárolja, és nyomtassa ki a részleteket, például a Miniatűr URL-cím, az eredeti URL-CÍMÉT, teljes számával együtt adja vissza a lemezképeket.  

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
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) 
* [Az Azure Cognitive Services SDK for Java-példák](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
