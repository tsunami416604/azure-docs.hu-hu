---
title: Az API – QnA Maker GenerateAnswer metaadatok
titleSuffix: Azure Cognitive Services
description: QnA Maker segítségével adhat hozzá a metaadatokat, kulcs/érték párok formájában a kérdés-válasz csoportjai. Ezt az információt a felhasználói lekérdezések az eredmények szűréséhez és tárolására is használható információk követő témák használható.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim88
ms.openlocfilehash: 8712f9e79965e09e21df768b1c06561a896b8e01
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214092"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Metaadatok és a GenerateAnswer API használatával

QnA Maker segítségével adhat hozzá a metaadatokat, kulcs-érték párok formájában a kérdés-válasz csoportjai. Ezt az információt a felhasználói lekérdezések az eredmények szűréséhez és tárolására is használható információk követő témák használható. További információkért lásd: [Tudásbázis](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>A QnA entitás

Először fontos tudni, hogyan a QnA Maker a kérdés-válasz adatait tárolja. Az alábbi ábrán egy kérdés-válasz entitás:

![A QnA entitás](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden egyes QnA entitás rendelkezik egy egyedi és állandó. Az azonosító egy adott QnA entitás frissítéséhez használható.

## <a name="generateanswer-api"></a>GenerateAnswer API

A robot vagy alkalmazás a GenerateAnswer API használatával a Tudásbázisban hozhatja ki a legmegfelelőbb a a kérdés-válasz csoportok felhasználói kérdés a.

### <a name="generateanswer-endpoint"></a>GenerateAnswer endpoint

Miután, közzéteheti a tudásbázist, az a [QnA Maker portal](https://www.qnamaker.ai), vagy a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), kérheti a GenerateAnswer végpont adatait.

A végpont adatait beolvasni:
1. Jelentkezzen be [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. A **saját tudásbázisok**, kattintson a **nézet kód** a Tudásbázis számára.
![saját tudásbázisok](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. A GenerateAnswer végpontjával kapcsolatos részletek beszerzése.

![végpont részletei](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adatait is beszerezheti a **beállítások** a Tudásbázis lapján.

### <a name="generateanswer-request"></a>GenerateAnswer kérelem

A HTTP POST-kérelmet GenerateAnswer hívható meg. Az mintakódot, amely bemutatja, hogyan hívhat meg GenerateAnswer, lásd: a [útmutatóink](../quickstarts/csharp.md).

- **Kérelem URL-címe**: https://{QnA készítő végpontot} /knowledgebases/ {Tudásbázis-azonosító} / generateAnswer

- **A kérés paraméterei**: 
    - **Tudásbázis-azonosító** (karakterlánc): A Tudásbázis GUID azonosítója.
    - **QnAMaker végpont** (karakterlánc): Az Azure-előfizetésében üzembe helyezve a végpont állomásneve.
- **Kérelem fejlécei**
    - **A Content-Type** (karakterlánc): Az API-nak küldött törzs médiatípusa.
    - **Engedélyezési** (karakterlánc): A végpont kulcs (EndpointKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Kérelem törzse**
    - **kérdés** (karakterlánc): Egy felhasználó kérdést, szemben a Tudásbázis kérdezhető le.
    - **felső** (nem kötelező, csak egész szám): A kimenetben: rangsorolt eredmények száma. Az alapértelmezett érték az 1.
    - **felhasználói azonosító** (nem kötelező, csak karakterlánc): Azonosíthatja a felhasználó egyedi azonosítója. Ezt az Azonosítót a csevegési naplók lesznek rögzítve.
    - **strictFilters** (nem kötelező, csak karakterlánc): Ha meg van adva, arra utasítja a QnA Maker, a választ csak, amelyek rendelkeznek a megadott metaadatok. További információkért lásd az alábbi.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer válasz

- **Válasz 200** – sikeres hívás az kérdés eredményét adja vissza. A válasz a következő mezőket tartalmazzák:
    - **válaszok** -válaszokat a felhasználó lekérdezése, rangsorolási pontszám sorrendbe rendezett listáját.
        - **pontszám**: 0 és 100 közötti rangsorolási pontszám.
        - **kérdések**: A felhasználó által megadott kérdéseket.
        - **answer**: A választ a kérdésére.
        - **forrás**: A forrás, amelyről a választ ki kell olvasni vagy menti a Tudásbázis neve.
        - **metaadatok**: A válasz társított metaadatokat.
            - név: Metaadat-neve. (karakterlánc, a maximális hossz: 100, a szükséges)
            - value: Metaadat-értékben. (karakterlánc, a maximális hossz: 100, a szükséges)
        - **ID**: A válasz rendelt egyedi azonosítója.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>Metaadat-példa

Fontolja meg a gyakori kérdések adatok alatt. Metaadatok a metaadatok ikonra kattintva adja hozzá a tudásbázist.

![add metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters-for-metadata-tags"></a>A metaadat-címkéket strictFilters az eredmények szűréséhez

Fontolja meg a felhasználói kérdés "Ha nem zárja be ezt a Szálloda?" Ha a leképezés implicit az étterem egyik "Paradicsom."

Mivel eredményei csak az étterem egyik "Paradicsom" szükséges, beállíthat egy szűrőt GenerateAnswer hívásában metaadatokról (éttermek Name), módon.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Környezet megtartása
A GenerateAnswer adott válasz a következő tartalmazza az egyező kérdés-válasz készlet megfelelő metaadat-információkat.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

Ez az információ az előző beszélgetés használatra kontextusában rögzítéséhez újabb témák használható. 

## <a name="next-steps"></a>További lépések

A közzétételi oldalon is biztosít választ létrehozásához szükséges adatok [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) és [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./create-knowledge-base.md)
