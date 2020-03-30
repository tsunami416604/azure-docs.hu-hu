---
title: Képelemzés kognitív készség
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search a képelemzés kognitív képességeivel kinyerheti a szemantikai szöveget.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4ff6972e2f7ea219a1c8c8dbabbf9fe12a8fa59e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369476"
---
# <a name="image-analysis-cognitive-skill"></a>Képelemzés kognitív készség

A **képelemzési** szakértelem a képtartalom alapján számos vizuális funkciót kinyer. Létrehozhat például egy képaláírást egy képből, címkéket hozhat létre, vagy hírességeket és tájékozódási pontokat azonosíthat. Ez a szakértelem a Cognitive Services [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) által biztosított gépi tanulási modelleket használja. 

> [!NOTE]
> Kis kötetek (20 tranzakció alatt) ingyenesen végrehajtható az Azure Cognitive Search, de a nagyobb számítási feladatok [hoz egy számlázható Cognitive Services-erőforrás.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| defaultLanguageCode   |  A visszaadandó nyelvet jelző karakterlánc. A szolgáltatás egy megadott nyelven adja vissza a felismerési eredményeket. Ha ez a paraméter nincs megadva, az alapértelmezett érték "en". <br/><br/>A támogatott nyelvek a következők: <br/>*hu* - Angol (alapértelmezett) <br/> *es* - Spanyol <br/> *ja* - japán <br/> *pt* - portugál <br/> *zh* - Egyszerűsített kínai|
| visualFeatures (vizuális funkciók) |  Karakterláncok tömbje, amely a visszaadandó vizuális jellemzőtípusokat jelzi. Az érvényes vizuális szolgáltatástípusok a következők:  <ul><li>*felnőtt* - érzékeli, ha a kép pornográf jellegű (meztelenséget vagy szexuális aktust ábrázol), vagy véres (szélsőséges erőszakot vagy vért ábrázol). Szexuálisan szuggesztív tartalom (más néven pikáns tartalom) is kimutatható.</li><li>*márkák* - észleli a különböző márkák egy képen belül, beleértve a hozzávetőleges helyét. A *márkák* vizuális funkciója csak angol nyelven érhető el.</li><li> *kategóriák* - kategorizálja a képtartalmat a Cognitive Services [Computer Vision dokumentációjában](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)meghatározott besorolás szerint. </li><li>*description* - a kép tartalmát egy teljes mondattal írja le támogatott nyelveken.</li><li>*arcok* - érzékeli, ha arcok vannak jelen. Ha jelen van, koordinátákat, nemet és életkort hoz létre.</li><li> *objektumok* - érzékeli a különböző objektumok egy képen belül, beleértve a hozzávetőleges helyét. Az *objektumok* vizuális funkciója csak angol nyelven érhető el.</li><li> *címkék* - címkék a kép egy részletes listát a szavak kapcsolatos kép tartalmát.</li></ul> A vizuális szolgáltatások nevei ben a kis- és nagybetűk et is figyelembe kell. Ne feledje, hogy a *szín* és az *imageType* vizuális funkciói elavultak, de ez a funkció továbbra is elérhető egy [egyéni szakértelemsegítségével.](https://go.microsoft.com/fwlink/?linkid=2121117)|
| Részletek   | Karakterláncok tömbje, amely jelzi, hogy melyik tartományspecifikus részletet adja vissza. Az érvényes vizuális szolgáltatástípusok a következők: <ul><li>*hírességek* - azonosítja hírességek, ha érzékeli a képen.</li><li>*tereptárgyak* - azonosítja a tereptárgyakat, ha az észlelhető a képen. </li></ul> |

## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Összetett típus. Jelenleg csak akkor működik, "/document/normalized_images" mező, amelyet az Azure Blob indexelő, ha ```imageAction``` van beállítva, hogy ```none```a. További információt a [mintában](#sample-output) talál.|



##  <a name="sample-skill-definition"></a>Minta szakértelem-definíció

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
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
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Mintaindex (csak a kategóriák, leírás, lapok és címkék mezők esetében)
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
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>A kimeneti mező leképezésének változása (beágyazott tulajdonságok)

A kimeneti mezőleképezéseket alacsonyabb szintű tulajdonságokhoz, például csak tájékozódási pontokhoz vagy hírességekhez definiálhatja. Ebben az esetben győződjön meg arról, hogy az indexséma rendelkezik egy mezővel, amely kifejezetten a tájékozódási pontokat tartalmazza.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Mintabevitel

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
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
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
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Hibaesetek
A következő hibaesetekben a rendszer nem von ki elemeket.

| Hibakód | Leírás |
|------------|-------------|
| Nem támogatott nyelv | A megadott nyelv nem támogatott. |
| InvalidImageUrl | A kép URL-címe rosszul formázott vagy nem érhető el.|
| ÉrvénytelenKépformátum | A bemeneti adatok nem érvényes lemezképek. |
| ÉrvénytelenImageSize | A bemeneti kép túl nagy. |
| Nem támogatott vizuális funkció  | A megadott szolgáltatástípus érvénytelen. |
| NotSupportedImage | Nem támogatott kép, például gyermekpornográfia. |
| InvalidDetails (InvalidDetails) | Nem támogatott tartományspecifikus modell. |

Ha a hiba hasonló `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`a hoz, ellenőrizze az elérési utat. Mind a hírességek, mind `detail`a tereptárgyak a tulajdonságalatt találhatók.

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

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
