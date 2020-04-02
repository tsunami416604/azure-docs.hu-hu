---
title: 'Oktatóanyag: Végpontkimondott szöveg áttekintése – LUIS'
description: Ebben az oktatóanyagban javítsa az alkalmazás-előrejelzéseket a LUIS HTTP-végponton keresztül kapott kimondott szövegek ellenőrzésével vagy javításával, amelyben a LUIS nem biztos. Bizonyos kimondott szövegek esetében a szándékot, míg más kimondott szövegek esetében az entitást kell ellenőrizni.
services: cognitive-services
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 307c18d3326cb1a64b884463a571985a015834ed
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548729"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Oktatóanyag: Javítsa ki a bizonytalan előrejelzéseket a végpontkimondott szöveg áttekintésével
Ebben az oktatóanyagban javítsa az alkalmazás-előrejelzéseket a LUIS HTTPS-végponton keresztül kapott kimondott szövegek ellenőrzésével vagy javításával, amelyben a LUIS nem biztos. Tekintse át a végpont kimondott szövegrendszeres részeként az ütemezett LUIS-karbantartás.

Ez az ellenőrzési folyamat lehetővé teszi a LUIS számára, hogy megismerje az alkalmazástartományt. A LUIS kiválasztja a felülvizsgálati listában megjelenő kimondott szövegeket. Ez a lista a következő tulajdonságokkal rendelkezik:

* Az alkalmazásra jellemző.
* Az alkalmazás előrejelzési pontosságának fejlesztésére szolgál.
* Rendszeresen át kell tekinteni.

A végponti kimondott szövegek áttekintésével ellenőrizheti vagy kijavíthatja a kimondott szöveg előrejelzett szándékát.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Példaalkalmazás importálása
> * A végpont beszédmódjainak áttekintése
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Példaalkalmazás importálása

Az alábbi lépésekkel importálhat egy alkalmazást.

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitásmódosítások alkalmazásra való alkalmazásához

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való eléréséhez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Kimondott szöveg hozzáadása a végponthoz

Ebben az alkalmazásban leképezések és entitások, de nincs végpont-használat. Ez a végpont használat szükséges az alkalmazás a végpont utterance (kifejezés) felülvizsgálattal az alkalmazás javítása.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le _YOUR_QUERY_HERE_ az alábbi táblázatban szereplő kimondott szövegekkel. Minden utterance (kifejezés) küldje el az utterance (kifejezés) és az eredmény. Ezután cserélje le az utterance (kifejezés) végén a következő utterance (kifejezés) helyett.

    |Végpont kimondott szövege|Igazított szándék|
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

Tekintse át a végpont kimondott szövegeket a megfelelően igazított szándék. Míg az összes verzióban felülvizsgálandó kimondott szövegkészlet, a szándék megfelelő igazításának folyamata csak az aktuális _aktív modellhez_ adja hozzá a példa utterance (kifejezés) értéket.

1. A **portál Build** szakaszában válassza **a Végpontkimondott szöveg áttekintése** a bal oldali navigációs. A lista az **ApplyForJob** szándék szerint van szűrve.

    > [!div class="mx-imgBorder"]
    > ![A bal oldali navigációs menüben található Végponti kimondott szövegek áttekintése gomb képernyőképe](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Ez az `I'm looking for a job with Natural Language Processing`utterance (kifejezés), a , nem a megfelelő szándékkal.

1.  Az utterance (kifejezés) igazításához az utterance (kifejezés) sorban válassza ki a megfelelő **igazított szándékot.** `GetJobInformation` Adja hozzá a módosított utterance (kifejezés) az alkalmazáshoz a pipa kiválasztásával.

    > [!div class="mx-imgBorder"]
    > ![A bal oldali navigációs menüben található Végponti kimondott szövegek áttekintése gomb képernyőképe](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Tekintse át a fennmaradó utterances ebben a szándékban, szükség szerint korrigálja az igazított szándékot. Használja a kezdeti utterance (kifejezés) tábla ebben az oktatóanyagban az igazított szándék megtekintéséhez.

    A **felülvizsgálati végpont utterances** lista már nem rendelkezik a javított utterances. Ha több kimondott szöveg jelenik meg, folytassa a munkát a listán, javítsa ki az igazított leképezéseket, amíg a lista üres nem lesz.

    Az entitás címkézésének bármilyen javítása a szándék igazítása után történik, a Szándék részletei lapról.

1. Tanítsa be és tegye ismét közzé az alkalmazást.

## <a name="get-intent-prediction-from-endpoint"></a>Leképezési előrejelzés beszereznie a végpontból

Ellenőrizze, hogy a megfelelően igazított példa utterances javult az alkalmazás előrejelzése, próbálkozzon egy utterance (kifejezés közel a javított utterance (kifejezés) .

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le _YOUR_QUERY_HERE_ a gombra. `Are there any natural language processing jobs in my department right now?`

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

   Most, hogy a bizonytalan kimondott szöveg ek megfelelően vannak igazítva, a helyes szándék **ot magas pontszámmal**jósolták meg.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Az áttekintés helyettesíthető további kimondott szövegek hozzáadásával?
Felmerülhet a kérdés, hogy miért ne adhatna hozzá további példaként szolgáló kimondott szövegeket. Mi a végponti kimondott szövegek áttekintésének célja? Egy valós LUIS-alkalmazásban a végponti kimondott szövegek a felhasználóktól származnak, Ön által még nem használt szóhasználattal és elrendezéssel. Ha ugyanazt a szóhasználatot és elrendezést alkalmazta volna, az eredeti előrejelzés nagyobb százalékos értékkel rendelkezne.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Miért szerepel a felső szándék a kimondott szövegek listáján?
Néhány végponti kimondott szöveg magas előrejelzési pontszámmal szerepel az áttekintési listán. Ezeknek a kimondott szövegeknek az áttekintésére és ellenőrzésére ugyanúgy szükség van. Azért szerepelnek a listán, mert a következő legnagyobb pontszámú szándék a legfelső szándék pontszámához túl közeli pontszámmal rendelkezik. Körülbelül 15%-os különbséget szeretne az első két szándék között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Az oktatóanyag során áttekintette a LUIS számára ismeretlen végponton elküldött kimondott szöveget. Miután ezek a kimondott szövegek ellenőrizve lettek, és át lettek helyezve a megfelelő szándékhoz kimondott példaszövegként, a LUIS javítja az előrejelzés pontosságát.

> [!div class="nextstepaction"]
> [Útmutató a minták használatához](luis-tutorial-pattern.md)
