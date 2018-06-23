---
title: Bing lemezkép feltöltése az elemzések |} Microsoft Docs
description: Konzol alkalmazás használ a kép Bing keresési API lemezkép feltöltése és lemezkép elemzések lekérése.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346923"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Oktatóanyag: Az elemzések Bing lemezkép feltöltése

A kép Bing keresési API biztosít egy `POST` is feltölthetők egy kép, és keresse meg a lemezkép információk. A C# Konzolalkalmazás feltölt egy képet, a lemezkép keresési végpont részletes információkat a lemezkép használatával.
Az eredmények rövid időre, JSON-objektumok, például az alábbiakat:

![[JSON eredmények]](media/cognitive-services-bing-images-api/jsonResult.jpg)

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * A kép kereséssel `/details` a végpont egy `POST` kérelem
> * Adja meg a kérelem fejléc
> * Adja meg az eredmények URL-címet a paraméterek segítségével
> * A kép adatok feltöltése és küldése a `POST` kérelem
> * A JSON-eredmények a konzol nyomtatása

## <a name="app-components"></a>Alkalmazások összetevői

Az oktatóanyag alkalmazás három rész tartalmazza:

> [!div class="checklist"]
> * Adja meg a Bing kép keresési végpont és a kötelező fejlécet a végpont-konfiguráció
> * Lemezkép-fájl feltöltése `POST` kérést a végpont
> * A JSON-eredmények, amelyek részletes adatai elemzés által visszaadott a `POST` kérelem

## <a name="scenario-overview"></a>Forgatókönyv áttekintése
Nincsenek [három kép keresési végpontok](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). A `/details` végpont használhatja egy `POST` kérelem a kérelem törzsében kép adatokkal.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
A `modules` URL paraméter következő `/details?` határozza meg, milyen típusú részletek az eredmények tartalmazzák:
* `modules=All`
* `modules=RecognizedEntities` (személyek vagy helyek az ábrán látható)

Adja meg `modules=All` a a `POST` beolvasása JSON-szöveg, amely az alábbi lista tartalmazza:
* `bestRepresentativeQuery` -a Bing visszaadó lekérdezést, képeket hasonló, a feltöltött lemezképen
* `detectedObjects` például egy határolókeret vagy a kép interaktív területek
* `image` metaadatok
* `imageInsightsToken` -a későbbi jogkivonatból `GET` kérésére, amely lekérdezi `RecognizedEntities` (személyek vagy helyek az ábrán látható) 
* `imageTags`
* `pagesIncluding` -Weblapok, amelyek tartalmazzák a lemezkép
* `relatedSearches`
* `visuallySimilarImages`

Adja meg `modules=RecognizedEntities` a a `POST` beolvasása csak a `imageInsightsToken`, amellyel egy későbbi `GET` kérelmet. Azonosítja a, vagy helyezi el a képen látható.

## <a name="webclient-and-headers-for-the-post-request"></a>Webes és a POST fejlécek kérése
Hozzon létre egy `WebClient` objektumot, és állítsa be a fejléceket. A Bing keresési API-JÁNAK minden kérelemhez szükséges egy `Ocp-Apim-Subscription-Key`. A `POST` fénykép feltöltése minden olyan kérést is meg kell adnia `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Feltölti a lemezképet, és az eredmény érhető el

A `WebClient` osztály tartalmazza a `UpLoadFile` módszerrel, amely az adatok formátumát az `POST` kérelem. Akkor formázza a `RequestStream` és hívások `HttpWebRequest`, elkerülve a nagy mennyiségű összetettségét.
Hívás `WebClient.UpLoadFile` rendelkező a `/details` végpont és a bináris fájl feltöltéséhez. A rendszer ezt a választ bináris adatok könnyen alakítható át JSON-NÁ. 

A JSON-szövegben használjon példányának inicializálása a `SearchResult` struktúra (lásd a [alkalmazás forráskódjához](tutorial-image-post-source.md) környezet).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Az eredmények nyomtatása
A kód a többi elemzi a JSON eredményt, és kiírja a konzol.

```
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
```
## <a name="get-request-using-the-imageinsightstoken"></a>GET kérelmet a ImageInsightsToken
Használatához a `ImageInsightsToken` eredményeit lett visszaadva egy `POST`, hozzon létre egy `GET` kérelem a következő:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Ha azonosítható személyek vagy a kép helyen, ehhez a kérelemhez visszatér a rájuk vonatkozó információkat.
A [Quickstarts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) számos kódpéldák tartalmaz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing kép keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

