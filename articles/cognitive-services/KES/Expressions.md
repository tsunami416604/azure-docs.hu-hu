---
title: A Tudásbázis feltárása Service API lekérdezési kifejezések strukturált |} Microsoft Docs
description: Megtudhatja, hogyan használja a strukturált lekérdezési kifejezések a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 070ee311a1153bc9fb59870dce68f385a43b15f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347091"
---
# <a name="structured-query-expression"></a>A strukturált lekérdezési kifejezésben
Strukturált lekérdezési kifejezésben az adatok index alapján kiértékelheti műveletkészlet határozza meg.  Az attribútum lekérdezési kifejezések és magasabb szintű funkciók áll.  Használja a [ *kiértékelése* ](evaluateMethod.md) számítási az objektumok a kifejezésnek megfelelő módszert.  A következő példában látható egy adja vissza az év 2013 óta Jaime Teevan által létrehozott kiadványok academic kiadványok tartományból.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Strukturált lekérdezési kifejezések szerezhetők be [ *értelmezhetők* ](interpretMethod.md) kérelmeket, ahol minden értelmezése szemantikai kimenetét a megfelelő index objektumok visszaadó strukturált lekérdezési kifejezésben a bemeneti természetes nyelvű lekérdezés.  Azt is megteheti akkor előfordulhat, hogy manuálisan hozhatóak létre, a jelen szakaszban ismertetett szintaxis használatával.

## <a name="attribute-query-expression"></a>Attribútum lekérdezési kifejezés
Egy attribútum lekérdezési kifejezésben azonosítja a szemben a specifikus attribútummal megfelelő objektumokat.  Különböző megfelelő műveletek támogatottak, attól függően, hogy az attribútum típusa és indexelt művelet szerepel a [séma](SchemaFormat.md):

| Típus | Művelet | Példák |
|------|-------------|------------|
| Sztring | egyenlő | Title = "rejtett szemantikai analysis" (kanonikus + szinonimák) |
| Sztring | egyenlő | Author.Name=='susan t dumais (kanonikus csak)|
| Sztring | starts_with | Title = "rejtett s"... |
| Int32 vagy Int64/dupla | egyenlő | Év = 2000 |
| Int32 vagy Int64/dupla | starts_with | Év "20 =... (a "20" kezdetű decimális értéke) |
| Int32 vagy Int64/dupla | is_between | Év&lt;2000 <br/> Év&lt;= 2000 <br/> Év&gt;2000 <br/> Év&gt;= 2000 <br/> Year=[2010,2012) *(tartalmazza a bal oldali csak határérték: 2010-es, 2011)* <br/> Év = [2000,2012] *(mindkét tartományhatár-értékek tartalmazza: 2010-es, 2011, 2012)* |
| Dátum | egyenlő | Születési dátumot = "1984-05-14' |
| Dátum | is_between | Születési dátumot&lt;= "2008/03/14' <br/> PublishDate = ["2000-01-01", "2009-12-31'] |
| GUID | egyenlő | Azonosító = "602DD052-CC47-4B23-A16A-26B52D30C05B" |


Például a kifejezés "Title =... 'rejtett s'" megfelel minden academic kiadványok esetében, amelynek cím "rejtett s" karakterlánccal kezdődik.  Ahhoz, hogy a kifejezés kiértékelése, az attribútum a cím meg kell adnia a "starts_with" műveletet a séma segítségével hozhatók létre az indexet.

Kapcsolódó szinonimák rendelkező attribútumok esetén egy lekérdezési kifejezésben objektumok, amelyek kanonikus értéke megegyezik a megadott karakterlánc a "==" operátor vagy objektumok használatával, ahol a kanonikus/szinonimát bármely felel meg a "=" operátorral is megadhat.  Az "equals" operátor attribútumdefiníciót meghatározott mindkét esetében.


## <a name="functions"></a>Functions
Nincs olyan beépített funkciók, amely lehetővé teszi az alapszintű attribútum lekérdezések kifinomultabb lekérdezési kifejezések létrehozása.

### <a name="and-function"></a>És funkció
`And(expr1, expr2)`

A két lekérdezési kifejezések metszetét adja vissza.

A következő példa a 2000 kapcsolatos információk lekérése közzétett academic kiadványok adja vissza:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Vagy nem működik
`Or(expr1, expr2)`

A két lekérdezési kifejezések Uniójának visszaadása.

A következő példa a 2000 kapcsolatos információk lekérése vagy a felhasználó modellezési közzétett academic kiadványok adja vissza:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Összetett függvény
`Composite(expr)`

Adja vissza egy kifejezés, amely magában foglalja a belső kifejezés álló közös összetett attribútum alárendelt attribútumok lekérdezéseket.  A beágyazás bármely megfelelő adatok objektum legalább egy érték, amely egyedileg eleget tesz a belső kifejezés összetett attribútum szükséges.  Vegye figyelembe, hogy egy lekérdezési kifejezésben alárendelt attribútumok egy összetett attribútum egyesítse a Composite() funkcióval, mielőtt kombinálható más lekérdezési kifejezések.

Például az alábbi kifejezés eredménye "harry shum" academic kiadványok szerint közben: "microsoft" volt:

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Az alábbi kifejezés, másrészt academic kiadványok esetében, ahol a szerzők egyik "harry shum" és a nézeteihez egyik "microsoft" adja vissza:

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
