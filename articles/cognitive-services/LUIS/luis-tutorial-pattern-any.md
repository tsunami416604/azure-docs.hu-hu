---
title: 'Oktatóanyag: minta. bármely entitás – LUIS'
titleSuffix: Azure Cognitive Services
description: A pattern.any entitás használata az adatok kimondott szövegekből való kinyeréséhez olyankor, amikor a kimondott szövegek helyesen formázottak, és az adatok vége könnyen összekeverhető a kimondott szöveg fennmaradó szavaival.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: c118a74e273e153746240465692b1c712bd25f47
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953603"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Oktatóanyag: szabad formátumú adatok kinyerése mintázattal. bármely entitás

Ebben az oktatóanyagban a pattern.any entitást használja az adatok kimondott szövegekből való kinyeréséhez olyankor, amikor a kimondott szövegek helyesen formázottak, és az adatok vége könnyen összekeverhető a kimondott szöveg fennmaradó szavaival. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Példa kimondott szöveg hozzáadása meglévő entitáshoz
> * Pattern.any entitás létrehozása
> * Minta létrehozása
> * Betanítás
> * Új minta tesztelése

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Minta. bármely entitás használata

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

|Példa kimondott sablonszövegre|
|--|
|Hol található a {FormName}[?]|
|Ki a szerzője ennek: {FormName}[?]|
|A {FormName} űrlap francia nyelven lett közzétéve[?]|

## <a name="import-example-app"></a>Alkalmazás importálása – példa

1. Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json).

1. A [Luis portál](https://www.luis.ai) **saját alkalmazások** LAPJÁN importálja a JSON-t egy új alkalmazásba.

1. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `patt-any` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="add-example-utterances"></a>Példa kimondott szövegek hozzáadása 

1. Válassza a **Build** (Összeállítás) lehetőséget a felső navigációs sávon, majd az **Intents** (Leképezések) elemet a bal oldali navigációs sávon.

1. A leképezések listájáról válassza a **FindForm** (Űrlap keresése) lehetőséget.

1. Adjon hozzá néhány példa kimondott szöveget:

    |Példa kimondott szöveg|
    |--|
    |Hol található a **Mi a teendő, ha tűz keletkezik a laborban** űrlap, és kinek kell aláírnia, miután elolvastam?|
    |Hol található a **2018-ban a vállalathoz került alkalmazott áthelyezési kérelme** űrlap a kiszolgálón?|
    |Ki a szerzője ennek: "**Egészségügyi és wellnesskérelmek a fő campuson**", és melyik a jelenlegi verziója?|
    |Az alábbi űrlapot keresem: "**Irodaáthelyezési kérelem, beleértve a fizikai eszközöket**". |

    Mivel az űrlapnevek nagyon változatosak, a Pattern.any entitás hiánya megnehezíti a LUIS szolgáltatás számára az űrlapcím végének meghatározását.

## <a name="create-a-patternany-entity"></a>Pattern.any entitás létrehozása
A Pattern.any entitás változó hosszúságú entitások kinyerését végzi. Csak mintában működik, mivel a minta jelöli az entitás elejét és végét.  

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs sávon.

1. Válassza a **Create new entity** (Új entitás létrehozása) elemet, adja meg a `FormName` nevet, és típusként válassza ki a **Pattern.any** lehetőséget. Válassza a **Done** (Kész) lehetőséget. 

    Az entitás nem címkézhető példa hosszúságú kimondott szöveg, mert egy minta. any csak egy mintában érvényes. 

    Ha a kivont adatokat olyan más entitásokba szeretné belefoglalni, mint a number vagy a datetimeV2, létre kell hoznia egy összetett entitást, amely a Pattern.any entitást, valamint a number és a datetimeV2 entitást is tartalmazza.

## <a name="add-a-pattern-that-uses-the-patternany"></a>A Pattern.any entitást használó minta hozzáadása

1. Válassza a **Patterns** (Minták) elemet a bal oldali navigációs sávban.

1. Válassza ki a **FindForm** (Űrlap keresése) leképezést.

1. Adja meg a következő, az új entitást használó kimondottszöveg-sablont:

    |Kimondottszöveg-sablonok|
    |--|
    |Hol található a ["]{FormName}["] űrlap, és kinek kell aláírnia, miután elolvastam[?]|
    |Hol található a ["]{FormName}["] űrlap a kiszolgálón[?]|
    |Ki a szerzője ennek: ["]{FormName}["], és melyik a jelenlegi verziója[?]|
    |Az alábbi űrlapot keresem: ["]{FormName}["][.]|

    Ha azt szeretné, hogy a rendszer figyelembe vegye a különböző űrlapváltozatokat, például ha szimpla idézőjelet (aposztrófot) adnak meg a dupla idézőjel helyett, vagy pontot a kérdőjel helyett, hozzon létre új mintát minden egyes változathoz.

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tesztelje az új mintát a szabad formátumú adatok kinyerésére
1. A tesztelési panel megnyitásához válassza a felső sávon a **Test** lehetőséget. 

1. Adja meg a következő kimondott szöveget: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Az entitáshoz és a leképezéshez tartozó teszteredmények megtekintéséhez válassza az eredmény alatti **Inspect** (Vizsgálat) lehetőséget.

    A leképezést az először megtalált `FormName` entitás, majd a megtalált minta határozza meg. Ha a tesztelés során a rendszer nem észlelte az entitásokat, és ezért nem találta a mintát, további példa kimondott szövegeket kell megadnia a leképezésben (nem a mintában).

1. A felső navigációs sáv **Test** gombjával zárja be a tesztelési panelt.

## <a name="using-an-explicit-list"></a>Explicit lista használata

Ha azt tapasztalja, hogy a Pattern.any entitást tartalmazó minta nem megfelelően vonja ki az entitásokat, egy [explicit lista](reference-pattern-syntax.md#explicit-lists) megoldhatja a problémát.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag egy meglévő leképezéshez adott hozzá példa kimondott szövegeket, majd létrehozott egy új Pattern.any entitást az űrlapnévhez. Az oktatóanyag ezután az új példa kimondott szövegekkel és entitással mintát hozott létre a meglévő leképezéshez. Az interaktív tesztelés megmutatta, hogy mivel az entitást a rendszer megtalálta, a minta és annak leképezései előrejelezhetők voltak. 

> [!div class="nextstepaction"]
> [Útmutató a szerepkörök és a minták együttes használatához](luis-tutorial-pattern-roles.md)
