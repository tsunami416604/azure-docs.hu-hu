---
title: Bing entity Search Java ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 8c987aa14e922573d01aa35fab609edf01e109b4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136773"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Java-hoz a Bing Entity Search ügyfélkódtárban. Bár a Bing Entity Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingEntitySearch)

## <a name="prerequisites"></a>Előfeltételek

* A [Java Fejlesztői Készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)

* A Bing Entity Search ügyfélkönyvtár java-hoz

Telepítse a Bing Entity Search ügyfélkönyvtár-függőségeket a Maven, a Gradle vagy más függőségkezelő rendszer használatával. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
<dependency>
  <groupId>com.microsoft.azure.cognitiveservices</groupId>
  <artifactId>azure-cognitiveservices-entitysearch</artifactId>
  <version>1.0.2</version>
</dependency>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


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

1. Valósítsa meg az `dominantEntityLookup` ügyfelet, amely az `ServiceClientCredentials` API-végpontot és az osztály egy példányát igényli. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```java
    public static EntitySearchAPIImpl getClient(final String subscriptionKey) {
        return new EntitySearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    A végrehajtásához hajtsa végre az `ServiceClientCredentials`alábbi lépéseket:

   1. felülírja `applyCredentialsFilter()` a függvényt, paraméterként egy `OkHttpClient.Builder` objektummal. 
        
       ```java
       //...
       new ServiceClientCredentials() {
               @Override
               public void applyCredentialsFilter(OkHttpClient.Builder builder) {
               //...
               }
       //...
       ```
    
   2. Belül `applyCredentialsFilter()`, `builder.addNetworkInterceptor()`hívja . Hozzon `Interceptor` létre egy új `intercept()` objektumot, `Chain` és felülbírálja a metódust, hogy elfogó objektumot vegyen.

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

   3. A `intercept` funkción belül hozzon létre változókat a kéréshez. A `Request.Builder()` kérelem összeállításához használható. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot `chain.proceed()` a fejléchez, és adja vissza a kérelemobjektumot.
            
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
## <a name="send-a-request-and-receive-a-response"></a>Kérés küldése és válasz fogadása

1. Hozzon létre egy új példányt a keresési ügyfél az előfizetési kulcs. keresési `client.entities().search()` kérelmet küldhet a keresési `satya nadella`lekérdezéshez, és választ kaphat. 
    
    ```java
    EntitySearchAPIImpl client = getClient(subscriptionKey);
    SearchResponseInner entityData = client.entities().search(
            "satya nadella", null, null, null, null, null, null, "en-us", null, null, SafeSearch.STRICT, null);
    ```

1. Ha entitásokat adott vissza, alakítsa át őket listává. Iterate rajtuk keresztül, és nyomtassa ki az erőfölényben lévő szervezet.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](../../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../../overview.md)
