---
title: 'Rövid útmutató: Új alkalmazás létrehozása a LUIS portálon'
description: Ebben a rövid útmutatóban hozzon létre egy alkalmazás, leképezések és entitások alapvető részeit, valamint a LUIS-portálon a minta utterance (kifejezés) tesztelése.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: f0c8f0c77f832e049dfc494f82e90edb61a8cb2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80244614"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Rövid útmutató: Új alkalmazás létrehozása a LUIS portálon

Ebben a rövid útmutatóban egy új alkalmazást hoz létre a LUIS-portálon. Először hozza létre az alkalmazás, a **szándékok**és **az entitások**alapvető részeit. Ezután tesztelje az alkalmazást egy minta felhasználói utterance (kifejezés) az interaktív tesztpanelen az előre jelzett szándék leképezése érdekében.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Alkalmazás létrehozása

1. Válassza a **+ Új alkalmazás a beszélgetéshez** lehetőséget a környezet eszköztárán, majd az Új alkalmazás a **beszélgetéshez**lehetőséget.

    > [!div class="mx-imgBorder"]
    > [![Új alkalmazás létrehozása a LUIS portálon](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Az előugró ablakban konfigurálja az alkalmazást a következő beállításokkal, majd kattintson a **Kész gombra.**

   |Beállítás neve| Érték | Cél|
   |--|--|--|
   |Név|`myEnglishApp`|Egyedi LUIS-alkalmazásnév<br>kötelező|
   |Kulturális környezet|**angol**|A felhasználók kimondott szövegének nyelve, **en-us**<br>kötelező|
   |Leírás (nem kötelező)|`App made with LUIS Portal`|Az alkalmazás leírása<br>választható|
   |Előrejelzési erőforrás (nem kötelező) |-  |Ne válasszon. A LUIS egy kezdőkulcsot ad, amelyet ingyenesen használhat a szerzői és 1000 előrejelzési végpont-kérelmekhez. |

   ![Új alkalmazásbeállítások megadása](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Szándékok létrehozása

A LUIS-alkalmazás létrehozása után leképezéseket kell létrehoznia. A leképezések a felhasználók szövegének kategorizálására adnak leésre. Egy humánerőforrás-alkalmazás például két funkcióval rendelkezhet. Segítség az embereknek:

 1. Álláskeresés és állásra jelentkezés
 1. Állásra jelentkező űrlapok keresése

Az alkalmazás két különböző _szándéka_ a következő szándékokhoz igazodik:

|Szándék|Példa szöveg a felhasználótól<br>úgy ismert, mint egy _utterance (kifejezés)_|
|--|--|
|Jelentkezés|`I want to apply for the new software engineering position in Cairo.`|
|Űrlap keresése|`Where is the job transfer form hrf-123456?`|

Leképezések létrehozásához hajtsa végre az alábbi lépéseket:

1. Az alkalmazás létrehozása után a **Build** szakasz **Szándékok** lapján található. Kattintson a **Létrehozás** gombra.

   [![Új szándék létrehozásához válassza a Létrehozás lehetőséget.](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Írja be a `FindForm`szándék nevét , majd válassza **a Kész gombot.**

## <a name="add-an-example-utterance"></a>Példa utterance (kifejezés) hozzáadása

Szándékok létrehozása után példát ad hozzá. Példa utterances olyan szöveget, amelyet a felhasználó belép egy csevegőrobot vagy más ügyfélalkalmazás. Leképezhetik a felhasználó szövegének szándékát egy LUIS-szándékhoz.

Ebben a példában `FindForm` az alkalmazás szándéka, példa utterances tartalmazza az űrlap számát. Az ügyfélalkalmazásnak szüksége van az űrlapszámra a felhasználó kérésének teljesítéséhez, ezért fontos, hogy az utterance (kifejezés) szerepeljen.

> [!div class="mx-imgBorder"]
> [![Példa utterances a FindForm szándék](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adja hozzá a következő 15 `FindForm` példa utterances a szándékhoz.

|#|Példák kimondott szövegekre|
|--|--|
|1|Keres hrf-123456|
|2|Hol van a hrf-234591 humánerőforrás-nyomtatvány?|
|3|hrf-345623, hol van|
|4|Lehet-e küldeni nekem hrf-345794|
|5|Szükségem van hrf-234695 alkalmazni a belső munkát?|
|6|A menedzseremnek tudnia kell, hogy a hrf-234091-es állásra jelentkezem?|
|7|Hová küldjem hrf-234918? Kapok egy e-mail választ kapott?|
|8|óra-234555|
|9|Mikor volt hrf-234987 frissítve?|
|10|A hrf-876345-ös nyomtatványt használom a mérnöki pozíciókra való jelentkezéshez?|
|11|Volt egy új változata hrf-765234 benyújtott az én nyílt req?|
|12|A hrf-234234-et nemzetközi munkákhoz használom?|
|13|hrf-234598 helyesírási hiba|
|14|lesz hrf-234567 kell szerkesztett az új követelmények|
|15|óra-123456, óra-123123, óra-234567|

Ezek a példautterances változhat a következő módokon:

* utterance kifejezés hossza
* Írásjelek
* szó választás
* igeidő (van, volt, lesz)
* szósorrend



## <a name="create-a-regular-expression-entity"></a>Reguláris kifejezés entitás létrehozása

Az űrlapszám visszaadásához a futásidejű előrejelzési válaszban az űrlapot entitásként kell megjelölni. Mivel az űrlapszám szövege erősen strukturált, reguláris kifejezésentitáshasználatával is megjelölheti. Hozza létre az entitást a következő lépésekkel:

1. Válassza az **Entitások lehetőséget** a bal oldali menüből.

1. Válassza a **Létrehozás gombot** az **Entitások** lapon.

1. Írja be `Human Resources Form Number`a nevet, válassza a **Regex** entitástípust, majd a **Tovább**gombot.

   ![Reguláris kifejezés entitás létrehozása](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Írja be a reguláris kifejezés `hrf-[0-9]{6}`(**RegEx**) kifejezést, . Ez a bejegyzés megfelel `hrf-`a konstans karaktereknek, a , és pontosan 6 számjegyet tesz lehetővé, majd válassza a **Létrehozás**lehetőséget.

   ![Entitás reguláris kifejezése](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Példakimondott szöveg hozzáadása a Nincs szándékhoz

A **Nincs** szándék a tartalék szándék, és nem szabad üresen hagyni. Ez a szándék tartalmaznia kell egy utterance (kifejezés) minden 10 példa utterances, hogy az alkalmazás más leképezések hozzáadott.

A **Nincs** szándék példa utterances kívül kell lennie az ügyfélalkalmazás-tartományon kívül.

1. Válassza **a leképezések** a bal oldali menüben, majd válassza a **Nincs** a szándékok listájában.

1. Adja hozzá a következő példa utterances a szándék:

   |Nincs szándék példa utterances|
   |--|
   |Az ugató kutyák idegesítőek|
   |Rendeljen nekem egy pizzát|
   |Pingvinek az óceánban|

   Ehhez az alkalmazáshoz ezek a példa utterances kívül vannak a tartományon. Ha a tartomány ban állatok, élelmiszer, vagy az óceán, majd különböző példa utterances a **Nincs** szándék.

## <a name="train-the-app"></a>Az alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Tekintse meg a reguláris kifejezés entitása a példa utterances

1. Ellenőrizze, hogy az entitás megtalálható-e a **FindForm** leképezésben a bal oldali menü **Szándékok** parancsának kiválasztásával. Ezután válassza **az Űrlap keresése** leképezés lehetőséget.

   Az entitás van megjelölve, ahol megjelenik a példa utterances. Ha az entitásnév helyett az eredeti szöveget szeretné látni, kapcsolja be **az Entitások nézetet** az eszköztárról.

   > [!div class="mx-imgBorder"]
   > [![Minden példa kimondott szöveg entitásokkal megjelölt](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Az új alkalmazás tesztelése az interaktív tesztablakkal

A LUIS-portál interaktív **tesztablakával** ellenőrizze, hogy az entitás kivan-e nyerve az alkalmazás által még nem látott új kimondott szövegből.

1. Válassza a jobb felső menü **Teszt** parancsát.

1. Adjon hozzá egy új utterance (kifejezés) szöveget, majd nyomja le az Enter billentyűt:

   ```Is there a form named hrf-234098```

    Válassza **a Vizsgálat** lehetőséget az entitás-előrejelzések megtekintéséhez.

   > [!div class="mx-imgBorder"]
   > ![Új kimondott szöveg tesztelése a tesztablakban](./media/get-started-portal-build-app/test-new-utterance.png)

   A felső előre jelzett szándék helyesen **FindForm** több mint 90%-os megbízhatósággal (0,977). A **Humánerőforrás-űrlapszám** entitás hrf-234098 értékkel kerül kinyerésre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a rövid útmutatóval, és nem lép tovább a következő rövid útmutatóra, válassza a felső navigációs menü **Saját alkalmazások** parancsát. Ezután jelölje be az alkalmazás bal oldali jelölőnégyzetét a listából, és válassza a **Törlés** lehetőséget a lista feletti környezeti eszköztáron.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [2. Alkalmazás telepítése](get-started-portal-deploy-app.md)
