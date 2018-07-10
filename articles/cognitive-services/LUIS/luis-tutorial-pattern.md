---
title: Az oktatóanyag minták segítségével javíthatja a LUIS-előrejelzés – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Ebben az oktatóanyagban a leképezések mintájának használatával javíthatja a LUIS szándékot és egyéb entitások előrejelzéseket.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 9793b98c384346dc0de68061d42b4bcb3c513ed4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866202"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Oktatóanyag: A minták előrejelzéseket javítása érdekében

Ebben az oktatóanyagban minták használatával növelheti a leképezés és egyéb entitások előrejelzési.  

> [!div class="checklist"]
* Hogyan azonosíthatja, hogy egy minta segíthet az alkalmazás
* A minta létrehozása 
* A minta előre elkészített és az egyéni entitások használata 
* Azt, hogyan ellenőrizheti, mintát előrejelzési fejlesztései
* Szerepkör hozzáadása egy entitás kontextusban-alapú entitások keresése
* Egy Pattern.any található szabad formátumú entitások hozzáadása

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="import-humanresources-app"></a>Emberi alkalmazás importálása
Ebben az oktatóanyagban egy emberi alkalmazás importál. Az alkalmazás három leképezések rendelkezik: None, GetEmployeeOrgChart, GetEmployeeBenefits. Az alkalmazás két entitás rendelkezik: előre elkészített száma és alkalmazottak. Az alkalmazott entitás, az alkalmazottak nevének kinyerése egy egyszerű entitás. 

1. Hozzon létre egy új fájlt a LUIS alkalmazás `HumanResources.json`. 

2. A következő app-definíciót másolja a fájlba:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. A LUIS szolgáltatáson alapuló **alkalmazások** lapon jelölje be **importálása új alkalmazás**. 

4. Az a **importálása új alkalmazás** párbeszédpanelen válassza ki a `HumanResources.json` az 1. lépésben létrehozott fájlt.

5. Válassza ki a **GetEmployeeOrgChart** szándékot, majd módosítsa a **entitások nézet** való **nézet jogkivonatok**. Néhány példa utterances jelennek meg. Minden kimondásakor egy nevet, amely egy alkalmazott entitás tartalmazza. Figyelje meg, hogy minden egyes neve nem egyezik, és a megfogalmazást elrendezésének eltérő az egyes utterance (kifejezés). Ez a sokféleség utterances számos további LUIS segítségével.

    ![Entitások megtekintése lap képernyőképe a leképezés vezérlőelem](media/luis-tutorial-pattern/utterances-token-view.png)

6. Válassza ki **betanításához** a felső navigációs sávban az alkalmazás betanításához. Várjon, amíg a zöld, sikeres sávon.

7. Válassza ki **teszt** a az ablak tetején. Adja meg `Who does Patti Owens report to?` majd válassza ki az Entert. Válassza ki **vizsgálat** alatt az utterance (kifejezés), a teszt kapcsolatos további információkat.
    
    Egy példa utterance (kifejezés) még nem használták Patti Owens, az alkalmazott neve. Ez a tesztüzenet arról, hogy a LUIS megtudhatta, a rendszer az utterance (kifejezés) megtekintéséhez a `GetEmployeeOrgChart` szándékot és az alkalmazott entitást kell `Patti Owens`. Az eredmény 50 %-os alatt kell lennie (. 50) esetében a `GetEmployeeOrgChart` szándékot. A leképezés helyességét, míg a pontszám értéke alacsony. Az alkalmazott entitás is megfelelően legyen azonosítva `Patti Owens`. Minták a kezdeti előrejelzési pontszám növelése. 

    ![Képernyőfelvétel: a teszt panel](media/luis-tutorial-pattern/original-test.png)

8. Zárja be a teszt panelen válassza a **tesztelése** a felső navigációs gombra. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Minták tanít LUIS kevesebb példákkal közös kimondott szöveg
Az emberi erőforrás-tartomány jellege miatt a szervezetben alkalmazott kapcsolatok kapcsolatos néhány gyakori módon is. Példa:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Ezek a kimondott szöveg szorosan határozzák meg az egyes nélkül számos utterance (kifejezés) példákkal környezetfüggő egyediségét. Ad hozzá egy minta megjelölésű, LUIS megtanulja megjelölésű közös utterance (kifejezés) minta számos utterance (kifejezés) Példák megadása nélkül. 

Példa a sablon utterances ezt szándék a következők:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

A reguláris kifejezések egyeztetésének és a machine learning egyik. Következő lépésként adja meg néhány sablont LUIS a minta további példákat utterance (kifejezés). Ezekben a példákban a szándék utterances adjon LUIS, milyen kimondott szöveg igazítása a leképezés átláthatóbbá és, amelyen belül az utterance (kifejezés), az entitás létezik. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>A sablon beszédmódok hozzáadása

1. A bal oldali navigációs területen **megnövelheti az alkalmazások teljesítményét**, jelölje be **minták** a bal oldali navigációs sávon.

2. Válassza ki a **GetEmployeeOrgChart** szándékot, majd adja meg a következő sablon megcímkézzen, egyenként meg kiválasztása után minden sablon utterance (kifejezés):

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    A `{Employee}` szintaxis jelöli meg a sablon utterance (kifejezés), valamint entitáshoz van az entitás helyére. 

    ![Képernyőkép a leképezés a sablon utterances megadása](./media/luis-tutorial-pattern/enter-pattern.png)

3. Válassza ki **Train** a felső navigációs sávban. Várjon, amíg a zöld, sikeres sávon.

4. Válassza ki **teszt** a az ablak tetején. Adja meg `Who does Patti Owens report to?` a szövegmezőben. Válassza ki a adja meg. Ez az az azonos utterance (kifejezés) az előző szakaszban tesztelni. Az eredmény nagyobb a kell lennie a `GetEmployeeOrgChart` szándékot. 

    A pontszám már sokkal hatékonyabb. Használatával egy Pattern.any entitást a minta lehetővé teszi, hogy így LUIS megfelelően kinyeri az űrlap neve elején és végén, a képernyő nevét adja meg.

    ![Az űrlap egy új leképezésének létrehozása](./media/luis-tutorial-pattern/high-score.png)

    Hozzon létre egy új célja, hogy a képernyők keres megcímkézzen számára. Válassza ki leképezések bal oldali navigációs sávon. 

5. Zárja be a teszt panelen válassza a **tesztelése** a felső navigációs gombra.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Nevezze el az új szándékot .
Adjon hozzá egy példa utterance (kifejezés). A képernyő címe az `Move Bob Jones from Seattle to Los Colinas`. Az utterance (kifejezés) kérése a helyet a következő formátumban van, és azt is kéri, olvassa el, az alkalmazott ellenőrzése alá kell. Egy Pattern.any entitás nélkül nehéz átlátni, ahol az űrlap címe ér véget, és bontsa ki az űrlap címe az utterance (kifejezés), egy egységként lenne. A képernyő címének Pattern.any entitás létrehozása 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>A Pattern.any entitás lehetővé teszi, hogy az entitások változó hosszúságú.
Csak működik a mintát, mert a minta elején és végén az entitás jelöli.

1. Válassza ki **entitások** a bal oldali navigációs menüben.
2. Az entitás neve **típusú**Pattern.any.**
3. Adott ehhez az oktatóanyaghoz nem kell minden olyan szerepkörök hozzáadása az entitáshoz. `MoveAssetsOrPeople`
4. Entitás neve és entitástípus párbeszédpanel

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Adja hozzá a Pattern.any használó minta](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Válassza ki a FindForm szándékot. Adja meg az új entitást használó sablon utterance (kifejezés) Képernyőkép a sablon utterance (kifejezés) pattern.any entitást használó 

5. A szabad formátumú adatok kinyerése az új mintára tesztelése

    ![Válassza ki tesztelése a felső sávon a teszt panel megnyitásához.](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. A kifejezés lista `portland`segítségével előrejelzési `move travis hinton from portland to orlando` javítása Az előugró párbeszédpanelen adja meg az új entitás nevét `Location`, és válassza ki **új entitás létrehozása**. Válassza ki a **egyszerű** entitástípus, és válassza ki **kész**.

    ![Képernyőkép az új hely entitás létrehozása](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Jelölje meg a hely neve a kimondott szöveg többi részétől. 

    ![Képernyőkép a megjelölt összes entitás](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    A minta word választási lehetőség és a sorrend, akkor az előző képen látható. Ha **nem** használatával mintákat, és a szándéka megcímkézzen rendelkezik, amely jól jelzi kell minták használatával egy felmerül a nyilvánvaló minta. 

    Ha várhatóan széles körének kimondott szöveg helyett egy olyan mintát, ezek nem megfelelő példa megcímkézzen lenne. Ebben az esetben a kifejezés vagy a word választás, utterance (kifejezés) hossza és entitás elhelyezési széles körben különböző utterances kapni szeretne. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Szerepkör hozzáadása hely entitáshoz 
Szerepkörök csak használható mintákat. A forrás és cél-szerepkörök hozzáadása a hely entitáshoz. 

1. Válassza ki **entitások** a bal oldali navigációs menüben, majd **hely** az entitások listájában.

2. Adjon hozzá `Origin` és `Destination` szerepkörök az entitáshoz.

    ![Képernyőkép a szerepkörök az új entitás](./media/luis-tutorial-pattern/location-entity.png)

    A szerepkörök a MoveAssetsOrPeople szándék oldalon nem vannak megjelölve, mert a szerepkörök nem léteznek a szándék kimondott szöveg. Csak léteznek, a minta sablon kimondott szöveg. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Hely és a cél szerepköröket használó sablon beszédmódok hozzáadása
Az új entitást használó sablon beszédmódok hozzáadása.

1. Válassza ki **minták** a bal oldali navigációs sávon.

2. Válassza ki a **MoveAssetsOrPeople** szándékot.

3. Adjon meg egy új sablont utterance (kifejezés) az új entitást használó `Move {Employee} from {Location:Origin} to {Location:Destination}`. Az entitásokhoz és a egy sablon utterance (kifejezés) belüli szerepkör szintaxisa `{entity:role}`.

    ![Képernyőkép a szerepkörök az új entitás](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Az alkalmazás az új szándékot, a személy és a minta betanításához.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>A szerepkör adatok kinyerése az új mintára tesztelése
Az új mintára ellenőrzés egy tesztet.

1. Válassza ki **teszt** a az ablak tetején. 
2. Írja be az utterance (kifejezés) `Move Tammi Carlson from Bellingham to Winthrop`.
3. Válassza ki **vizsgálat** entitáshoz és leképezés a teszteredmények megtekintéséhez az eredmény alapján.

    ![Képernyőkép a szerepkörök az új entitás](./media/luis-tutorial-pattern/test-with-roles.png)

    Az entitásokat először találhatók, akkor megtalálja a mintát, a célt jelző. Ha rendelkezik egy vizsgálati eredmény, ahol az entitások nem észlelt, és ezért nem található a minta, szüksége további példa beszédmódok hozzáadása a célt (nem a minta). 

4. Zárja be a teszt panelen válassza a **tesztelése** a felső navigációs gombra.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Szabad formátumú entitások található mintát használja egy Pattern.any entitás
Az emberi alkalmazás képernyők a vállalat alkalmazottai is segít. Az űrlapok számos rendelkezik vannak változó hosszúságú leltárát. A változó hosszúságú tartalmazza a kifejezéseket, előfordulhat, hogy összekeveri a LUIS kapcsolatos, ahol az űrlap neve véget ér. Használatával egy **Pattern.any** entitást a minta lehetővé teszi, hogy így LUIS megfelelően kinyeri az űrlap neve elején és végén, a képernyő nevét adja meg. 

### <a name="create-a-new-intent-for-the-form"></a>Az űrlap egy új leképezésének létrehozása
Hozzon létre egy új célja, hogy a képernyők keres megcímkézzen számára.

1. Válassza ki **leképezések** bal oldali navigációs sávon.

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

3. Nevezze el az új szándékot `FindForm`.

4. Adjon hozzá egy példa utterance (kifejezés).

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Képernyőkép a szerepkörök az új entitás](./media/luis-tutorial-pattern/intent-findform.png)

    A képernyő címe az `What to do when a fire breaks out in the Lab`. Az utterance (kifejezés) kérése a helyet a következő formátumban van, és azt is kéri, olvassa el, az alkalmazott ellenőrzése alá kell. Egy Pattern.any entitás nélkül nehéz átlátni, ahol az űrlap címe ér véget, és bontsa ki az űrlap címe az utterance (kifejezés), egy egységként lenne.

### <a name="create-a-patternany-entity-for-the-form-title"></a>A képernyő címének Pattern.any entitás létrehozása
A Pattern.any entitás lehetővé teszi, hogy az entitások változó hosszúságú. Csak működik a mintát, mert a minta elején és végén az entitás jelöli. Ha talál, amely a mintát, ha egy Pattern.any tartalmazza, nem megfelelően, használja-e kivonatokat entitások egy [explicit lista](luis-concept-patterns.md#explicit-lists) a probléma elhárításához. 

1. Válassza ki **entitások** a bal oldali navigációs menüben.

2. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. 

3. Az entitás neve `FormName` típusú **Pattern.any**. Adott ehhez az oktatóanyaghoz nem kell minden olyan szerepkörök hozzáadása az entitáshoz.

    ![Entitás neve és entitástípus párbeszédpanel](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Adja hozzá a Pattern.any használó minta

1. Válassza ki **minták** a bal oldali navigációs sávon.

2. Válassza ki a **FindForm** szándékot.

3. Adja meg az új entitást használó sablon utterance (kifejezés) `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Képernyőkép a sablon utterance (kifejezés) pattern.any entitást használó](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Az alkalmazás az új szándékot, a személy és a minta betanításához.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>A szabad formátumú adatok kinyerése az új mintára tesztelése
1. Válassza ki **tesztelése** a felső sávon a teszt panel megnyitásához. 

2. Írja be az utterance (kifejezés) `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Válassza ki **vizsgálat** entitáshoz és leképezés a teszteredmények megtekintéséhez az eredmény alapján.

    ![Képernyőkép a sablon utterance (kifejezés) pattern.any entitást használó](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Hozzon létre egy új célja, hogy a képernyők keres megcímkézzen számára. Ha rendelkezik egy vizsgálati eredmény, ahol az entitások nem észlelt, és ezért nem található a minta, szüksége további példa beszédmódok hozzáadása a célt (nem a minta).

4. Zárja be a teszt panelen válassza a **tesztelése** a felső navigációs gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza a három pontot (***...*** ) az alkalmazások listájában, az alkalmazás nevétől jobbra, válassza ki a **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A kifejezés lista segítségével előrejelzési javítása](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions