---
title: Lekérdezési kifejezés szintaxisa – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használható a lekérdezési kifejezés szintaxisa a Academic Knowledge APIban.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4056ecba7ac80436952228da9e1b74dc7382448c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448953"
---
# <a name="query-expression-syntax"></a>Lekérdezési kifejezés szintaxisa

Láttuk, hogy egy **értelmező** kérelemre adott válasz egy lekérdezési kifejezést tartalmaz. A felhasználó lekérdezését értelmező nyelvtan egy lekérdezési kifejezést hozott létre az egyes értelmezésekhez. A lekérdezési kifejezés ezután felhasználható egy **kiértékelési** kérelem kiküldésére az entitás keresési eredményeinek lekéréséhez.

Saját lekérdezési kifejezéseket is létrehozhat, amelyeket **kiértékelési** kérelemben is használhat. Ez akkor lehet hasznos, ha saját felhasználói felületet hoz létre, amely a felhasználó műveleteire adott válaszként létrehoz egy lekérdezési kifejezést. Ehhez ismernie kell a lekérdezési kifejezések szintaxisát.  

A lekérdezési kifejezésben szereplő minden entitás attribútum egy adott adattípussal és a lehetséges lekérdezési operátorok készletével rendelkezik. Az egyes attribútumokhoz tartozó entitás-attribútumok és a támogatott operátorok halmaza az [entitás attribútumaiban](EntityAttributes.md)van megadva. Egy egyértékű lekérdezés esetében az attribútumnak támogatnia kell az *EQUAL* műveletet. Az előtag-lekérdezés megköveteli, hogy az attribútum támogassa a *StartsWith* műveletet. A numerikus tartomány lekérdezéséhez az attribútumnak támogatnia kell a *IsBetween* műveletet.

Az entitások egy részét összetett attribútumokként tárolja a rendszer, ahogy azt a "." pont jelzi az attribútum nevében. A szerzői/kapcsolati adatok például összetett attribútumként jelennek meg. 4 összetevőt tartalmaz: AuN, AuId, AfN, AfId. Ezek az összetevők különálló adatmennyiséget alkotnak, amelyek egyetlen entitás-attribútum értékét alkotják.

Megjegyzés: az összes lekérdezési kifejezésnek kisbetűs és speciális karakterek nélkül kell lennie.

**String attribútum: egyetlen érték** (tartalmazza a szinonimák egyezését)  
Ti = ' a látens szemantikai elemzés szerint '  
Összetett (AA. AuN = ' Sue dumais ')

**String attribútum: pontos egyszeri érték** (csak a kanonikus értékek egyezése)  
Ti = = ' a látens szemantikai elemzés által indexelve '  
Összetett (AA. AuN = = ' Susan t dumais ')
     
**String attribútum: előtag értéke**   
Ti = ' a látens Seman indexelése...  
Összetett (AA. AuN = ' Sue du '...)

**Numerikus attribútum: egyetlen érték**  
Y = 2010
 
**Numerikus attribútum: tartomány értéke**  
Y > 2005  
Y > = 2005  
Y < 2010  
Y < = 2010  
Y =\[2010, 2012\) (csak a bal oldali határ értékét tartalmazza: 2010, 2011)  
Y =\[2010, 2012\] (beleértve a határos értékeket is: 2010, 2011, 2012)
 
**Numerikus attribútum: előtag értéke**  
Y = ' 19 '... (a 19 értékkel kezdődő numerikus értékek) 
 
**Date attribútum: egyetlen érték**  
D = ' 2010-02-04 '

**Date attribútum: tartomány értéke**  
D > "2010-02-03"  
D = [' 2010-02-03 ', ' 2010-02-05 ']

**És/vagy lekérdezések:**  
És (Y = 1985, ti = ' elrendezetlen elektronikus rendszerek ')  
Vagy (ti = "nem rendezett elektronikus rendszerek", ti = "hibatűrési alapelvek és gyakorlat")  
És (vagy (Y = 1985, Y = 2008), ti = ' megrendelt elektronikus rendszerek ')
 
**Összetett lekérdezések:**  
Egy összetett attribútum összetevőinek lekérdezéséhez csatolni kell a lekérdezési kifejezés azon részét, amely a kompozit () függvény összetett attribútumára hivatkozik. 

Ha például a szerző neve alapján szeretne dokumentumokat lekérdezni, használja a következő lekérdezést:
```
Composite(AA.AuN='mike smith')
```
<br>Ha a szerző egy adott intézménynél szeretne lekérdezni egy adott szerző által készített dokumentumokat, használja a következő lekérdezést:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Az összetett () függvény összekapcsolja az összetett attribútum két részét együtt. Ez azt jelenti, hogy csak olyan dokumentumokat kapunk, amelyekben a szerzők egyike "Mike Smith", miközben a Harvardon volt. 

Ha egy adott szerző által az adott intézményhez tartozó (más) szerzőkkel való kapcsolattal szeretne dokumentumokat lekérdezni, használja a következő lekérdezést:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Ebben a verzióban, mivel az összetett () alkalmazás a szerzőre és a kapcsolatra külön van alkalmazva a (z) és () esetében, az összes olyan dokumentumot megkapjuk, ahol a szerzők egyike "Mike Smith", a szerzői kapcsolatok egyike pedig "Harvard". Ez az előző lekérdezési példához hasonlóan hangzik, de nem ugyanaz a dolog.

Általánosságban vegye figyelembe a következő példát: a C kompozit attribútummal rendelkezik, amely az A és B két összetevővel rendelkezik. Egy entitás több "C" értékkel rendelkezhet. Ezek az entitások:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>A lekérdezés 
```
Composite(And(C.A=1, C.B=2))
```

<br>csak olyan entitásokra illeszkedik, amelyeknek értéke c. a c. A 1, a C. B összetevő pedig 2. Csak az E1 egyezik a lekérdezéssel.

A lekérdezés 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>azokat az entitásokat tartalmazza, amelyek értéke c, ahol a c. A 1, és a c. B értéknél a c. B értéke is a 2. Az E1 és az E2 is megfelel a lekérdezésnek.

Megjegyzés:  
- Összetett () függvényen kívül nem hivatkozhat összetett attribútum egy részére.
- Egyazon összetett () függvényen belül nem hivatkozhat két különböző összetett attribútum részeire.
- Nem hivatkozhat olyan attribútumra, amely nem része összetett () függvényen belüli összetett attribútumnak.
