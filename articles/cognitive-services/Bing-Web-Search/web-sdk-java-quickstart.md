---
title: 'Rövid útmutató: A Bing Web Search SDK használata Javához'
description: A telepítő a Web Search SDK konzolalkalmazást.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: 1cad3c6ea3e670d214c05e9e61acd566c85c2eb7
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888491"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-java"></a>Rövid útmutató: A Bing Web Search SDK használata Javához

A Bing Web Search SDK tartalmazza a REST API, webes lekérdezések és az elemzési eredmények funkcióit.

A [forráskódját Java a Bing Web Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) elérhető a Githubon.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Bing Web Search SDK függőségeket a Maven, gradle-t vagy a kedvenc függőségkezelési rendszer. A Maven POM-fájljába van szükség:
```
  <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-websearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies>
```

## <a name="web-search-client"></a>Webes keresés ügyfél

Az osztály implementációját importálások hozzáadása:
```
import com.microsoft.azure.cognitiveservices.websearch.*;
import com.microsoft.azure.cognitiveservices.websearch.implementation.SearchResponseInner;
import com.microsoft.azure.cognitiveservices.websearch.implementation.WebSearchAPIImpl;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
```
Alkalmazzon a `WebSearchAPIImpl` -ügyfelet, amely egy példányát igényli a `ServiceClientCredentials`:
```
public static WebSearchAPIImpl getClient(final String subscriptionKey) {
    return new WebSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
            new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                    builder.addNetworkInterceptor(
                            new Interceptor() {
                                @Override
                                public Response intercept(Chain chain) throws IOException {
                                    Request request = null;
                                    Request original = chain.request();
                                    // Request customization: add request headers
                                    Request.Builder requestBuilder = original.newBuilder()
                                            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                                    request = requestBuilder.build();
                                    return chain.proceed(request);
                                }
                            });
                }
            });
}

```
Keresse meg az eredményeket.  A következő metódust keresések használatával egyetlen lekérdezéssel, "Xbox", és kinyomtatja `name`, és `URL` első webes, kép, híreket és videókat eredményeket.
```
public static void WebSearchResultTypesLookup(String subscriptionKey)
{
    WebSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        SearchResponseInner webData = client.webs().search("Yosemite");
        System.out.println("\r\nSearched for Query# \" Yosemite \"");

        //WebPages
        if (webData.webPages().value().size() > 0)
        {
            // find the first web page
            WebPage firstWebPagesResult = webData.webPages().value().get(0);

            if (firstWebPagesResult != null)
            {
                System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
                System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
                System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
            }
            else
            {
                System.out.println("Couldn't find web results!");
            }
        }
        else
        {
            System.out.println("Didn't see any Web data..");
        }

        //Images
        if (webData.images().value().size() > 0)
        {
            // find the first image result
            ImageObject firstImageResult = webData.images().value().get(0);

            if (firstImageResult != null)
            {
                System.out.println(String.format("Image Results#%d", webData.images().value().size()));
                System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
                System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
            }
            else
            {
                System.out.println("Couldn't find first image results!");
            }
        }
        else
        {
            System.out.println("Didn't see any image data..");
        }

        //News
        if (webData.news().value().size() > 0)
        {
            // find the first news result
            NewsArticle firstNewsResult = webData.news().value().get(0);

            if (firstNewsResult != null)
            {
                System.out.println(String.format("News Results#%d", webData.news().value().size()));
                System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
                System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
            }
            else
            {
                System.out.println("Couldn't find any News results!");
            }
        }
        else
        {
            System.out.println("Didn't see first news data..");
        }

        //Videos
        if (webData.videos() != null && webData.videos().value().size() > 0)
        {
            // find the first video result
            VideoObject firstVideoResult = webData.videos().value().get(0);

            if (firstVideoResult != null)
            {
                System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
                System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
                System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
            }
            else
            {
                System.out.println("Couldn't find first video results!");
            }
        }
        else
        {
            System.out.println("Didn't see any video data..");
        }
    }

    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }

}

```
Keresse meg a "Budapesti ajánlott éttermek", ellenőrizze a eredmények számát, és nyomtassa ki az `name` és `URL` az első eredmény:
```
public static void WebResultsWithCountAndOffset(String subscriptionKey)
{
    WebSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        SearchResponseInner webData = client.webs().search(
                "Best restaurants in Seattle", null, null, null, null, null, 10, null, 20, null, "en-us", null,
                        null, null, SafeSearch.STRICT, null, null, null);
        System.out.println("\r\nSearched for Query# \" Best restaurants in Seattle \"");

        if (webData.webPages().value().size() > 0)
        {
            // find the first web page
            WebPage firstWebPagesResult = webData.webPages().value().get(0);

            if (firstWebPagesResult != null)
            {
                System.out.println(String.format("Web Results#%d", webData.webPages().value().size()));
                System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
                System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
            }
            else
            {
                System.out.println("Couldn't find first web result!");
            }
        }
        else
        {
            System.out.println("Didn't see any Web data..");
        }
    }
    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Keressen a "Microsoft" válasz szűrőkkel rendelt `news`. Első hír nyomtatási részletei.
```
public static void WebSearchWithResponseFilter(String subscriptionKey)
{
    WebSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        List<AnswerType> responseFilterstrings = new ArrayList<AnswerType>();
        responseFilterstrings.add(AnswerType.NEWS);
        SearchResponseInner webData = client.webs().search(
        "Best restaurants in Seattle", null, null, null, null, null, 10, null, 20, null, "en-us", null,
                null, responseFilterstrings, SafeSearch.STRICT, null, null, null);

        System.out.println("\\r\\nSearched for Query# \" Microsoft \" with response filters \"news\"");

        //News
        if (webData.news() != null && webData.news().value().size() > 0)
        {
            // find the first news result
            NewsArticle firstNewsResult = webData.news().value().get(0);

            if (firstNewsResult != null)
            {
                System.out.println(String.format("News Results#%d", webData.news().value().size()));
                System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
                System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
            }
            else
            {
                System.out.println("Couldn't find first News results!");
            }
        }
        else
        {
            System.out.println("Didn't see any News data..");
        }

    }
    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Lekérdezés "Niagara esik", a keresés használatával `answerCount` és `promote` paramétereket. Nyomtassa ki a részletes eredmények.
```
public static void WebSearchWithAnswerCountPromoteAndSafeSearch(String subscriptionKey)
{
    WebSearchAPIImpl client = getClient(subscriptionKey);

    try
    {
        List<AnswerType> promoteAnswertypeStrings = new ArrayList<AnswerType>();
        promoteAnswertypeStrings.add(AnswerType.VIDEOS);
        SearchResponseInner webData = client.webs().search(
            "Niagara Falls", null, null, null, null, null, 10, null, 20, null, "en-us", null,
            promoteAnswertypeStrings, null, SafeSearch.STRICT, null, null, null);
        System.out.println("\r\nSearched for Query# \" Niagara Falls \"");

        if (webData.videos().value().size() > 0)
        {
            VideoObject firstVideosResult = webData.videos().value().get(0);

            if (firstVideosResult != null)
            {
                System.out.println(String.format("Video Results#%d", webData.videos().value().size()));
                System.out.println(String.format("First Video result name: %s ", firstVideosResult.name()));
                System.out.println(String.format("First Video result URL: %s ", firstVideosResult.contentUrl()));
            }
            else
            {
                System.out.println("Couldn't find videos results!");
            }
        }
        else
        {
            System.out.println("Didn't see any data..");
        }
    }
    catch (ErrorResponseException ex)
    {
        System.out.println("Encountered exception. " + ex.getLocalizedMessage());
    }
}

```
Az előző módszerek hozzáadása egy osztályt, futtassa a kódot a fő függvényt:
```
package javaWebSDK;
import com.microsoft.azure.cognitiveservices.websearch.*;
public class webSDK{

    public static void main(String [ ] args) {

        WebSearchResultTypesLookup("YOUR-SUBSCRIPTION-KEY");
        WebResultsWithCountAndOffset("YOUR-SUBSCRIPTION-KEY");
        WebSearchWithResponseFilter("YOUR-SUBSCRIPTION-KEY");
        WebSearchWithAnswerCountPromoteAndSafeSearch("YOUR-SUBSCRIPTION-KEY");

    }
    // Add methods previoiusly documented.
}
```
## <a name="next-steps"></a>További lépések

[Cognitive Services Java SDK-minták](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
