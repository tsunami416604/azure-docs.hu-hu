---
title: Az API – QnA Maker GenerateAnswer metaadatok
titleSuffix: Azure Cognitive Services
description: QnA Maker segítségével adhat hozzá a metaadatokat, kulcs/érték párok formájában a kérdés-válasz csoportjai. Ezt az információt a felhasználói lekérdezések az eredmények szűréséhez és tárolására is használható információk követő témák használható.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.openlocfilehash: c18ededc428b215720f8a6a6857a2eabd93bff8b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683590"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>A Tudásbázis válasz GenerateAnswer API és metaadatok beolvasása

Az előre jelzett a felhasználó kérdésre adott válasz, amelyet a GenerateAnswer API-t. Tudásbázis közzététele, ha ezt az információt az API-t használja a közzétételi oldalon jelenik meg. Konfigurálja az API-válaszok metaadatokat címkék alapján szűrheti az is, és a teszt lekérdezési karakterlánc paraméterrel a végpontról a Tudásbázis tesztelése.

QnA Maker segítségével adhat hozzá a metaadatokat, kulcs-érték párok formájában a kérdés-válasz csoportjai. Ezt az információt a felhasználói lekérdezések az eredmények szűréséhez és tárolására is használható információk követő témák használható. További információkért lásd: [Tudásbázis](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>Kérdések és válaszok tárolására QnA entitáshoz

Először fontos tudni, hogyan a QnA Maker a kérdés-válasz adatait tárolja. Az alábbi ábrán egy kérdés-válasz entitás:

![A QnA entitás](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden egyes QnA entitás rendelkezik egy egyedi és állandó. Az azonosító egy adott QnA entitás frissítéséhez használható.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Első válasz előrejelzéseket GenerateAnswer API-val

A robot vagy alkalmazás a GenerateAnswer API használatával a Tudásbázisban hozhatja ki a legmegfelelőbb a a kérdés-válasz csoportok felhasználói kérdés a.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer végpont közzététele 

Miután, közzéteheti a tudásbázist, az a [QnA Maker portal](https://www.qnamaker.ai), vagy a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), kérheti a GenerateAnswer végpont adatait.

A végpont adatait beolvasni:
1. Jelentkezzen be itt: [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. A **saját tudásbázisok**, kattintson a **nézet kód** a Tudásbázis számára.
    ![saját tudásbázisok](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. A GenerateAnswer végpontjával kapcsolatos részletek beszerzése.

    ![végpont részletei](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adatait is beszerezheti a **beállítások** a Tudásbázis lapján.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer kérelem konfiguráció

A HTTP POST-kérelmet GenerateAnswer hívható meg. Az mintakódot, amely bemutatja, hogyan hívhat meg GenerateAnswer, lásd: a [útmutatóink](../quickstarts/csharp.md).

A **kérelem URL-címe** formátuma a következő: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|HTTP-kérelem tulajdonság|Name (Név)|Typo|Cél|
|--|--|--|--|
|URL-cím útvonal-paraméter|Tudásbázis-azonosító|sztring|A Tudásbázis GUID azonosítója.|
|URL-cím útvonal-paraméter|QnAMaker végponti gazdagép|sztring|Az Azure-előfizetésében üzembe helyezve a végpont állomásneve. Ez érhető el a beállítások lapon a Tudásbázis közzététele után. |
|Fejléc|Content-Type|sztring|Az API-nak küldött törzs médiatípusa. Alapértelmezett érték: ''|
|Fejléc|Engedélyezés|sztring|A végpont kulcs (EndpointKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Bejegyzés törzse|JSON-objektum|JSON|A kérdés beállításokkal|


JSON-törzse többféle beállításokkal rendelkezik:

|JSON-törzse tulajdonság|Szükséges|Typo|Cél|
|--|--|--|--|
|`question`|szükséges|sztring|Egy felhasználó kérdést kell küldeni a tudásbázist.|
|`top`|választható|egész szám|A kimenetben: rangsorolt eredmények száma. Az alapértelmezett érték az 1.|
|`userId`|választható|sztring|Azonosíthatja a felhasználó egyedi azonosítója. Ezt az Azonosítót a csevegési naplók lesznek rögzítve.|
|`isTest`|választható|logikai|Ha igaz érték esetén eredményét adja vissza `testkb` Search-index közzétett index helyett.|
|`strictFilters`|választható|sztring|Ha meg van adva, arra utasítja a QnA Maker, a választ csak, amelyek rendelkeznek a megadott metaadatok. Használat `none` jelzi a válasz nem fájlmetaadat-szűrők kell rendelkeznie. |

Egy példa JSON-törzse hasonlóan néz ki:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer válasz tulajdonságai

A sikeres válasz állapota 200-as és a egy JSON-választ adja vissza. 

|Válaszok tulajdonság (pontszám szerint rendezve)|Cél|
|--|--|
|pontszám|0 és 100 közötti rangsorolási pontszám.|
|Azonosító|A válasz rendelt egyedi azonosítója.|
|Kérdések|A felhasználó által megadott kérdéseket.|
|Válasz|A választ a kérdésére.|
|source|A forrás, amelyről a választ ki kell olvasni vagy menti a Tudásbázis neve.|
|metaadatok|A válasz társított metaadatokat.|
|metadata.name|Metaadat-neve. (karakterlánc, a maximális hossz: 100, a szükséges)|
|metadata.value: Metaadat-értékben. (karakterlánc, a maximális hossz: 100, a szükséges)|


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

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>Metaadatok segítségével lehetővé teszi egyéni metaadatokat címkék alapján szűri az adott válaszok

Metaadatok hozzáadása lehetővé teszi, hogy a válaszok e metaadatokat címkék alapján szűri. Fontolja meg a gyakori kérdések adatok alatt. Metaadatok a metaadatok ikonra kattintva adja hozzá a tudásbázist.

![add metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>A metaadat-címkéket strictFilters az eredmények szűréséhez

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

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Kérdést és választ eredmények beszélgetés környezet használata

A GenerateAnswer adott válasz tartalmazza az egyező kérdés-válasz készlet megfelelő metaadat-információkat. Ezeket az adatokat tárolni a korábbi beszélgetés használatra kontextusában újabb beszélgetések használható az ügyfélalkalmazásban. 

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

## <a name="next-steps"></a>További lépések

A közzétételi oldalon is biztosít választ létrehozásához szükséges adatok [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) és [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./create-knowledge-base.md)
