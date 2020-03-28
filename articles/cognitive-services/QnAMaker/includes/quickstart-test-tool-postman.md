---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 46947579ea72e2199af116442472eec330b38009
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112349"
---
Ez a Postman-alapú rövid útmutató végigvezeti önt arról, hogy választ kapjon a tudásbázisából.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**postás**](https://www.getpostman.com/).
* Önnek rendelkeznie kell
    * [A QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md)
    * A betanított és közzétett tudásbázis a rövid útmutatóból származó [kérdésekkel és válaszokkal](../Quickstarts/add-question-metadata-portal.md) metaadatokkal és csevegéssel van konfigurálva.

> [!NOTE]
> Ha készen áll arra, hogy választ adjon egy kérdésre a tudásbázisából, be kell [tanítania](../Quickstarts/create-publish-knowledge-base.md#save-and-train) és közzé kell [tennie](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) tudásbázisát. A tudásbázis közzétételekor a **Közzététel** lap megjeleníti a HTTP-kérelem beállításait a válasz létrehozásához. A **Postman** lapon a válasz létrehozásához szükséges beállítások láthatók.

## <a name="set-up-postman-for-requests"></a>Postás beállítása kérésekhez

Ez a rövid útmutató ugyanazokat a beállításokat használja a Postman **POST** kéréshez, majd konfigurálja a POST body JSON-t, amelyet a lekérdezési cél alapján küld a szolgáltatásnak.

Ezzel az eljárással konfigurálhatja a Postmant, majd minden további szakaszt elolvashat a Post body JSON beállításához.

1. A tudásbázis **Beállítások** lapján válassza a **Postman** lapot a tudásbázisból a válasz létrehozásához használt konfiguráció megtekintéséhez. Másolja a következő adatokat a Postman programban való használatra.

    |Név|Beállítás|Cél és érték|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Ez az URL-cím HTTP-metódusa és útvonala.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Ez az URL-cím állomása. Fűzze össze a host és a Post értékeket a teljes generateAnswer URL-cím leválasztásához.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|A fejléc értéke, hogy engedélyezze a kérelmet az Azure-ba. |
    |`Content-type`|`application/json`|A tartalom fejlécértéke.|
    ||`{"question":"<Your question>"}`|A POST-kérelem törzse JSON-objektumként. Ez az érték az egyes következő szakaszokban megváltozik attól függően, hogy a lekérdezés mire készült.|

1. Nyissa meg a Postman t, és hozzon létre egy új alap **POST** kérelmet a közzétett tudásbázis-beállításokkal. A következő szakaszokban módosítsa a POST test JSON-t a lekérdezés tudásbázisra való módosításához.

## <a name="use-metadata-to-filter-answer"></a>Válasz szűrése metaadatokkal

Egy korábbi rövid útmutatóban a metaadatok két QnA-készlethez kerültek, hogy megkülönböztessék a két különböző kérdést. Adja hozzá a metaadatokat a lekérdezéshez, hogy a szűrőt csak a megfelelő QnA-készletre korlátozza.

1. A Postman alkalmazásban csak a JSON `strictFilters` lekérdezést módosítsa úgy, `service:qna_maker`hogy hozzáadja a tulajdonságot a nevével/értékpárjával. A test JSON kell:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    A kérdés csak egy `size`szó, amely a két kérdés-válasz halmaz bármelyikét visszatudja adni. A `strictFilters` tömb azt mondja a `qna_maker` választ, hogy csökkentsék csak a válaszokat.

1. A válasz csak a szűrőfeltételeknek megfelelő választ tartalmazza.

    Az olvashatóság érdekében a következő válasz lett formázva:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Ha van olyan kérdés-válasz készlet, amely nem felel meg a keresési kifejezésnek, de megfelelt a szűrőnek, akkor nem lesz visszaadva. Ehelyett az általános `No good match found in KB.` választ adja vissza.

## <a name="use-debug-query-property"></a>Hibakeresési lekérdezéstulajdonság használata

A hibakeresési információk segítenek megérteni, hogyan határozták meg a visszaadott választ. Bár ez hasznos, ez nem szükséges. Ha hibakeresési adatokkal szeretne választ `debug` adni, adja hozzá a tulajdonságot:

1. A Postman, változtatni csak a test `debug` JSON hozzáadásával az ingatlan. A JSON legyen:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. A válasz tartalmazza a választ. A következő JSON-kimenetben néhány hibakeresési részletet három ponttal cserélt le.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Teszttudás-alap használata

Ha választ szeretne kapni a teszttudásbázistól, `isTest` használja a testtulajdonságot.

A Postman, változtatni csak a test `isTest` JSON hozzáadásával az ingatlan. A JSON legyen:

```json
{
    'question':'size',
    'isTest': true
}
```

A JSON-válasz ugyanazt a sémát használja, mint a közzétett tudásbázis-lekérdezés.

> [!NOTE]
> Ha a teszt és a közzétett tudásbázisok pontosan azonosak, akkor is előfordulhat, hogy van némi eltérés, mert a tesztindex meg oszlik az erőforrás összes tudásbázisa között.

## <a name="query-for-a-chit-chat-answer"></a>Chit-chat válasz lekérdezése

1. A Postman, változtassa meg csak a szervezet JSON egy beszélgetés-végződő nyilatkozatot a felhasználótól. A JSON legyen:

    ```json
    {
        'question':'thank you'
    }
    ```

1. A válasz tartalmazza a pontszám és a válasz.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Mivel a `Thank you` utasításhoz tartozó kérdés pontosan megegyezik egy csevegési kérdéssel, a QnA Maker 100-as pontszámmal teljesen biztos a válaszban. A QnA Maker az összes kapcsolódó kérdést, valamint a Chit-chat metaadat-címkét tartalmazó metaadat-tulajdonságot is visszaadta.

## <a name="use-threshold-and-default-answer"></a>Küszöbérték és alapértelmezett válasz használata

A válasz minimális küszöbértékét kérheti. Ha a küszöbérték nem teljesül, az alapértelmezett választ adja vissza.

1. A Postman, változtassa meg csak a szervezet JSON egy beszélgetés-végződő nyilatkozatot a felhasználótól. A JSON legyen:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    A tudásbázis nem találja meg ezt a választ, mert a kérdés pontszáma 71%, és ehelyett a tudásbázis létrehozásakor megadott alapértelmezett választ adja vissza.

    A visszaadott JSON válasz, beleértve a pontszám és a válasz:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker vissza `0`adott egy pontszámot , ami azt jelenti, nincs bizalom. Az alapértelmezett választ is visszaadta.

1. Módosítsa a küszöbértéket 60%-ra, és kérje újra a lekérdezést:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    A visszaadott JSON megtalálta a választ.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```