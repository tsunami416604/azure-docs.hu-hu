---
title: Gyors üzembe helyezés nyissa meg az Azure kognitív szolgáltatások esetében a keresést a Bing API |} Microsoft Docs
description: Gyorsan használatának megkezdésében nyelvi nyissa meg a Bing webes keresés API a Microsoft Azure kognitív Services lekérdezni.
services: cognitive-services
author: Nhoya
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 03/09/2018
ms.author: rosh
ms.reviewer: nhoyadx@gmail.com, v-gedod
ms.openlocfilehash: 86cb67d46bca40c83c2f175ab7fdf6fbf52cb02f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347750"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-go"></a>Hívás-válasz: Nyissa meg a első Bing keresést a lekérdezés

A webes Bing keresési API Bing.com, a felhasználó kapcsolódik vissza a keresési eredmények hasonlít. Az eredmények lehet, hogy tartalmazzák a weblapok, képek, videók, híreket és entitások, kapcsolódó keresési lekérdezések, helyesen adta-e javításokat, időzónák, egység átalakítás, fordítások és számítások együtt. Az eredmények a relevanciájának és a Bing keresési API-k riasztásról a szint alapul.

Tekintse meg a [API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) az API-k vonatkozó további információért.

## <a name="prerequisites"></a>Előfeltételek
Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

Telepítse a [nyissa meg a bináris fájlok](https://golang.org/dl/).
 
Ez az oktatóanyag csak használja **core** szalagtárak, ezért jelenleg nem rendelkezik külső függőségekkel.

## <a name="core-libraries"></a>Alap függvénytárai

Használja `http` elküldeni a kérelmet a végpont `ioutil` beolvasni a választ `time` és `json` kezelni a json és `fmt` a kimeneti nyomtatása

```
package main

import (
    "fmt"
    "net/http"
    "io/iutil"
    "time"
    "encoding/json"
)
```

## <a name="define-variables"></a>Változók meghatározása
Állítsa be az API-végpont és a kívánt keresőkifejezést.

```
//This is the valid endpoint at the time of the writing
const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
//API token
token := "YOUR-ACCESS-KEY"
searchTerm := "Microsoft Cognitive Services"
```

## <a name="building-and-sending-the-request"></a>Kialakításához, és a kérés elküldése

```
//Declare a new GET request
req, err := http.NewRequest("GET", endpoint, nil)
if err != nil {
    panic(err)
}
//Add the payload to the request
param := req.URL.Query()
param.Add("q", searchTerm)
//Encoding the payload
req.URL.RawQuery = param.Encode()

//Insert the API token in the request header
req.Header.Add("Ocp-Apim-Subscription-Key", token)

//create new client
client := new(http.Client)
//Send the request
resp, err := client.Do(req)
if err != nil {
    panic(err)
}
//Close the response body at the function exit
defer resp.Body.Close() 
```

## <a name="printing-the-answer"></a>A válasz nyomtatás
A Keresés eredménye belül a `resp` változó. A válasz törzsében kinyomtatásához változó.

```
body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    panic(err)
}
//Convert body from byte to string.
fmt.Println(string(body))
```

## <a name="put-everything-together"></a>Minden hozzáfoghat

```
package main
import (
    "fmt"
    "net/http"
    "io/ioutil"
    "time"
    "encoding/json"
)

//The struct that will contain the answer
type BingAnswer struct {
        Type         string `json:"_type"`
        QueryContext struct {
                OriginalQuery string `json:"originalQuery"`
        } `json:"queryContext"`
        WebPages struct {
                WebSearchURL          string `json:"webSearchUrl"`
                TotalEstimatedMatches int    `json:"totalEstimatedMatches"`
                Value                 []struct {
                        ID               string    `json:"id"`
                        Name             string    `json:"name"`
                        URL              string    `json:"url"`
                        IsFamilyFriendly bool      `json:"isFamilyFriendly"`
                        DisplayURL       string    `json:"displayUrl"`
                        Snippet          string    `json:"snippet"`
                        DateLastCrawled  time.Time `json:"dateLastCrawled"`
                        SearchTags       []struct {
                                Name    string `json:"name"`
                                Content string `json:"content"`
                        } `json:"searchTags,omitempty"`
                        About []struct {
                                Name string `json:"name"`
                        } `json:"about,omitempty"`
                } `json:"value"`
        } `json:"webPages"`
        RelatedSearches struct {
                ID    string `json:"id"`
                Value []struct {
                        Text         string `json:"text"`
                        DisplayText  string `json:"displayText"`
                        WebSearchURL string `json:"webSearchUrl"`
                } `json:"value"`
        } `json:"relatedSearches"`
        RankingResponse struct {
                Mainline struct {
                        Items []struct {
                                AnswerType  string `json:"answerType"`
                                ResultIndex int    `json:"resultIndex"`
                                Value       struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"mainline"`
                Sidebar struct {
                        Items []struct {
                                AnswerType string `json:"answerType"`
                                Value      struct {
                                        ID string `json:"id"`
                                } `json:"value"`
                        } `json:"items"`
                } `json:"sidebar"`
        } `json:"rankingResponse"`
}

func main() {
    const endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/search"
    token := "YOUR-ACCESS-KEY"
    searchTerm := "Microsoft Cognitive Services"
    
    req, err := http.NewRequest("GET", endpoint, nil)
    if err != nil {
        panic(err)
    }
    
    param := req.URL.Query()
    param.Add("q", searchTerm)
    req.URL.RawQuery = param.Encode()

    req.Header.Add("Ocp-Apim-Subscription-Key", token)
    
    client := new(http.Client)
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close() 
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    //creating a new answer struct
    ans := new(BingAnswer)
    err = json.Unmarshal(body, &ans)
    if err != nil {
         fmt.Println(err)
    }
    //Iterate over search results
    for _, result := range ans.WebPages.Value {
         //Printing result name and URL
         fmt.Println(result.Name, "||", result.URL)
    }

}
```

## <a name="next-steps"></a>További lépések

[Bing webes keresés – áttekintés](../overview.md)  
[Próbálja ki](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Egy ingyenes próba hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing webes keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

