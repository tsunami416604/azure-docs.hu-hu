---
title: 'Gyors útmutató: Hajtsa végre a hírkeresési – a Bing News Search SDK a Javához készült'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével hírkeresés a Bing News Search SDK használata Javához készült, és a válasz feldolgozása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 49356d79ce9f8c4efdd27dc946f7373c3efe59cd
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264563"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Gyors útmutató: A Javához készült Bing News Search SDK hírkeresés

Ez a rövid útmutató segítségével megkezdheti a Bing News Search SDK a Javához készült fésülhet keresése. Míg a Bing News Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Előfeltételek

Telepítse a Bing News Search SDK függőségeket a Maven, gradle-t vagy egy másik függőségkezelési rendszer. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Keresés ügyfél és a tároló hitelesítő adatainak létrehozása

1. Hozzon létre egy meghívott metódus `getClient()` , amely adja vissza egy új `NewsSearchAPIImpl` keresési ügyfél. A végpont hozzáadása az első paraméterként az új`NewsSearchAPIImpl` objektum és a egy új `ServiceClientCredentials` objektum tárolja a hitelesítő adatait.

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Hozhat létre a `ServiceClientCredentials` objektumazonosító, bírálja felül a `applyCredentialsFilter()` függvény. Adja át a `OkHttpClient.Builder` a metódust, és használja a jelentéskészítő `addNetworkInterceptor()` metódussal hoz létre a hitelesítő adatokat az SDK-hívás.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Küldeni és fogadni egy keresési kérelmet

1. Hozzon létre egy módszer meghívásához `getClient()` és a egy keresési kérelmet küld a Bing News Search szolgáltatás. Szűrheti a keresést, az a *piaci* és *száma* paramétereket, majd nyomtassa ki az első hírek eredmény információt: név, URL-cím, közzététel dátuma, leírás, szolgáltató nevét és teljes száma a becsült a keresési találat.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Adja hozzá a keresési metódust egy `main()` metódust, amely végrehajtja a kódot.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
[Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
