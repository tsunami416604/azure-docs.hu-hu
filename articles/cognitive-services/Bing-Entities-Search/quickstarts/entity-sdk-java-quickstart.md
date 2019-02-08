---
title: 'Gyors útmutató: Keresés a Bing Entity Search SDK a Javához készült rendelkező entitások esetében'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg a Bing Entity Search SDK a Javához készült rendelkező entitások
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 1534b2a85067867800c147c86ee7d82374de8794
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866195"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Gyors útmutató: A Bing Entity Search SDK Java-keresési kérelem küldése

Ez a rövid útmutató segítségével kezdeni a keresést, a Bing Entity Search SDK a Javához készült rendelkező entitások esetében. Míg a Bing Entity Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch).

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztési Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* A Bing Entity Search SDK-Java

Telepítse a Bing Entity Search SDK függőségeit a Maven, a Gradle vagy más függőségkezelési rendszer segítségével. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

    ```java
    import com.microsoft.azure.cognitiveservices.entitysearch.*;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.EntitySearchAPIImpl;
    import com.microsoft.azure.cognitiveservices.entitysearch.implementation.SearchResponseInner;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List;
    ```

2. Hozzon létre egy változót az előfizetési kulcs

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Hozzon létre egy ügyfél-keresés

2. Alkalmazzon a `dominantEntityLookup` -ügyfelet, amely az API-végpont, és a egy példányát igényli a `ServiceClientCredentials` osztály.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Megvalósítása a `ServiceClientCredentials`, kövesse az alábbi lépéseket:

    1. bírálja felül a `applyCredentialsFilter()` funkciót, az egy `OkHttpClient.Builder` paraméterként objektum. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. Belül `applyCredentialsFilter()`, hívja `builder.addNetworkInterceptor()`. Hozzon létre egy új `Interceptor` objektumot, és felülbírálják a `intercept()` metódus tennie egy `Chain` elfogó objektum.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. Belül a `intercept` működik, a kérelem változók létrehozása. Használat `Request.Builder()` hozhat létre a kérést. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejlécére, majd a visszaadandó `chain.proceed()` a kérelem objektum.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```
## <a name="send-a-request-and-receive-a-response"></a>A kéréseket küldeni és fogadni a választ

1. Hozzon létre egy új példányát a keresési ügyfél az előfizetési kulcs. használjon `client.entities().search()` egy keresési kérelmet, a keresési lekérdezés küldése `satya nadella`, és választ kaphat. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

2. Minden olyan entitást adott vissza, ha alakíthatja őket listáját. Végigvenni ezeket, és nyomtassa ki a domináns entitás.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entrys = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entrys) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }

## Next steps

> [!div class="nextstepaction"]
> [Build a single-page web app](../tutorial-bing-entities-search-single-page-app.md)

* [What is the Bing Entity Search API?](../overview.md )