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
ms.openlocfilehash: f3a1a33b2fe859839deec587191b3b3a319c0cf8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77495522"
---
Ez a cURL-alapú rövid útmutató végigvezeti önt a rról, hogy választ kapjon a tudásbázisából.

## <a name="prerequisites"></a>Előfeltételek

* Legújabb [**cURL**](https://curl.haxx.se/).
* Önnek rendelkeznie kell
    * [A QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md)
    * Betanított és közzétett tudásbázis kérdésekkel és válaszokkal, az előző [rövid útmutatóból,](../Quickstarts/add-question-metadata-portal.md)amely metaadatokkal és csevegéssel van konfigurálva.

> [!NOTE]
> Ha készen áll arra, hogy választ adjon egy kérdésre a tudásbázisából, be kell [tanítania](../Quickstarts/create-publish-knowledge-base.md#save-and-train) és közzé kell [tennie](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) tudásbázisát. A tudásbázis közzétételekor a **Közzététel** lap megjeleníti a HTTP-kérelem beállításait a válasz létrehozásához. A **cURL** lapon láthatók a parancssori eszköz válaszának létrehozásához szükséges beállítások.

## <a name="use-metadata-to-filter-answer"></a>Válasz szűrése metaadatokkal

Használja az előző gyors tudásbázist, hogy metaadatokon alapuló választ keressen.

1. A tudásbázis **Beállítások** lapján válassza a **CURL** lapot egy példa cURL parancs megtekintéséhez, amely a tudásbázisból választ generál.
1. Másolja a parancsot szerkeszthető környezetbe (például szövegfájlba), hogy szerkeszthesse a parancsot. A kérdés értékét az alábbiak szerint szerkesztheti, `service:qna_maker` hogy a metaadatok a QnA-készletek szűrőjeként legyenek használva.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    A kérdés csak egy `size`szó, amely a két QnA-készlet bármelyikét visszatudja adni. A `strictFilters` tömb azt mondja a `qna_maker` választ, hogy csökkentsék csak a válaszokat.

1. A válasz csak a szűrőfeltételeknek megfelelő választ tartalmazza. Az olvashatóság érdekében a következő cURL-választ formázták:

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

```json
Debug: {Enable:true}
```

1. További információk megtekintéséhez szerkesztsd a cURL parancsot, hogy az tartalmazza a hibakeresési tulajdonságot.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```

1. A válasz tartalmazza a választ. A következő JSON kimenetben néhány hibakeresési részletet a rövidség kedvéért három ponttal helyettesített.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
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

A tulajdonság logikai érték.

```json
isTest:true
```

A cURL parancs így néz ki:

```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

A JSON-válasz ugyanazt a sémát használja, mint a közzétett tudásbázis-lekérdezés.

> [!NOTE]
> Ha a teszt és a közzétett tudásbázisok pontosan azonosak, akkor is előfordulhat, hogy van némi eltérés, mert a tesztindex meg oszlik az erőforrás összes tudásbázisa között.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Chit-chat válasz lekérdezése cURL használatával

1. A cURL-kompatibilis terminálon használjon a felhasználó robotbeszélgetés-befejezési utasítását, például `Thank you` a kérdést. Nincs más beállítandó tulajdonság.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```

1. Futtassa a cURL parancsot, és megkapja a JSON választ, beleértve a pontszámot és a választ.

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

## <a name="use-curl-with-threshold-and-default-answer"></a>CURL használata küszöbértékkel és alapértelmezett válaszal

A válasz minimális küszöbértékét kérheti. Ha a küszöbérték nem teljesül, az alapértelmezett választ adja vissza.

1. Használja a következő cURL parancsot, lecserélve a saját erőforrás nevét, tudásbázis-azonosítóját és végpontkulcsát, hogy 80%-os vagy annál nagyobb `size` küszöbértékkel kérjen választ. A tudásbázis nem találja meg ezt a választ, mert a kérdés pontszáma 71%, és ehelyett a tudásbázis létrehozásakor megadott alapértelmezett választ adja vissza.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Futtassa a cURL parancsot, és megkapja a JSON választ, beleértve a pontszámot és a választ.

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

## <a name="use-curl-with-threshold-and-default-answer"></a>CURL használata küszöbértékkel és alapértelmezett válaszal

A válasz minimális küszöbértékét kérheti. Ha a küszöbérték nem teljesül, az alapértelmezett választ adja vissza.

1. Add `threshold` hozzá a szálláshelyet, `size` hogy 80%-os vagy annál nagyobb küszöbértékkel kérj választ. A tudásbázis nem talál erre a választ, mert a kérdés pontszáma 71%. Az eredmény a tudásbázis létrehozásakor megadott alapértelmezett választ adja vissza.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Futtassa a cURL parancsot, és fogadja meg a JSON-választ.

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

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
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
