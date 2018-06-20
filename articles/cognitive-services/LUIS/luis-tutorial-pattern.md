---
title: Az oktatóanyagot minták használatával LUIS előrejelzéseket - Azure javítására |} Microsoft Docs
titleSuffix: Azure
description: Ebben az oktatóanyagban leképezések mintát segítségével LUIS leképezés és egyéb entitások előrejelzéseket javítása.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265316"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Oktatóanyag: A minták használatával javíthatja az előrejelzés

Ebben az oktatóanyagban minták segítségével növelheti a célt és egyéb entitások előrejelzés.  

> [!div class="checklist"]
* Azonosítása, hogy a minta segít az alkalmazás
* A minta létrehozása 
* A minta használata előre elkészített és egyéni entitások 
* Minta előrejelzés fejlesztései ellenőrzése
* Egy entitás található entitások összefüggéseikben való alapú szerepkör hozzáadása
* Egy szabad formátumú entitások található Pattern.any hozzáadása

Ez a cikk van szüksége egy szabad [LUIS] [ LUIS] fiók ahhoz, hogy a LUIS alkalmazás létrehozásához.

## <a name="import-humanresources-app"></a>Importálás emberi alkalmazás
Ez az oktatóanyag egy emberi app importálja. Az alkalmazás még három leképezések: None, GetEmployeeOrgChart, GetEmployeeBenefits. Az alkalmazás még két entitás: előre elkészített száma és alkalmazottak. A alkalmazott egy egyszerű entitás kibontásához egy alkalmazott neve. 

1. Hozzon létre egy új LUIS app fájlt, és adjon neki nevet `HumanResources.json`. 

2. Másolja a következő app-definíciót a fájlba:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. A a LUIS **alkalmazások** lapon jelölje be **importálási új alkalmazás**. 

4. Az a **importálási új alkalmazás** párbeszédpanelen válassza a `HumanResources.json` az 1. lépésben létrehozott fájlt.

5. Válassza ki a **GetEmployeeOrgChart** célt, majd módosítsa a **entitások nézet** való **nézet jogkivonatok**. Néhány példa utterances vannak felsorolva. Minden egyes utterance alkalmazott entitás nevét tartalmazza. Figyelje meg, hogy minden olyan név különböző, és a megállapodás szövegének minden utterance eltérő. Ez a változatosság utterances számos további LUIS segítségével.

    ![Képernyőfelvétel a cél lap váltható entitások nézet](media/luis-tutorial-pattern/utterances-token-view.png)

6. Válassza ki **betanítása** betanítása az alkalmazás a felső navigációs sávon. Várjon, amíg a zöld sikeres sáv.

7. Válassza ki **teszt** a az ablak tetején. Adja meg `Who does Patti Owens report to?` , majd válassza ki, adja meg. Válassza ki **vizsgálat** további információ a próba utterance alatt.
    
    Az alkalmazott nevét, Patti Owens nem szerepel egy példa utterance még. Ez az egy ellenőrizze, hogy mennyire LUIS megtanulta, ez utterance szolgál a `GetEmployeeOrgChart` leképezés és az alkalmazott entitás kell `Patti Owens`. Az eredmény 50 % alatt kell lennie (. 50) esetében a `GetEmployeeOrgChart` leképezés. A leképezés helyességét, amíg a pontszám értéke alacsony. Az alkalmazott entitás is megfelelően azonosítottak `Patti Owens`. Minták növelje meg a kezdeti előrejelzés pontszámot. 

    ![Képernyőfelvétel a teszt panel](media/luis-tutorial-pattern/original-test.png)

8. Zárja be a teszt panel kiválasztásával a **tesztelése** a felső navigációs sáv gombjára. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Mintákat mutatja meg LUIS közös utterances kevesebb-példákkal
Az emberi erőforrás-tartomány jellege miatt módon néhány gyakori kérni a szervezetben alkalmazott kapcsolatok. Példa:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Ezek utterances szorosan határozzák meg a környezetfüggő egyediségének sok utterance példák megadása nélkül. Egy mintát megjelölésű hozzáadásával LUIS megjelölésű közös utterance mintái megtanulja sok utterance példák megadása nélkül. 

Példa sablon utterances ezt leképezési a következők:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

A minta nem reguláris kifejezések egyeztetésének és a gépi tanulás. Ezt követően adjon meg néhány-sablont a minta további LUIS utterance példák. Ezekben a példákban a leképezési utterances adjon LUIS, milyen utterances megfelelően a célt átláthatóbbá és ott, ahol, a utterance belül az entitás. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>A sablon utterances hozzáadása

1. A bal oldali navigációs alatt **alkalmazás teljesítményének javítása**, jelölje be **minták** a bal oldali navigációs sávon.

2. Válassza ki a **GetEmployeeOrgChart** célt, majd írja be a következő sablon utterances, egyenként, kiválasztása után minden sablon utterance adja meg:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    A `{Employee}` szintaxis jelöli meg a sablon utterance, valamint entitáshoz van az entitás helyére. 

    ![Képernyőkép a sablon utterances a cél megadása](./media/luis-tutorial-pattern/enter-pattern.png)

3. Válassza ki **vonat** a felső navigációs sávban. Várjon, amíg a zöld sikeres sáv.

4. Válassza ki **teszt** a az ablak tetején. Adja meg `Who does Patti Owens report to?` a szövegmezőben. Válassza ki a adja meg. Ez az a azonos utterance tesztelve az előző szakaszban. Az eredmény a nagyobbnak kell lennie a `GetEmployeeOrgChart` leképezés. 

    A pontszám már javulás. LUIS megtanulta, a célt vonatkozó mintát sok példák megadása nélkül.

    ![Képernyőfelvétel a teszt panel a legjobb eredmény eredménye](./media/luis-tutorial-pattern/high-score.png)

    Az entitás először található, akkor megtalálja a mintát, a célt jelző. Ha egy tesztelési eredménye, ahol az entitás nem található a szolgáltatás, és ezért nem megtalálja a mintát, szüksége további példa utterances adja hozzá a célt (nem a minta). 

5. Zárja be a teszt panel kiválasztásával a **tesztelése** a felső navigációs sáv gombjára.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Egy entitás használja a mintában szerepkörrel
A LUIS app alkalmazottak áthelyezése egyik helyről egy másikra elősegítésére szolgál. Egy példa utterance van `Move Bob Jones from Seattle to Los Colinas`. Minden egyes helyére a utterance jelentése van. Budapest származási helyét, és másik Colinas az áthelyezés célhelye. Azokon a helyeken, az alábbi szakaszok a mintában megkülönböztetéséhez hoz létre egy egyszerű entitás hely két szerepkörök: forrás és cél. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Hozzon létre egy új leképezés áthelyezésére, személyek és eszközök
Hozzon létre egy új, amely mozgó felhasználók vagy eszközök készül egyetlen utterances leképezésének.

1. Válassza ki **leképezések** bal oldali navigációs sávon
2. Válassza ki **új leképezés létrehozásához**
3. Az új cél neve `MoveAssetsOrPeople`
4. Példa utterances hozzáadása:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Példa utterance MoveAssetsOrPeople leképezés a bemutató képernyőkép](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    A példa utterances célja elég példákat mutatnak. Ha a későbbi szakaszában a vizsgálat a hely entitás nem észlelhető, és ezért nem észlelte a minta, térjen vissza ehhez a lépéshez, és adja hozzá a további példákat. Majd betanítását, majd tesztelje újból. 

5. A példa utterances entitást az alkalmazott entitással megjelölni az Utónév, majd a Vezetéknév egy utterance kiválasztja, akkor az alkalmazott entitás listából történő kiválasztásakor.

    ![Képernyőfelvétel az alkalmazott entitás jelölésű MoveAssetsOrPeople utterances](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Válassza ki a szöveget `portland` a utterance a `move travis hinton from portland to orlando`. Az előugró párbeszédpanelen adja meg az új entitásnév `Location`, és válassza ki **új entitás létrehozása**. Válassza ki a **egyszerű** entitástípus, és válassza **végzett**.

    ![Képernyőkép a hely új entitás létrehozása](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Jelölje meg a többi a utterances a hely nevét. 

    ![Képernyőkép a megjelölt összes entitás](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    A minta word választott és egy rendezési nem egyértelmű az előző ábrán. Ha **nem** használatát, és a leképezés a utterances rendelkezik, amely kell használni minták jól jelzi egy nyilvánvaló mintában. 

    Ha várhatóan széles körét utterances helyett egy olyan mintát, ez a nem megfelelő példa utterances értendő. Ebben az esetben kívánt igen eltérő utterances, kifejezés vagy a word választás, a utterance hossza és a entitás elhelyezését. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Hely entitás szerepkör hozzáadása 
Szerepkörök csak mintára használható. A forrás és cél szerepkörök hozzáadása a hely entitás. 

1. Válassza ki **entitások** a bal oldali navigációs, majd **hely** az entitások listájából.

2. Adja hozzá `Origin` és `Destination` szerepkörök az entitásban.

    ![Új entitás szerepkörök képernyőképe](./media/luis-tutorial-pattern/location-entity.png)

    A szerepkörök nincsenek megjelölve MoveAssetsOrPeople leképezési lapon, mert a szerepkörök nem található a leképezési utterances. Csak léteznek, a minta sablon utterances. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Sablon utterances használó hely és a célkiszolgáló szerepkörök hozzáadása
Adja hozzá az új entitás használó sablon utterances.

1. Válassza ki **minták** a bal oldali navigációs sávon.

2. Válassza ki a **MoveAssetsOrPeople** leképezés.

3. Adjon meg egy új sablont utterance használatával az új entitás `Move {Employee} from {Location:Origin} to {Location:Destination}`. A szervezet és a sablon utterance belül szerepkör szintaxisa a következő `{entity:role}`.

    ![Új entitás szerepkörök képernyőképe](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Az alkalmazást az új leképezés, entitás és minta betanításához.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Az új mintát szerepkör adatok kiolvasásához tesztelése
Ellenőrizze az új mintát a teszt.

1. Válassza ki **teszt** a az ablak tetején. 
2. Adja meg a utterance `Move Tammi Carlson from Bellingham to Winthrop`.
3. Válassza ki **vizsgálat** személy és a leképezés a teszteredmények megtekintéséhez eredménye alapján.

    ![Új entitás szerepkörök képernyőképe](./media/luis-tutorial-pattern/test-with-roles.png)

    Az entitások először találhatók, akkor megtalálja a mintát, a célt jelző. Ha egy tesztelési eredménye, ahol az entitások nem észleli a rendszer, és ezért nem megtalálja a mintát, szüksége további példa utterances adja hozzá a célt (nem a minta). 

4. Zárja be a teszt panel kiválasztásával a **tesztelése** a felső navigációs sáv gombjára.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>A minta szabad formátumú entitások kereséséhez egy Pattern.any entitás használhatja
Az emberi alkalmazást is lehetővé teszi a vállalat képernyők alkalmazottak. Az űrlapok számos rendelkezik nevet, amely a rendszer változó hosszúságú. A változó hosszúságú kifejezéseket, ahol véget ér az űrlap neve kapcsolatos LUIS megzavarhatja tartalmazza. Használja a **Pattern.any** bankkártyaszám entitás lehetővé teszi a elején és végén lévő nevére, LUIS megfelelően kibontja az űrlap neve. 

### <a name="create-a-new-intent-for-the-form"></a>Az űrlap egy új leképezés létrehozásához
Hozzon létre egy új, amely a keresett űrlapok utterances leképezésének.

1. Válassza ki **leképezések** bal oldali navigációs sávon.

2. Válassza ki **új leképezés létrehozásához**.

3. Az új leképezés neve `FindForm`.

4. Adjon hozzá egy példa utterance.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Új entitás szerepkörök képernyőképe](./media/luis-tutorial-pattern/intent-findform.png)

    A képernyő cím `What to do when a fire breaks out in the Lab`. A utterance az űrlap helyzete által kért, és azt is kéri, akik be kell jelentkeznie, olvassa el az alkalmazott ellenőrzése. Egy Pattern.any entitás nélkül nehéz lenne tudni, ahol a címe véget ér, és bontsa ki a címe a utterance egy egységként.

### <a name="create-a-patternany-entity-for-the-form-title"></a>A képernyő cím Pattern.any entitás létrehozása
A Pattern.any entitás lehetővé teszi, hogy az entitások változó hosszúságú. Ez csak működik egy mintát, mert a minta elején és végén az entitás jelöli. Ha talál, amely a mintát, ha az egy Pattern.any, kivonatok entitások helytelenül, használjon egy [explicit lista](luis-concept-patterns.md#explicit-lists) a probléma elhárítása érdekében. 

1. Válassza ki **entitások** a bal oldali navigációs.

2. Válassza ki **új entitás létrehozása**. 

3. Az entitás neve `FormName` típusú **Pattern.any**. Az adott oktatóanyag nem kell a szerepkörök hozzáadása az entitáshoz.

    ![Az entitás neve és entitástípus párbeszédpanel képe](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Adja hozzá a Pattern.any használó minta

1. Válassza ki **minták** a bal oldali navigációs sávon.

2. Válassza ki a **FindForm** leképezés.

3. Adja meg a sablon utterance használatával az új entitáshoz `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Képernyőkép a sablon utterance pattern.any entitás használatával](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Az alkalmazást az új leképezés, entitás és minta betanításához.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Az új mintát, szabad formátumú adatokat kiolvasásához tesztelése
1. Válassza ki **tesztelése** a a felső sávon a teszt panel megnyitásához. 

2. Adja meg a utterance `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Válassza ki **vizsgálat** személy és a leképezés a teszteredmények megtekintéséhez eredménye alapján.

    ![Képernyőkép a sablon utterance pattern.any entitás használatával](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Az entitás először található, akkor megtalálja a mintát, a célt jelző. Ha egy tesztelési eredménye, ahol az entitások nem észleli a rendszer, és ezért nem megtalálja a mintát, szüksége további példa utterances adja hozzá a célt (nem a minta).

4. Zárja be a teszt panel kiválasztásával a **tesztelése** a felső navigációs sáv gombjára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, a LUIS alkalmazás törlése. Ehhez az szükséges, válassza a három pont menü (...) az alkalmazás nevétől jobbra alkalmazáslistájában, jelölje be **törlése**. Az előugró párbeszédpanelen **Delete app?**, jelölje be **Ok**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kifejezés lista segítségével javíthatja az előrejelzés](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions