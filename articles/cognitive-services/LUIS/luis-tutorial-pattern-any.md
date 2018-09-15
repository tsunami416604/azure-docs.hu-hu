---
title: '5. oktatóanyag: Pattern.any entitás szabad formátumú szöveg'
titleSuffix: Azure Cognitive Services
description: Használja a pattern.any entitás adatok kinyerését utterances ahol megcímkézzen helyes formátumú, és ahol az adatok végéig lehet, hogy könnyen keverendő össze a az utterance (kifejezés), a fennmaradó szavakat.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: dce75710137f4d4160cb2f55f856066c7c93ac78
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45628983"
---
# <a name="tutorial-5-extract-free-form-data"></a>Oktatóanyag: 5. Szabad formátumú adatokat nyerhet ki

Ebben az oktatóanyagban a pattern.any entitás használatával adatok kinyerése kimondott szöveg, amelyben megcímkézzen helyes formátumú, és ahol az adatok végéig lehet, hogy könnyen keverendő össze a az utterance (kifejezés), a fennmaradó szavakat. 

A pattern.any entitás szabad formátumú adatok kereséséhez, ahol az entitás parancsmagtól, így nehéz határozza meg a végén a vállalat, a többi az utterance (kifejezés) teszi lehetővé. 

Az emberi erőforrások alkalmazás segítségével az alkalmazottak céges képernyők. 

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

A változó hosszúságú magában foglalja a szavak, előfordulhat, hogy összekeveri a LUIS kapcsolatos, ahol az entitás véget ér. A mintában egy Pattern.any entitást használó lehetővé teszi, LUIS megfelelően kinyeri az űrlap neve elején és végén, a képernyő nevét adja meg.

|A példasablonban utterance (kifejezés)|
|--|
|Ahol a {űrlapnév} [?]|
|{Űrlapnév} [?] számára készített|
|Francia [?] {űrlapnév} van közzétéve|

**Ebből az oktatóanyagból megtudhatja, hogyan lehet:**

> [!div class="checklist"]
> * Használja meglévő oktatóanyag alkalmazása
> * Példa beszédmódok hozzáadása létező entitásba
> * Pattern.any entitás létrehozása
> * A minta létrehozása
> * Betanítás
> * Új minta

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az alkalmazás nevű az előző oktatóanyagban létrehozott **emberi**. 

Ha az előző oktatóanyagban az emberi alkalmazás nem rendelkezik, használja az alábbi lépéseket:

1.  Töltse le és mentse [alkalmazás JSON-fájlt](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json).

2. A JSON importálja egy új alkalmazást.

3. Az a **kezelés** részben, a a **verziók** lapon klónozza a verziót, és adja neki `patt-any`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. A verzió nevét az URL-útvonal részeként használja, mert a név nem tartalmazhat, amelyek nem érvényes URL-karaktereket.

## <a name="add-example-utterances"></a>Példa beszédmódok hozzáadása 
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

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>A szabad formátumú adatok kinyerése az új mintára tesztelése
1. Válassza ki **tesztelése** a felső sávon a teszt panel megnyitásához. 

2. Adja meg a következő utterance (kifejezés): 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Válassza ki **vizsgálat** entitáshoz és leképezés a teszteredmények megtekintéséhez az eredmény alapján.

    Az entitás `FormName` először is megtalálható, akkor megtalálja a mintát, amely meghatározza, hogy a célt. Ha rendelkezik egy vizsgálati eredmény, ahol az entitások nem észlelt, és ezért nem található a minta, szüksége további példa beszédmódok hozzáadása a célt (nem a minta).

4. Zárja be a teszt panelen válassza a **tesztelése** a felső navigációs gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban példa utterances hozzáadni egy meglévő szándékot, majd létrehozott egy új Pattern.any képernyő nevét. Az oktatóanyag majd mintaként a meglévő leképezést a létrehozott új példa utterances és entitás. Interaktív vizsgálati kimutatta, hogy a minta és annak szándékát voltak előre jelzett, mert az entitás található. 

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan használja a szerepkörök mintával](luis-tutorial-pattern-roles.md)