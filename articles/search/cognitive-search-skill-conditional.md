---
title: Feltételes cognitive search szakértelem (Azure Search) |} A Microsoft Docs
description: A feltételes szakértelem lehetővé teszi a szűrés, az alapértelmezett érték létrehozása és értékek egyesítése.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791521"
---
#   <a name="conditional-skill"></a>Feltételes szakértelem

A *feltételes szakértelem* meghatározni az adatokat egy kimeneti hozzárendelése logikai művelet igénylő Azure Search olyan forgatókönyveket tesz lehetővé. Ezek a forgatókönyvek között megtalálható a szűrést, a hozzárendelése egy alapértelmezett értéket, és a egy feltétel alapján adatok egyesítése.

A következő pseudocode mutat be, hogy mi a feltételes szakértelem műveleteket hajtja végre:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Szakértelem az Azure Cognitive Services API nincs kötve, és nem kell fizetnie, a. Azonban továbbra is ajánlott [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) felül az "Ingyenes" erőforrás-beállítás, amely korlátozza, hogy naponta végrehajtott információbeolvasás kis számú.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Értékelt mezők

Szakértelem különleges, mert a hozzá tartozó bemenetek kiértékelt mezőket.

A következő elemekre egy kifejezés érvényes értékek:

-   Jegyzetek elérési utak (kifejezések szereplő elérési utakat kell elválasztani "$(" and ")")
 <br/>
    Példák:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literálok (karakterláncokat, számok, true, false, NULL értékű) <br/>
    Példák:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Összehasonlító operátorok tartalmazó kifejezések (==,! =, > =, >, < =, <) <br/>
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

-   Numerikus operátorokat tartalmazó kifejezések (+, -, \*, /, %) <br/>
    Példák: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Mivel a feltételes szakértelem értékelési támogatja, használhatja a kisebb adatátalakításhoz. Lásd a [szakértelem definíció 4](#transformation-example).

## <a name="skill-inputs"></a>Ismeretek bemenetek
Bemenet-és nagybetűk.

| Input (Bemenet)   | Leírás |
|-------------|-------------|
| condition   | A bemeneti egy [értékeli ki a mező](#evaluated-fields) , amely jelzi, hogy a feltétel kiértékelése. Ez a feltétel ki kell értékelni a logikai érték (*igaz* vagy *hamis*).   <br/>  Példák: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/ /dokumentumoldal/\*/language) == $(/ dokumentum/expectedLanguage)" <br/> |
| whenTrue    | A bemeneti egy [értékeli ki a mező](#evaluated-fields) , amely képviseli, a visszatérési érték, ha a feltétel értékelése van *igaz*. Állandók karakterláncokat a rendszer visszalépteti szimpla idézőjelek ("és"). <br/>Példaértékek: <br/> "a"szerződés"="<br/>"= $(/document/contractType)" <br/> "= $(/dokumentum/entitások/\*)" <br/> |
| whenFalse   | A bemeneti egy [értékeli ki a mező](#evaluated-fields) , amely képviseli, a visszatérési érték, ha a feltétel értékelése van *hamis*. <br/>Példaértékek: <br/> "a"szerződés"="<br/>"= $(/document/contractType)" <br/> "= $(/dokumentum/entitások/\*)" <br/>

## <a name="skill-outputs"></a>Ismeretek kimenetek
Van egy egyetlen kimeneti, amelynek a neve egyszerűen "kimeneti." A visszatérési érték *whenFalse* Ha a feltétel hamis vagy *whenTrue* a feltétel teljesülése esetén.

## <a name="examples"></a>Példák

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Minta szakértelem 1 definíciója: Dokumentumok a csak francia dokumentumokat szűrése

A következő kimenet ("/ dokumentum/frenchSentences") mondatokat tömbjét adja eredményül, ha a nyelv, a dokumentum francia. A nyelv nem francia, ha az értéke *null*.

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
Ha a "/ dokumentum/frenchSentences" szolgál a *környezet* egy másik szaktudás, szakértelem csak akkor fut, ha "/ dokumentum/frenchSentences" beállítása nem *null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Minta szakértelem 2 definíciója: Egy érték, amely nem létezik az alapértelmezett érték

A következő kimenet jegyzet ("/ dokumentum/languageWithDefault"), és állítsa be a dokumentum a nyelvet, vagy "es" Ha a nyelv nem hoz létre.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Minta szakértelem 3 definíciója: Egy két mező értékeit egyesítése

Ebben a példában az egyes mondatokat rendelkezik egy *frenchSentiment* tulajdonság. Minden alkalommal, amikor a *frenchSentiment* tulajdonság null értékű, szeretnénk használni a *englishSentiment* értéket. Azt rendelje a kimenetet, amelynek a neve tag *vélemények* ("/ dokumentum-/ vélemények / * / vélemények").

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

## <a name="transformation-example"></a>Átalakítás példa
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Minta szakértelem 4 definíciója: Adatátalakítás egyetlen mező

Ebben a példában az általunk kapott egy *vélemények* 0 és 1 közötti. Szeretnénk őket 1 és 1 közé kell esnie. A feltételes szakértelem használatával hajtsa végre a kisebb átalakításában.

Ebben a példában nem vesszük a szakértelem feltételes aspektusa mivel a feltétel mindig *igaz*.

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
Egyes paraméterek értékeli ki, így különösen ügyeljen a dokumentált mintát követik a szükséges. Kifejezések egyenlőségjellel kell kezdődnie. Egy elérési utat kell elválasztani "$(" and ")". Ellenőrizze, hogy a karakterláncok put szimpla idézőjelek között. A gyűjteménykiértékelő karakterláncok és a útvonalakat és a kezelők között, amelyek segítségével. Ne felejtse el az operátorok körüli üres terület (például egy "*" elérési út azt jelenti, hogy más, mint a multiply).


## <a name="next-steps"></a>További lépések

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
