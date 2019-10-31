---
title: Képelemzési kognitív keresési képesség – Azure Search
description: Szemantikai szöveg kinyerése a képelemzés segítségével a ImageAnalysis kognitív képességgel egy Azure Search alkoholtartalom-növelési folyamatban.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: luisca
ms.openlocfilehash: e2c8f0519ffcbdbc2445d1fed2725b6f6b948cd1
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064078"
---
#   <a name="image-analysis-cognitive-skill"></a>Képelemzés – kognitív képesség

A **képelemzési** képesség a vizualizációs funkciók gazdag készletét Kinyeri a kép tartalma alapján. Létrehozhat például egy képfeliratot egy képből, létrehozhat címkéket, vagy azonosíthatja a hírességeket és a tereptárgyait. Ez a képesség a Cognitive Services [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) által biztosított gépi tanulási modelleket használja. 

> [!NOTE]
> A kis méretű kötetek (20 tranzakció alatt) ingyenesen végrehajthatók Azure Searchban, de a nagyobb munkaterhelésekhez [számlázható Cognitive Services erőforrást kell csatolni](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. vízió. ImageAnalysisSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| defaultLanguageCode   |  A visszaadni kívánt nyelvet jelző sztring. A szolgáltatás egy megadott nyelven adja vissza az elismerés eredményét. Ha a paraméter nincs megadva, az alapértelmezett érték az "en". <br/><br/>A támogatott nyelvek a következők: <br/>*hu* – angol (alapértelmezett) <br/> *zh* -egyszerűsített kínai|
|visualFeatures |   Karakterláncok tömbje, amely a vizuális szolgáltatások visszatérési típusait jelzi. A vizuális funkciók érvényes típusai a következők:  <ul><li> *Kategóriák* – a rendszerképek tartalmának kategorizálása a Cognitive Services [Computer Vision dokumentációjában](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)meghatározott besorolásnak megfelelően. </li><li> *címkék* – a képet a képtartalommal kapcsolatos szavak részletes listájával címkézheti.</li><li>*Leírás* – a rendszerkép tartalmának teljes angol mondattal történő leírása.</li><li>*Faces* – észleli, hogy vannak-e arcok. Ha van, a a koordinátákat, a nemeket és a kort hozza létre.</li><li>    *imageType* – észleli, ha a kép ClipArt vagy vonalas rajz.</li><li>  *Color (szín* ) – meghatározza az ékezetes színeket, a domináns színeket, valamint azt, hogy a képek fekete & fehérek-e.</li><li>*felnőtt* – észleli, ha a rendszerkép a természetben található (a meztelenség vagy a szexuális cselekedet ábrázolása). A rendszer a szexuálisan szuggesztív tartalmat is észleli.</li></ul> A vizualizációs funkciók nevei megkülönböztetik a kis-és nagybetűket.|
| Részletek   | Karakterláncok tömbje, amely azt jelzi, hogy melyik tartományra vonatkozó adatokat kell visszaadnia. A vizuális funkciók érvényes típusai a következők: <ul><li>*hírességek* – azonosítja a hírességeket, ha a rendszerkép észleli őket.</li><li>*tereptárgyak* – a rendszerképben észlelt tereptárgyak azonosítására szolgál. </li></ul> |

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Összetett típus. A jelenleg csak az Azure Blob indexelő által előállított "/Document/normalized_images" mezővel működik, ha a ```imageAction``` értéke ```none```tól eltérő értékre van állítva. További információért tekintse meg a [mintát](#sample-output) .|



##  <a name="sample-skill-definition"></a>Példa a szaktudás meghatározására

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "Tags",
                "Categories",
                "Description",
                "Faces"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Minta index (csak a kategóriák, a leírás, az arcok és a címkék mezőihez)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Minta kimeneti mező leképezése (a fenti indexhez)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>A kimeneti mezők leképezésének variációja (beágyazott tulajdonságok)

Az alacsonyabb szintű tulajdonságokhoz (például tereptárgyak vagy hírességek) is meghatározhat kimeneti mező-hozzárendeléseket. Ebben az esetben győződjön meg arról, hogy az index sémája tartalmaz egy mezőt, amely kifejezetten a tereptárgyak adatait tartalmazza.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Minta bemenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Példa kimenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "categories": [
                    {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                        "name": "people_",
                        "score": 0.83984375,
                        "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": [
                                        {
                                            "x": 273,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 431
                                        },
                                        {
                                            "x": 273,
                                            "y": 431
                                        }
                                    ],
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                        "faceBoundingBox": [
                            {
                                "x": 1601,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 447
                            },
                            {
                                "x": 1601,
                                "y": 447
                            }
                        ]
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
            }
        }
    ]
}
```


## <a name="error-cases"></a>Hibák esetei
A következő hiba esetekben egyetlen elem sincs kibontva.

| Hibakód | Leírás |
|------------|-------------|
| NotSupportedLanguage | A megadott nyelv nem támogatott. |
| InvalidImageUrl | A képurl-cím helytelen formátumú vagy nem érhető el.|
| InvalidImageFormat | A bemeneti adatok nem érvényes rendszerkép. |
| InvalidImageSize | A bemeneti rendszerkép túl nagy. |
| NotSupportedVisualFeature  | A megadott szolgáltatástípus érvénytelen. |
| NotSupportedImage | Nem támogatott rendszerkép, például gyermekpornográfia. |
| InvalidDetails | A tartományhoz tartozó modell nem támogatott. |

Ha a `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`hoz hasonló hibaüzenetet kap, ellenőrizze az elérési utat. A hírességek és a tereptárgyak a `detail`alatt található tulajdonságok.

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Lásd még:

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
