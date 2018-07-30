---
title: Egyszerű alkalmazás létrehozása két szándékkal – Azure | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy egyszerű LUIS-alkalmazást két szándékkal és entitások nélkül a kimondott felhasználói szövegek azonosításához.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: 0668ba050a6918995deb42d8feea5afbbab3b010
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865811"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Oktatóanyag: 1. Egyéni tartományt használó alkalmazás létrehozása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan használhatja a **szándékokat** a felhasználó _szándékának_ meghatározásához az alkalmazás számára elküldött kimondott szöveg (szöveg) alapján. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

Ez az alkalmazás a legegyszerűbb típusú LUIS-alkalmazás, mert nem nyer ki adatokat a kimondott szövegekből. Kizárólag a felhasználó szándékát határozza meg a kimondott szöveg alapján.

<!-- green checkmark -->
> [!div class="checklist"]
> * Új alkalmazás létrehozása az emberi erőforrások (HR) tartományához 
> * A GetJobInformation szándék hozzáadása
> * Kimondott példaszövegek hozzáadása a GetJobInformation szándékhoz 
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez
> * Az ApplyForJob szándék hozzáadása
> * Kimondott példaszövegek hozzáadása az ApplyForJob szándékhoz 
> * Betanítás, közzététel és végpont ismételt lekérdezése 

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md#luis-website)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="purpose-of-the-app"></a>Az alkalmazás célja
Az alkalmazás rendelkezik szándékokkal. Az első szándék (**`GetJobInformation`**) azonosítja, amikor egy felhasználó a vállalatnál meghirdetett állásokkal kapcsolatban szeretne információt kapni. A második szándék (**`None`**) az összes többi típusú kimondott szöveget azonosítja. A rövid útmutató későbbi szakaszában hozzáadunk egy harmadik szándékot is: `ApplyForJob`. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
1. Jelentkezzen be a [LUIS](luis-reference-regions.md#luis-website) webhelyére. Győződjön meg arról, hogy abban a [régióban](luis-reference-regions.md#publishing-regions) jelentkezik be, ahol közzé szeretné tenni a LUIS-végpontokat.

2. A [LUIS](luis-reference-regions.md#luis-website) webhelyén válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.  

    [![](media/luis-quickstart-intents-only/app-list.png "A Saját alkalmazások oldal képernyőképe")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Az előugró párbeszédpanelen írja be a következő nevet: `HumanResources`. Ez az alkalmazás a vállalat emberi erőforrások részlegével kapcsolatos kérdéseket fedi le. Az ilyen típusú részleg a foglalkoztatással kapcsolatos ügyeket intézi, például a betöltendő pozíciókat a vállalatnál.

    ![LUIS – új alkalmazás](./media/luis-quickstart-intents-only/create-app.png)

4. Amikor a folyamat befejeződött, az alkalmazás megjeleníti az **Intents** (Szándékok) lapot és rajta a **None** szándékot. 

    [![](media/luis-quickstart-intents-only/intents-list.png "Szándéklistát tartalmazó oldal képernyőképe")](media/luis-quickstart-intents-only/intents-list.png#lightbox)

## <a name="create-getjobinformation-intention"></a>A GetJobInformation szándék létrehozása
1. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. Adja meg az új szándék nevét: `GetJobInformation`. A rendszer erre a szándékra következtet, ha a felhasználó a vállalatnál elérhető állásokról szeretne információt kapni.

    ![](media/luis-quickstart-intents-only/create-intent.png "Az Új szándék párbeszédablak képernyőképe")

    A szándék létrehozásával azt az információkategóriát hozza létre, amelyet azonosítani szeretne. A kategória elnevezése lehetővé teszi, hogy a LUIS lekérdezési eredményeit használó egyéb alkalmazások is használják ezt a kategórianevet a megfelelő válasz megkereséséhez. A LUIS nem válaszol ezekre a kérdésekre, csak azonosítja a természetes nyelven kért információ típusát. 

2. Adjon hozzá hét kimondott szöveget ehhez a szándékhoz, amelyet a felhasználók várhatóan használni fognak, például:

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

3. A LUIS-alkalmazásnak jelenleg nincsenek kimondott szövegei a **None** szándékhoz. Olyan kimondott szövegekre van szüksége, amelyekre az alkalmazás nem válaszol. Ne hagyja üresen a szándékot. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget. 

4. Válassza ki a **None** szándékot. Adjon hozzá három olyan kimondott szöveget, amelyet a felhasználó beírhat, de az alkalmazás számára nem releváns. Ha az alkalmazás a meghirdetett állásokkal kapcsolatos, akkor jó példák lehetnek **None** kimondott szövegre a következők:

    | Példák kimondott szövegekre|
    |--|
    |Az ugató kutyák idegesítőek|
    |Rendeljen nekem egy pizzát|
    |Pingvinek az óceánban|

    Ha a LUIS-t hívó alkalmazásban, például egy csevegőrobotban a LUIS a **None** szándékot adja vissza egy kimondott szöveghez, a robot meg tudja kérdezni, hogy a felhasználó be szeretné-e fejezni a beszélgetést. A csevegőrobot további iránymutatásokat is adhat a beszélgetés folytatásához, ha a felhasználó azt szeretné. 

## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele
1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra. 

    ![Betanítás gomb](./media/luis-quickstart-intents-only/train-button.png)

    A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![Sikeres betanítást jelző állapotsáv](./media/luis-quickstart-intents-only/trained.png)

2. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget a Publish (Közzététel) oldal megnyitásához. A termelési hely alapértelmezés szerint ki van választva. Kattintson a **Publish** (Közzététel) gombra a kiválasztott termelési helynél. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    Nem kell létrehoznia LUIS-végpontkulcsot az Azure Portalon a közzététel előtt vagy a végpont URL-címének tesztelése előtt. Minden LUIS-alkalmazás ingyenes indulókulccsal rendelkezik a tartalomkészítéshez. Ez korlátlan tartalomkészítést és [néhány végponttalálatot](luis-boundaries.md#key-limits) biztosít. 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Végpont lekérdezése a GetJobInformation szándékhoz
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `I'm looking for a job with Natual Language Processing`. Az utolsó lekérdezésisztring-paraméter a `q`, a kimondott szöveg pedig **query**. A kimondott szöveg nem egyezik meg a 4. lépésben található egyik kimondott példaszöveggel sem, ezért tesztnek megfelelő, és a `GetJobInformation` szándékot kell visszaadnia a legmagasabb pontszámot elérő szándékként. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
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

## <a name="create-applyforjob-intention"></a>Az ApplyForJob szándék létrehozása
Térjen vissza a LUIS-webhely böngészőlapjára, és hozzon létre egy új, állásra jelentkezési szándékot.

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

    [Betanítás és közzététel](#train-and-publish-the-app) újból. 

## <a name="query-endpoint-for-applyforjob-intent"></a>Végpont lekérdezése az ApplyForJob szándékhoz
A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Az új böngészőablakban az URL-cím végén adja meg a következőt: `Can I submit my resume for job 235986`. 

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás mindössze néhány szándékkal azonosított egy természetes nyelvi lekérdezést, amely ugyanahhoz a szándékhoz tartozik, csak más szavakkal van megfogalmazva. 

A JSON-eredmény azonosítja a legmagasabb pontszámú szándékot. Minden pontszám 1 és 0 közé esik, a jobb pontszám 1-hez közelebb található. A `GetJobInformation` és a `None` szándék pontszáma sokkal közelebb van a nullához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, a topScoringIntent eredmény segítségével megkeresheti az információkat (amelyek nem a LUIS-ban vannak tárolva) a kérdés megválaszolásához, vagy befejezheti a beszélgetést. Ezek programozható lehetőségek a robothoz vagy a hívó alkalmazáshoz. Ezt nem végzi el a LUIS. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza a **My apps** (Saját alkalmazások) elemet a bal oldali felső menüben. Válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (***...***), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre összeállított szándékok és entitások hozzáadása ehhez az alkalmazáshoz](luis-tutorial-prebuilt-intents-entities.md)
