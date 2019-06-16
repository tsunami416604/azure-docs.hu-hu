---
title: Az API – QnA Maker GenerateAnswer metaadatok
titleSuffix: Azure Cognitive Services
description: QnA Maker segítségével adhat hozzá a metaadatokat, kulcs/érték párok formájában a kérdés-válasz csoportjai. Szűrheti az eredményeket a felhasználói lekérdezések, és tárolására is használható információk követő témák.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: b18d47b4b09c6fa9c4d5f0ef87d7ebe73f151c60
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693240"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Válasz a GenerateAnswer API és a metaadatok

Az előre jelzett a felhasználó kérdésre adott válasz, amelyet a GenerateAnswer API-t. Amikor közzétesz egy Tudásbázis, láthatja az API használata az információ a **közzététel** lapot. Is konfigurálhatja a válaszok metaadatokat címkék alapján szűrheti az API-t, és a teszt lekérdezési karakterlánc paraméterrel a végpontról a Tudásbázis tesztelése.

QnA Maker segítségével adhat hozzá a metaadatokat, kulcs-érték párok formájában a kérdések és válaszok részhalmazához. Ezután használhatja ezeket az információkat, szűrheti az eredményeket a felhasználói lekérdezések és tárolására is használható információk követő témák. További információkért lásd: [Tudásbázis](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Kérdések és válaszok QnA entitáshoz Store

Fontos tudni, hogyan a QnA Maker a kérdés és válasz adatokat tároló. Az alábbi ábrán egy kérdés-válasz entitás:

![A QnA entitás ábrája](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden egyes QnA entitás rendelkezik egy egyedi és állandó. Az Alkalmazásazonosítót használhatja, hogy a frissítések adott QnA entitáson.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Első válasz előrejelzéseket GenerateAnswer API-val

A GenerateAnswer API-t a robot vagy alkalmazás használhatja a Tudásbázisban, a felhasználói kérdés, a legmegfelelőbb beszerezni a kérdés és válasz állítja be.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>GenerateAnswer végpont közzététele 

A Tudásbázis, vagy a közzététel után a [QnA Maker portal](https://www.qnamaker.ai), vagy a [API](https://go.microsoft.com/fwlink/?linkid=2092179), kérheti a GenerateAnswer végpont adatait.

A végpont adatait beolvasni:
1. Jelentkezzen be itt: [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. A **saját tudásbázisok**, jelölje be **nézet kód** a Tudásbázis számára.
    ![Képernyőfelvétel a My tudásbázisok](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. A GenerateAnswer végpontjával kapcsolatos részletek beszerzése.

    ![Képernyőkép a végpont részletei](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adatait is beszerezheti a **beállítások** a Tudásbázis lapján.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer kérelem konfiguráció

A HTTP POST-kérelmet GenerateAnswer hívható meg. Az mintakódot, amely bemutatja, hogyan hívhat meg GenerateAnswer, lásd: a [útmutatóink](../quickstarts/csharp.md).

A **kérelem URL-címe** formátuma a következő: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|HTTP-kérelem tulajdonság|Name (Név)|Típus|Cél|
|--|--|--|--|
|URL-cím útvonal-paraméter|Tudásbázis-azonosító|string|A Tudásbázis GUID azonosítója.|
|URL-cím útvonal-paraméter|QnAMaker végponti gazdagép|string|Az Azure-előfizetésében üzembe helyezve a végpont állomásneve. Ez az elérhető a **beállítások** lapon a Tudásbázis közzététele után. |
|Fejléc|Content-Type|string|Az API-nak küldött törzs médiatípusa. Alapértelmezett érték: ''|
|Fejléc|Engedélyezés|string|A végpont kulcs (EndpointKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Bejegyzés törzse|JSON-objektum|JSON|A kérdés beállításokkal.|


JSON-törzse többféle beállításokkal rendelkezik:

|JSON-törzse tulajdonság|Szükséges|Típus|Cél|
|--|--|--|--|
|`question`|Szükséges|string|Egy felhasználó kérdést kell küldeni a tudásbázist.|
|`top`|Nem kötelező|egész szám|A kimenetben: rangsorolt eredmények száma. Az alapértelmezett érték az 1.|
|`userId`|Nem kötelező|string|Azonosíthatja a felhasználó egyedi azonosítója. Ezt az Azonosítót a csevegési naplók lesznek rögzítve.|
|`scoreThreshold`|Nem kötelező|egész szám|Csak azokat a válaszokat, a rendszer magabiztossági pontszámot a küszöbérték fölött lesz visszaadva. Az alapértelmezett érték a 0.|
|`isTest`|Nem kötelező|Boolean|Ha igaz érték esetén eredményét adja vissza `testkb` Search-index közzétett index helyett.|
|`strictFilters`|Nem kötelező|string|Ha meg van adva, arra utasítja a QnA Maker, a választ csak, amelyek rendelkeznek a megadott metaadatok. Használat `none` jelzi a válasz nem fájlmetaadat-szűrők kell rendelkeznie. |
|`RankerType`|Nem kötelező|string|Ha a megadott `QuestionOnly`, arra utasítja a QnA Maker kérdések csak kereséséhez. Ha nincs megadva, a QnA Maker kérdéseket és válaszokat keres.

Egy példa JSON-törzse hasonlóan néz ki:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
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
|Metadata.Value|Metaadat-értékben. (karakterlánc, a maximális hossz: 100, a szükséges)|


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

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Metaadatok használata egyéni metaadatokat címkék alapján szűri az adott válaszok

Metaadatok hozzáadása lehetővé teszi, hogy a válaszok e metaadatokat címkék alapján szűri. A metaadatok oszlop a következő oszlop hozzáadása a **beállításai** menü. Kiválasztásával a metaadatokat ad hozzá metaadatokat a Tudásbázis **+** ikonra kattintva adja hozzá a metaadatokat pár. Ez a páros egy kulcs és a egy értéke áll.

![Képernyőkép a metaadatok hozzáadása](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>A metaadat-címkéket strictFilters az eredmények szűréséhez

Fontolja meg a felhasználói kérdés "Ha nem zárja be ezt a Szálloda?", ahol a cél implicit az étterem egyik "Paradicsom."

Eredmények csak a "Paradicsom" éttermi szükségesek, mert a metaadatok "Éttermi Name" GenerateAnswer hívásában beállíthatja a szűrőt. A következő példa ezt mutatja be:

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

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Kérdést és választ eredmények beszélgetés környezet használata

A GenerateAnswer adott válasz tartalmazza az egyező kérdést és választ készlet megfelelő metaadat-információkat. Ez az információ az ügyfélalkalmazásban található segítségével tárolja a korábbi beszélgetés használatra kontextusában újabb beszélgetések. 

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

## <a name="match-questions-only-by-text"></a>Kérdések, csak megfelelő szöveg szerint

Alapértelmezés szerint a QnA Maker – kérdések és válaszok keres. Ha azt szeretné, csak a kérdések keresgélnie, készítése a választ valamely kérdésre, használja a `RankerType=QuestionOnly` a GenerateAnswer kérelem bejegyzés törzse.

A közzétett kb is kereshet használatával `isTest=false`, vagy a tesztelési kb használatával `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>További lépések

A **közzététel** oldal is nyújt választ létrehozásához szükséges adatok [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) és [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./create-knowledge-base.md)
