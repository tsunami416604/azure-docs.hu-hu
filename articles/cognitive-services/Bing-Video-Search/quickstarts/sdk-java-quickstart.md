---
title: 'Gyors útmutató: videók keresése a Javához készült SDK-Bing Video Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videó-keresési kéréseket küldhet a Javához készült Bing Video Search SDK-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: cbe5f0023b16aec1ed68e7a78ddb3103f40eb85b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378594"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Gyors útmutató: videós keresés végrehajtása a Java Bing Video Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a Java-hoz készült Bing Video Search SDK-val kapcsolatos hírek keresését. Habár a Bing Video Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), és további megjegyzéseket és funkciókat is tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [Gson-kódtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Telepítse a Bing Video Search SDK függőségeit a Maven, a Gradle vagy más függőségkezelési rendszer segítségével. A Maven POM-fájlhoz a következő deklarációra van szükség:

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

1. Implementálja az `VideoSearchAPIImpl`-ügyfelet, amely az API-végpontot és a `ServiceClientCredentials` osztály egy példányát igényli.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    `ServiceClientCredentials`megvalósításához kövesse az alábbi lépéseket:

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

## <a name="send-a-search-request-and-receive-the-response"></a>Keresési kérelem küldése és a válasz fogadása 

1. Hozzon létre egy `VideoSearch()` nevű függvényt, amely karakterláncként veszi fel az előfizetési kulcsot. A korábban létrehozott keresési ügyfél példányának létrehozása.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. `VideoSearch()`on belül küldjön egy videó-keresési kérést az ügyfél használatával, a keresési kifejezéssel `SwiftKey`. Ha a Video Search API eredményt adott eredményül, szerezze be az első eredményt, és nyomtassa ki az azonosítóját, nevét és URL-címét, valamint a visszaadott videók teljes számát. 
    
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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas Webalkalmazás létrehozása](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi a Bing Video Search API?](../overview.md)
* [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)