---
title: 'Gyors útmutató: A Bing Video Search SDK használata Javához készült videók keresése'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével a Bing Video Search SDK használata Javához készült videókeresési kérelmek küldésére.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ff214a545023364b09a0e757462a1b3f3d963d44
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885021"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Gyors útmutató: Hajtsa végre a videókeresési Bing Video Search SDK-val Java

Ez a rövid útmutató segítségével kezdeni a keresést hírkeresés a Bing Video Search SDK a Javához készült együtt. Míg a Bing Video Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ehhez a mintához a forráskód találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), amely további jegyzetek és a Bing Videókeresési funkciókat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztési Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

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

## <a name="create-and-initalize-a-project"></a>Hozzon létre és a egy projektet sikerült


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

## <a name="create-a-search-client"></a>Hozzon létre egy ügyfél-keresés

2. Alkalmazzon a `VideoSearchAPIImpl` -ügyfelet, amely az API-végpont, és a egy példányát igényli a `ServiceClientCredentials` osztály.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Megvalósításához `ServiceClientCredentials`, kövesse az alábbi lépéseket:

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

## <a name="send-a-search-request-and-recieve-the-response"></a>A keresési kéréseket küldeni és fogadni a válasz 

1. Hozzon létre egy függvényt, nevű `VideoSearch()` , amely karakterláncként az előfizetési kulcs szükséges. Hozza létre a korábban létrehozott keresési ügyfél.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. Belül `VideoSearch()`, az ügyfél használata a videókeresési kérés küldése `SwiftKey` , a keresési kifejezés. A Video Search API egy eredményt adott vissza, ha az első eredményének beolvasása, és nyomtassa ki az azonosítóját, nevét és URL-címet adja vissza a videók teljes számával együtt. 
    
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

3. A keresési metódus hívása a fő módszert.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy egyoldalas webalkalmazást](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Videókeresési API?](../overview.md)
* [Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)