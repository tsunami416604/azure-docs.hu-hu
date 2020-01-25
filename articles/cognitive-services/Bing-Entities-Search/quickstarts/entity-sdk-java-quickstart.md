---
title: 'Gyors útmutató: entitások keresése a Javához készült SDK-Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkeresheti az entitásokat a Javához készült Bing Entity Search SDK-val
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: aahi
ms.openlocfilehash: 49e3492bae1dfe248e07227caff11a6d00e59c08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716474"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Gyors útmutató: keresési kérelem küldése a Javához készült Bing Entity Search SDK-val

Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Javához készült Bing Entity Search SDK-val. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch)található.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* A Javához készült Bing Entity Search SDK

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

2. Változó létrehozása az előfizetési kulcshoz

    ```java
    String subscriptionKey = "your-key-here"
    ```

## <a name="create-a-search-client"></a>Keresési ügyfél létrehozása

1. Implementálja az `dominantEntityLookup`-ügyfelet, amely az API-végpontot és a `ServiceClientCredentials` osztály egy példányát igényli. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    A `ServiceClientCredentials`megvalósításához kövesse az alábbi lépéseket:

   1. felülbírálja a `applyCredentialsFilter()` függvényt egy `OkHttpClient.Builder` objektum paraméterként. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. A `applyCredentialsFilter()`on belül hívja meg `builder.addNetworkInterceptor()`. Hozzon létre egy új `Interceptor` objektumot, és bírálja felül a `intercept()` metódust egy `Chain` Interceptor objektum elvégzéséhez.

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

   3. A `intercept` függvényben hozzon létre változókat a kérelemhez. A kérés létrehozásához használja a `Request.Builder()`. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és adja vissza `chain.proceed()` a kérelem objektumon.
            
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
      ## <a name="send-a-request-and-receive-a-response"></a>Kérelem küldése és válasz fogadása

1. Hozzon létre egy új példányt a keresési ügyfélhez az előfizetési kulccsal. a `client.entities().search()` használatával küldhet keresési kéréseket a keresési lekérdezéshez `satya nadella`és választ kaphat. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Ha bármelyik entitást visszaadott, alakítsa át őket egy listára. Ismételje meg őket, és nyomtassa ki a domináns entitást.

    ```java
    if (entityData.entities().value().size() > 0){
        // Find the entity that represents the dominant entity
        List<Thing> entries = entityData.entities().value();
        Thing dominateEntry = null;
        for(Thing thing : entries) {
            if(thing.entityPresentationInfo().entityScenario() == EntityScenario.DOMINANT_ENTITY) {
                System.out.println("\r\nSearched for \"Satya Nadella\" and found a dominant entity with this description:");
                System.out.println(thing.description());
                break;
            }
        }
    }
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )
