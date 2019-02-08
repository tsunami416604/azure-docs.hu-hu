---
title: Az API-konzol - Content Moderator munkafolyamatokat tartalom-jóváhagyás
titlesuffix: Azure Cognitive Services
description: Az Azure Content Moderator a munkafolyamat-műveletek használatával létrehozhat vagy a munkafolyamat frissítésére, vagy a munkafolyamat adatainak lekérése a felülvizsgálati API-val.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1c18544a0fd135eb546660c442b865bf1249dfe5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883083"
---
# <a name="workflows-from-the-api-console"></a>Az API-konzol munkafolyamatokat

Használja a [munkafolyamat-műveletek](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) az Azure Content Moderator létrehozása vagy a munkafolyamat frissítésére, vagy a munkafolyamat adatainak lekérése a felülvizsgálati API-val. A munkafolyamatok számára egyszerű, összetett és még beágyazott kifejezésekre definiálható az API-val. A munkafolyamatok jelennek meg a felülvizsgálati eszköz használata a csapat számára. A munkafolyamatok is használja a felülvizsgálati API-feladat műveletei.

## <a name="prerequisites"></a>Előfeltételek

1. Nyissa meg a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com/). Iratkozzon fel, ha még tette. 
2. A felülvizsgálati eszközben alatt **beállítások**, jelölje be a **munkafolyamatok** lapon jelennek meg a felülvizsgálati eszköz [munkafolyamat oktatóanyag](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Tallózással keresse meg a munkafolyamatok képernyő

A Content Moderator irányítópultján válassza ki a **felülvizsgálati** > **beállítások** > **munkafolyamatok**. Egy alapértelmezett munkafolyamatot láthatja.

  ![Alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Az alapértelmezett munkafolyamat JSON-definíció beolvasása

Válassza ki a **szerkesztése** a munkafolyamathoz lehetőséget, majd válassza ki a **JSON** fülre. A következő JSON-kifejezés jelenik meg:

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>A munkafolyamat adatainak beolvasása

Használja a **munkafolyamat - Get** részletes a meglévő alapértelmezett munkafolyamat-művelet.

A felülvizsgálati eszköz nyissa meg a [hitelesítő adatok](Review-Tool-User-Guide/credentials.md#the-review-tool) szakaszban.

### <a name="browse-to-the-api-reference"></a>Tallózással keresse meg az API-referencia

1. Az a **hitelesítő adatok** nézetben válassza [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Ha a **munkafolyamat - létrehozási vagy frissítési** lap megnyitásakor, ugorjon a [munkafolyamat - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) hivatkozást.

### <a name="select-your-region"></a>Válassza ki a régiót

A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.

  ![A munkafolyamat - Get-régió kiválasztása](images/test-drive-region.png)

  A **munkafolyamat - Get** API-konzol megnyitása.

### <a name="enter-parameters"></a>Paraméterek megadása

Adjon meg értéket a **csapat**, **workflowname**, és **Ocp-Apim-Subscription-Key** (az előfizetési kulcs):

- **csapat**: A csoport azonosítója, üzembe helyezésekor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: A munkafolyamat nevét. Használat `default`.
- **Ocp-Apim-Subscription-Key**: Található a **beállítások** fülre. További információkért lásd az [Áttekintést](overview.md).

  ![Lekérdezési paraméterek és a fejlécek](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>A kérelem beküldése
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** jelenít meg az alábbi JSON-munkafolyamat:

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Munkafolyamat létrehozása

A felülvizsgálati eszköz nyissa meg a [hitelesítő adatok](Review-Tool-User-Guide/credentials.md#the-review-tool) szakaszban.

### <a name="browse-to-the-api-reference"></a>Tallózással keresse meg az API-referencia

Az a **hitelesítő adatok** nézetben válassza [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). A **munkafolyamat - létrehozási vagy frissítési** lap megnyitásakor.

### <a name="select-your-region"></a>Válassza ki a régiót

A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti.

  ![A munkafolyamat - létrehozása vagy módosítása lapján körzet](images/test-drive-region.png)

  A **munkafolyamat - létrehozási vagy frissítési** API-konzol megnyitása.

### <a name="enter-parameters"></a>Paraméterek megadása

Adjon meg értéket a **csapat**, **workflowname**, és **Ocp-Apim-Subscription-Key** (az előfizetési kulcs):

- **csapat**: A csoport azonosítója, üzembe helyezésekor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Az új munkafolyamat neve.
- **Ocp-Apim-Subscription-Key**: Található a **beállítások** fülre. További információkért lásd az [Áttekintést](overview.md).

  ![A munkafolyamat - vagy frissítés konzol lekérdezési paraméterek és a fejlécek létrehozása](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Adja meg a munkafolyamat-definíció

1. Szerkessze a **kérelem törzse** adja meg a JSON-kérelem részleteit tartalmazó mezőben **leírása** és **típusa** (képet vagy szöveget). 
2. A **kifejezés**, másolja ki az alapértelmezett munkafolyamat-kifejezést az előző szakaszból származó itt látható módon:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    A kérés törzse a következő JSON-kérelem hasonlóan néz ki:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>A kérelem beküldése
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válasz állapota** van `200 OK`, és a **válasz tartalma** mezőben az `true`.

### <a name="check-out-the-new-workflow"></a>Tekintse meg az új munkafolyamat

Válassza ki a vizsgálóeszközt **felülvizsgálati** > **beállítások** > **munkafolyamatok**. Az új munkafolyamat jelenik meg, és készen áll a használatra.

  ![Munkafolyamatok listáját felülvizsgálati eszköz](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Tekintse át az új munkafolyamat

1. Válassza ki a **szerkesztése** a munkafolyamathoz lehetőséget, majd válassza ki a **Designer** és **JSON** lapokon.

   ![A kijelölt munkafolyamat Tervező lap](images/workflow-console-new-workflow-designer.PNG)

2. A munkafolyamat JSON-nézet megtekintéséhez válassza ki a **JSON** fülre.

## <a name="next-steps"></a>További lépések

* Összetettebb munkafolyamat példák: a [munkafolyamatok áttekintése](workflow-api.md).
* Munkafolyamatok használata [tartalom-jóváhagyás feladatok](try-review-api-job.md).
