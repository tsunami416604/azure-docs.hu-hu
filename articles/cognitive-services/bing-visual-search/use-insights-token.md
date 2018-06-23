---
title: Bing Visual keresési API-val insights token használatával |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visual Search API beolvasni a képet kaphat a token bemutatja, hogyan használja a képfájl betekintést.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 6470bb5b7b5b8404683d1ae6fd4f16f0436982ef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349339"
---
# <a name="using-an-insights-token-to-get-insights-about-an-image"></a>Egy képet kaphat a beolvasandó insights token használatával

Bing Visual Search API, amely megadja a képfájl információt ad vissza. A kép URL-címe, lexikális elem, vagy egy kép feltöltésével egy insights segítségével megadhatja a lemezképet. További információ a kapcsolókról: [Mi az Bing Visual Search API?](overview.md). Ez a cikk bemutatja, hogy insights token használatával. További példák, amelyek bemutatják, nyerhet, hogy egy kép feltöltésével: a quickstarts ([C#](quickstarts\csharp.md) | [Java](quickstarts\java.md) | [Node.js](quickstarts\nodejs.md)  |  [Python](quickstarts\python.md)).


Ha Visual keresési küldi el egy kép token vagy URL-címe, a következő jeleníti meg az űrlap adatait meg kell adni a FELADÁS egy vagy több törzsében. Az űrlap adatait tartalmaznia kell a tartalomtípus-szabályozó fejléc és a `name` paraméter "knowledgeRequest" értékre kell állítani. A vonatkozó további információért a `imageInfo` objektumazonosító, lásd: [a kérelem](#the-request).

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

Ebben a cikkben szereplő példák szemléltetik a insights jogkivonat. A insights jogkivonat beszerzése egy /images/search API-válaszból kép objektumára. További információ a insights token első: [Bing kép Search API](../Bing-Image-Search/overview.md).

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


A insights jogkivonat példákért lásd [C#](#using-csharp) | [Java](#using-java) | [Node.js](#using-nodejs) | [Python](#using-python).

<a name="csharp" />
## <a name="using-c"></a>C# használatával

### <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Visual Studio 2017](https://www.visualstudio.com/downloads/) Ez a kód futtatása Windows eléréséhez. (Az ingyenes közösségi Edition fog működni.)

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Új konzol megoldás létrehozása a Visual Studióban.
1. Cserélje le a tartalmát `Program.cs` a gyors üzembe helyezés látható kóddal.
2. Cserélje le a `accessKey` az Előfizetés kulcs értékének.
2. Cserélje le a `insightsToken` érték a/képek/keresési választ insights tokenhez.
3. Futtassa a programot.

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




## <a name="using-java"></a>Java használatával

### <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [JDK 7 vagy 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) fordításához, és futtassa a következő kódot. A Java IDE lehet használni, ha kedvenc, de elegendő egy szövegszerkesztőben.

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Töltse le és telepítse a [gson könyvtár](https://github.com/google/gson). Is elérhető, Maven keresztül.
2. Hozzon létre egy új Java-projektet a kedvenc IDE vagy szerkesztő.
3. A megadott kód hozzáadása nevű fájlba `VisualSearch.java`.
4. Cserélje le a `subscriptionKey` az Előfizetés kulcs értékének.
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

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

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


<a name="using-nodejs" />
## <a name="using-nodejs"></a>A Node.js használata

### <a name="prerequisites"></a>Előfeltételek

Szüksége [Node.js 6](https://nodejs.org/en/download/) futtatásához ezt a kódot.

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a projekthez (vagy a kedvenc IDE vagy-szerkesztő).
2. A parancssor vagy a Terminálszolgáltatások keresse meg az imént létrehozott mappára.
3. A kérelem-modulok telepítése:  
  ```  
  npm install request  
  ```  
3. A űrlapadat modulok telepítése:  
  ```  
  npm install form-data  
  ```  
4. Hozzon létre egy GetVisualInsights.js nevű fájlt, és adja hozzá a következő kódot az.
5. Cserélje le a `subscriptionKey` az Előfizetés kulcs értékének.
7. Futtassa a programot.  
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




## <a name="using-python"></a>Pythonos környezetekben


### <a name="prerequisites"></a>Előfeltételek

Szüksége [Python 3](https://www.python.org/) futtatásához ezt a kódot.

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új Python-projektet a kedvenc IDE vagy szerkesztő.
2. Hozzon létre egy visualsearch.py nevű fájlt, és adja hozzá a kódot, a gyors üzembe helyezés látható.
3. Cserélje le a `SUBSCRIPTION_KEY` az Előfizetés kulcs értékének.
4. Futtassa a programot.


```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

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

[Bing Visual keresési alkalmazás oktatóanyag](tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual keresési áttekintése](overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Egy ingyenes próba hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual keresési API-referencia](https://aka.ms/bingvisualsearchreferencedoc)
