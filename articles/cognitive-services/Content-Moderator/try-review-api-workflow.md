---
title: Az Azure Content moderátor - moderálás munkafolyamatok az API-konzolról tartalom |} Microsoft Docs
description: Megtudhatja, hogyan használhatja a tartalom moderálás munkafolyamatokat az API-konzolról.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 700b2bea5e902141659266a94d61ceb810c1b802
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347134"
---
# <a name="workflows-from-the-api-console"></a>Az API-konzolról munkafolyamatok

Használja a [a munkafolyamat műveletei](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) Azure tartalom moderátor létrehozása vagy frissítése munkafolyamat vagy munkafolyamat részleteit a felülvizsgálati API használatával. A munkafolyamatok egyszerű, összetett és még beágyazott kifejezések az API használatával adhat meg. A munkafolyamatok jelennek meg a csoport nem használja a felülvizsgálati eszköz. A munkafolyamatok is használják a felülvizsgálati API feladat műveletek.

## <a name="prerequisites"></a>Előfeltételek

1. Lépjen a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/). Iratkozzon fel, ha még nem tette meg még. 
2. A felülvizsgálati eszköz alatt **beállítások**, jelölje be a **munkafolyamatok** lap, ahogy az a felülvizsgálati eszköz [munkafolyamat oktatóanyag](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Keresse meg a munkafolyamatok képernyő

Válassza ki a tartalom moderátor irányítópult **felülvizsgálati** > **beállítások** > **munkafolyamatok**. Megjelenik az alapértelmezett munkafolyamat.

  ![Alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Az alapértelmezett munkafolyamat JSON definíciójának beolvasása

Válassza ki a **szerkesztése** a munkafolyamat lehetőséget, majd válassza ki a **JSON** fülre. A következő JSON kifejezés jelenik meg:

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

## <a name="get-workflow-details"></a>Munkafolyamat részletes

Használja a **munkafolyamat - Get** műveletet a meglévő alapértelmezett munkafolyamat részletes.

A felülvizsgálati eszközben navigáljon a [hitelesítő adatok](Review-Tool-User-Guide/credentials.md#the-review-tool) szakasz.

### <a name="browse-to-the-api-reference"></a>Keresse meg az API-referencia

1. Az a **hitelesítő adatok** nézetben jelölje ki [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Ha a **munkafolyamat - létrehozása vagy frissítése** lap megnyitásakor, írja be a [munkafolyamat - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) hivatkozás.

### <a name="select-your-region"></a>Válassza ki a régiót

A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.

  ![Munkafolyamat - Get terület kiválasztása](images/test-drive-region.png)

  A **munkafolyamat - Get** API-konzol megnyitása.

### <a name="enter-parameters"></a>Paraméterek megadása

Adja meg az értékeket **team**, **workflowname**, és **Ocp-Apim-előfizetés-kulcs** (az Előfizetés kulcs):

- **Team**: A csoport azonosítója, amelyet beállításakor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: a munkafolyamat nevét. Használjon `default`.
- **Az OCP-Apim-előfizetés-kulcs**: található a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

  ![Lekérdezés-paraméterek és a fejlécek](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Az igénylés küldéséhez
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válaszállapot** van `200 OK`, és a **válasz tartalom** jelenít meg a következő JSON-munkafolyamat:

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

A felülvizsgálati eszközben navigáljon a [hitelesítő adatok](Review-Tool-User-Guide/credentials.md#the-review-tool) szakasz.

### <a name="browse-to-the-api-reference"></a>Keresse meg az API-referencia

Az a **hitelesítő adatok** nézetben jelölje ki [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). A **munkafolyamat - létrehozása vagy frissítése** lap megnyitásakor.

### <a name="select-your-region"></a>Válassza ki a régiót

A **nyílt API-t tesztelési konzol**, válassza ki azt a régiót, amely a leginkább leírja a tartózkodási helyét.

  ![Munkafolyamat - létrehozása vagy módosítása lapján terület kiválasztása](images/test-drive-region.png)

  A **munkafolyamat - létrehozása vagy frissítése** API-konzol megnyitása.

### <a name="enter-parameters"></a>Paraméterek megadása

Adja meg az értékeket **team**, **workflowname**, és **Ocp-Apim-előfizetés-kulcs** (az Előfizetés kulcs):

- **Team**: A csoport azonosítója, amelyet beállításakor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: az új munkafolyamat nevét.
- **Az OCP-Apim-előfizetés-kulcs**: található a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

  ![Munkafolyamat - vagy frissítés konzol lekérdezési paraméterek és a fejlécek létrehozása](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Adja meg a munkafolyamat-definíció

1. Szerkesztés a **Request body** mezőben adja meg a JSON-kérelmi részleteinek **leírása** és **típus** (kép vagy szöveg). 
2. A **kifejezés**, másolja az alapértelmezett munkafolyamat-kifejezés az előző szakaszban, ahogy az itt látható:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    A kérelem törzsében a következő JSON-kérelmi néz ki:

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
 
### <a name="submit-your-request"></a>Az igénylés küldéséhez
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **válaszállapot** van `200 OK`, és a **válasz tartalom** mezőben az `true`.

### <a name="check-out-the-new-workflow"></a>Tekintse meg az új munkafolyamat

A felülvizsgálati eszközt, jelölje ki **felülvizsgálati** > **beállítások** > **munkafolyamatok**. Az új munkafolyamat jelenik meg, és készen áll a használatra.

  ![Munkafolyamatok felülvizsgálati eszköz listája](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Tekintse át az új munkafolyamat részletes adatait

1. Válassza ki a **szerkesztése** a munkafolyamat lehetőséget, majd válassza ki a **Designer** és **JSON** lapokon.

   ![A kijelölt munkafolyamat-Tervező lap](images/workflow-console-new-workflow-designer.PNG)

2. A munkafolyamat a JSON-nézet megtekintéséhez válassza ki a **JSON** fülre.

## <a name="next-steps"></a>További lépések

* Összetettebb munkafolyamat, tekintse meg a [munkafolyamatok áttekintése](workflow-api.md).
* A munkafolyamatok használata [moderálás feladatok tartalom](try-review-api-job.md).
