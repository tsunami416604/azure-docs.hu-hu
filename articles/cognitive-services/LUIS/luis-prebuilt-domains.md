---
title: A nyelvi Understa előre összeállított tartományok
titleSuffix: Azure Cognitive Services
description: A LUIS előre összeállított tartományok esetében gyakori, természetes nyelvi felhasználói esetek rövid idő alatt hozzáadhat egy készletét tartalmazza.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 05c6981c4962827154764df0e874824e5398d96d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074400"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Gyakori használati forgatókönyvek az előre összeállított tartományok hozzáadása 

A LUIS tartalmaz egy, az előre összeállított tartományok gyorsan a közös leképezések és beszédmódok hozzáadása az előre összeállított leképezések. Ez az egy könnyen és gyorsan, anélkül, hogy ezek a képességek a modelleket tervezhet kellene képességek hozzáadása a természetes nyelvi ügyfélalkalmazás. 

## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

1. Az a **saját alkalmazások** lapon, válassza ki az alkalmazást. Ekkor megnyílik az alkalmazás a **összeállítása** szakaszában. 

1. Az a **leképezések** lapon jelölje be **előre összeállított tartományok hozzáadása** az alsó, bal oldali eszköztáron. 

1. Válassza ki a **naptár** szándékának akkor válassza **tartomány hozzáadása** gombra.

    ![Naptár előre összeállított tartomány hozzáadása](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Válassza ki **leképezések** a naptár leképezések megtekintéséhez a bal oldali navigációs. A tartomány minden leképezés a következő előtaggal kezdődik `Calendar.`. Beszédmódok, valamint a tartományhoz tartozó két entitás az alkalmazás kerülnek: `Calendar.Location` és `Calendar.Subject`. 

## <a name="train-and-publish"></a>Betanítás és közzététel

1. Miután hozzáadta a tartományt, az alkalmazás betanításához kiválasztásával **betanításához** TOP, jobb gombbal az eszköztáron. 

1. A felső eszköztáron válassza **közzététel**. Közzététele **éles**. 

1. Ha a zöld, sikeres értesítés jelenik meg, válassza ki a **tekintse meg a végpontok listáját** hivatkozásra kattintva megtekintheti a végpontok.

1. Válasszon végpontot. Egy új böngészőlapon nyílik meg, hogy a végpont. Ne zárja be a böngészőlapot, és továbbra is a **teszt** szakaszban.

## <a name="test"></a>Tesztelés

Teszt új célja a végponton által hozzáadott értéket a **q** paraméter: `Schedule a meeting with John Smith in Seattle next week`.

A LUIS értéket ad vissza, a megfelelő leképezés és értekezlet tulajdonos:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Előre összeállított útmutatója](./luis-reference-prebuilt-domains.md)
