---
title: Jóváhagyás-munkafolyamatok – a Content Moderator
titlesuffix: Azure Cognitive Services
description: Munkafolyamatok használata a felülvizsgálati API-feladat műveletei automatizálására emberi hurok felülvizsgálatok a tartalomszabályzat és küszöbértékek alapján.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0a13d86afe3d395cb34f592b03c1eb9daa18076b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454851"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Jóváhagyás felülvizsgálatok a munkafolyamatok automatizálása

A Content Moderator magában foglalja, eszközök és API-munkafolyamatok kezelése. Munkafolyamatok használata a [tekintse át API-feladat műveletei](review-api.md) automatizálhatja az emberi hurok felülvizsgálat létrehozása a tartalomszabályzat és küszöbértékek alapján.

A felülvizsgálati API emberi felvenni a tartalom-jóváhagyás folyamat az alábbi lehetőségeket kínálja:

1. A **feladat** kezdő gépi támogatású képmoderálás és emberi műveletei létrehozása egy lépéssel, tekintse át.
1. A **tekintse át** emberi műveletei létrehozását, a moderálás lépés kívül tekintse át.
1. A **munkafolyamat** műveletek vizsgálata a küszöbértékek automatizáló munkafolyamatok kezelése, tekintse át létrehozása.

Ez a cikk ismerteti a **munkafolyamat** műveleteket. Olvassa el a [feladatokat, és áttekinti](review-api.md) további információ a tartalom-jóváhagyás – áttekintés feladatokat, és ellenőrzi.

Kivétel a **alapértelmezett** munkafolyamat az a legjobb módszer használatának első lépései a Tartalommoderátor ismertetése munkafolyamatokat.

## <a name="your-first-workflow"></a>Az első munkafolyamat

Az első munkafolyamat származik az a [tekintse át az eszköz csapat](https://contentmoderator.cognitive.microsoft.com/). Iratkozzon fel, ha még nem meg már.

Keresse meg a [tekintse át az eszköz által munkafolyamatok](Review-Tool-User-Guide/Workflows.md) képernyő a beállítások lapon. Megjelenik egy **alapértelmezett** munkafolyamat a következő képen látható módon:

![Content Moderator munkafolyamatok](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Nyissa meg az alapértelmezett munkafolyamat

Használja a **szerkesztése** beállítással nyissa meg a munkafolyamat szerkesztése oldal a következő képen látható módon: ![Content Moderator alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>A Tervező nézetben

Megjelenik a **Designer** fülre a munkafolyamathoz. A Tervező nézetben látható a következő lépéseket:

1. A **feltétel** ki kell értékelni a munkafolyamathoz. Ebben az esetben a munkafolyamat-hívások a Content Moderator a kép API és az ellenőrzések e a `isAdult` egyenlő kimeneti `true`.
1. A **művelet** kell elvégezni, ha a feltétel teljesül. Ebben az esetben a munkafolyamatot hoz létre egy tekintse át a vizsgálóeszközt Ha a `isAdult` kimenet `true`.

![Content Moderator alapértelmezett munkafolyamat - Tervező](images/default-workflow-designer.png)

### <a name="the-json-view"></a>A JSON-nézet

Válassza ki a **JSON** lapján megtekintheti a munkafolyamat a JSON-definícióját.

    {
        "Type": "Logic",
        If": {
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

### <a name="key-learning"></a>Kulcs tanulás

A Content Moderator a munkafolyamatok, könnyen konfigurálható és rugalmas. Ha a beépített tervező nem felel meg a követelményeknek, megírhatja a munkafolyamat-definíció a **JSON** formátumban. Majd használja a JSON-definíciót a [munkafolyamat API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) hozhat létre és kezelhet a munkafolyamatot az alkalmazásból.

## <a name="define-a-custom-workflow"></a>Egy egyéni munkafolyamatokat

A Content Moderator munkafolyamat-funkciók lehetővé teszik a meghatározása, és egyéni munkafolyamatok használatával. Használja a [tekintse át az eszköz munkafolyamatok útmutató](Review-Tool-User-Guide/Workflows.md) cikket, egy egyéni munkafolyamatokat. Ez a munkafolyamat a Content Moderator optikai Karakterfelismerés képesség segítségével szöveg kinyerése egy képet. Majd létrehoz egy felülvizsgálatot a felülvizsgálati eszközben.

### <a name="the-sample-image"></a>A képet

Mentse a [képet](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) helyi meghajtójára. A gyakorlatban ez a rendszerkép van szükség.

### <a name="the-designer-view"></a>A Tervező nézetben

Válassza ki a **Designer** fülre, és a [munkafolyamat létrehozása oktatóanyag](Review-Tool-User-Guide/Workflows.md) , egy egyéni munkafolyamatokat. Az alábbi képen látható a tervező **feltétel** megtekintése. Tekintse meg a következő oktatóanyagban a lépéseket további részeinek megtekintéséhez.

![A Content Moderator - munkafolyamat-feltétel](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>A JSON-nézet

Válassza ki a **JSON** lapján megtekintheti az egyéni munkafolyamat a következő JSON-definícióját. Figyelje meg a **If-majd** utasítások a JSON-definíciójában felelnek meg az Útmutató lépéseit, a Tervező nézettel.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Munkafolyamat-eredmény

A munkafolyamat a munkafolyamatok képernyőről, tesztelését követően a következő felülvizsgálat jön létre. Keresse meg a **kép** lapjára **tekintse át** áttekintésre megtekintéséhez.
A munkafolyamat létrehozása a felülvizsgálatot, mert az elsődleges feltétel tesztelése pozitív szöveg meglétének. A felülvizsgálat is ki van emelve a **`a`** címke a rendszerkép felülvizsgálat alatt.

![A Content Moderator – egyszerű munkafolyamat kimenete](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Speciális munkafolyamati szolgáltatással

### <a name="the-sample-image"></a>A képet

Ugyanaz, mint [képet](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) az előző szakaszban használt.

Azonban módosíthatja ezen idő körülbelül két ellenőrzés kombinált az elsődleges feltételt. Szöveg keresése, kívül ellenőrizze, hogy a szöveg rendelkezik-e bármilyen közönséges kifejezést. A munkafolyamat felülvizsgálat hoz létre, ha úgy találja, szöveges **és** cenzúrázása észleli.

### <a name="the-designer-view"></a>A Tervező nézetben

Módosíthatja a **feltétel** , egy **kombináció**, módosítsa a munkafolyamat. Az alábbi képen láthatja az új nézet a tervezőben látható.

![A Content Moderator – módosított munkafolyamat-feltétel](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>A JSON-nézet

Válassza ki a **JSON** lapján megtekintheti a módosított egyéni munkafolyamat a következő JSON-definícióját. Figyelje meg a **If-majd** utasítások a JSON-definíciójában felel meg a munkafolyamat hozzáadott új lépéseit.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Munkafolyamat-eredmény

Után újra a munkafolyamat tesztelése, meg, hogy létrejött-e nem áttekintése. Felülvizsgálat hiányában erősítse meg, lépjen a **kép** lapjára **tekintse át**.
A munkafolyamat nem hozott létre a felülvizsgálatot, mert nem sikerült a kinyert szöveg vulgáris.

![A Content Moderator – módosított munkafolyamat kimenete](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>A munkafolyamat API

A [munkafolyamat-műveletek](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) a alkalmazásprogramozási felülete a munkafolyamat funkciókat biztosítanak. Munkafolyamatokat hozhat létre, a munkafolyamat adatainak beolvasása és a munkafolyamat API-val a munkafolyamat-definíciók frissítéséhez.

### <a name="get-all-workflow-details"></a>[All] Get munkafolyamat részletei

A **munkafolyamat-Get** műveletet az alábbi ráfordítások fogad el:

- **csapat**: A csoport azonosítója, üzembe helyezésekor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: A munkafolyamat nevét. Használat `default` először.
- **Ocp-Apim-Subscription-Key**: Található a **beállítások** fülre. További információkért lásd az [Áttekintést](overview.md).

Ha a művelet sikeres, a **válasz állapota** van `200 OK` és a **válasz tartalma** be a munkafolyamat-definíció a JSON formátumban jeleníti meg.
További tudnivalókért olvassa el a [munkafolyamat API a konzolhoz rövid](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Létrehozni vagy frissíteni a munkafolyamat

A létrehozási és frissítési művelet lehetővé teszi, hogy a munkafolyamat létrehozása az API-ból.

A **munkafolyamat létrehozása vagy frissítése** műveletet az alábbi ráfordítások fogad el:

- **csapat**: A csoport azonosítója, üzembe helyezésekor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: A munkafolyamat nevét. Használat `default` először.
- **Ocp-Apim-Subscription-Key**: Található a **beállítások** fülre. További információkért lásd az [Áttekintést](overview.md).

Ha a művelet sikeres, a **válasz állapota** van `200 OK` és a **válasz tartalma** mezőben az `true`. További információért [kipróbálása a `Create` művelet](try-review-api-job.md).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre egyéni munkafolyamatokat, tekintse át a [eszköz munkafolyamat-oktatóanyag áttekintésével](Review-Tool-User-Guide/Workflows.md). 

Próbálja ki a [munkafolyamat API-konzol](try-review-api-job.md) , és a REST API-Kódminták. 

Végül a az egyéni munkafolyamatok a **feladat** operations, ahogyan [feladat API-konzol](try-review-api-job.md) és a [feladatok .NET – rövid útmutató](moderation-jobs-quickstart-dotnet.md).
