---
title: Az adatáttekintési jogkivonat használata – Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Azt mutatja be, hogyan használható a képek betekintési jogkivonata Bing Visual Search API a rendszerképekkel kapcsolatos információk lekéréséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.openlocfilehash: 251197c456ece4fe2dbbe264219d52f3502b7492
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67341725"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Elemzések lekérése a rendszerképekről

A Bing Visual Search API információkat ad vissza egy Ön által megadott képről. A kép feltöltéséhez használhatja a kép URL-címét, egy megállapítási jogkivonatot, vagy feltöltheti magát a képet. Ezekről a beállításokról a [Mi az Bing Visual Search API?](overview.md)című témakörben olvashat bővebben. Ez a cikk az adatellenőrzési jogkivonat használatát mutatja be. Az olyan példákat, amelyek bemutatják, hogyan tölthet fel egy képet az elemzések lekéréséhez, lásd a gyors útmutatókat ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node. js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)).

Ha képtokent vagy URL-címet küld Bing Visual Search, az alábbi ábrán láthatók a bejegyzés törzsében szerepeltetni kívánt űrlapadatok. Az űrlapon szerepelnie kell `Content-Disposition` a fejlécnek, és a `name` paramétert "knowledgeRequest" értékre kell állítania. Az `imageInfo` objektum részleteit a kérelemben tekintheti meg:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Az ebben a cikkben szereplő példák bemutatják, hogyan használhatja az adatellenőrzési tokent. Egy/images/Search API-válaszban lévő `Image` objektumból beolvassa az adatkivonatot. További információ az adatfelismerési token beszerzéséről: [Mi a Bing Image Search API?](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

Az információkból álló tokent használó Példákért lásd: [C#](#use-with-c) | [Java](#use-with-java) | [Node. js](#use-with-nodejs) | [Python](#use-with-python).

## <a name="use-with-c"></a>Használat a C használatával #

### <a name="c-prerequisites"></a>C# előfeltételek

- A [Visual Studio 2019](https://www.visualstudio.com/downloads/) bármely verziója a Windows rendszeren futó kód beszerzéséhez.
- Azure-előfizetés. Ebben a rövid útmutatóban egy [ingyenes próbaverziós](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetési kulcsot vagy egy fizetős előfizetési kulcsot is használhat.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy konzolos megoldást a Visual Studióban.
2. Cserélje le a Program.cs tartalmát az ebben a rövid útmutatóban látható kóddal.
3. Cserélje le az `accessKey` értéket saját előfizetői azonosítójára.
4. Cserélje le `insightsToken` az értéket egy/images/Search-válaszból származó, bepillantást kapott jogkivonatra.
5. Futtassa a programot.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>Használat a Javával

### <a name="java-prerequisites"></a>A Java előfeltételei

- A kód fordításához és futtatásához a [JDK 7-es vagy 8-as](https://aka.ms/azure-jdks) verzióját kell használnia. Ha kedvence van, akkor használhat egy Java IDE, de a szövegszerkesztővel is elegendő.
- Ebben a rövid útmutatóban egy [ingyenes próbaverziós](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetési kulcsot vagy egy fizetős előfizetési kulcsot is használhat.

## <a name="run-the-java-application"></a>A Java-alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Töltse le vagy telepítse a [Gson Java-függvénytárat](https://github.com/google/gson). A Gson-t a Maven használatával is beszerezheti.
2. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
3. Adja hozzá a megadott kódot egy `VisualSearch.java` nevű fájlban.
4. Cserélje le az `subscriptionKey` értéket saját előfizetői azonosítójára.
5. Futtassa a programot.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Használat a Node. js-sel

### <a name="nodejs-prerequisites"></a>A Node. js előfeltételei

- A kód futtatásához [Node. js 6](https://nodejs.org/en/download/) szükséges.
- Ebben a rövid útmutatóban egy [ingyenes próbaverziós](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetési kulcsot vagy egy fizetős előfizetési kulcsot is használhat.

## <a name="run-the-javascript-application"></a>A JavaScript-alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új mappát a projekthez (vagy használja kedvenc IDE-környezetét vagy szerkesztőjét).
2. Egy parancssorban vagy terminálablakban lépjen a létrehozott mappába.
3. Telepítse a lekérési modulokat:
  
   ```
   npm install request  
   ```
1. Telepítse az űrlapadatmodulokat:  
   ```
   npm install form-data  
   ```
1. Hozzon létre egy GetVisualInsights.js nevű fájlt, és adja hozzá az alábbi kódot.
1. Cserélje le az `subscriptionKey` értéket saját előfizetői azonosítójára.
1. Futtassa a programot.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Használat a Python használatával

### <a name="python-prerequisites"></a>A Python előfeltételei

- A kód futtatásához [Python 3](https://www.python.org/) szükséges.
- Ehhez a rövid útmutatóhoz használhatja az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetői azonosítóját, vagy beszerezhet egy fizetős előfizetői azonosítót.

## <a name="run-the-python-application"></a>A Python-alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
2. Hozzon létre egy visualsearch.py nevű fájlt, és adja hozzá a jelen rövid útmutatóban szereplő kódot.
3. Cserélje le az `SUBSCRIPTION_KEY` értéket saját előfizetői azonosítójára.
4. Futtassa a programot.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


def main():

    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))


# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>További lépések

[Visual Search egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)  
[Mi az a Bing Visual Search API?](overview.md)  
[A Cognitive Services kipróbálása](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próbaverzióhoz tartozó hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Képek – Visual Search](https://aka.ms/bingvisualsearchreferencedoc)
