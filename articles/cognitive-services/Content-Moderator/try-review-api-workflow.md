---
title: A REST API-konzol – a Content Moderator moderálás munkafolyamatok definiálása
titlesuffix: Azure Cognitive Services
description: Az Azure tartalom Moderator felülvizsgálati API-k segítségével egyéni munkafolyamatok és a küszöbértékek a tartalom szabályzatok alapján.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756585"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiálja és moderálás munkafolyamatok (REST)

Munkafolyamatokat olyan felhőalapú testre szabott szűrőket, amelyek segítségével hatékonyabban kezelni a tartalom. A munkafolyamatok számos különböző módon tartalom szűrése és a megfelelő a megfelelő műveletet szolgáltatás képes csatlakozni. Ez az útmutató bemutatja, hogyan használhatja a munkafolyamat REST API-k, az API-konzolról, munkafolyamatok létrehozásához és használatához. Miután megismerkedett az API-k szerkezete, egyszerűen az bármely REST-kompatibilis platform hívásokat a portot is.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) hely.

## <a name="create-a-workflow"></a>Munkafolyamat létrehozása

Létrehozása vagy frissítése egy munkafolyamatot, nyissa meg a **[munkafolyamat - létrehozása vagy frissítése](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API lapra hivatkozik, és válassza ki a gombot, a kulcs régiója (található ez a végpont URL-címe az az **hitelesítő adatok**  lapján a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/)). Ezzel elindítja az API-konzol, ahol egyszerűen hozhat létre és futtassa a REST API-hívások.

![A munkafolyamat - létrehozása vagy módosítása lapján körzet](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Adja meg REST-hívás paramétereit

Adjon meg értéket a **csapat**, **workflowname**, és **Ocp-Apim-Subscription-Key**:

- **csapat**: A csoport azonosítója, amely beállításakor létrehozott a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/) fiók (található a **azonosító** mezőt a vizsgálóeszköz hitelesítő adatok képernyőn).
- **workflowname**: A hozzáadandó új munkafolyamat (vagy egy meglévő nevét, ha frissíti a meglévő munkafolyamatok) neve.
- **Ocp-Apim-Subscription-Key**: A Content Moderator kulcs. A megtalálhatja a **beállítások** lapján a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com).

![A munkafolyamat - vagy frissítés konzol lekérdezési paraméterek és a fejlécek létrehozása](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Adja meg a munkafolyamat-definíció

1. Szerkessze a **kérelem törzse** adja meg a JSON-kérelem részleteit tartalmazó mezőben **leírása** és **típusa** (vagy `Image` vagy `Text`).
2. A **kifejezés**, másolja ki az alapértelmezett munkafolyamat JSON kifejezést. A végső JSON-karakterláncot kell kinéznie:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Egyszerű, összetett és még beágyazott kifejezésekre meghatározhatja a munkafolyamatok, az API használatához. A [munkafolyamat - létrehozási vagy frissítési](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentáció példái összetettebb logika rendelkezik.

### <a name="submit-your-request"></a>A kérelem beküldése
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** mezőben az `true`.

### <a name="examine-the-new-workflow"></a>Vizsgálja meg az új munkafolyamat

Az a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/)válassza **beállítások** > **munkafolyamatok**. Az új munkafolyamat meg kell jelennie a listában.

![Munkafolyamatok listáját felülvizsgálati eszköz](images/workflow-console-new-workflow.PNG)

Válassza ki a **szerkesztése** a munkafolyamathoz lehetőséget, majd nyissa meg a **Designer** fülre. Itt látható a JSON-logic-intuitív reprezentációját.

![A kijelölt munkafolyamat Tervező lap](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>A munkafolyamat adatainak beolvasása

Meglévő munkafolyamatok adatainak lekéréséhez lépjen a **[munkafolyamat - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API lapra hivatkozik, és válassza ki a gombot az Ön régiójában (a régiót, amelyben a kulcsát felügyelete).

![A munkafolyamat - Get-régió kiválasztása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereket, mint a fenti szakaszban. Győződjön meg arról, hogy ezúttal **workflowname** meglévő munkafolyamat neve.

![Lekérdezési paraméterek és a fejlécek](images/workflow-get-default.PNG)

Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** mező megjeleníti a munkafolyamat JSON formátumban, például a következőképpen:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>További lépések

- Munkafolyamatok használata [tartalom-jóváhagyás feladatok](try-review-api-job.md).