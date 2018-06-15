---
title: Metaadatok használata mellett a GenerateAnswer API a Tudásbázis |} Microsoft Docs
description: Metaadatok segítségével GenerateAnswer API-hoz
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "35349931"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Metaadatok és a GenerateAnswer API használatával

Kérdések és válaszok Maker lehetővé teszi ad hozzá metaadatokat, a kérdés-válasz beállítása kulcs/érték párok formájában. Ezt az információt a különböző módokon, például a felhasználói lekérdezések eredmények szűréséhez, bizonyos eredmények kiemelése és tárolására használható további adatokat követő témák használható. További információkért lásd: [Tudásbázis](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>Kérdések és válaszok entitás

Először fontos megérteni, hogyan kérdések és válaszok készítő eltárolja a kérdés-válasz. Az alábbi illusztráció egy kérdés-válasz entitás:

![Kérdések és válaszok entitás](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden egyes kérdések és válaszok entitás rendelkezik egy egyedi és állandó. Az azonosító is használható a frissítések adott kérdések és válaszok entitáson.

## <a name="generateanswer-api"></a>GenerateAnswer API

Egy felhasználó kérdést a legmegfelelőbb lekérése a kérdés-válasz beállítása a Tudásbázisban a GenerateAnswer API-t a Botot vagy alkalmazás használja.

### <a name="generateanswer-endpoint"></a>GenerateAnswer végpont

Ha közzéteszi a Tudásbázis, vagy a a [kérdések és válaszok készítő portal](https://www.qnamaker.ai), vagy a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), a GenerateAnswer végpont a részletekért.

A végpont adatait beolvasása:
1. Jelentkezzen be [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. A **a Tudásbázis körrel**, kattintson a **nézet kód** a Tudásbázis –.
![a Tudásbázis körrel](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Ezzel a GenerateAnswer végpont adatokat.

![végpont részletei](../media/qnamaker-how-to-metadata-usage/view-code.png)

A végpont adataiból is lehet a **beállítások** a Tudásbázis lapján.

### <a name="generateanswer-request"></a>GenerateAnswer kérelem

A HTTP POST-kérelmet GenerateAnswer hívható meg. Az mintakód bemutatja, hogyan hívhatja meg GenerateAnswer, tekintse meg a [quickstarts](../quickstarts/csharp.md).

- **Kérelem URL-CÍMÉT**: https://{QnA Maker végpont} {Tudásbázis Azonosítójú} /knowledgebases/ / generateAnswer

- **A kérelem paraméterei**: 
    - **Tudásbázis azonosító** (karakterlánc): a Tudásbázis a GUID-azonosító.
    - **QnAMaker végpont** (karakterlánc): a végpont telepítése az Azure-előfizetéshez az állomásnevet.
- **Kérelem fejlécei**
    - **Tartalomtípus** (karakterlánc): a szervezet az API-nak küldött adathordozó-típusát.
    - **Engedélyezési** (karakterlánc): A végpont-kulcs.
- **Kérés törzsében**
    - **kérdés** (karakterlánc): A felhasználói kérdés kérdezhető le a Tudásbázis ellen.
    - **felső** (nem kötelező, egész szám): a kimenetben a rangsorolt eredmények száma. Az alapértelmezett értéke 1.
    - **userId** (nem kötelező, karakterlánc): a felhasználó azonosítására egyedi Azonosítót. Ezt az Azonosítót a Csevegés naplók lesznek rögzítve.
    - **strictFilters** (nem kötelező, karakterlánc): Ha meg van adva, közli a kérdések és válaszok Maker csak a megadott metaadatok rendelkező választ. További információkért lásd az alábbi.
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

- **200-as válasz** -sikeres meghívását a kérdés eredményét adja vissza. A válasz a következő mezőket tartalmazza:
    - **válaszok** -választ a felhasználó lekérdezése, csökkenő pontszám prioritás szerinti sorrendben rendezett listája.
        - **pontszám**: 0 és 100 közötti rangsorolási pontszámot.
        - **kérdések**: az a felhasználó által megadott kérdéseket.
        - **forrás**: a forrás-, amelyből a válasz kivételi vagy menti a Tudásbázis nevére.
        - **metaadatok**: a válasz társított metaadatokat.
            - Name: metaadat neve. (karakterlánc, a maximális hossz: 100, a szükséges)
            - érték: metaadataihoz megadott érték. (karakterlánc, a maximális hossz: 100, a szükséges)
        - **Azonosító**: a válasz rendelt egyedi Azonosítót.
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

## <a name="metadata-example"></a>Metaadatok – példa

Vegye figyelembe az alábbi Hyderabad az éttermekben adatait gyakran ismételt kérdések. Metaadatok hozzáadása a Tudásbázis fogaskerék ikonra kattint.

![metaadatok hozzáadása](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Szűrő eredmények strictFilters

Fontolja meg a felhasználói kérdéshez "Ha nem zárja be a szállodák?" ahol nincs beleértve az a célt a a éttermi "Paradicsom."

Mivel az eredmény csak a "Paradicsom" éttermi szükséges, adhatók meg a szűrő GenerateAnswer hívásában a metaadatok "Éttermi Name", az alábbiak szerint.

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
A GenerateAnswer adott válasz tartalmazza a megfelelő metaadatok közül a megfelelő kérdés-válasz, az alábbiak szerint.

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

Ez az információ az előző beszélgetés használatra keretében rögzítéséhez újabb témák használható. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./create-knowledge-base.md)
