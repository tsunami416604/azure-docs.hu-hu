---
title: 'Oktatóanyag: a végpont hosszúságú kimondott szöveg áttekintése – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban a Luis nem biztos, hogy a Luis HTTP-végponton keresztül kapott hosszúságú kimondott szöveg ellenőrzésével vagy javításával fejleszti az alkalmazások előrejelzéseit. Bizonyos kimondott szövegek esetében a szándékot, míg más kimondott szövegek esetében az entitást kell ellenőrizni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 3cecf334189989574e82772205c7d32298240867
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447820"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Oktatóanyag: a nem biztos előrejelzések kijavítása a végpontok hosszúságú kimondott szöveg áttekintésével
Ebben az oktatóanyagban a Luis HTTPS-végponton keresztül fogadott hosszúságú kimondott szöveg ellenőrzésével vagy javításával fejlesztheti az alkalmazás-előrejelzéseket, hogy a LUIS nem biztos benne. Tekintse át a végpontok hosszúságú kimondott szöveg az ütemezett LUIS-karbantartás rendszeres részeként.

Ez a felülvizsgálati folyamat lehetővé teszi a LUIS számára az alkalmazás tartományának megismerését. LUIS kiválasztja a felülvizsgálati listában megjelenő hosszúságú kimondott szöveg. Ez a lista a következő tulajdonságokkal rendelkezik:

* Az alkalmazásra jellemző.
* Az alkalmazás előrejelzési pontosságának fejlesztésére szolgál.
* Rendszeresen át kell tekinteni.

A végponti kimondott szövegek áttekintésével ellenőrizheti vagy kijavíthatja a kimondott szöveg előrejelzett szándékát.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * A végpont beszédmódjainak áttekintése
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Alkalmazás importálása – példa

Egy alkalmazás importálásához kövesse az alábbi lépéseket.

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

1. Az [előnézeti Luis-portálon](https://preview.luis-ai)importálja a. JSON fájlt egy új alkalmazásba.

1. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `review` nevet.

    > [!TIP]
    > Az alkalmazás módosítása előtt az új verzióra történő klónozás ajánlott eljárás. Ha befejez egy verziót, exportálja a verziót (. JSON-vagy. lu-fájlként), és keresse meg a fájlt a verziókövetés rendszerében.


1. Az alkalmazás betanításához válassza a **betanítás**lehetőséget.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Hosszúságú kimondott szöveg hozzáadása a végponton

Ebben az alkalmazásban szándékai és entitásai vannak, de nem rendelkezik végpont-használattal. Ez a végpont-használat szükséges ahhoz, hogy az alkalmazás javítsa a végpontok teljes áttekintését.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. A végpont használatával adja hozzá a következő hosszúságú kimondott szöveg.

    |Végpontok kimondása|Igazított leképezés|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy melyik verziót szerkeszti, vagy az alkalmazás melyik verziója lett közzétéve a végponton.

## <a name="review-endpoint-utterances"></a>A végpont beszédmódjainak áttekintése

Tekintse át a végpont hosszúságú kimondott szöveg a megfelelően igazított szándékhoz. Habár a hosszúságú kimondott szöveg egyetlen készlete van, amely minden verzióban áttekinthető, a szándék megfelelő igazításának folyamata csak a jelenlegi _aktív modellhez_ járul hozzá a példaként.

1. A portál **Létrehozás** szakaszában válassza a bal oldali navigációs sávon a **végpont hosszúságú kimondott szöveg áttekintése** elemet. A lista az **ApplyForJob** szándék szerint van szűrve.

    > [!div class="mx-imgBorder"]
    > ![a bal oldali navigációs végpont hosszúságú kimondott szöveg gombjának áttekintése](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Ez a Kimondás, `I'm looking for a job with Natural Language Processing`, nem a megfelelő szándékú.

1.  Ha ezt a megjelölést szeretné igazítani, a teljes sorban válassza ki a `GetJobInformation`megfelelő **igazított** célját. A jelölőnégyzet bejelölésével adja hozzá a megváltozott kiírást az alkalmazáshoz.

    > [!div class="mx-imgBorder"]
    > ![a bal oldali navigációs végpont hosszúságú kimondott szöveg gombjának áttekintése](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Tekintse át a további hosszúságú kimondott szöveg ebben a szándékban, és szükség szerint javítsa ki az igazított szándékot. Ebben az oktatóanyagban a kezdeti lemondás tábla segítségével tekintheti meg az igazított szándékot.

    A **felülvizsgálati végpont hosszúságú kimondott szöveg** listájának már nem kell a korrigált hosszúságú kimondott szöveg. Ha további hosszúságú kimondott szöveg jelennek meg, folytassa a listát, és javítsa a igazított leképezéseket, amíg a lista üres nem lesz.

    Az entitások címkézésének minden kijavítása a szándék igazítása után történik, a szándék részletek lapról.

1. Tanítsa be és tegye ismét közzé az alkalmazást.

## <a name="get-intent-prediction-from-endpoint"></a>Leképezési előrejelzés beolvasása a végpontról

Ha ellenőrizni szeretné, hogy a megfelelően igazított példa hosszúságú kimondott szöveg javította-e az alkalmazás előrejelzését, próbáljon meg egy teljes kizárót kijavítani.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez, és írja be a következőt: `Are there any natural language processing jobs in my department right now?`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Most, hogy a bizonytalan hosszúságú kimondott szöveg helyesen vannak igazítva, a megfelelő szándékot **magas pontszámot**jósolta meg.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Az áttekintés helyettesíthető további kimondott szövegek hozzáadásával?
Felmerülhet a kérdés, hogy miért ne adhatna hozzá további példaként szolgáló kimondott szövegeket. Mi a végponti kimondott szövegek áttekintésének célja? Egy valós LUIS-alkalmazásban a végponti kimondott szövegek a felhasználóktól származnak, Ön által még nem használt szóhasználattal és elrendezéssel. Ha ugyanazt a szóhasználatot és elrendezést alkalmazta volna, az eredeti előrejelzés nagyobb százalékos értékkel rendelkezne.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Miért szerepel a felső szándék a kimondott szövegek listáján?
Néhány végponti kimondott szöveg magas előrejelzési pontszámmal szerepel az áttekintési listán. Ezeknek a kimondott szövegeknek az áttekintésére és ellenőrzésére ugyanúgy szükség van. Azért szerepelnek a listán, mert a következő legnagyobb pontszámú szándék a legfelső szándék pontszámához túl közeli pontszámmal rendelkezik. Körülbelül 15%-os különbséget szeretne az első két szándék között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során áttekintette a LUIS számára ismeretlen végponton elküldött kimondott szöveget. Miután ezek a kimondott szövegek ellenőrizve lettek, és át lettek helyezve a megfelelő szándékhoz kimondott példaszövegként, a LUIS javítja az előrejelzés pontosságát.

> [!div class="nextstepaction"]
> [Útmutató a minták használatához](luis-tutorial-pattern.md)
