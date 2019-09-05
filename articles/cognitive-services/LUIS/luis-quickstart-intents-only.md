---
title: 'Oktatóanyag: Előrejelzési szándékok – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban hozzon létre egy egyéni alkalmazást, amely előrejelzést készít a felhasználó szándékáról. Ez az alkalmazás a legegyszerűbb típusú LUIS-alkalmazás, mert a kimondott szövegből nem nyer ki különféle adatelemeket, például e-mail-címeket vagy dátumokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7139876f64841a877e688ec6faf03597c527d1f2
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375821"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Oktatóanyag: LUIS-alkalmazás létrehozása a felhasználói szándékok meghatározásához

Ebben az oktatóanyagban létrehoz egy egyéni Emberi erőforrások (HR) alkalmazást, amely előrejelzi a felhasználók szándékát a kimondott szöveg alapján. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása 
> * Szándékok létrehozása
> * Példa kimondott szövegek hozzáadása
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Szándék lekérése végpontból


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Felhasználói szándékok

Az alkalmazás célja a társalgási, természetes nyelvű szöveg szándékának meghatározása: 

`Are there any new positions in the Seattle office?`

Ezek különféle **szándékokként** vannak csoportosítva. 

Az alkalmazás rendelkezik szándékokkal. 

|Szándék|Cél|
|--|--|
|ApplyForJob|Annak megállapítása, hogy a felhasználó alkalmaz-e feladatot.|
|GetJobInformation|Állapítsa meg, hogy a felhasználó az általános vagy egy adott feladatra vonatkozó információt keres-e.|
|Nincsenek|Annak megállapítása, hogy a felhasználó kér-e valamilyen alkalmazást, nem kell válaszolnia. Ez a szándék az alkalmazások létrehozásának részeként van megadva, és nem törölhető. |

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Feladatra vonatkozó szándék létrehozása

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. Adja meg az új szándék nevét: `GetJobInformation`. Ezt a szándékot akkor kell megjósolni, amikor egy felhasználó információt kér a vállalatban megnyitott feladatokról. 

    ![Új leképezési párbeszédpanel képernyőképe a Language Understanding (LUIS)](media/luis-quickstart-intents-only/create-intent.png "új szándék párbeszédpanel képernyőképe a Language Understanding (LUIS)")

1. Válassza a **Done** (Kész) lehetőséget.

2. Vegyen fel több példát a hosszúságú kimondott szöveg erre a célra, ha a felhasználó a következőt kéri:

    | Példák kimondott szövegekre|
    |--|
    |Vannak ma megjelent új állásajánlatok?|
    |Vannak új állások meghirdetve a seattle-i irodában?|
    |Nyitottak-e a mérnökök a távoli munkavégzők vagy a távmunka-feladatok?|
    |Elérhető adatbázisokkal kapcsolatos állás?|
    |Közös munkavégzést keresek a Tampa-irodában.|
    |Van gyakornok a San Francisco-beli irodában?|
    |Van-e részmunkaidős munka a kollégiumban dolgozó személyek számára?|
    |Új lehetőségek keresése a könyvelés területén|
    |Munkát keres a New York-i régióban a kétnyelvű hangszórókhoz.|
    |Új helyzetet keres a nyilvántartási feladatokkal kapcsolatban.|
    |Új állások?|
    |Az elmúlt 2 napban hozzáadott mérnökök összes feladatának megjelenítése.|
    |A mai feladatok közzétételei?|
    |Milyen nyilvántartási pozíciók vannak nyitva a londoni irodában?|
    |Milyen állások érhetők el vezető mérnökök számára?|
    |Hol található a meghirdetett állások listája?|

    [![Képernyőkép MyStore szándékot az új utterances megadásáról](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "képernyőkép megadásáról MyStore szándékot az új kimondott szöveg")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Ha _például hosszúságú kimondott szöveg_-t használ, akkor a Luis betanítása arról nyújt segítséget, hogy milyen hosszúságú kimondott szöveg kell előre jelezni. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanítása tesztelés vagy közzététel előtt

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele a végpontról történő lekérdezéshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Következtetések előrejelzése a végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Lépjen az URL-cím végéhez a címsorban, és írja be a következőt: `I'm looking for a job with Natural Language Processing`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. A kimondott szöveg nem egyezik meg egyik kimondott példaszöveggel sem, ezért tesztnek megfelelő, és a `GetJobInformation` szándékot kell visszaadnia a legmagasabb pontszámot elérő szándékként. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    A `verbose=true` querystring paraméter azt jelenti, hogy az alkalmazás lekérdezési eredményei tartalmazzák az **összes leképezést** . Az entitások tömbje üres, mert az alkalmazás jelenleg nem rendelkezik entitásokkal. 

    A JSON-eredmény a **`topScoringIntent`** tulajdonságként azonosítja a legmagasabb pontszámot elérő szándékot. Minden pontszám 1 és 0 közé esik, a jobb pontszám 1-hez közelebb található. 

## <a name="create-intent-for-job-applications"></a>Szándék létrehozása a feladatok alkalmazásaihoz

Térjen vissza a LUIS-portálra, és hozzon létre egy új szándékot annak megállapításához, hogy a felhasználó nyilatkozik-e a feladatokra vonatkozó kérelemről.

1. Válassza a **Build** (Létrehozás) lehetőséget a jobb felső menüben az alkalmazáskészítéshez való visszatéréshez.

1. A bal oldali **menüben válassza a szándékok** lehetőséget, hogy megszerezze a szándékok listáját.

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget, majd adja meg a következő nevet: `ApplyForJob`. 

    ![LUIS-párbeszédpanel új szándék létrehozásához](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Ehhez a szándékhoz számos olyan kimondott szöveget is hozzáadhat, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Jelentkezés kitöltése az 123456. számú álláshoz|
    |Ez az önéletrajzom a 654234. számú álláshoz|
    |Itt van a folytatás a részmunkaidőben lévő recepciós bejegyzésben.|
    |Erre a papírmunkára alkalmazom az Art Desk-feladatot.|
    |Felhasználom a nyári főiskolai gyakorlatot a San Diego-beli kutatás és fejlesztés terén|
    |Kérésem, hogy beküldem a folytatást a cafeteria ideiglenes helyére.|
    |Elküldem a folytatást az új Autocar csapatnak a Columbus-ben, OH|
    |Szeretnék jelentkezni az új könyvelői állásra|
    |A 456789-es feladatok nyilvántartási gyakorlata|
    |567890. számú állás és a dokumentumaim|
    |A Tulsa-nyilvántartási gyakorlattal kapcsolatos dokumentumok csatolva vannak.|
    |Saját papírmunka az ünnepi kézbesítési pozícióhoz|
    |Kérjük, küldje el újra az új számviteli feladatot Seattle-ben|
    |Önéletrajz beadása a mérnöki pozícióra|
    |Ez az önéletrajzom a post 234123-es verziójának a Tampa-ban.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Betanítás újra

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Közzététel újra

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Leképezési előrejelzés újbóli lekérése

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Az új böngészőablakban az URL-cím végén adja meg a következőt: `Can I submit my resume for job 235986`. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Az eredmények között a meglévő szándékok mellett szerepel az új **ApplyForJob** szándék is. 

## <a name="client-application-next-steps"></a>Ügyfél – alkalmazás következő lépései

Miután a LUIS visszaadja a JSON-választ, a LUIS nem foglalkozik tovább a kéréssel. A LUIS nem ad választ a felhasználók kimondott szövegeire, csak azonosítja a természetes nyelven kért információ típusát. A beszélgetés utáni nyomon követést az ügyfélalkalmazás, például egy Azure bot nyújtja. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások típusai](luis-concept-entity-types.md)
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)
* [Azure-robot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozta az Emberi erőforrások (HR) alkalmazást és két szándékot, kimondott példaszövegeket adott az egyes szándékokhoz és a None szándékhoz, valamint betanítást, közzétételt és tesztelést végzett a végponton. Ezek a LUIS-modellek létrehozásának alapvető lépései. 

Folytassa az alkalmazással, [és adjon hozzá egy egyszerű entitást és egy kifejezést tartalmazó listát](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Előre összeállított szándékok és entitások hozzáadása ehhez az alkalmazáshoz](luis-tutorial-prebuilt-intents-entities.md)
