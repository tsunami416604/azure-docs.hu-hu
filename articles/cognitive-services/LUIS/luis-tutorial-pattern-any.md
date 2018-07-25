---
title: Az oktatóanyag pattern.any entitást használó javítása a LUIS-előrejelzés – Azure |} A Microsoft Docs
titleSuffix: Cognitive Services
description: Ebben az oktatóanyagban a pattern.any entitás használatával javíthatja a LUIS-leképezés és entitás előrejelzéseket.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: ca4ed4444cd753c0fe22c6596dfd34d033b7b0cf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237002"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Oktatóanyag: Az entitás pattern.any alkalmazás fejlesztéséhez

Ebben az oktatóanyagban használja a pattern.any entitás szándékot és egyéb entitások előrejelzési növelése érdekében.  

> [!div class="checklist"]
* Ismerje meg, mikor és hogyan pattern.any használata
* Pattern.any használó minta létrehozása
* Előrejelzési fejlesztései ellenőrzése

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik az emberi erőforrások alkalmazásból a [szerepkörök mintát](luis-tutorial-pattern-roles.md) az oktatóanyagban [importálása](luis-how-to-start-new-app.md#import-new-app) a JSON-kódot egy új alkalmazást a [LUIS](luis-reference-regions.md#luis-website) webhely. Az alkalmazás importálása megtalálható a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) GitHub-adattárban.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `patt-any`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="the-purpose-of-patternany"></a>Pattern.any célját
A pattern.any entitás szabad formátumú adatok kereséséhez, ahol az entitás parancsmagtól, így nehéz határozza meg a végén a vállalat, a többi az utterance (kifejezés) teszi lehetővé. 

Az emberi erőforrások alkalmazás segítségével az alkalmazottak céges képernyők. Űrlapok verzióban lettek hozzáadva a [reguláris kifejezés az oktatóanyagban](luis-quickstart-intents-regex-entity.md). A képernyő nevét az oktatóanyag a használt reguláris kifejezés kinyerése egy űrlap neve, amely volt helyes formátumú, például a képernyő nevét a félkövér az alábbi táblázatban utterance (kifejezés):

|Kimondott szöveg|
|--|
|Hol van **HRF-123456**?|
|Ki lett létrehozva **HRF-123234**?|
|**HRF-456098** közzé van téve a francia?|

Azonban minden egyes űrlap mindkét formátumú névvel rendelkezik, az előző táblázatban, valamint egy rövid nevet, például a használt `Request relocation from employee new to the company 2018 version 5`. 

A rövid űrlapot nevű kimondott szöveg a következőhöz hasonló:

|Kimondott szöveg|
|--|
|Hol van **adatáthelyezési kérhet alkalmazott a vállalati 2018 5-ös verzió újdonságai**?|
|Ki lett létrehozva **"Adatáthelyezési kérhet a 2018-as vállalati 5-ös verzió újdonságai alkalmazott"**?|
|**Az adatáthelyezési kérhet alkalmazott a vállalati 2018 5-ös verzió újdonságai** közzé van téve a francia?|

A változó hosszúságú tartalmazza a kifejezéseket, előfordulhat, hogy összekeveri a LUIS kapcsolatos, ahol az entitás véget ér. A mintában egy Pattern.any entitást használó lehetővé teszi, LUIS megfelelően kinyeri az űrlap neve elején és végén, a képernyő nevét adja meg.

**Minták lehetővé teszik, hogy kevesebb példa kimondott szöveg, ha az entitások nem észlelt, miközben a mintázat nem felel meg.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>A meglévő beszédszándék FindForm példa beszédmódok hozzáadása 
Távolítsa el az előre összeállított keyPhrase entitás, nehezen létrehozható és címke az Űrlapnév entitás esetén. 

1. Válassza ki **összeállítása** a felső navigációs sávon, majd válassza ki **leképezések** bal oldali navigációs sávon.

2. Válassza ki **FindForm** leképezések listájából.

3. Néhány példa beszédmódok hozzáadása:

    |Példa utterance (kifejezés)|
    |--|
    |Hol található az űrlap **Mi a teendő, ha tűz-hívásokra osztja a laborban** és aláírja azt követően elolvasni kell?|
    |Hol van **adatáthelyezési kérhet a cég az új alkalmazottak** a kiszolgálón?|
    |Ki lett létrehozva "**egészséges életmód kérelmeket a fő campus**", és mi az a legújabb verziót?|
    |Az általam keresett nevű űrlap "**Office áthelyezési kérelem, beleértve a fizikai eszközök**". |

    Egy Pattern.any entitás nélkül nehéz lenne a LUIS megértéséhez, ahol az űrlap címe véget ér, a számos változata létezik űrlap nevek miatt.

## <a name="create-a-patternany-entity"></a>Pattern.any entitás létrehozása
A Pattern.any entitás változó hosszúságú entitásokat ad eredményül. Csak működik a mintát, mert a minta elején és végén az entitás jelöli. Ha talál, amely a mintát, ha egy Pattern.any tartalmazza, nem megfelelően, használja-e kivonatokat entitások egy [explicit lista](luis-concept-patterns.md#explicit-lists) a probléma elhárításához. 

1. Válassza ki **entitások** a bal oldali navigációs menüben.

2. Válassza ki **új entitás létrehozása**, adja meg a nevét `FormName`, és válassza ki **Pattern.any** típusaként. Válassza a **Done** (Kész) lehetőséget. 

    A leképezést az entitás nem címkét, mert egy Pattern.any csak akkor érvényes a mintát. 

    Ha azt szeretné, hogy a kibontott adatok más entitások, például számot vagy datetimeV2 tartalmazza, hozzon létre egy összetett entitást, amely tartalmazza az Pattern.any, valamint száma és datetimeV2 szeretne.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Adja hozzá a Pattern.any használó minta

1. Válassza ki **minták** a bal oldali navigációs sávon.

2. Válassza ki a **FindForm** szándékot.

3. Adja meg a következő sablon utterances, az új entitást használó:

    |Sablon kimondott szöveg|
    |--|
    |Hol található az űrlap ["] {űrlapnév} ["], és ki kell bejelentkeznie, miután elolvasni [?]|
    |Hol van ["] {űrlapnév} ["] a következő kiszolgálón: [?]|
    |Akik készített ["] {űrlapnév} ["], és mi az a legfrissebb verziója [?]|
    |Az általam keresett nevű űrlap ["] {űrlapnév} ["] [.]|

    Ha azt szeretné, hogy figyelembe vegye az űrlap például helyett idézőjeleket szimpla idézőjelek között vagy a kérdőjel helyett időszak változata, hozzon létre egy új mintát, az egyes módosítások.

4. Ha eltávolította a keyPhrase entitás, adja hozzá újból az alkalmazást. 

5. Az alkalmazás betanításához.


## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>A szabad formátumú adatok kinyerése az új mintára tesztelése
1. Válassza ki **tesztelése** a felső sávon a teszt panel megnyitásához. 

2. Adja meg a következő utterance (kifejezés): 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Válassza ki **vizsgálat** entitáshoz és leképezés a teszteredmények megtekintéséhez az eredmény alapján.

    Az entitás `FormName` először is megtalálható, akkor megtalálja a mintát, amely meghatározza, hogy a célt. Ha rendelkezik egy vizsgálati eredmény, ahol az entitások nem észlelt, és ezért nem található a minta, szüksége további példa beszédmódok hozzáadása a célt (nem a minta).

4. Zárja be a teszt panelen válassza a **tesztelése** a felső navigációs gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza a három pontot (***...*** ) az alkalmazások listájában, az alkalmazás nevétől jobbra, válassza ki a **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használja a szerepkörök mintával](luis-tutorial-pattern-roles.md)