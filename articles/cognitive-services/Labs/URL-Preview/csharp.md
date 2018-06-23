---
title: Gyorsútmutató C# projekt URL-cím Preview - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: A Microsoft Azure kognitív Services projekt URL-cím Preview az első lépéseiben.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 17d44bd0c23d0a1e67da5a0e91248700d3166c1a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348635"
---
# <a name="url-preview-query-in-c"></a>A C# URL-cím Preview lekérdezés

Az alábbi C# példakód létrehozza a SwiftKey webhely URL-cím előnézete: https://swiftkey.com/en.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz [Visual Studio 2017](https://www.visualstudio.com/downloads/) ezt a kódot futtathatnak Windows. (Az ingyenes közösségi Edition fog működni.)

Az ingyenes próbaidőszakra hozzáférési kulcs beszerzése [kognitív szolgáltatások Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Kód forgatókönyv

A következő C#-kódban hoz létre a SwiftKey webhely URL-cím előnézete: https://swiftkey.com/en. 

Az alábbi lépéseket a megvalósított:
1. Deklarálja a változókat, adja meg a végpont és az előzetes lekérdezési URL.  
2. Hozzon létre a kérelmet.
3. Adja hozzá a *Ocp-Apim-előfizetés-kulcs* fejléc. 
4. A webes kérelem aszinkron módon futnak. 
5. A válasz olvasása.
6. A fejlécek és a JSON-eredmények a konzol nyomtatása.

**Forráskód**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
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
## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához:

1. Új konzol megoldás létrehozása a Visual Studióban.
2. Cserélje le `Program.cs` a megadott kód.
3. Cserélje le a `YOUR-ACCESS-KEY` egy érvényes elérési kulcsot az előfizetéshez tartozó értéket.
4. Futtassa a programot.

## <a name="next-steps"></a>További lépések
- [Java gyors üzembe helyezés](java-quickstart.md)
- [JavaScript gyors üzembe helyezés](javascript.md)
- [Csomópont gyors üzembe helyezés](node-quickstart.md)
- [Python gyors üzembe helyezés](python-quickstart.md)