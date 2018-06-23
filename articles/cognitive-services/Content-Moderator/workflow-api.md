---
title: Az Azure Content moderátor - moderálás munkafolyamatok |} Microsoft Docs
description: Munkafolyamatok használata tartalom moderálás.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 079fcd119f1536f9e76ca57fccc76538b3c3ed78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347194"
---
# <a name="moderation-workflows"></a>Moderálás munkafolyamatok

Tartalom moderátor magában foglalja, eszközök és API-k munkafolyamatok kezelése. A munkafolyamatok használhatja a [tekintse át API feladat operations](review-api.md) automatizálhatja a HR-részleg hurok felülvizsgálati létrehozását a tartalom házirendek és a küszöbértékek alapján.

A felülvizsgálati API emberi felügyeletet szerepeljenek a tartalom moderálás folyamat a következő lehetőséget kínál:

1. A **feladat** műveletek olyan gép támogatású moderálás és emberi, tekintse át egy lépésben létrehozása.
1. A **tekintse át** emberi műveletek létrehozása a moderálás lépés kívül tekintse át.
1. A **munkafolyamat** műveletek automatizálása a küszöbértékek vizsgálatát munkafolyamatok kezelése, tekintse át létrehozását.

Ez a cikk ismerteti a **munkafolyamat** műveletek. Olvassa el a [feladatokat, és ellenőrzi, hogy](review-api.md) feladatokat, és ellenőrzi, hogy további információt a tartalom moderálás áttekintése.

Kikérése a **alapértelmezett** munkafolyamat az a legjobb módszer a kezdéshez a tartalom moderátor ismertetése munkafolyamatok.

## <a name="your-first-workflow"></a>Az első munkafolyamat

Az első munkafolyamat részeként elérhető együtt csomagolt a [tekintse át az eszköz team](https://contentmoderator.cognitive.microsoft.com/). Regisztráljon, ha még nem meg már.

Keresse meg a [tekintse át az eszköz munkafolyamatok](Review-Tool-User-Guide/Workflows.md) képernyőn a beállítások lapon. Megjelenik egy **alapértelmezett** munkafolyamat a következő ábrán látható módon:

![Tartalom moderátor munkafolyamatok](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Nyissa meg az alapértelmezett munkafolyamat

Használja a **szerkesztése** elemmel nyithatja meg a munkafolyamat lap szerkesztésével, a következő ábrán látható módon: ![tartalom moderátor alapértelmezett munkafolyamat](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>A Tervező nézetben

Megjelenik a **Designer** a munkafolyamat-lapon. A Tervező nézetben látható a következő lépéseket:

1. A **feltétel** a munkafolyamat el lesz kiértékelve. Ebben az esetben a munkafolyamat hívások a tartalom moderátor tartozó kép API, és ellenőrzi, hogy a `isAdult` egyenlő kimeneti `true`.
1. A **művelet** végrehajtását, ha a feltétel teljesül. Ebben az esetben a munkafolyamat hoz létre egy tekintse át a felülvizsgálati eszköz Ha a `isAdult` kimenete `true`.

![Tartalom moderátor alapértelmezett munkafolyamat - Tervező](images/default-workflow-designer.png)

### <a name="the-json-view"></a>A JSON-nézet

Válassza ki a **JSON** fülre, és tekintse meg a munkafolyamat JSON-definícióból.

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

A tartalom moderátor munkafolyamatok könnyen konfigurálható és rugalmas. Ha a beépített designer nem felel meg a követelményeknek, írni a munkafolyamat-definíciót a **JSON** formátumban. A JSON-definíció használja a [munkafolyamat API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) létrehozásához és kezeléséhez a munkafolyamat az alkalmazásból.

## <a name="define-a-custom-workflow"></a>Adja meg egy egyéni munkafolyamat

Tartalom moderátor munkafolyamat tehát lehetővé teszi a meghatározását, és olyan egyéni munkafolyamatokat. Használja a [tekintse át az eszköz munkafolyamatok – útmutató](Review-Tool-User-Guide/Workflows.md) cikk egy egyéni munkafolyamat meghatározásához. Ez a munkafolyamat tartalom moderátor OCR funkció használatával szöveg kinyerése a minta-lemezkép. A felülvizsgálati eszköz majd létre áttekintése.

### <a name="the-sample-image"></a>A minta kép

Mentse a [minta kép](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) helyi meghajtójára. A gyakorlatban ez a rendszerkép van szükség.

### <a name="the-designer-view"></a>A Tervező nézetben

Válassza ki a **Designer** lapon és a [munkafolyamat létrehozása oktatóanyag](Review-Tool-User-Guide/Workflows.md) egyéni munkafolyamat meghatározásához. A következő kép bemutatja a tervező **feltétel** nézet. Tekintse meg az oktatóanyag a további lépéseket.

![Tartalom moderátor - munkafolyamat-feltétel](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>A JSON-nézet

Válassza ki a **JSON** lapon a következő JSON-definícióból egyéni munkafolyamatba való megjelenítéséhez. Értesítés az **Ha-akkor** JSON-definícióból lévő utasítások felel meg a Tervező nézetben megadott lépéseket.

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

### <a name="workflow-result"></a>Munkafolyamat eredménye

A munkafolyamatok képernyőről munkafolyamat tesztelése után a következő felülvizsgálati jön létre. Keresse meg a **kép** lap **tekintse át** felülvizsgálandó megjelenítéséhez.
A munkafolyamat létrehozása a felülvizsgálati mivel az elsődleges feltétel tesztel pozitív szöveg jelenlétét. A kijelölt is tekintse át a **`a`** címke a kép felülvizsgálat alatt.

![Tartalom moderátor - egyszerű munkafolyamat kimeneti](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Speciális munkafolyamati értékkombinációit

### <a name="the-sample-image"></a>A minta kép

Ugyanazon [minta kép](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) az előző szakaszban leírt használt.

Azonban módosíthatja a időre körülbelül két ellenőrzés kombinációjává a elsődleges feltételt. Mellett szöveg keresése, ellenőrizze, hogy a szöveg bármely Profanitás van-e. A munkafolyamat áttekintése hoz létre, ha úgy találja, szöveges **és** Profanitás észleli.

### <a name="the-designer-view"></a>A Tervező nézetben

Módosíthatja a **feltétel** való egy **kombinációja**, a munkafolyamat módosítása. Az alábbi ábrán láthatja az új nézet a tervezőben.

![Tartalom moderátor - módosítási munkafolyamat-feltétel](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>A JSON-nézet

Válassza ki a **JSON** lapon a következő JSON-definícióból módosított egyéni munkafolyamatba való megjelenítéséhez. Értesítés az **Ha-akkor** JSON-definícióból lévő utasítások felel meg a munkafolyamat hozzáadott új lépéseit.

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

    
### <a name="workflow-result"></a>Munkafolyamat eredménye

Után ismét ellenőrizze a munkafolyamatot, meg, hogy nincs felülvizsgálati jön létre. Erősítse meg a felülvizsgálati hiányában, lépjen a **kép** lap **tekintse át**.
A munkafolyamat nem hozta létre a tekintse át, mert nem sikerült Profanitás észlelheti a kibontott szöveg.

![Tartalom moderátor - módosítási munkafolyamat kimeneti](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>A munkafolyamat API

A [a munkafolyamat műveletei](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) az alkalmazásprogramozási felület a munkafolyamat-funkciókat biztosítanak. Munkafolyamatok létrehozása, a munkafolyamat részletes és a munkafolyamat API-jával munkafolyamat-definíciók frissítése.

### <a name="get-all-workflow-details"></a>[All] Get munkafolyamat részletei

A **munkafolyamat-Get** művelet elfogadja az alábbi adatokat:

- **Team**: A csoport azonosítója, amelyet beállításakor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: a munkafolyamat nevét. Használjon `default` kezd.
- **Az OCP-Apim-előfizetés-kulcs**: található a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

Ha a művelet sikeres, a **válaszállapot** van `200 OK` és a **válasz tartalom** jelenít meg a munkafolyamat-definíciót a JSON formátumban.
További tudnivalókért olvassa el a [munkafolyamat API konzol gyors üzembe helyezés](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Létrehozásának vagy frissítésének munkafolyamata

A létrehozási és frissítési művelet lehetővé teszi, hogy az API-munkafolyamat létrehozásakor.

A **munkafolyamat létrehozása vagy frissítése** művelet elfogadja az alábbi adatokat:

- **Team**: A csoport azonosítója, amelyet beállításakor hozott létre a [tekintse át az eszköz fiók](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: a munkafolyamat nevét. Használjon `default` kezd.
- **Az OCP-Apim-előfizetés-kulcs**: található a **beállítások** fülre. További információkért lásd: [áttekintése](overview.md).

Ha a művelet sikeres, a **válaszállapot** van `200 OK` és a **válasz tartalom** mezőben az `true`. További, [tesztelése a `Create` művelet](try-review-api-job.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan hozzon létre egyéni munkafolyamatokat, tekintse meg a [tekintse át az eszköz munkafolyamat oktatóanyag](Review-Tool-User-Guide/Workflows.md). 

Tesztelése a [munkafolyamat API konzol](try-review-api-job.md) és a REST API-Kódminták használja. 

Végül, használja az egyéni munkafolyamatok a **feladat** műveleteket, mint a shon [feladat API konzol](try-review-api-job.md) és a [feladatok .NET gyors üzembe helyezés](moderation-jobs-quickstart-dotnet.md).
