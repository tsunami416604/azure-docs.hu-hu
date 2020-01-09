---
title: 'Rövid útmutató: Hírek keresése a Javához készült SDK használatával – Bing News Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval híreket kereshet a Javához készült Bing News Search SDK-val, és feldolgozhatja a választ.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b8c2ca3bf919d70123b7b5b6d13c0301e0c1fd13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75383578"
---
# <a name="quickstart-search-for-news-with-the-bing-news-search-sdk-for-java"></a>Gyors útmutató: Hírek keresése a Javához készült Bing News Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a Java-hoz készült Bing News Search SDK-val kapcsolatos hírek keresését. Habár a Bing News Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)található.

## <a name="prerequisites"></a>Előfeltételek

Telepítse a Bing News Search SDK-függőségeket a Maven, a Gradle vagy más függőségi felügyeleti rendszeren. A Maven POM-fájlhoz a következő deklarációra van szükség:

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

## <a name="create-a-search-client-and-store-credentials"></a>Keresési ügyfél létrehozása és a hitelesítő adatok tárolása

1. Hozzon létre egy `getClient()` nevű metódust, amely új `NewsSearchAPIImpl` keresési ügyfelet ad vissza. Adja hozzá a végpontot az új`NewsSearchAPIImpl` objektum első paramétereként, valamint egy új `ServiceClientCredentials` objektumot a hitelesítő adatok tárolásához. 

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. A `ServiceClientCredentials` objektum létrehozásához bírálja felül a `applyCredentialsFilter()` függvényt. Adjon át egy `OkHttpClient.Builder` a metódusnak, és a Builder `addNetworkInterceptor()` metódusával hozza létre az SDK-híváshoz tartozó hitelesítő adatait.

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

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása

1. Hozzon létre egy metódust, amely meghívja a `getClient()`t, és keresési kérelmet küld a Bing News Search szolgáltatásnak. Szűrje a keresést a *piaci* és a *Count* paraméterrel, majd nyomtassa ki az első Hírek eredményét: név, URL, közzététel dátuma, leírás, szolgáltató neve, valamint a keresés becsült egyezésének teljes száma.

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

2. Adja hozzá a keresési metódust egy `main()` metódushoz a kód végrehajtásához.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
