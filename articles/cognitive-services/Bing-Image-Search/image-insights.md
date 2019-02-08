---
title: Nyerjen kép – a Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Ismerje meg a kép további információt szeretne kapni a Bing Image Search API használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b2dfbeaae46be58e94f6c3cfc5bbd97d82382e54
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876606"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>A Bing Image Search API-lemezkép elemzések lekérése

> [!IMPORTANT]
> A/képek/részletek végpont kép elemzésekkel helyett használjon [vizuális keresés](../bing-visual-search/overview.md) óta átfogóbb elemzéseket biztosít.


Minden egyes képe egy insights jogkivonatot, amellyel a rendszerkép adatainak lekérése is tartalmaz. Például kaphat kapcsolódó képeket, a képet tartalmazó weblapokra, vagy a kereskedők listája ahol vásárolhat a termék a képen látható.  

Kaphat elemzési információkat egy képet, a lemezkép rögzítése [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) jogkivonat a válaszban.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Ezután hívja a lemezkép adatait végpont, és állítsa be a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) lekérdezési paraméter token `imageInsightsToken`.  

Adja meg az elemzéseket szeretne kapni, állítsa be a `modules` lekérdezési paraméter. Az összes elemzések lekérése, állítsa `modules` való `All`. Állítsa be, hogy csak a feliratot, és a gyűjtemény elemzéseket `modules` való `Caption%2CCollection`. Lehetséges elemzéseket teljes listáját lásd: [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Nem minden insights az összes rendszerkép érhetők el. A válasz tartalmazza a kért, az elemzés, ha elérhető.

Az alábbi példa az előző képen az összes rendelkezésre álló insights kérelmeket.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Egy ismert lemezkép elemzések lekérése

Ha a kép URL-címe, amelyet szeretne az elemzéseket, használja a [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) lekérdezési paraméter helyett a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) paramétert adja meg a lemezképet. Vagy, ha a bináris fájl rendelkezik, a rendszerkép a bináris küldhet egy POST-kérés törzse. Ha egy POST-kérés esetén használja a `Content-Type` fejlécet állítsa `multipart/data-form`. Bármelyik lehetőséget választja a lemezkép mérete nem haladhatja meg az 1 MB.  

Ha a kép URL-CÍMÉT, az alábbi példa bemutatja, hogyan kérhet egy kép insights.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Az összes rendszerkép elemzések lekérése  

Kérelem az elemzés egy kép, állítsa be a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) lekérdezési paramétert `All`. Kapcsolódó keresések lekéréséhez a kérelem tartalmaznia kell a felhasználói lekérdezési karakterláncot. Ez a példa bemutatja, hogy használatával a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) , adja meg a lemezképet.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A legfelső szintű objektum egy [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) objektumazonosító helyett egy [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektum.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>FELISMERVE a képet entitások  

Az entitás szolgáltatás azonosítja az entitásokat a képet, jelenleg csak a felhasználók. A kép entitások azonosítása, állítsa be a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) lekérdezési paramétert `RecognizedEntities`.  

> [!NOTE]
> Ez a modul nem határozhat meg bármely egyéb modult. Ha ez a modul más modulokkal adja meg, a válasz nem tartalmaz felismert entitásokat.  

A következő használatával adja meg a kép mutatja a [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) paraméter. URL-CÍMÉT a vágólapra a lekérdezési paraméterek kódolása.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Az alábbiakban az előző kérelemre adott válasz látható. A rendszerkép tartalmazza a két személynek, mert a válasz mindenki, aki egy régiót azonosítja. Ebben az esetben a személyeknek felismerhető CelebrityAnnotations és CelebRecognitionAnnotations csoportokban. A Bing minden csoport a valószínűsége, hogy azok megfelelnek-e a személyt az eredeti rendszerkép alapján emberek listája. A lista egy megbízhatósági szerinti csökkenő sorrendben. A CelebRecognitionAnnotations csoport biztosít a legmagasabb szintű megbízhatóság, hogy helyesen szerepel-e az egyezés.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

A `region` mező azonosítja a terület a kép, ahol ismeri fel a Bing az entitás. Az emberek a régiót jelöli a személy face.  

Az értékeket a téglalap képest relatív szélességét és magasságát az eredeti rendszerkép és 0,0 és 1,0 közé kell esnie. Például, ha a kép 300 x 200-as és a terület felső pontján (10, 20) a bal felső sarokban található és alján, jobb oldali sarokban (290, 150) ponton, akkor a normalizált téglalap:  

-   A bal oldalon: 10 / 300 = 0.03333...  
-   Felső:  20 / 200 = 0.1  
-   Jobb: 290 / 300 = 0.9667...  
-   Alsó: 150 / 200 = 0.75  

Használhatja a régiót, amelyben a Bing, az ezt követő insights hívások adja vissza. Ha például vizuálisan hasonló képek a felismert entitás beolvasásához. További információkért lásd: vágása rendszerképek vizuálisan hasonló és entitások felismerése modulok. Az alábbiakban látható a régió mezőt és a lekérdezési paraméterek közötti lemezképek levágni használható.  

-   Balra képez [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   Az első lesz leképezve a [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   Jobb képez [autó](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   A maps alsó [cab-fájl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Vizuálisan hasonló képek keresése  

Keresse meg az eredeti rendszerkép vizuálisan hasonló képek, állítsa be a [modulok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) lekérdezése SimilarImages paramétert.  

A következő kérelmet vizuálisan hasonló képek bekérése mutatja be. A kérelem a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) lekérdezési paraméter az eredeti rendszerkép azonosításához. Találati pontosság javítása érdekében a felhasználói lekérdezési karakterláncot tartalmaznia kell.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Az alábbiakban az előző kérelemre adott válasz látható.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>A rendszerképek vágása vizuálisan hasonló és entitások felismerése modulok  

A régió, amely a Bing vizuálisan hasonló képek megállapításához vagy entitások felismerése végrehajtásához használja a kép megadásához használja a [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab), és [autó](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) lekérdezési paramétereket. Alapértelmezés szerint a Bing használja a teljes kép.  

A paramétereket adja meg a felső és alsó, bal felső sarokban a régióban, az összehasonlítást használó Bing jobb felső sarkában. Adja meg az értékeket az eredeti kép szélessége és magassága részeként. Kezdje a kitalálva (0.0, 0.0) felül, bal felső sarokban található és a végén (1.0-s, 1.0-s), a jobb alsó sarokban. Adja meg, hogy a felső, a bal oldali sarokban indul módja negyedéves le elejére, és módon bal oldalán, negyedév, például állítsa be a `cal` 0,25, és `cat` 0,25.  

A következő lépéseket a hívások a hatása a körbevágási régió megadása látható. Az első hívás nem tartalmazza a vágás, és a Bing felismeri a két személynek a kép közepén egymás mellett állandó.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

A válasz két felismert entitásokat jeleníti meg.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

A második hívás körülvágja a képet a középső vertikálisan le, és a Bing egyetlen személy felismeri a kép jobb oldalán.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A válasz egy felismert entitás jeleníti meg.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Vizuálisan hasonló termékek keresése  

Keresse meg, amelyek tartalmazzák a termékhez, melyek a termékek található az eredeti képet a vizuálisan hasonló képek, állítsa be a [modueles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) lekérdezési paraméter, SimilarProducts.  

A következő kérelmet termékek vizuálisan hasonló képek bekérése mutatja be. A kérelem a [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) lekérdezési paraméterrel meghatározhatja az eredeti rendszerkép, amely a korábbi kérelmekre adott vissza. Találati pontosság javítása érdekében a felhasználói lekérdezési karakterláncot tartalmaznia kell.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Az alábbiakban az előző kérelemre adott válasz látható. A válasz egy hasonló termék képet tartalmaz, és azt jelzi, hogy hány kereskedők kínálnak a termék online, a termék minősítések vannak-e, és a legalacsonyabb ár található (lásd a `aggregateOffer` mezőben).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

A kereskedők, a termék online kínáló listáját (lásd a [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) mező), az API-t hívja meg újra, és állítsa be `modules` ShoppingSources való. Ezután adja meg a `insightsToken` lekérdezési paraméter a token található a termék összefoglaló kép.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A korábbi kérelemre adott válasz a következő:  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
