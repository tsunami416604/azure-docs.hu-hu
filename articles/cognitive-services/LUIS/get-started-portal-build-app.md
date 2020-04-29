---
title: 'Rövid útmutató: új alkalmazás létrehozása a LUIS portálon'
description: Ebben a rövid útmutatóban létrehoz egy alkalmazás, szándékok és entitások alaprészeit, valamint teszteli a minta-kisegítő lehetőséget a LUIS-portálon.
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382573"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rövid útmutató: új alkalmazás létrehozása a LUIS portálon

Ebben a rövid útmutatóban egy új alkalmazást hoz létre a LUIS portálon. Először hozza létre az alkalmazás, a **szándékok**és az **entitások**alapvető részeit. Ezt követően tesztelje az alkalmazást úgy, hogy az interaktív tesztelési panelen megadta a minta felhasználótól az előre jelzett szándékot.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Alkalmazás létrehozása

1. A helyi eszköztáron válassza az **+ új alkalmazás** lehetőséget, majd válassza az **új alkalmazás a beszélgetéshez**lehetőséget.

    > [!div class="mx-imgBorder"]
    > [![Új alkalmazás létrehozása a LUIS Portalon](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Az előugró ablakban konfigurálja az alkalmazást a következő beállításokkal, majd válassza a **kész**lehetőséget.

   |Beállítás neve| Érték | Cél|
   |--|--|--|
   |Name (Név)|`myEnglishApp`|Egyedi LUIS-alkalmazás neve<br>kötelező|
   |Kulturális környezet|**angol**|A felhasználók hosszúságú kimondott szöveg nyelve, **en-us**<br>kötelező|
   |Leírás (nem kötelező)|`App made with LUIS Portal`|Az alkalmazás leírása<br>választható|
   |Előrejelzési erőforrás (nem kötelező) |-  |Ne válassza a lehetőséget. A LUIS egy alapszintű kulcsot biztosít a szerzői műveletek és a 1 000-es előrejelzési végpontokra vonatkozó kérések használatához. |

   ![Adja meg az új alkalmazás beállításait](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Szándékok létrehozása

A LUIS-alkalmazás létrehozása után szándékait kell létrehoznia. A szándékok segítségével kategorizálhatja a felhasználókat. Előfordulhat például, hogy egy emberi erőforrás alkalmazásnak két funkciója van. Az emberek segítése:

 1. Feladatok keresése és alkalmazása
 1. A feladatokra alkalmazandó űrlapok keresése

Az alkalmazás két különböző _szándéka az alábbi célokhoz_ igazodik:

|Szándék|Példa szövegre a felhasználótól<br>más néven a _Kimondás_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

A leképezések létrehozásához hajtsa végre a következő lépéseket:

1. Az alkalmazás létrehozása után a **Build** szakasz **szándékok** lapján található. Kattintson a **Létrehozás** gombra.

   [![Új leképezés létrehozásához válassza a létrehozás lehetőséget.](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Adja meg a leképezés nevét, `FindForm`majd kattintson a **kész**gombra.

## <a name="add-an-example-utterance"></a>Példa Kimondás hozzáadása

A leképezések létrehozása után példaként adja hozzá a hosszúságú kimondott szöveg. Például a hosszúságú kimondott szöveg olyan szöveg, amelyet a felhasználó egy csevegési robotba vagy más ügyfélalkalmazásba ír. A felhasználó szövegének szándékát egy LUIS-szándékra képezik le.

Ebben a példában az alkalmazás `FindForm` célja, például a hosszúságú kimondott szöveg fogja tartalmazni az űrlapot. Az ügyfélalkalmazás számára szükséges, hogy megfeleljen a felhasználó kérelmének, ezért fontos, hogy belefoglalja azt a teljességbe.

> [!div class="mx-imgBorder"]
> [![Adja meg például a FindForm szándék hosszúságú kimondott szöveg](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adja hozzá a következő 15 példa hosszúságú kimondott szöveg a `FindForm` szándékhoz.

|#|Példák kimondott szövegekre|
|--|--|
|1|HRF keresése – 123456|
|2|Hol található a HRF-234591 típusú emberi erőforrás?|
|3|HRF-345623, hol van|
|4|Lehetséges a HRF-345794 küldése|
|5|Szükség van-e a HRF-234695-re a belső feladatokra való jelentkezéshez?|
|6|Tudnia kell, hogy a felettesem a HRF-234091-es feladatokhoz való jelentkezést kéri.|
|7|Hová küldhetek HRF-234918? Kapok e-mailben kapott választ?|
|8|HRF-234555|
|9|Mikor frissítették a HRF-234987 frissítést?|
|10|Használhatom a HRF-876345 formanyomtatványt a mérnöki pozíciókra való jelentkezéshez|
|11|A HRF-765234 új verziója lett elküldve a nyitott igényléshez?|
|12|Használhatok HRF-234234 nemzetközi feladatokhoz?|
|13|HRF-234598 – helyesírási hiba|
|14|a HRF-234567 új követelmények szerint lesz módosítva|
|15|HRF-123456, HRF-123123, HRF-234567|

Ez a példa a hosszúságú kimondott szöveg a következő módokon változhat:

* Kimondás hossza
* [központozási](luis-reference-application-settings.md#punctuation-normalization)
* szó választása
* a művelet feszült (is, volt, lesz)
* Word-sorrend


## <a name="create-a-regular-expression-entity"></a>Reguláris kifejezés entitásának létrehozása

Ha vissza szeretné állítani az űrlap számát a futásidejű előrejelzési válaszban, az űrlapot entitásként kell megjelölni. Mivel az űrlap-szám szövege erősen strukturált, egy reguláris kifejezéssel rendelkező entitás használatával jelölheti meg. Hozza létre az entitást a következő lépésekkel:

1. Válassza az **entitások** lehetőséget a bal oldali menüben.

1. Válassza a **Létrehozás** lehetőséget az **entitások** lapon.

1. Adja meg a `Human Resources Form Number`nevet, válassza ki a **regex** entitás típusát, majd kattintson a **tovább**gombra.

   ![Reguláris kifejezés entitásának létrehozása](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Adja meg a reguláris kifejezés (**regex**) kifejezését `hrf-[0-9]{6}`. Ez a bejegyzés megegyezik a literális `hrf-`karakterekkel, és lehetővé teszi pontosan 6 számjegy használatát, majd válassza a **Létrehozás**lehetőséget.

   ![Adja meg az entitás reguláris kifejezését](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Példa hosszúságú kimondott szöveg hozzáadása a none szándékhoz

A **none** cél a tartalék szándék, és nem hagyható üresen. Ennek a szándéknak tartalmaznia kell minden 10 példa hosszúságú kimondott szöveg, amelyet az alkalmazás többi szándékához hozzáadott.

A **nincs** leképezési példa hosszúságú kimondott szöveg az ügyfélalkalmazás tartományán kívül kell lennie.

1. Válassza a bal oldali menüben a **leképezések** lehetőséget, majd válassza a **nincs** lehetőséget a leképezések listából.

1. Adja hozzá a következő példa hosszúságú kimondott szöveg a szándékhoz:

   |Nincs leképezési példa hosszúságú kimondott szöveg|
   |--|
   |Az ugató kutyák idegesítőek|
   |Rendeljen nekem egy pizzát|
   |Pingvinek az óceánban|

   Ebben az alkalmazásban a példa hosszúságú kimondott szöveg a tartományon kívül esnek. Ha a tartomány tartalmazza az állatokat, az élelmiszereket vagy az óceánt, akkor a **none (nincs** ) cél hosszúságú kimondott szöveg eltérő példát kell használnia.

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Tekintse meg a reguláris kifejezés entitást a példában szereplő hosszúságú kimondott szöveg

1. Győződjön meg arról, hogy az entitás a **FindForm** szándékban található, a bal oldali menüben a **leképezések** lehetőség kiválasztásával. Ezután válassza a **FindForm** szándék lehetőséget.

   Az entitás meg van jelölve, ahol megjelenik a példában szereplő hosszúságú kimondott szöveg. Ha az entitás neve helyett az eredeti szöveget szeretné látni, váltson az **entitások nézetre** az eszköztárból.

   > [!div class="mx-imgBorder"]
   > [![Az összes példa entitásokkal jelölt hosszúságú kimondott szöveg](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Tesztelje az új alkalmazást az interaktív teszt panel segítségével

A LUIS-portál interaktív **teszt** paneljén ellenőrizheti, hogy az entitás kinyerve lett-e az új hosszúságú kimondott szöveg, az alkalmazás még nem látott.

1. Válassza a **tesztelés** lehetőséget a jobb felső menüben.

1. Adjon hozzá egy új értéket, majd nyomja le az ENTER billentyűt:

   ```Is there a form named hrf-234098```

    Válassza a **vizsgálat** lehetőséget az entitások előrejelzésének megtekintéséhez.

   > [!div class="mx-imgBorder"]
   > ![Új kifejezés tesztelése a teszt ablaktáblán](./media/get-started-portal-build-app/test-new-utterance.png)

   A felső előre jelzett szándék megfelelően **FindForm** , több mint 90%-os megbízhatósággal (0,977). A **Human Resources Form számú** entitást hrf-234098 értékkel extraháljuk.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezzel a rövid útmutatóval végzett, és nem a következő rövid útmutatóra vált, válassza a **saját alkalmazások** lehetőséget a felső navigációs menüből. Ezután jelölje be az alkalmazás bal oldali jelölőnégyzetét a listából, majd válassza a **Törlés** lehetőséget a lista fölötti helyi eszköztáron.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [2. alkalmazás üzembe helyezése](get-started-portal-deploy-app.md)
