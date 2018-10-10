---
title: Lekérdezési kifejezés szintaxisa – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az Academic Knowledge API lekérdezési kifejezés szintaxisa.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: bf6dbde725670030046aad4fccf41554b8d917fe
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901277"
---
# <a name="query-expression-syntax"></a>Lekérdezési kifejezés szintaxisa

Úgy találtuk, hogy a válasz egy **értelmezése** kérelem tartalmazza a lekérdezés. A szintaxis, amely értelmezi a felhasználó lekérdezése létrehozott egy lekérdezési kifejezés minden értelmezéséhez. A lekérdezés használhatók a probléma egy **kiértékelése** eredményeivel beolvasására irányuló kérelem.

Is hozhat létre saját lekérdezési kifejezések, és ezeket használhatja a egy **kiértékelése** kérelmet. Ez akkor lehet hasznos, ha a saját felhasználói felület, amely a felhasználói műveleteknek hoz létre a lekérdezés épít. Ehhez az szükséges, akkor ismernie kell a lekérdezési kifejezések szintaxisát.  

Minden entitás, amely tartalmazhat egy lekérdezési kifejezésben attribútumnak adott adattípussal és lehetséges lekérdezési operátorokkal számos. Tevékenységentitás-attribútumok és a támogatott operátorok minden attribútum van megadva az [tevékenységentitás-attribútumok](EntityAttributes.md). Egy egyértékű lekérdezés megköveteli, hogy támogatja az attribútum a *egyenlő* műveletet. Előtag lekérdezés megköveteli, hogy támogatja az attribútum a *StartsWith* műveletet. Numerikustartomány lekérdezések megköveteli, hogy támogatja az attribútum a *IsBetween* műveletet.

Egyes entitás tárolt összetett attribútumként egy pont jelzett módon "." attribútum nevében. Például Szerző/kapcsolatot információ jelenik meg egy összetett attribútum. 4 összetevőket tartalmazza: AuN, AuId, AfN, AfId. Ezeket az összetevőket külön darabokat egy egyetlen entitás attribútumérték alkotó adatok.


**Karakterlánc-attribútum: Egyetlen érték** (tartalmazza a szinonimák egyezések)  
Ti = 'segít a rejtett szemantikai általi indexelés:  
Összetett (AA. AuN = "tudja dumais")

**: Karakterlánc-attribútum a pontos egyetlen érték** (megfelel csak forgalmára kanonikus értékeket)  
Ti == 'segít a rejtett szemantikai általi indexelés:  
Összetett (AA. AuN == "susan t dumais")
     
**Karakterlánc-attribútum: Előtag-értéke**   
TI = "segít a rejtett seman általi indexelés"...  
Összetett (AA. AuN "tudja du" =...)

**Numerikus attribútum: Egy érték**  
Y = 2010
 
**Numerikus attribútum: Tartományértéke**  
Y &GT; 2005  
Y &GT; = 2005  
Y &LT; 2010  
Y &LT; = 2010  
Y =\[2010, 2012\) (tartalmazza a bal oldali csak határérték: 2010, 2011)  
Y =\[2010, 2012\] (mindkét tartományhatár-értékek tartalmazza: 2010, 2011, 2012)
 
**Numerikus attribútum: Előtag-értéke**  
Y = "19"... (bármely numerikus érték, amely elindítja a 19) 
 
**Dátum attribútum: Egyetlen érték**  
D = "2010-02-04:

**: Dátum tartomány attribútumérték**  
D &GT; "2010-02-03"  
D = ["2010-02-03", "2010-02-05']

**És/vagy lekérdezéseket:**  
És (Y = 1985, Ti = "disordered elektronikus rendszer")  
Vagy (Ti = 'disordered elektronikus rendszerek"Ti ="tartalék tolerancia alapelvek és eljárás")  
And(OR(Y=1985,Y=2008), Ti = "disordered elektronikus rendszer")
 
**Összetett lekérdezéseket:**  
Egy összetett attribútum lekérdezés összetevői kell tenni a lekérdezési kifejezés, amely hivatkozik a Composite() függvényben az összetett attribútum része. 

Szerző neve úgy lekérdezheti a tanulmányok, például használja a következő lekérdezést:
```
Composite(AA.AuN='mike smith')
```
<br>Tanulmányok szerint egy adott szerző lekérdezése közben a szerző adott intézménynél, használja a következő lekérdezést:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>A Composite() függvény együtt összeköt az összetett attribútum két részét. Ez azt jelenti, hogy csak kapunk tanulmányok, a szerzők egyik "Kovács János" közben, Harvard volt. 

Tanulmányok szerint egy adott szerző, a tagságok az adott intézmény (egyéb) szerzők lekérdezéséhez használja a következő lekérdezést:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Ebben a verzióban mert Composite() vonatkozik a szerző és külön-külön And(), mielőtt a tagságot kapunk minden tanulmányok, ahol a szerző egyik "Kovács János" és a szerzői tagságok egyik "Harvard". Ez az előző lekérdezés példához hasonló hangzik, de nem ugyanaz.

Általánosságban elmondható, fontolja meg az alábbi példa: egy, két részből áll a és b összetett attribútum C rendelkezésünkre Egy entitás több érték lehet a c-hez Az entitások a következők:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>A lekérdezés 
```
Composite(And(C.A=1, C.B=2))
```

<br>csak olyan entitások, ahol az összetevő C.A 1, az összetevő C.B 2 C értékkel rendelkező illeszkedik. Csak E1 a lekérdezésnek megfelelő.

A lekérdezés 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>entitások, amelyek egy értéket a c nyelvhez készült C.A esetén 1 és is rendelkezik értékkel a c nyelvhez készült C.B esetén 2 illeszkedik. E1 és E2 mind a lekérdezésnek megfelelő.

Megjegyzés:  
- Egy összetett attribútum kívül Composite() függvény egy részét nem lehet hivatkozni.
- Az azonos Composite() függvényen belül két különböző összetett attribútumokat részei nem hivatkozhat.
- Olyan attribútum, amely nem része egy Composite() függvényen belül egy összetett attribútum nem hivatkozhat.
