---
title: Feltételes kognitív készség
titleSuffix: Azure Cognitive Search
description: A feltételes szakértelem az Azure Cognitive Search lehetővé teszi a szűrést, az alapértelmezett értékek létrehozása és az értékek egyesítése egy skillset definícióban.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792051"
---
# <a name="conditional-cognitive-skill"></a>Feltételes kognitív készség

A **feltételes** szakértelem lehetővé teszi az Azure Cognitive Search forgatókönyvek, amelyek megkövetelik a logikai művelet határozza meg az adatokat hozzárendelni egy kimenethez. Ezek a forgatókönyvek közé tartozik a szűrés, az alapértelmezett érték hozzárendelése és az adatok egy feltétel alapján történő egyesítése.

A következő pszeudokód bemutatja, hogy a feltételes szakértelem mit ér el:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Ez a szakértelem nem kötődik az Azure Cognitive Services API-hoz, és nem kell fizetnie a használatáért. Azonban továbbra is csatolja a [Cognitive Services-erőforrás](cognitive-search-attach-cognitive-services.md) felülírja a "Szabad" erőforrás-beállítás, amely korlátozza, hogy egy kis számú dúsítások naponta.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Kiértékelt mezők

Ez a szakértelem azért különleges, mert a bemenetek kiértékelt mezők.

A következő elemek egy kifejezés érvényes értékei:

-   Jegyzetelérési utak (a kifejezésekben lévő görbéket "$(" és ")" taggal kell elhatároltan elhatárolta)
 <br/>
    Példák:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Konstans (karakterláncok, számok, igaz, hamis, null) <br/>
    Példák:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Összehasonlító operátorokat használó kifejezések (==, !=, >=, >, <=, <) <br/>
    Példák:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Logikai operátorokat használó kifejezések (&&, ||, !, ^) <br/>
    Példák:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Numerikus operátorokat használó kifejezések (+, -, \*,, %) <br/>
    Példák: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Mivel a feltételes szakértelem támogatja a kiértékelést, kisebb átalakítási forgatókönyvekben is használható. Lásd például a [4.](#transformation-example)

## <a name="skill-inputs"></a>Szakértelem bemenetei
A bemenetek nem imitátrálják a kis- és nagybetűket.

| Input (Bemenet)   | Leírás |
|-------------|-------------|
| Feltétel   | Ez a bemenet egy [kiértékelt mező,](#evaluated-fields) amely a kiértékelendő feltételt jelöli. Ennek a feltételnek logikai értéket *(igaz* vagy *hamis)* kell kiértékelnie.   <br/>  Példák: <br/> "= igaz" <br/> "= $(/dokumentum/nyelv) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue (igaz)    | Ez a bemenet egy [kiértékelt mező,](#evaluated-fields) amely a visszaadandó értéket jelöli, ha a feltétel igaz értékre van *kiértékelve.* Az állandók karakterláncait idézőjelben (" és ') kell visszaadni. <br/>Mintaértékek: <br/> "= "szerződés""<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | Ez a bemenet egy [kiértékelt mező,](#evaluated-fields) amely a visszaadandó értéket jelöli, ha a feltétel *hamis.* <br/>Mintaértékek: <br/> "= "szerződés""<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>Szakértelem kimenetei
Van egy kimenet, amit egyszerűen "kimenetnek" hívnak. Visszaadja a *whenFalse értéket,* ha a feltétel hamis, vagy *whenTrue,* ha a feltétel igaz.

## <a name="examples"></a>Példák

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>1. mintaszakértelem-definíció: Csak francia dokumentumok visszaküldéséhez szükséges dokumentumok szűrése

A következő kimenet egy sor mondatot ad vissza ("/document/frenchSentences"), ha a dokumentum nyelve francia. Ha a nyelv nem francia, az érték *null*értékű.

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
Ha a "/document/frenchSentences" egy másik szakértelem *kontextusaként* használatos, akkor ez a szakértelem csak akkor fut, ha a "/document/frenchSentences" értéke nem *null.*


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>2. mintaszakértelem-definíció: Alapértelmezett érték beállítása egy nem létező értékhez

A következő kimenet létrehoz egy olyan notnotan ("/document/languageWithDefault"), amely a dokumentum nyelvére vagy "es"-re van állítva, ha a nyelv nincs beállítva.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>3. mintaszakértelem-definíció: Értékek egyesítése két mezőből egybe

Ebben a példában néhány mondat egy *frenchSentiment* tulajdonsággal rendelkezik. Ha a *frenchSentiment* tulajdonság null értékű, az *englishSentiment* értéket szeretnénk használni. A kimenetet egy *véleménynek* nevezett taghoz rendeljük ("/document/sentiment/*/sentiment").

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

## <a name="transformation-example"></a>Példa átalakítási példa
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>4. mintaszakértelem-definíció: Adatátalakítás egyetlen mezőben

Ebben a példában 0 és 1 közötti *érzést* kapunk. Azt akarjuk, hogy átalakítsuk - 1 és 1 között. Használhatjuk a feltételes képesség, hogy ezt a kis átalakítás.

Ebben a példában nem használjuk a szakértelem feltételes aspektusát, mert a feltétel mindig *igaz.*

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
Egyes paraméterek kiértékelése, ezért különösen óvatosnak kell lennie a dokumentált minta követésére. A kifejezéseknek egyenlőségjellel kell kezdődniük. Az elérési utat "$(" és ")" tagoltnak kell lennie. Ügyeljen arra, hogy a karakterláncokat idézőjelek közé helyezze. Ez segít az értékelőnek megkülönböztetni a karakterláncokat és a tényleges elérési utakat és operátorokat. Ügyeljen arra is, hogy a kezelők körül szóközt helyezzen el (például egy "*" egy görbén valami mást jelent, mint a szorzás).


## <a name="next-steps"></a>További lépések

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
