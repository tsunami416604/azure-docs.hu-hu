---
title: Bing Video Search Java-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.openlocfilehash: 8124afef1aa12dbf3ec51e10597cb1567fc85551
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80289769"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Java-hoz készült Bing Video Search ügyfélkódtárban. Bár a Bing Video Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon,](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch)további jegyzetekkel és funkciókkal.

## <a name="prerequisites"></a>Előfeltételek

* A [Java Fejlesztői Készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [Gson-kódtár](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

Telepítse a Bing Video Search ügyfélkönyvtár-függőségeket a Maven, a Gradle vagy más függőségkezelő rendszer használatával. A Maven POM-fájlhoz a következő deklarációra van szükség:

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

1. Valósítsa meg az `VideoSearchAPIImpl` ügyfelet, amely az `ServiceClientCredentials` API-végpontot és az osztály egy példányát igényli.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    A `ServiceClientCredentials`megvalósításához hajtsa végre az alábbi lépéseket:

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

## <a name="send-a-search-request-and-receive-the-response"></a>Keresési kérelem küldése és válasz fogadása 

1. Hozzon létre `VideoSearch()` egy nevű függvényt, amely az előfizetési kulcsot karakterláncként veszi fel. A korábban létrehozott keresési ügyfél létrehozása.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. Belül `VideoSearch()`, Videókeresési kérelem küldése az `SwiftKey` ügyfél használatával, a keresési kifejezéssel. Ha a Video Search API eredményt adott vissza, megkapja az első eredményt, és kinyomtatja az azonosítóját, nevét és URL-címét, valamint a visszaadott videók teljes számát. 
    
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

3. Hívja meg a keresési módszert a fő módszerből.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Video Search API?](../../overview.md)
* [Kognitív szolgáltatások .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
