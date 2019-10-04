---
title: 'Gyors útmutató: Projekt URL-címének előzetes verziójaC#'
titlesuffix: Azure Cognitive Services
description: Az URL-cím előnézete projekt használatának első lépései a C#-pal.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 7f900b95b30f6ab5298e98661e6922e4e4d360c7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706968"
---
# <a name="quickstart-url-preview-query-in-c"></a>Gyors útmutató: URL-előnézet lekérdezése itt:C#

Az alábbi C#-példa létrehoz egy URL-előnézetet a SwiftKey webhelyhez: https://swiftkey.com/en.

## <a name="prerequisites"></a>Előfeltételek

A kód Windows rendszeren való futtatásához a [Visual Studio 2017-es vagy újabb verziójára](https://www.visualstudio.com/downloads/) lesz szüksége. (Az ingyenes Community Edition is elegendő.)

Hozzáférési kulcs lekérése a [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-answer-search) ingyenes próbaverziójához

## <a name="code-scenario"></a>Kódforgatókönyv

Az alábbi C#-kód létrehozza a SwiftKey webhely URL-előnézetét: https://swiftkey.com/en. 

Implementálására a következő lépésekben kerül sor:
1. Deklarálja a változókat a végpont megadásához, valamint egy lekérdezési URL-címet az előnézethez.  
2. Létrehozza a kérést.
3. Hozzáadja az *Ocp-Apim-Subscription-Key* fejlécet. 
4. Aszinkron módon futtatja a webes kérést. 
5. Beolvassa a választ.
6. Megjeleníti a fejléceket és a JSON-eredményeket a konzolon.

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

Az alkalmazás futtatása:

1. Hozzon létre egy új konzolmegoldást a Visual Studióban.
2. Írja felül a `Program.cs` értékét a megadott kóddal.
3. A `YOUR-ACCESS-KEY` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

## <a name="next-steps"></a>További lépések
- [Java – rövid útmutató](java-quickstart.md)
- [JavaScript – rövid útmutató](javascript.md)
- [Node – rövid útmutató](node-quickstart.md)
- [Python – rövid útmutató](python-quickstart.md)
