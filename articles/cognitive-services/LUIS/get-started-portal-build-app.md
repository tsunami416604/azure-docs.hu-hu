---
title: 'Gyors útmutató: Hozzon létre egy új alkalmazást a LUIS-portálon'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehozhat egy új alkalmazást a LUIS-portálon. Hozzon létre egy alkalmazást, szándékok és entitások alapszintű részeit. Az alkalmazás tesztelése azáltal, hogy egy minta felhasználói utterance beolvasni az előre jelzett szándékot az interaktív vizsgálati panelen. Egy alkalmazás létrehozásával ingyenes; nincs szükség Azure-előfizetéssel.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 5ec38c510fedee7f489b3b100285eeb7c75f4561
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690972"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Gyors útmutató: Hozzon létre egy új alkalmazást a LUIS-portálon

Ez a rövid útmutatóban egy új alkalmazás készítése a [LUIS portál](https://www.luis.ai). Először hozzon létre egy alkalmazást, az alapszintű részeit **leképezések**, és **entitások**. Az alkalmazás ezután azáltal, hogy egy minta felhasználói utterance (kifejezés) az interaktív vizsgálati panelen beolvasni az előre jelzett leképezés tesztelése.

Egy alkalmazás létrehozásával ingyenes, és nem igényel Azure-előfizetést. Amikor elkészült, helyezze üzembe az alkalmazást, tekintse meg a [gyors útmutató: alkalmazás üzembe helyezése](get-started-portal-deploy-app.md). Ez bemutatja, hogyan hozzon létre egy Azure Cognitive Service erőforrást, és rendelje hozzá az alkalmazást.

## <a name="create-an-app"></a>Alkalmazás létrehozása

1. Nyissa meg a [LUIS portál](https://www.luis.ai) egy böngészőt, és jelentkezzen be. Ha a jelentkezik be először, hozzon létre egy ingyenes LUIS-portál felhasználói fiókot szeretne.

1. Válassza ki **új alkalmazás létrehozása** a környezet eszköztáron.

   [![Új alkalmazás létrehozása a LUIS-portál](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Az előugró ablakban az alkalmazás konfigurálása a következő beállításokkal, majd **kész**.

   |Beállítás neve| Érték | Cél|
   |--|--|--|
   |Name (Név)|`myEnglishApp`|A LUIS-alkalmazás egyedi neve<br>szükséges|
   |Kulturális környezet|**angol**|A felhasználók, kimondott szöveg nyelvét **en-us**<br>szükséges|
   |Leírás|`App made with LUIS Portal`|Alkalmazás leírása<br>választható|
   | | | |

   ![Adja meg az új beállítások](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Szándékok létrehozása

A LUIS-alkalmazás létrehozása után leképezések létrehozásához szükséges. Leképezések módon szöveget a felhasználók kategorizálását. Ha például egy emberi erőforrások alkalmazáson előfordulhat két függvényt. Felhasználók számára, hogy:

 1. Keresse meg és a feladatok
 1. Keresse meg a feladatok a alkalmazni űrlapok

Az alkalmazás a két különböző _céljaira_ , a következő leképezések igazítása:

|Szándék|A felhasználó mintaszöveggel<br>más néven egy _utterance (kifejezés)_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Leképezések létrehozásához tegye a következőket:

1. Az alkalmazás létrehozása után a rendszer a **leképezések** lapján a **összeállítása** szakasz. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

   [![Hozzon létre új szándék gomb kiválasztása](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Adja meg a leképezés nevét `FindForm`, majd válassza ki **kész**.

   ![Adja meg a FindForm leképezés nevét](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Adjon hozzá egy példa utterance (kifejezés)

Példa utterances leképezések létrehozása után adja hozzá. Példa kimondott szöveg, hogy a felhasználó egy csevegőrobot, vagy más ügyfélalkalmazás szöveget. A LUIS beszédszándék leképezik a szándéka az a felhasználó szöveget.

Ebben a példában alkalmazás `FindForm` szándék, például kimondott szöveg tartalmazza az űrlap számát. Az ügyfélalkalmazásnak szüksége van az űrlap szám teljesítéséhez a felhasználó kérésére, ezért fontos, hogy belefoglalja az utterance (kifejezés).

Adja hozzá a következő példa 15 kimondott szöveg a `FindForm` szándékot.

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

A kialakításból fakadóan e példában utterances eltérőek lehetnek, a következő módon:

* utterance (kifejezés) hossza
* Absztrakt
* a Word választás
* művelet igeidőt (, lett, lesz)
* szórendjét

[![Adja meg például utterances FindForm célja a](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-a-regular-expression-entity"></a>Entitás létrehozása egy reguláris kifejezés

A képernyő számát az előrejelzések futtatókörnyezeti válasz részeként adja vissza lekéréséhez az űrlap egy egységként kell megjelölni. A képernyő szövege magas strukturált, mert azt jelölhető egy reguláris kifejezésnek entitás használatával. Az entitás létrehozása a következő lépéseket:

1. Válassza ki **entitások** a bal oldali menüből.

1. Válassza ki **új entitás létrehozása** a a **entitások** lapot.

1. Adja meg a nevét `Human Resources Form Number`, jelölje be a **Regex** entitás írja be, és adja meg a reguláris kifejezés `hrf-[0-9]{6}`. Ez a bejegyzés megegyezik a literális karakter `hrf-`, és lehetővé teszi, hogy pontosan 6 számjegyű.

   ![Adja meg a reguláris kifejezésnek entitás az Entitásadatok](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Válassza a **Done** (Kész) lehetőséget.

## <a name="add-example-utterances-to-the-none-intent"></a>A none szándék példa beszédmódok hozzáadása

A **nincs** leképezés a tartalék célt, és nem hagyható üresen. Ez a leképezés tartalmaznia kell minden, hogy a más leképezések az alkalmazás a hozzáadott 10 példa megcímkézzen számára egy utterance (kifejezés).

A **nincs** leképezés a példa utterances az ügyfél doména aplikace kívül kell esniük.

1. Válassza ki **leképezések** a bal oldali menüben, és válassza ki a **nincs** leképezések listájából.

1. Adja hozzá a következő példa utterances célja:

   |Nincs leképezés példa kimondott szöveg|
   |--|
   |Az ugató kutyák idegesítőek|
   |Rendeljen nekem egy pizzát|
   |Pingvinek az óceánban|

   Az emberi erőforrások alkalmazás számára az e példában utterances vannak a tartományon kívüli. Az emberi erőforrások tartomány például állatokat, food vagy az óceán, akkor a különböző példa utterances kell használnia a **nincs** szándékot.

## <a name="train-the-app"></a>Az alkalmazás betanítása

A menüben kattintson a jobb felső sarokban, válassza ki a **Train** a alkalmazni a leképezés és entitás modellt az aktuális verzióra, az alkalmazás módosítja.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Tekintse meg a reguláris kifejezés entitás az a példában kimondott szöveg

1. Ellenőrizze, hogy az entitás megtalálható a **FindForm** szándék kiválasztásával **leképezések** a bal oldali menüből. Válassza ki **FindForm** szándékot.

   Az entitás van megjelölve, ahol megjelenik a példa kimondott szöveg. Ha meg szeretné tekinteni a az entitás neve helyett az eredeti szöveg, váltani **entitások nézet** az eszköztáron.

   [![Az entitások megjelölve valamennyi példa kimondott szöveg](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Az új alkalmazás és az interaktív teszt panel tesztelése

Interaktív **teszt** ablaktáblán ellenőrizze, hogy az entitás az alkalmazás még nem látható új utterances kinyert LUIS portálon található.

1. Válassza ki **teszt** a jobb felső menüben.

1. Adjon hozzá egy új utterance (kifejezés), és nyomja le az ENTER billentyűt:

   ```Is there a form named hrf-234098```

   ![Teszt panel tesztelése új utterance (kifejezés)](./media/get-started-portal-build-app/test-new-utterance.png)

   Felső előre jelzett célja megfelelően **FindForm** több mint 90 %-os megbízhatósági (0.977). A **emberi erőforrások űrlap szám** entitás hrf-234098 értékkel ki kell olvasni.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a rövid útmutatóban létrehozta, és nem helyez át be a következő gyorsútmutatóval, jelölje be **saját alkalmazások** a felső navigációs menüjében. Jelölje be az alkalmazás bal oldali jelölőnégyzetet a listában, majd válassza a **törlése** a környezet eszköztáron az eszközlista feletti.

[![Alkalmazás törlése a saját alkalmazások listája](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [2. Alkalmazás üzembe helyezése](get-started-portal-deploy-app.md)
