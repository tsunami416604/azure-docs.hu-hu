---
title: Bing News Search Java ügyfélkönyvtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: 76a752bc108158c998d13a548da535d5fb1d5ee2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503873"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Java-hoz használt Bing News Search ügyfélkönyvtárban. Bár a Bing News Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)

## <a name="prerequisites"></a>Előfeltételek

Telepítse a Bing News Search ügyfélkönyvtár-függőségeket a Maven, a Gradle vagy más függőségkezelő rendszer használatával. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

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

## <a name="create-a-search-client-and-store-credentials"></a>Keresési ügyfél létrehozása és hitelesítő adatok tárolása

1. Hozzon létre `getClient()` egy metódust, amely új `NewsSearchAPIImpl` keresési ügyfelet ad vissza. Adja hozzá a végpontot az`NewsSearchAPIImpl` új objektum első `ServiceClientCredentials` paramétereként, és adjon hozzá egy új objektumot a hitelesítő adatok tárolásához. 

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Az `ServiceClientCredentials` objektum létrehozásához felülbírálja a `applyCredentialsFilter()` függvényt. Adja `OkHttpClient.Builder` át a metódust, és `addNetworkInterceptor()` a szerkesztő módszerével hozza létre a hitelesítő adatokat az ügyféltár-híváshoz.

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

1. Hozzon létre `getClient()` egy metódust, amely hívásokat, és elküldi a keresési kérelmet a Bing News Search szolgáltatás. Szűrje a keresést a *piac* és a *számlálási* paraméterekkel, majd nyomtassa ki az első hírek eredményére vonatkozó információkat: név, URL, közzétételdátum, leírás, szolgáltató neve és a keresés becsült találatainak teljes száma.

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

2. Adja hozzá a `main()` keresési módszert a kód végrehajtásához szükséges metódushoz.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-news-search-single-page-app.md)
