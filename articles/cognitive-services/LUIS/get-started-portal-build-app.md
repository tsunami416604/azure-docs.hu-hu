---
title: 'Gyors útmutató: Új alkalmazás létrehozása a LUIS-portállal'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehozhat egy új alkalmazást a LUIS-portálon. Hozzon létre egy alkalmazást, szándékok és entitások alapszintű részeit, majd tesztelje azáltal, hogy egy minta felhasználói utterance beolvasni az előre jelzett szándékot az interaktív vizsgálati panelen. Egy alkalmazás létrehozásával ingyenes; nincs szükség Azure-előfizetéssel.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786868"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Gyors útmutató: Hozzon létre egy új alkalmazást a LUIS-portálon

Ebben a rövid útmutatóban létrehozhat egy új alkalmazást a [LUIS portál](https://www.luis.ai). Hozzon létre egy alkalmazást, az alapszintű részeit **leképezések** és **entitások**, tesztelje azáltal, hogy egy minta felhasználói utterance beolvasni az előre jelzett szándékot az interaktív vizsgálati panelen.

Egy alkalmazás létrehozásával ingyenes; nincs szükség Azure-előfizetéssel. Amikor készen áll az alkalmazás üzembe helyezéséhez, akkor hozzon létre egy Azure Cognitive Service erőforrást, és rendelje hozzá az alkalmazást. A rendszer a telepítési folyamat a [következő gyorsútmutatóval](get-started-portal-deploy-app.md).

## <a name="create-app"></a>App létrehozása 

1. Nyissa meg a [LUIS portál](https://www.luis.ai) egy böngészőt, és jelentkezzen be. Ha ez az első alkalommal jelentkezik be, hozzon létre egy ingyenes LUIS-portál felhasználói fiókot szeretne.

1. Válassza ki **új alkalmazás létrehozása** a környezet eszköztáron.

    [![Új alkalmazás létrehozása a LUIS-portál](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Az előugró ablakban az alkalmazás konfigurálása a következő beállításokkal, majd válassza ki **kész**.

    |Beállítás neve| Érték | Cél|
    |--|--|--|
    |Name (Név)|`myEnglishApp`|A LUIS-alkalmazás egyedi neve<br>szükséges|
    |Kulturális környezet|**angol**|A felhasználók, kimondott szöveg nyelvét **en-us**<br>szükséges|
    |Leírás|`App made with LUIS Portal`|Alkalmazás leírása<br>választható|

    ![Adja meg az új beállítások](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Szándék létrehozása 

Ez az alkalmazás létrehozása után a következő lépés az leképezések létrehozásához. Leképezések módon szöveget a felhasználók kategorizálását. Ha egy emberi erőforrások alkalmazáson, amely két funkcióval rendelkezik: először keresse meg és a feladatok mások, és a második képernyők a alkalmazni munkahelyek, ezek két különböző _céljaira_ , a következő leképezések igazítása:

|Szándék|A felhasználó mintaszöveggel<br>más néven egy _utterance (kifejezés)_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Az alkalmazás létrehozása után a rendszer a **leképezések** lapján a **összeállítása** szakasz. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

    [![Hozzon létre új szándék gomb kiválasztása](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Adja meg a leképezés nevét `FindForm` válassza **kész**.

    ![Adja meg a FindForm leképezés nevét](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Példa utterance (kifejezés) hozzáadása 

Miután létrehozta a célt, a következő lépés az példa beszédmódok hozzáadása. Ez a szöveg, csevegőrobotot vagy más ügyfél alkalmazásban, a LUIS-leképezés az a felhasználó szöveges szándéka leképező a felhasználó által megadott. 

Ebben a példában alkalmazás `FindForm` szándék, például kimondott szöveg tartalmazza az űrlap számot, amely az utterance (kifejezés) fontos információkat, hogy az ügyfélalkalmazásnak szüksége van ahhoz, hogy fullfil a felhasználó kérésére. 

Adja hozzá a következő példa tizenöt kimondott szöveg a `FindForm` szándékot. 

|#|Példák kimondott szövegekre|
|--|--|
|1|Hrf-123456 keres|
|2|Hol található az emberi erőforrások űrlap-234591 hrf?|
|3|hrf-345623, hogy hol vannak|
|4|Az is kérek hrf-345794|
|5|Egy belső feladat a alkalmazni hrf-234695 van szükségem?|
|6|Nem főnökömtől tudni szeretnék vagyok alkalmazása hrf-234091 a feladat|
|7|Amikor elküldi a hrf-234918? Kaphatok érkezett e-mailben választ?|
|8|hrf-234555|
|9|Amikor frissült a hrf-234987?|
|10|Használhatom-e űrlap hrf-876345 mérnöki pozíciók a alkalmazni|
|11|Egy új verzióját a saját nyílt névtörlési kérés elküldve hrf 765234 volt?|
|12|Használhatok hrf-234234 nemzetközi feladatok?|
|13|hrf-234598 gépelési hibát|
|14|fog hrf-234567 módosíthatók új követelményeket|
|15|hrf-123456, hrf-123123, hrf-234567|

E példában utterances változnak, a következő módon:

* utterance (kifejezés) hossza
* Absztrakt
* a Word választás
* művelet igeidőt (, lett, lesz)
* szórendjét

[![Adja meg például utterances FindForm célja a](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Reguláris kifejezés entitás létrehozása 

A képernyő számát az előrejelzések futtatókörnyezeti válasz részeként adja vissza, hogy az űrlap megjelölni egy entitás rendelkezik. A képernyő szövege magas strukturált, mivel azt jelölhető egy reguláris kifejezésnek entitás használatával. Az alábbi lépéseket követve hozza létre az entitást. 

1. Válassza ki **entitások** a bal oldali navigációs menüjében. 

1. Válassza ki **új entitás létrehozása** az entitások oldalon.

1. Adja meg a nevét `Human Resources Form Number`, jelölje be a **Regex** entitás írja be, és adja meg a reguláris kifejezés `hrf-[0-9]{6}`. Ez megegyezik a literális karakter `hrf-`, és lehetővé teszi, hogy pontosan 6 számjegyű. 

    ![Adja meg a reguláris kifejezésnek entitás az Entitásadatok](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Válassza a **Done** (Kész) lehetőséget. 

## <a name="add-example-utterances-to-none-intent"></a>Nincs leképezés példa beszédmódok hozzáadása

A **nincs** leképezés a tartalék célt, és nem hagyható üresen. Ez a leképezés a leképezések az alkalmazás többi minden 10 1 utterance (kifejezés) kell rendelkezniük. 

A **nincs** leképezés a példa utterances az ügyfél doména aplikace kívül kell esniük. 

1. Válassza ki **leképezések** a bal oldali menüben, majd válassza ki **nincs** leképezések listájából.

1. Adja hozzá a következő példa utterances célja:

    |Nincs leképezés példa kimondott szöveg|
    |--|
    |Az ugató kutyák idegesítőek|
    |Rendeljen nekem egy pizzát|
    |Pingvinek az óceánban|

    Az emberi erőforrások alkalmazás számára az e példában utterances vannak a tartományon kívüli. Ha az emberi erőforrások tartomány, például állatokat, étele és az óceán majd ezeket a példa utterances kell nem használható a **nincs** szándékot. 

## <a name="train-the-app"></a>Az alkalmazás betanítása

Válassza ki a jobb felső navigációs **Train** a alkalmazni a leképezés és entitás modellt az aktuális verzióra, az alkalmazás módosítja. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Tekintse meg a reguláris kifejezés entitás az a példában kimondott szöveg

1. Ellenőrizze, hogy az entitás megtalálható a **FindForm** szándék kiválasztásával **leképezések** a bal oldali menüben, majd válassza ki **FindForm** szándékot. 

    Az entitás van megjelölve, ahol megjelenik a példa kimondott szöveg. Ha meg szeretné tekinteni az eredeti szöveg, az entitás neve helyett váltani **entitások nézet** az eszköztáron.

    [![Az entitások megjelölve valamennyi példa kimondott szöveg](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Az új alkalmazás és az interaktív teszt panel tesztelése

Interaktív **teszt** panelen a LUIS-portálon az entitás ellenőrzése az alkalmazás még nem látható új utterances kinyert.

1. Válassza ki **teszt** a jobb felső menüben.

1. Adjon hozzá egy új utterance (kifejezés), majd nyomja le az Enter billentyűt:

    ```Is there a form named hrf-234098```

    ![Teszt panel tesztelése új utterance (kifejezés)](./media/get-started-portal-build-app/test-new-utterance.png)

    Felső előre jelzett célja megfelelően **FindForm** több mint 90 %-os megbízhatósági (0.977) és a **emberi erőforrások űrlap szám** entitás hrf-234098 értékkel ki kell olvasni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha ez a rövid útmutató befejezése, és nem helyez át a következő gyorsútmutatóval be, válassza ki a **saját alkalmazások** a felső navigációs menüjében. Ezután válassza ki a bal oldali jelölőnégyzetet az alkalmazást a listából, és válassza ki **törlése** a környezet eszköztáron az eszközlista feletti. 

[![Alkalmazás törlése a saját alkalmazások listája](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [2. Alkalmazás üzembe helyezése](get-started-portal-deploy-app.md)