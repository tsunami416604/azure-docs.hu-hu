---
title: Bing Video Search Java ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 2b3d4993406f150b2983d4d820f7d070b5de1e96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375604"
---
Ezzel a rövid útmutatóval megkezdheti a Java-hoz készült Bing Video Search ügyféloldali kódtáraval kapcsolatos hírek keresését. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), és további megjegyzéseket és funkciókat is tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [Gson-kódtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

Telepítse az Bing Video Search ügyféloldali függvénytár-függőségeit Maven, Gradle vagy más függőségi felügyeleti rendszer használatával. A Maven POM-fájlhoz a következő deklarációra van szükség:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása


Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>Keresési ügyfél létrehozása

1. Implementálja az- `VideoSearchAPIImpl` ügyfelet, amely az API-végpontot és az osztály egy példányát igényli `ServiceClientCredentials` .

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    A megvalósításához `ServiceClientCredentials` kövesse az alábbi lépéseket:

    1. felülbírálja a `applyCredentialsFilter()` függvényt `OkHttpClient.Builder` paraméterként egy objektummal. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. `applyCredentialsFilter()`A-n belül hívja meg a t `builder.addNetworkInterceptor()` . Hozzon létre egy új `Interceptor` objektumot, és bírálja felül a `intercept()` metódusát egy Interceptor objektum elvégzéséhez `Chain` .

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

    3. A `intercept` függvényen belül hozzon létre változókat a kérelemhez. `Request.Builder()`A paranccsal felépítheti a kérést. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és térjen vissza a `chain.proceed()` kérelem objektumra.
            
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

## <a name="send-a-search-request-and-receive-the-response"></a>Keresési kérelem küldése és a válasz fogadása 

1. Hozzon létre egy nevű függvényt `VideoSearch()` , amely karakterláncként veszi fel az előfizetési kulcsot. A korábban létrehozott keresési ügyfél példányának létrehozása.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. A `VideoSearch()` alkalmazásban a keresési kifejezéssel küldje el a videó keresési kérelmét az ügyféllel `SwiftKey` . Ha a Video Search API eredményt adott eredményül, szerezze be az első eredményt, és nyomtassa ki az azonosítóját, nevét és URL-címét, valamint a visszaadott videók teljes számát. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Hívja meg a keresési módszert a Main metódusból.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Video Search API?](../../overview.md)
* [A kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
