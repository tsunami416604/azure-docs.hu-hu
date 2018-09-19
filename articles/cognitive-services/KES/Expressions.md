---
title: Strukturált lekérdezési kifejezéssé – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a strukturált lekérdezési kifejezések a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bdde2dfc9ab8e8ffdf7123c916538a8c98ecfce9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129167"
---
# <a name="structured-query-expression"></a>Strukturált lekérdezési kifejezés

A lekérdezés strukturált műveletek az adatok index mentén értékeli egy halmazát határozza meg.  Attribútum lekérdezési kifejezések és a magasabb szintű függvények áll.  Használja a [ *kiértékelése* ](evaluateMethod.md) metódus az objektumok a kifejezés a megfelelő számítási.  Az alábbi példában látható, a tudományos kiadványok tartományból, amely az év 2013 óta Jaime Teevan által írt kiadványok adja vissza.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Strukturált lekérdezési kifejezéssé szerezhetők be [ *értelmezése* ](interpretMethod.md) kéréseket, ahol a szemantikai egyes értelmezése kimenete egy strukturált lekérdezési kifejezés, amely a megfelelő index objektumokat adja vissza a a bemeneti természetes nyelvű lekérdezések.  Azt is megteheti hogy előfordulhat, hogy manuálisan hozhatóak létre a jelen szakaszban bemutatott szintaxist használva.

## <a name="attribute-query-expression"></a>Attribútum lekérdezési kifejezés

Attribútum lekérdezési kifejezés egy objektumokat a megfelelő szemben egy adott attribútum alapján azonosítja.  Különböző megfelelő műveletek támogatottak a attribútum típusától függően és indexelt művelet a megadott a [séma](SchemaFormat.md):

| Típus | Művelet | Példák |
|------|-------------|------------|
| Sztring | egyenlő | Title = "segít a rejtett szemantikai" (kanonikus + szinonimák) |
| Sztring | egyenlő | Author.Name=='susan t dumais (kanonikus csak)|
| Sztring | starts_with | Title = "segít a rejtett s"... |
| Int32/Int64/dupla | egyenlő | Év = 2000 |
| Int32/Int64/dupla | starts_with | Év = "20"... (minden olyan decimális értéket "20" kezdetű) |
| Int32/Int64/dupla | is_between | Év&lt;2000 <br/> Év&lt;= 2000 <br/> Év&gt;2000 <br/> Év&gt;= 2000 <br/> Year=[2010,2012) *(tartalmazza a bal oldali csak határérték: 2010, 2011)* <br/> Év = [2000,2012] *(mindkét tartományhatár-értékek tartalmazza: 2010, 2011, 2012)* |
| Dátum | egyenlő | Születési dátum ='1984. 05. 14' |
| Dátum | is_between | Születési dátum&lt;= "2008/03/14' <br/> PublishDate = ["2000-01-01", "2009-12-31'] |
| GUID | egyenlő | ID = "602DD052-CC47-4B23-A16A-26B52D30C05B" |


Például a kifejezés "Title ="segít a rejtett s"..." megfelel az összes academic kiadványok esetében, amelynek címe "segít a rejtett s" karakterlánccal kezdődik.  Annak érdekében, hogy ez a kifejezés kiértékelése, az attribútum a cím meg kell adnia a "starts_with" műveletet a sémát, az index összeállításához.

A szinonimák kapcsolódó attribútumokat a lekérdezés előfordulhat, hogy adja meg objektumok, amelyek kanonikus értéke megegyezik a megadott karakterlánc a "==" operátor vagy objektumok használatával, amelyben a canonical/szinonimát értékeket megegyeznek az "=" operátor használata.  Az attribútum definíciója adható meg a "egyenlő" operátor is szükséges.


## <a name="functions"></a>Functions

Van olyan beépített funkciókat, így kifinomultabb lekérdezési kifejezések az alapvető attribútum lekérdezések felépítése.

### <a name="and-function"></a>És a függvény

`And(expr1, expr2)`

A két bemeneti lekérdezési kifejezések metszetét adja vissza.

Az alábbi példa kapcsolatos adatok beolvasása a 2000-ben közzétett academic kiadványok adja vissza:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Vagy függvény

`Or(expr1, expr2)`

A két bemeneti lekérdezési kifejezések unióját adja vissza.

Az alábbi példa a 2000 kapcsolatos információk lekérése vagy a felhasználó modellezési közzétett academic kiadványok adja vissza:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Összetett függvény

`Composite(expr)`

Visszaadja egy kifejezés, amely magában foglalja egy belső kifejezés mikroszolgáltatásokból álló, lekérdezéseket az alárendelt attribútumok egy közös összetett attribútum.  A beágyazás minden egyező objektum legalább egy olyan értékkel, amely külön-külön eleget tesz a belső kifejezés összetett attribútuma van szükség.  Vegye figyelembe, hogy rendelkezik-e a lekérdezési kifejezés egy összetett attribútum alárendelt attribútumok egyesítse a Composite() függvény használatával, mielőtt kombinálható más lekérdezési kifejezéseket.

Például a következő kifejezést ad vissza academic kiadványok szerint "harry shum", míg a "Microsoft" volt:

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

A következő kifejezést, másrészt academic kiadványok esetében, ahol a szerző egyik "harry shum" és a tagságok egyik "microsoft" adja vissza:

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
