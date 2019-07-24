---
title: 'Gyors útmutató: Entitások keresése a Javához készült Bing Entity Search SDK-val'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkeresheti az entitásokat a Javához készült Bing Entity Search SDK-val
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: f0dad214ceaf0606699eb8eca7cfb1c325a38eab
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404933"
---
# <a name="quickstart-send-a-search-request-with-the-bing-entity-search-sdk-for-java"></a>Gyors útmutató: Keresési kérelem küldése a Javához készült Bing Entity Search SDK-val

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

1. Implementálja `dominantEntityLookup` az-ügyfelet, amely az API-végpontot és az `ServiceClientCredentials` osztály egy példányát igényli.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    A megvalósításához `ServiceClientCredentials`kövesse az alábbi lépéseket:

   1. felülbírálja `applyCredentialsFilter()` a függvényt paraméterként egy `OkHttpClient.Builder` objektummal. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. A `applyCredentialsFilter()`-n `builder.addNetworkInterceptor()`belül hívja meg a t. Hozzon létre `Interceptor` egy új objektumot, és `intercept()` bírálja felül a `Chain` metódusát egy Interceptor objektum elvégzéséhez.

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

   3. A `intercept` függvényen belül hozzon létre változókat a kérelemhez. A `Request.Builder()` paranccsal felépítheti a kérést. Adja hozzá az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez, `chain.proceed()` és térjen vissza a kérelem objektumra.
            
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

1. Hozzon létre egy új példányt a keresési ügyfélhez az előfizetési kulccsal. a `client.entities().search()` használatával keresési kérelmet küldhet a keresési lekérdezéshez `satya nadella`, és választ kaphat. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Ha bármelyik entitást visszaadott, alakítsa át őket egy listára. Ismételje meg őket, és nyomtassa ki a domináns entitást.

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
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )
