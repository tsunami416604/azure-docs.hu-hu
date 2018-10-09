---
title: '1. oktatóanyag: Szándékok keresése egyéni LUIS-alkalmazásban'
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy egyéni alkalmazást, amely előrejelzi a felhasználók szándékát. Ez az alkalmazás a legegyszerűbb típusú LUIS-alkalmazás, mert a kimondott szövegből nem nyer ki különféle adatelemeket, például e-mail-címeket vagy dátumokat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b229dbc90f3f6ecc226c88ee393114f233bcf1a2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035406"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>1. oktatóanyag: Egyéni alkalmazás létrehozása felhasználói szándék meghatározására

Ebben az oktatóanyagban létrehoz egy egyéni Emberi erőforrások (HR) alkalmazást, amely előrejelzi a felhasználók szándékát a kimondott szöveg alapján. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

Az alkalmazás célja a beszélgetések természetes nyelvű szövegei által kifejezett szándék meghatározása. Ezek különféle **szándékokként** vannak csoportosítva. Az alkalmazás rendelkezik szándékokkal. Az első szándék (**`GetJobInformation`**) azonosítja, amikor egy felhasználó a vállalatnál meghirdetett állásokkal kapcsolatban szeretne információt kapni. A második szándék (**`None`**) a felhasználó azon kimondott szövegeit jelöli, amelyek az alkalmazás _tartományán_ (hatókörén) kívül esnek. Később hozzáadunk egy harmadik szándékot is (**`ApplyForJob`**), amely az állásokra való jelentkezéssel kapcsolatos kimondott szövegekre vonatkozik. Ez a harmadik szándék más, mint a `GetJobInformation`, mivel az állásra jelentkező felhasználók már ismerik az állással kapcsolatos információkat. Azonban a szóhasználattól függően nehéz lehet meghatározni, melyik szándékról van szó, hiszen mindkettő állással kapcsolatos.

Miután a LUIS visszaadja a JSON-választ, a LUIS nem foglalkozik tovább a kéréssel. A LUIS nem ad választ a felhasználók kimondott szövegeire, csak azonosítja a természetes nyelven kért információ típusát. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Új alkalmazás létrehozása 
> * Szándékok létrehozása
> * Példa kimondott szövegek hozzáadása
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Szándék lekérése végpontból

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása

1. Jelentkezzen be a LUIS portálra a [https://www.luis.ai](https://www.luis.ai) URL-címen. 

2. Válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.  

    [![](media/luis-quickstart-intents-only/app-list.png "A Language Understanding (LUIS) My Apps (Saját alkalmazások) lapjának képernyőképe")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Az előugró párbeszédpanelen írja be a következő nevet: `HumanResources`. Hagyja meg az alapértelmezett kulturális környezet, amely az **English** (angol). A leírást hagyja üresen.

    ![LUIS – új alkalmazás](./media/luis-quickstart-intents-only/create-app.png)

    Ezután az alkalmazás megjeleníti az **Intents** (Szándékok) lapot és rajta a **None** szándékot.

## <a name="getjobinformation-intent"></a>A GetJobInformation szándék

1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. Adja meg az új szándék nevét: `GetJobInformation`. A rendszer ezt a szándékot jelzi előre, ha a felhasználó a vállalatnál elérhető állásokról szeretne információt kapni.

    ![](media/luis-quickstart-intents-only/create-intent.png "A Language Understanding (LUIS) New intent (Új szándék) párbeszédablakának képernyőképe")

2. _Kimondott példaszövegek_ megadásával betaníthatja a LUIS-t arra, milyen típusú kimondott szövegeket kell előre jeleznie ehhez a szándékhoz. Ehhez a szándékhoz számos olyan kimondott példaszöveget is hozzáadhat, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Vannak ma megjelent új állásajánlatok?|
    |Milyen állások érhetők el vezető mérnökök számára?|
    |Elérhető adatbázisokkal kapcsolatos állás?|
    |Új lehetőségek keresése a könyvelés területén|
    |Hol található a meghirdetett állások listája?|
    |Új állások?|
    |Vannak új állások meghirdetve a seattle-i irodában?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Új kimondott szövegek megadásának képernyőképe a MyStore szándékhoz")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>A None szándék 
Az ügyfélalkalmazásnak tudnia kell, ha egy kimondott szöveg az alkalmazás tárgyán kívül esik. Ha a LUIS a **None** szándékot adja vissza egy kimondott szöveghez, az ügyfélalkalmazás meg tudja kérdezni, hogy a felhasználó be szeretné-e fejezni a beszélgetést. Az ügyfélalkalmazás további iránymutatásokat is adhat a beszélgetés folytatásához, ha a felhasználó azt szeretné. 

Ezeket a tárgyon kívül eső kimondott példaszövegeket a rendszer a **None** szándékba csoportosítja. Ne hagyja üresen a szándékot. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

2. Válassza ki a **None** szándékot. Adjon meg három olyan kimondott szöveget, amelyet a felhasználó megadhat, de az Emberi erőforrások alkalmazás nem fog foglalkozni velük. Ha az alkalmazás a meghirdetett állásokkal kapcsolatos, akkor példák lehetnek **None** kimondott szövegre a következők:

    | Példák kimondott szövegekre|
    |--|
    |Az ugató kutyák idegesítőek|
    |Rendeljen nekem egy pizzát|
    |Pingvinek az óceánban|


## <a name="train"></a>Betanítás 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Szándék lekérése

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez a címsorban, és írja be a következőt: `I'm looking for a job with Natural Language Processing`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. A kimondott szöveg nem egyezik meg egyik kimondott példaszöveggel sem, ezért tesztnek megfelelő, és a `GetJobInformation` szándékot kell visszaadnia a legmagasabb pontszámot elérő szándékként. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    Az eredmények között szerepel az alkalmazásban található **összes szándék**, vagyis jelenleg 2 szándék. Az entitások tömbje üres, mert az alkalmazás jelenleg nem rendelkezik entitásokkal. 

    A JSON-eredmény a **`topScoringIntent`** tulajdonságként azonosítja a legmagasabb pontszámot elérő szándékot. Minden pontszám 1 és 0 közé esik, a jobb pontszám 1-hez közelebb található. 

## <a name="applyforjob-intent"></a>Az ApplyForJob szándék
Térjen vissza a LUIS-webhelyre, és hozzon létre egy új szándékot, amely meghatározza, hogy egy felhasználói kimondott szöveg állásra való jelentkezésről szól-e.

1. Válassza a **Build** (Létrehozás) lehetőséget a jobb felső menüben az alkalmazáskészítéshez való visszatéréshez.

2. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

3. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget, majd adja meg a következő nevet: `ApplyForJob`. 

    ![LUIS-párbeszédpanel új szándék létrehozásához](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Ehhez a szándékhoz számos olyan kimondott szöveget is hozzáadhat, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Szeretnék jelentkezni az új könyvelői állásra|
    |Jelentkezés kitöltése az 123456. számú álláshoz|
    |Önéletrajz beadása a mérnöki pozícióra|
    |Ez az önéletrajzom a 654234. számú álláshoz|
    |567890. számú állás és a dokumentumaim|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Új kimondott szövegek megadásának képernyőképe az ApplyForJob szándékhoz")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    A címkézett szándék piros színnel van bekeretezve, mert LUIS nem biztos a szándék helyességében. Az alkalmazás betanítása megtanítja LUIS-t arra, hogy a kimondott szövegek a megfelelő szándékhoz tartoznak. 

## <a name="train-again"></a>Betanítás újra

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Közzététel újra

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Szándék lekérése újra

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Az új böngészőablakban az URL-cím végén adja meg a következőt: `Can I submit my resume for job 235986`. 

    ```JSON
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    Az eredmények között a meglévő szándékok mellett szerepel az új **ApplyForJob** szándék is. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozta az Emberi erőforrások (HR) alkalmazást és két szándékot, kimondott példaszövegeket adott az egyes szándékokhoz és a None szándékhoz, valamint betanítást, közzétételt és tesztelést végzett a végponton. Ezek a LUIS-modellek létrehozásának alapvető lépései. 

> [!div class="nextstepaction"]
> [Előre összeállított szándékok és entitások hozzáadása ehhez az alkalmazáshoz](luis-tutorial-prebuilt-intents-entities.md)
