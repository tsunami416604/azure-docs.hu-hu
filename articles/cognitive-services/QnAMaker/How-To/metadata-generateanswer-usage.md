---
title: Metaadatok használata a Tudásbázis és a GenerateAnswer API-val |} A Microsoft Docs
description: Metaadatok GenerateAnswer API használatával
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: e1b7c82e6998705bdc7e1c1a5d279bda7793667a
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43305199"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Metaadatok és a GenerateAnswer API használatával

QnA Maker segítségével adhat hozzá a metaadatokat, kulcs/érték párok formájában a kérdés-válasz csoportjai. Ezt az információt a felhasználói lekérdezések az eredmények szűréséhez és tárolására is használható információk követő témák használható. További információkért lásd: [Tudásbázis](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>A QnA entitás

Először fontos tudni, hogyan a QnA Maker a kérdés-válasz adatait tárolja. Az alábbi ábrán egy kérdés-válasz entitás:

![A QnA entitás](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden egyes QnA entitás rendelkezik egy egyedi és állandó. Az azonosító egy adott QnA entitás frissítéséhez használható.

## <a name="generateanswer-api"></a>GenerateAnswer API

A robot vagy alkalmazás a GenerateAnswer API használatával a Tudásbázisban hozhatja ki a legmegfelelőbb a a kérdés-válasz csoportok felhasználói kérdés a.

### <a name="generateanswer-endpoint"></a>GenerateAnswer végpont

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
    - **Tudásbázis-azonosító** (karakterlánc): a Tudásbázis a GUID-azonosító.
    - **QnAMaker végpont** (karakterlánc): az Azure-előfizetésében üzembe helyezve a végpont állomásneve.
- **Kérelem fejlécei**
    - **A Content-Type** (karakterlánc): az API-nak küldött törzs médiatípusa.
    - **Engedélyezési** (karakterlánc): A végpont kulcs (EndpointKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).
- **Kérelem törzse**
    - **kérdés** (karakterlánc): egy felhasználó kérdést, szemben a Tudásbázis kérdezhető le.
    - **felső** (nem kötelező, csak egész szám): a kimenetben: rangsorolt eredmények száma. Az alapértelmezett érték az 1.
    - **felhasználói azonosító** (nem kötelező, csak karakterlánc): egy egyedi Azonosítót a felhasználó azonosítására. Ezt az Azonosítót a csevegési naplók lesznek rögzítve.
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
        - **kérdések**: az a felhasználó által megadott kérdésre.
        - **válasz**: A választ a kérdésére.
        - **forrás**: a forrás, amelyről a választ ki kell olvasni vagy menti a Tudásbázis nevére.
        - **metaadatok**: a válasz társított metaadatokat.
            - Name: metaadat neve. (string, maximum: 100, a szükséges)
            - érték: metaadat-értékben. (string, maximum: 100, a szükséges)
        - **ID**: a válasz rendelt egyedi azonosító.
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

Vegye figyelembe az alábbi Hyderabad az éttermek – gyakori kérdések adatait. Metaadatok a fogaskerék ikonra kattintva adja hozzá a tudásbázist.

![metaadatok hozzáadása](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>StrictFilters az eredmények szűréséhez

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

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./create-knowledge-base.md)
