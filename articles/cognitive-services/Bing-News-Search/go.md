---
title: 'Gyors útmutató: Hírek beolvasása Bing News Search REST API és go használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató a go nyelvet használja a Bing News Search API meghívásához. Az eredmények közé tartoznak a lekérdezési karakterlánc által azonosított hírforrások nevei és URL-címei.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 2/21/2019
ms.author: aahi
ms.openlocfilehash: e08fe23f99cbf2fac7fc0528b04360f36d22b875
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222116"
---
# <a name="quickstart-get-news-results-using-the-bing-news-search-rest-api-and-go"></a>Rövid útmutató: Hírek eredményeinek beolvasása a Bing News Search REST API és go használatával

Ez a rövid útmutató a go nyelvet használja a Bing News Search API meghívásához. Az eredmények közé tartoznak a lekérdezési karakterlánc által azonosított hírforrások nevei és URL-címei.

## <a name="prerequisites"></a>Előfeltételek
* A [Go bináris fájljainak](https://golang.org/dl/) telepítése
* Az eredmények megjelenítéséhez telepítse a go-köp függvénytárat az IT szép nyomtatóhoz
    * A könyvtár telepítése: `$ go get -u https://github.com/davecgh/go-spew`

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-libraries"></a>Projekt létrehozása és kódtárak importálása

Hozzon létre egy új go-projektet az IDE vagy a szerkesztőben. Ezután importálja `net/http` a kérelmekhez, `ioutil` a válasz olvasásához, és `encoding/json` az eredmények JSON-szövegének kezeléséhez. A go-köp függvénytár a JSON elemzéséhez szükséges. 

```
package main

import (
    "fmt"
    "net/http"
    "io/ioutil"
    "encoding/json"
    "github.com/davecgh/go-spew/spew"
)

```

## <a name="create-a-struct-to-format-the-news-search-results"></a>A Hírek keresési eredményeinek formázásához hozzon létre egy struct-t

A `NewsAnswer` struktúra formázza a válaszban megadott adatokat. A válasz JSON többszintű és meglehetősen összetett.  A következő implementáció az alapvető tudnivalókat tartalmazza.

```
// This struct formats the answer provided by the Bing News Search API.
type NewsAnswer struct {
    ReadLink       string `json: "readLink"` 
    QueryContext   struct {
        OriginalQuery   string   `json: "originalQuery"`
        AdultIntent     bool        `json: "adultIntent"`
    } `json: "queryContext"`
    TotalEstimatedMatches   int  `json: totalEstimatedMatches"` 
    Sort  []struct {
        Name    string  `json: "name"`
        ID       string    `json: "id"`
        IsSelected       bool  `json: "isSelected"`
        URL      string   `json: "url"`
    }  `json: "sort"` 
    Value   []struct   {
        Name     string   `json: "name"`
        URL   string    `json: "url"`
        Image   struct   {
            Thumbnail   struct  {
                ContentUrl  string  `json: "thumbnail"`
                Width   int  `json: "width"`
                Height  int   `json: "height"`
            } `json: "thumbnail"` 
            Description  string  `json: "description"`
            Provider  []struct   {
                Type   string    `json: "_type"`
                Name  string     `json: "name"`
            } `json: "provider"` 
            DatePublished   string   `json: "datePublished"`
        } `json: "image"` 
    } `json: "value"` 
}

```

## <a name="declare-the-main-function-and-define-variables"></a>A fő függvény deklarálása és a változók megadása  

A következő kód deklarálja a fő függvényt, és hozzárendeli a szükséges változókat. Győződjön meg arról, hogy helyes a végpont, és cserélje le a `token` értékét egy érvényes előfizetői azonosítóra az Azure-fiókjából.

```
func main() {
    // Verify the endpoint URI and replace the token string with a valid subscription key.  
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"

    // Declare a new GET request.
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }

    // The rest of the code in this example goes here in the main function.
}
```

## <a name="query-and-header"></a>Lekérdezés és fejléc

A lekérdezési karakterlánc és az elérési kulcs fejlécének hozzáadása

```
// Add the query to the request.  
param := req.URL.Query()
param.Add("q", searchTerm)
req.URL.RawQuery = param.Encode()

// Insert the subscription-key header.  
req.Header.Add("Ocp-Apim-Subscription-Key", token)

```

## <a name="get-request"></a>Kérelem kérése

Hozza létre az ügyfelet, és küldje el a Get kérelmet. 

```
// Instantiate a client.  
client := new(http.Client)

// Send the request to Bing.  
resp, err := client.Do(req)
if err != nil {
    panic(err)
}

```

## <a name="send-the-request"></a>A kérelem elküldése

Küldje el a kérést, és olvassa el az eredményeket `ioutil`használatával.

```
resp, err := client.Do(req)
    if err != nil {
        panic(err)
}

// Close the connection.    
defer resp.Body.Close()

// Read the results
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}

```

## <a name="handle-the-response"></a>A válasz kezelése

A `Unmarshall` függvény kinyeri az adatokat a News Search API által visszaadott JSON-szövegből.  Ezután az eredmények csomópontjait a `go-spew` Pretty nyomtató használatával jelenítheti meg.

```
// Create a new answer object 
ans := new(NewsAnswer)
err = json.Unmarshal(body, &ans)
if err != nil {
    fmt.Println(err)
}
    
fmt.Print("Output of BingAnswer: \r\n\r\n")
    
// Iterate over search results and print the result name and URL.
for _, result := range ans.Value{
spew.Dump(result.Name, result.URL)
}

```

## <a name="results"></a>Results (Eredmények)

Az eredmény az egyes eredmények nevét és URL-címét tartalmazza.

```
(string) (len=91) "Cognitive Services Market: Global Industry Analysis and Opportunity Assessment, 2019 - 2025"
(string) (len=142) "https://www.marketwatch.com/press-release/cognitive-services-market-global-industry-analysis-and-opportunity-assessment-2019---2025-2019-02-21"
(string) (len=104) "Microsoft calls for greater collaboration to harness the power of AI to empower people with disabilities"
(string) (len=115) "https://indiaeducationdiary.in/microsoft-calls-greater-collaboration-harness-power-ai-empower-people-disabilities-2/"
(string) (len=70) "Microsoft 'Intelligent Cloud Hub' to build AI-ready workforce in India"
(string) (len=139) "https://cio.economictimes.indiatimes.com/news/cloud-computing/microsoft-intelligent-cloud-hub-to-build-ai-ready-workforce-in-india/67187807"
(string) (len=81) "Skills shortage is stopping many Asian companies from embracing A.I., study shows"
(string) (len=106) "https://www.cnbc.com/2019/02/20/microsoft-idc-study-skills-shortages-stopping-companies-from-using-ai.html"
(string) (len=143) "Cognitive Computing in Healthcare Market Emerging Top Key Vendors- Apixio, MedWhat, Healthcare X.0, Apple, Google, Microsoft, and IBM 2017-2025"
(string) (len=40) "http://www.digitaljournal.com/pr/4163064"
(string) (len=49) "Microsoft launches AI skills initiative in Brazil"
(string) (len=80) "https://www.zdnet.com/article/microsoft-launches-ai-skills-initiative-in-brazil/"
(string) (len=45) "Kuwait's CITRA and Microsoft host AI OpenHack"
(string) (len=70) "http://www.itp.net/618639-kuwaits-citra-and-microsoft-host-ai-openhack"
(string) (len=51) "CITRA and Microsoft collaborate to host AI workshop"
(string) (len=123) "https://www.zawya.com/mena/en/press-releases/story/CITRA_and_Microsoft_collaborate_to_host_AI_workshop-ZAWYA20190212105751/"

```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Mi az Bing News Search?](search-the-web.md)
