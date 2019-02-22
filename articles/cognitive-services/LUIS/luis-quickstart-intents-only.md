---
title: Céljaira előrejelzése
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban, amely a felhasználó szándékának képes egyéni alkalmazás létrehozása. Ez az alkalmazás a legegyszerűbb típusú LUIS-alkalmazás, mert a kimondott szövegből nem nyer ki különféle adatelemeket, például e-mail-címeket vagy dátumokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 067829a1d9425ede1320242e364eca7c30bb7053
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593913"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Oktatóanyag: Felhasználói céljaira meghatározni a LUIS-alkalmazás létrehozása

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

## <a name="user-intentions-as-intents"></a>Felhasználói céljaira, szándék

Az alkalmazás feladata annak megállapítása a szándéka beszélgetések, a természetes nyelvű szöveget: 

`Are there any new positions in the Seattle office?`

Ezek különféle **szándékokként** vannak csoportosítva. 

Az alkalmazás rendelkezik szándékokkal. 

|Szándék|Cél|
|--|--|
|ApplyForJob|Határozza meg, ha a felhasználó egy feladat alkalmazza.|
|GetJobInformation|Határozza meg, ha a felhasználó az általános feladatok vagy egy adott feladat adatainak keres.|
|None|Határozza meg, ha a felhasználó által kért valami alkalmazást nem lehet a választ. Ez szándék, ha az alkalmazások létrehozásának részeként, és nem lehet törölni. |

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>A feladat adatainak leképezésének létrehozása

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. Adja meg az új szándék nevét: `GetJobInformation`. A leképezés van előre jelzett, amikor a felhasználó használni szeretne a cég nyílt feladatokkal kapcsolatos információkat. 

    ![Új leképezési párbeszédpanel képernyőképe a Language Understanding (LUIS)](media/luis-quickstart-intents-only/create-intent.png "új szándék párbeszédpanel képernyőképe a Language Understanding (LUIS)")

1. Válassza a **Done** (Kész) lehetőséget.

2. Néhány példa beszédmódok hozzáadása a célja, hogy egy felhasználó tehet fel és várt:

    | Példák kimondott szövegekre|
    |--|
    |Vannak ma megjelent új állásajánlatok?|
    |Vannak új állások meghirdetve a seattle-i irodában?|
    |Vannak-e minden olyan távoli dolgozók, vagy távolsági adatcsere feladatok nyílt mérnökök számára?|
    |Elérhető adatbázisokkal kapcsolatos állás?|
    |Az általam keresett egy közös munkát a helyzet a Tampai office.|
    |Van egy szakmai gyakorlat a san franciscóban office?|
    |Van bármilyen részmunkaidős college dolgozó személyekhez?|
    |Új lehetőségek keresése a könyvelés területén|
    |Keres egy feladatot a new york city kétnyelvű előadók számára.|
    |Egy új helyzet lévő számviteli tartományvezérlőkért keres.|
    |Új állások?|
    |Az elmúlt 2 napban hozzáadott mérnökök számára az összes feladat megjelenítése.|
    |Az aktuális feladat hozzászólásai?|
    |Milyen elszámolási pozíciók nyitva a londoni irodájában?|
    |Milyen állások érhetők el vezető mérnökök számára?|
    |Hol található a meghirdetett állások listája?|

    [![Képernyőkép MyStore szándékot az új utterances megadásáról](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "képernyőkép megadásáról MyStore szándékot az új kimondott szöveg")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Azáltal, hogy _példa utterances_, LUIS képzési kapcsolatos kimondott szöveg milyen kell elvégezni, az a szándéka áll. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>A none szándék példa beszédmódok hozzáadása 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Az alkalmazás betanításához tesztelési vagy közzététele előtt

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Az alkalmazás közzététele a végpontról lekérdezés

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>A végpontról szándék előrejelzés beolvasása

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

    A `verbose=true` lekérdezési karakterlánc paraméter azt jelenti, például **összes leképezések** az alkalmazás lekérdezés eredményében. Az entitások tömbje üres, mert az alkalmazás jelenleg nem rendelkezik entitásokkal. 

    A JSON-eredmény a **`topScoringIntent`** tulajdonságként azonosítja a legmagasabb pontszámot elérő szándékot. Minden pontszám 1 és 0 közé esik, a jobb pontszám 1-hez közelebb található. 

## <a name="create-intent-for-job-applications"></a>Feladat alkalmazások leképezésének létrehozása

Térjen vissza a LUIS-portálra, és hozzon létre egy új célja annak megállapításához, hogy a felhasználó utterance (kifejezés) egy feladat alkalmazásával kapcsolatos.

1. Válassza a **Build** (Létrehozás) lehetőséget a jobb felső menüben az alkalmazáskészítéshez való visszatéréshez.

1. Válassza ki **leképezések** leképezések listájának beolvasásához a bal oldali menüből.

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget, majd adja meg a következő nevet: `ApplyForJob`. 

    ![LUIS-párbeszédpanel új szándék létrehozásához](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Ehhez a szándékhoz számos olyan kimondott szöveget is hozzáadhat, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Jelentkezés kitöltése az 123456. számú álláshoz|
    |Ez az önéletrajzom a 654234. számú álláshoz|
    |Íme a részmunkaidős dolgozók recepciós bejegyzés a saját folytatása.|
    |E kérelmezése vagyok a dokumentumokat a legkorszerűbb ügyfélszolgálati feladathoz.|
    |Tudom a nyári college szakmai gyakorlat a kutatás és fejlesztés a San Diego vagyok kérelmezése|
    |Kérek, elküldéséhez a saját folytatása a önkiszolgáló étterem ideiglenes helyére.|
    |Az új Autocar csapat Amerika felfedezésének, OH saját folytatása küldöm|
    |Szeretnék jelentkezni az új könyvelői állásra|
    |Feladat 456789 nyilvántartási szakmai gyakorlat dokumentumokat érhető el|
    |567890. számú állás és a dokumentumaim|
    |A tulsa nyilvántartási szakmai gyakorlat a saját tanulmányok vannak csatolva.|
    |Saját dokumentumokat szünnap kézbesítési pozice|
    |Budapesti küldje el saját az új számlázási feladat folytatása|
    |Önéletrajz beadása a mérnöki pozícióra|
    |Ez az önéletrajzom a bejegyzés a Tampai 234123.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Betanítás újra

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Közzététel újra

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Újra szándék előrejelzés beolvasása

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

## <a name="client-application-next-steps"></a>Ügyfélalkalmazás lépések

Miután a LUIS visszaadja a JSON-választ, a LUIS nem foglalkozik tovább a kéréssel. A LUIS nem ad választ a felhasználók kimondott szövegeire, csak azonosítja a természetes nyelven kért információ típusát. Az ügyfélalkalmazás, például az Azure Bot biztosít a természetes nyelvi nyomon követése. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitástípus](luis-concept-entity-types.md)
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)
* [Az Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozta az Emberi erőforrások (HR) alkalmazást és két szándékot, kimondott példaszövegeket adott az egyes szándékokhoz és a None szándékhoz, valamint betanítást, közzétételt és tesztelést végzett a végponton. Ezek a LUIS-modellek létrehozásának alapvető lépései. 

Ez az alkalmazás folytatásához [hozzáadása egy egyszerű entitás- és kifejezés listát](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Előre összeállított szándékok és entitások hozzáadása ehhez az alkalmazáshoz](luis-tutorial-prebuilt-intents-entities.md)
