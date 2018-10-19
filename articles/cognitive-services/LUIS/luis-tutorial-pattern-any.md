---
title: '5. oktatóanyag: Pattern.any entitás szabad formátumú szöveghez'
titleSuffix: Azure Cognitive Services
description: A pattern.any entitás használata az adatok kimondott szövegekből való kinyeréséhez olyankor, amikor a kimondott szövegek helyesen formázottak, és az adatok vége könnyen összekeverhető a kimondott szöveg fennmaradó szavaival.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 90dc7b8bc69c86128b65c16920886b7c4af5c5cf
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888305"
---
# <a name="tutorial-5-extract-free-form-data"></a>5. oktatóanyag: Szabad formátumú adatok kinyerése

Ebben az oktatóanyagban a pattern.any entitást használja az adatok kimondott szövegekből való kinyeréséhez olyankor, amikor a kimondott szövegek helyesen formázottak, és az adatok vége könnyen összekeverhető a kimondott szöveg fennmaradó szavaival. 

A pattern.any entitás lehetővé teszi szabad formátumú adatok keresését olyankor, amikor az entitás szövegezése nehézzé teszi az entitás végének a kimondott szöveg fennmaradó részétől való elkülönítését. 

Ez az Emberierőforrások-alkalmazás segítséget nyújt az alkalmazottaknak a vállalati űrlapok keresésében. 

|Kimondott szöveg|
|--|
|Hol található a **HRF-123456**?|
|Ki a szerzője ennek: **HRF-123234**?|
|A **HRF-456098** űrlap francia nyelven lett közzétéve?|

Ugyanakkor minden űrlapnak két neve van: egyrészt az előző táblázatban használt formázott neve, másrészt egy könnyebb elnevezése, mint például `Request relocation from employee new to the company 2018 version 5`. 

A könnyebb űrlapnévvel rendelkező kimondott szöveg a következőképpen néz ki:

|Kimondott szöveg|
|--|
|Hol található a **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió**?|
|Ki a szerzője ennek: **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió**?|
|A **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme, 5. verzió** űrlap francia nyelven lett közzétéve?|

A változó hosszúságú szöveg olyan szavakat tartalmaz, amelyek megnehezítik a LUIS szolgáltatás számára az entitás végének meghatározását. A Pattern.any entitás mintában való használata lehetővé teszi az űrlapnév elejének és végének megadását, így a LUIS helyesen nyeri ki az űrlapnevet.

|Példa kimondott szöveg sablonja|
|--|
|Hol található a {FormName}[?]|
|Ki a szerzője ennek: {FormName}[?]|
|A {FormName} űrlap francia nyelven lett közzétéve[?]|

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * Példa kimondott szöveg hozzáadása meglévő entitáshoz
> * Pattern.any entitás létrehozása
> * Minta létrehozása
> * Betanítás
> * Új minta tesztelése

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `patt-any` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="add-example-utterances"></a>Példa kimondott szövegek hozzáadása 
Ha nehéz létrehozni és címkézni a FormName entitást, távolítsa el az előre összeállított keyPhrase entitást. 

1. Válassza a **Build** (Összeállítás) lehetőséget a felső navigációs sávon, majd az **Intents** (Leképezések) elemet a bal oldali navigációs sávon.

2. A leképezések listájáról válassza a **FindForm** (Űrlap keresése) lehetőséget.

3. Adjon hozzá néhány példa kimondott szöveget:

    |Példa kimondott szöveg|
    |--|
    |Hol található a **Mi a teendő, ha tűz keletkezik a laborban** űrlap, és kinek kell aláírnia, miután elolvastam?|
    |Hol található a **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme** űrlap a kiszolgálón?|
    |Ki a szerzője ennek: "**Egészségügyi és wellnesskérelmek a fő campuson**", és melyik a jelenlegi verziója?|
    |Az alábbi űrlapot keresem: "**Irodaáthelyezési kérelem, beleértve a fizikai eszközöket**". |

    Mivel az űrlapnevek nagyon változatosak, a Pattern.any entitás hiánya megnehezíti a LUIS szolgáltatás számára az űrlapcím végének meghatározását.

## <a name="create-a-patternany-entity"></a>Pattern.any entitás létrehozása
A Pattern.any entitás változó hosszúságú entitások kinyerését végzi. Csak mintában működik, mivel a minta jelöli az entitás elejét és végét. Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](luis-concept-patterns.md#explicit-lists) megoldhatja a problémát. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs sávon.

2. Válassza a **Create new entity** (Új entitás létrehozása) elemet, adja meg a `FormName` nevet, és típusként válassza ki a **Pattern.any** lehetőséget. Válassza a **Done** (Kész) lehetőséget. 

    A leképezésben nem tudja címkézni az entitást, mivel a Pattern.any csak mintában érvényes. 

    Ha a kivont adatokat olyan más entitásokba szeretné belefoglalni, mint a szám vagy a datetimeV2, létre kell hoznia egy összetett entitást, amely a Pattern.any entitást, valamint a számot és a datetimeV2-t is tartalmazza.

## <a name="add-a-pattern-that-uses-the-patternany"></a>A Pattern.any entitást használó minta hozzáadása

1. Válassza a **Patterns** (Minták) elemet a bal oldali navigációs sávban.

2. Válassza ki a **FindForm** (Űrlap keresése) leképezést.

3. Adja meg a következő, az új entitást használó kimondottszöveg-sablont:

    |Kimondottszöveg-sablon|
    |--|
    |Hol található a ["]{FormName}["] űrlap, és kinek kell aláírnia, miután elolvastam[?]|
    |Hol található a ["]{FormName}["] űrlap a kiszolgálón[?]|
    |Ki a szerzője ennek: ["]{FormName}["], és melyik a jelenlegi verziója[?]|
    |Az alábbi űrlapot keresem: ["]{FormName}["][.]|

    Ha azt szeretné, hogy a rendszer figyelembe vegye a különböző űrlapváltozatokat, például ha szimpla idézőjelet (aposztrófot) adnak meg a dupla idézőjel helyett, vagy pontot a kérdőjel helyett, hozzon létre új mintát minden egyes változathoz.

4. Ha eltávolította a keyPhrase entitást, adja hozzá újból az alkalmazáshoz. 

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tesztelje az új mintát a szabad formátumú adatok kinyerésére
1. A tesztelési panel megnyitásához válassza a felső sávon a **Test** lehetőséget. 

2. Adja meg a következő kimondott szöveget: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Az entitáshoz és a leképezéshez tartozó teszteredmények megtekintéséhez válassza az eredmény alatti **Inspect** (Vizsgálat) lehetőséget.

    A leképezést az először megtalált `FormName` entitás, majd a megtalált minta határozza meg. Ha a tesztelés során a rendszer nem észlelte az entitásokat, és ezért nem találta a mintát, további példa kimondott szövegeket kell megadnia a leképezésben (nem a mintában).

4. A felső navigációs sáv **Test** gombjával zárja be a tesztelési panelt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag egy meglévő leképezéshez adott hozzá példa kimondott szövegeket, majd létrehozott egy új Pattern.any entitást az űrlapnévhez. Az oktatóanyag ezután az új példa kimondott szövegekkel és entitással mintát hozott létre a meglévő leképezéshez. Az interaktív tesztelés megmutatta, hogy mivel az entitást a rendszer megtalálta, a minta és annak leképezései előrejelezhetők voltak. 

> [!div class="nextstepaction"]
> [Ismerje meg a szerepkörök mintával való használatát](luis-tutorial-pattern-roles.md)