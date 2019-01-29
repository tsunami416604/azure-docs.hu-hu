---
title: Lekérdezési kifejezés szintaxisa – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az Academic Knowledge API lekérdezési kifejezés szintaxisa.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: c130c6cd5fcb5191195712f570db66408734200a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150872"
---
# <a name="query-expression-syntax"></a>Lekérdezési kifejezés szintaxisa

Úgy találtuk, hogy a válasz egy **értelmezése** kérelem tartalmazza a lekérdezés. A szintaxis, amely értelmezi a felhasználó lekérdezése létrehozott egy lekérdezési kifejezés minden értelmezéséhez. A lekérdezés használhatók a probléma egy **kiértékelése** eredményeivel beolvasására irányuló kérelem.

Is hozhat létre saját lekérdezési kifejezések, és ezeket használhatja a egy **kiértékelése** kérelmet. Ez akkor lehet hasznos, ha a saját felhasználói felület, amely a felhasználói műveleteknek hoz létre a lekérdezés épít. Ehhez az szükséges, akkor ismernie kell a lekérdezési kifejezések szintaxisát.  

Minden entitás, amely tartalmazhat egy lekérdezési kifejezésben attribútumnak adott adattípussal és lehetséges lekérdezési operátorokkal számos. Tevékenységentitás-attribútumok és a támogatott operátorok minden attribútum van megadva az [tevékenységentitás-attribútumok](EntityAttributes.md). Egy egyértékű lekérdezés megköveteli, hogy támogatja az attribútum a *egyenlő* műveletet. Előtag lekérdezés megköveteli, hogy támogatja az attribútum a *StartsWith* műveletet. Numerikustartomány lekérdezések megköveteli, hogy támogatja az attribútum a *IsBetween* műveletet.

Egyes entitás tárolt összetett attribútumként egy pont jelzett módon "." attribútum nevében. Például Szerző/kapcsolatot információ jelenik meg egy összetett attribútum. 4 összetevőket tartalmazza: AuN, AuId, AfN, AfId. Ezeket az összetevőket külön darabokat egy egyetlen entitás attribútumérték alkotó adatok.


**Karakterlánc-attribútum: Egyetlen érték** (tartalmazza a szinonimák egyezések)  
Ti = 'segít a rejtett szemantikai általi indexelés:  
Összetett (AA. AuN = "tudja dumais")

**Karakterlánc-attribútum: A pontos egyetlen érték** (megfelel csak forgalmára kanonikus értékeket)  
Ti == 'segít a rejtett szemantikai általi indexelés:  
Összetett (AA. AuN == "susan t dumais")
     
**Karakterlánc-attribútum: Előtag-értéke**   
TI = "segít a rejtett seman általi indexelés"...  
Összetett (AA. AuN "tudja du" =...)

**Numerikus attribútum: Egyetlen érték**  
Y=2010
 
**Numerikus attribútum: A tartomány értékét**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y =\[2010, 2012\) (csak a bal oldali határ értékét tartalmazza: 2010, 2011)  
Y =\[2010, 2012\] (mindkét tartományhatár-értékek tartalmazza: 2010, 2011, 2012)
 
**Numerikus attribútum: Előtag-értéke**  
Y = "19"... (bármely numerikus érték, amely elindítja a 19) 
 
**Attribútum időpontja: Egyetlen érték**  
D='2010-02-04'

**Attribútum időpontja: A tartomány értékét**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**És/vagy lekérdezéseket:**  
And(Y=1985, Ti='disordered electronic systems')  
Vagy (Ti = 'disordered elektronikus rendszerek"Ti ="tartalék tolerancia alapelvek és eljárás")  
And(Or(Y=1985,Y=2008), Ti='disordered electronic systems')
 
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

Általánosságban elmondható vegye figyelembe az alábbi példában: Rendelkezünk egy összetett attribútum C, amely két részből áll a és B. Egy entitás több érték lehet a c-hez Az entitások a következők:
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
