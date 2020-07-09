---
title: 'Oktatóanyag: a végpont hosszúságú kimondott szöveg áttekintése – LUIS'
description: Ebben az oktatóanyagban a Luis nem biztos, hogy a Luis HTTP-végponton keresztül kapott hosszúságú kimondott szöveg ellenőrzésével vagy javításával fejleszti az alkalmazások előrejelzéseit. Bizonyos kimondott szövegek esetében a szándékot, míg más kimondott szövegek esetében az entitást kell ellenőrizni.
services: cognitive-services
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: 082e625efeeb4764aaa1ac5101eb2b0013348b19
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959030"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Oktatóanyag: a nem biztos előrejelzések kijavítása a végpontok hosszúságú kimondott szöveg áttekintésével
Ebben az oktatóanyagban a Luis HTTPS-végponton keresztül fogadott hosszúságú kimondott szöveg ellenőrzésével vagy javításával fejlesztheti az alkalmazás-előrejelzéseket, hogy a LUIS nem biztos benne. Tekintse át a végpontok hosszúságú kimondott szöveg az ütemezett LUIS-karbantartás rendszeres részeként.

Ez a felülvizsgálati folyamat lehetővé teszi a LUIS számára az alkalmazás tartományának megismerését. LUIS kiválasztja a felülvizsgálati listában megjelenő hosszúságú kimondott szöveg. Ez a lista a következő tulajdonságokkal rendelkezik:

* Az alkalmazásra jellemző.
* Az alkalmazás előrejelzési pontosságának fejlesztésére szolgál.
* Rendszeresen át kell tekinteni.

A végponti kimondott szövegek áttekintésével ellenőrizheti vagy kijavíthatja a kimondott szöveg előrejelzett szándékát.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * A végpont beszédmódjainak áttekintése
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Alkalmazáshoz tartozó JSON-fájl letöltése

Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

## <a name="import-json-file-for-app"></a>JSON-fájl importálása az alkalmazáshoz


1. A [Luis portál](https://www.luis.ai) **saját alkalmazások** lapján válassza az **+ új alkalmazás a beszélgetéshez**, majd az **Importálás JSON-ként**lehetőséget. Keresse meg az előző lépésben mentett JSON-fájlt. Nem kell módosítania az alkalmazás nevét. Válassza a **kész** lehetőséget

1. Válassza **Build** a létrehozás **, majd a szándékok** lehetőséget, hogy megtekintse a Luis-alkalmazás fő építőelemeit.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Váltás a verziók lapról a szándékok lapra.":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitás módosításának alkalmazására

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Hosszúságú kimondott szöveg hozzáadása a végponton

Ebben az alkalmazásban szándékai és entitásai vannak, de nem rendelkezik végpont-használattal. Ez a végpont-használat szükséges ahhoz, hogy az alkalmazás javítsa a végpontok teljes áttekintését.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HEREt_ az alábbi táblázatban szereplő hosszúságú kimondott szöveg. Minden Kimondás esetén küldje el a teljes kilépést, és szerezze be az eredményt. Ezután cserélje le a lemondás végére a következő kifejezéssel.

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

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="A bal oldali navigációs sávon a végpontok hosszúságú kimondott szöveg áttekintése gomb képernyőképe.":::

    Ez a Kimondás `I'm looking for a job with Natural Language Processing` nem a megfelelő szándékú, _GetJobInformation_. A két szándékban a feladatok neveinek és műveleteinek hasonlósága miatt _ApplyForJob_ .

1.  A Kimondás igazításához válassza ki a megfelelő **igazított szándékot** `GetJobInformation` . A jelölőnégyzet bejelölésével adja hozzá a megváltozott kiírást az alkalmazáshoz.

    Tekintse át a további hosszúságú kimondott szöveg ebben a szándékban, és szükség szerint javítsa ki az igazított szándékot. Ebben az oktatóanyagban a kezdeti lemondás tábla segítségével tekintheti meg az igazított szándékot.

    A **felülvizsgálati végpont hosszúságú kimondott szöveg** listájának már nem kell a korrigált hosszúságú kimondott szöveg. Ha további hosszúságú kimondott szöveg jelennek meg, folytassa a listát, és javítsa a igazított leképezéseket, amíg a lista üres nem lesz.

    Az entitások címkézésének minden kijavítása a szándék igazítása után történik, a szándék részletek lapról.

1. Tanítsa be és tegye ismét közzé az alkalmazást.

## <a name="get-intent-prediction-from-endpoint"></a>Leképezési előrejelzés beolvasása a végpontról

Ha ellenőrizni szeretné, hogy a megfelelően igazított példa hosszúságú kimondott szöveg javította-e az alkalmazás előrejelzését, próbáljon meg egy teljes kizárót kijavítani.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HEREt_ a következőre: `Are there any natural language processing jobs in my department right now?` .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
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
                                        "value": "2020-07-02 21:45:50"
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

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Az oktatóanyag során áttekintette a LUIS számára ismeretlen végponton elküldött kimondott szöveget. Miután ezek a kimondott szövegek ellenőrizve lettek, és át lettek helyezve a megfelelő szándékhoz kimondott példaszövegként, a LUIS javítja az előrejelzés pontosságát.

> [!div class="nextstepaction"]
> [Útmutató a minták használatához](luis-tutorial-pattern.md)
