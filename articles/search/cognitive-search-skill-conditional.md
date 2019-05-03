---
title: Feltételes cognitive search szakértelem (Azure Search) |} A Microsoft Docs
description: Feltételes szakértelem, amely lehetővé teszi a szűrés, az alapértelmezett érték létrehozása és értékek egyesítése.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028425"
---
#   <a name="conditional-skill"></a>Feltételes szakértelem

A **feltételes szakértelem** lehetővé teszi, hogy egy logikai művelet dönthet arról, hogy hozzá kell rendelni egy kimeneti adatokat olyan forgatókönyvek száma különböző. Ilyen például, amikor: szűrés, egy alapértelmezett értéket, és adatok egyesítése egy feltétel alapján.

A következő pseudocode ismerteti, mi a feltételes szakértelem műveleteket hajtja végre:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Szakértelem nincs kötve a Cognitive Services API-t, és nem terheli útmutatójához. Továbbra is ajánlott [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md), azonban felül a **ingyenes** erőforrás beállítás, amely korlátozza, hogy naponta napi végrehajtott információbeolvasás kis számú.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Értékelt mezők

Szakértelem különleges, mert a hozzá tartozó bemenetek kiértékelt mezőket.

Egy kifejezés érvényes értékei a következők:

-   Jegyzetek elérési utak (kifejezések szereplő elérési utakat kell elválasztani "$(" and ")") <br/>
    Példák:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literálok (karakterláncokat, számok, true, false, NULL értékű) <br/>
    Példák:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
       "= null"                 // null value
    ```

-  Egy összehasonlító operátort tartalmazó kifejezések (==,! =, > =, >, < =, <) <br/>
    Példák:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Logikai operátorokat tartalmazó kifejezések (& &, ||,!, ^) <br/>
    Példák:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Egy numerikus operátort tartalmazó kifejezések (+, -, \*, /, %) <br/>
    Példák: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Az értékelés támogatott, mert a feltételes szakértelem kisebb adatátalakításhoz is használható. Tekintse meg a minta [szakértelem definíció 4](#transformation-examples) példaként.

## <a name="skill-inputs"></a>Ismeretek bemenetek
Bemenet-és nagybetűk.

| Bemenetek      | Leírás |
|-------------|-------------|
| feltétel   | A bemeneti egy [értékeli ki a mező](#evaluated-fields) , amely jelzi, hogy a feltétel kiértékelése. Ez a feltétel ki kell értékelni a logikai érték (IGAZ vagy hamis).   <br/>  Példák: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/ /dokumentumoldal/\*/language) == $(/ dokumentum/expectedLanguage)" <br/> |
| whenTrue    | A bemeneti egy [értékeli ki a mező](#evaluated-fields). A visszatérési érték, ha a feltétel kiértékelése történik az igaz. Állandók karakterláncok vissza kell adni a "" idézőjelek között. <br/>Példaértékek: <br/> "a"szerződés"="<br/>"= $(/document/contractType)" <br/> "= $(/dokumentum/entitások/\*)" <br/> |
| whenFalse   | A bemeneti egy [értékeli ki a mező](#evaluated-fields). A visszaadandó értéket, ha a feltétel hamis kiértékelése történik.  <br/>Példaértékek: <br/> "a"szerződés"="<br/>"= $(/document/contractType)" <br/> "= $(/dokumentum/entitások/\*)" <br/>

## <a name="skill-outputs"></a>Ismeretek kimenetek
A "kimeneti" nevű egyetlen kimeneti van. Ha a feltétel hamis whenFalse vagy whenTrue értéke a feltétel teljesülése esetén ad vissza.

## <a name="examples"></a>Példák

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>Minta szakértelem 1 definíciója: Dokumentumok a csak a "Franciaország" dokumentumokat szűrése

A következő kimenet egy tömb a mondatok ("/ dokumentum/frenchSentences") Ha a nyelv, a dokumentum francia adja vissza. Ha a nyelv nem francia, ezt az értéket fogja beállítani a null.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Ha "/ dokumentum/frenchSentences" lesz a *környezet* egy másik szakértelem, szakértelem csak akkor fog futni, ha a "/ dokumentum/frenchSentences" értéke nem null


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>Minta szakértelem 2 definíciója: Ha még nem létezik a beállítás alapértelmezett értéke.

A következő kimenet jegyzet ("/ dokumentum/languageWithDefault"), amely a dokumentum a nyelv, vagy az "es" értékre van állítva, ha nincs megadva a nyelv hoz létre.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>Minta szakértelem 3 definíciója: Két különböző mezők értékeit egyesítése egyetlen mező

Ebben a példában az egyes mondatokat rendelkezik egy *frenchSentiment* tulajdonság. Minden alkalommal, amikor a *frenchSentiment* tulajdonság null értékű, szeretnénk használni a *englishSentiment* értéket. Egyszerűen nevű tag azt rendelje a kimenetet *vélemények* ("/ dokumentum-/ vélemények / * / vélemények").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-examples"></a>Átalakítás példák
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>Minta szakértelem 4 definíciója: Egyetlen mező adatátalakítások végrehajtása

Ebben a példában egy 0 és 1 közötti vélemények érkező, és szeretnénk, hogy 1 és 1 közötti átalakítás során. Ez az, hogy javítanunk kell a feltételes szakértelem használatával kis matematikai átalakítást.

Ebben a példában soha nem használjuk a szakértelem feltételes aspektusa, a feltétel mindig értéke igaz. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```


## <a name="special-considerations"></a>Különleges szempontok
Vegye figyelembe, hogy egyes paraméterek értékeli ki, ezért ügyeljen arra, különösen a dokumentált minta a következő kell. Kifejezések egy egyenlő bejelentkezési "=" kell kezdődnie, és elérési utak elválasztva kell "$(" and ")". Ellenőrizze, hogy a karakterláncok put "szimpla idézőjelben", a gyűjteménykiértékelő karakterláncok és a útvonalakat és a kezelők között, amelyek segítségével. Ne felejtse el az operátorok körül egy szóközt (például egy * az elérési utat, mint a szorzás operátor eltérő jelentéssel rendelkezik).


## <a name="next-steps"></a>További lépések

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
