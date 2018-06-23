---
title: A Academic Knowledge API kifejezésszintaxist lekérdezése |} Microsoft Docs
description: Megtudhatja, hogyan szintaxissal lekérdezési kifejezés Academic Knowledge API-ban. a Microsoft kognitív számára.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6ec338fff09954e2f14066ce2b83bc1228794af8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346907"
---
# <a name="query-expression-syntax"></a>Lekérdezés kifejezés szintaxisa

Úgy találtuk, hogy a válasz egy **értelmezhetők** kérelem tartalmazza a lekérdezési kifejezésben. A nyelvtan, amelyeket a felhasználó lekérdezése értelmezni létre minden egyes értelmezéséhez lekérdezési kifejezésben. Egy lekérdezési kifejezésben használhatók a probléma egy **kiértékelése** entitás keresési eredmények lekérésére irányuló kérelem.

Is összeállítani saját lekérdezési kifejezések, és használja azokat egy **kiértékelése** kérelmet. Ez akkor lehet hasznos, ha a saját felhasználói felületet, amely a felhasználói műveletek válaszul hoz létre egy lekérdezési kifejezésben. Ehhez szüksége tudni, hogy a lekérdezési kifejezések szintaxisát.  

Minden entitás attribútum egy lekérdezési kifejezésben szereplő különleges adattípust és lehetséges lekérdezési operátorok rendelkezik. Entitásattribútumhoz és minden attribútum támogatott operátorok megadott [Entitásattribútumhoz](EntityAttributes.md). Egy egyértékű lekérdezés megköveteli, hogy támogatja az attribútum a *egyenlő* műveletet. Előtag lekérdezés megköveteli, hogy támogatja az attribútum a *megadott módon kezdődő* műveletet. Numerikus lekérdezések megköveteli, hogy támogatja az attribútum a *IsBetween* műveletet.

Entitás adatok egy részét tárolt összetett attribútumok, ahogy azt egy pont '.' attribútum nevében. Szerző/dolgozói viszony például egy összetett attribútum jelzi. 4 összetevőt tartalmaz: AuN, AuId, AfN, AfId. Ezeket az összetevőket külön olyan adatok, amelyek egy egyetlen entitás attribútumérték alkotnak.


**Karakterlánc attribútum: Egyetlen érték** (tartalmazza a szinonimák egyezések)  
TI = "indexelő rejtett szemantikai elemzés"  
Összetett (AA. AuN = "ági dumais")

**Karakterlánc attribútum: Pontos egyetlen értékek** (megegyezik az csak kanonikus értékeket)  
TI == "indexelő rejtett szemantikai elemzés"  
Összetett (AA. AuN == "susan t dumais")
     
**: Karakterlánc előtag attribútumérték**   
TI = "indexelő által rejtett seman"...  
Összetett (AA. AuN "ági du" =...)

**Numerikus attribútum: Egyetlen értéket**  
Y = 2010
 
**Numerikus attribútum: A tartomány értékét**  
Y &GT; 2005  
Y &GT; = 2005  
Y &LT; 2010  
Y &LT; = 2010  
Y =\[2010-es, 2012\) (tartalmazza a bal oldali csak határérték: 2010-es, 2011)  
Y =\[2010-es, 2012\] (mindkét tartományhatár-értékek tartalmazza: 2010-es, 2011, 2012)
 
**: Numerikus előtag attribútumérték**  
Y = "19"... (a numerikus értéket 19 kezdődik) 
 
**Dátum attribútum: Egyetlen érték**  
D = "2010-02-04"

**Dátuma attribútum: A tartomány értékét**  
D &GT; "2010-02-03"  
D = ["2010-02-03", "2010-02-05"]

**És/vagy lekérdezéseket:**  
És (Y = 1985, Ti = "disordered elektronikus rendszerek")  
Vagy (Ti = "disordered elektronikus rendszerek", Ti = "hiba tolerancia alapelveket és eljárás")  
And(OR(Y=1985,Y=2008), Ti = "disordered elektronikus rendszerek")
 
**Összetett lekérdezéseket:**  
Lekérdezési összetevők összetett attribútum tegye a lekérdezési kifejezés, amely hivatkozik a Composite() függvényben összetett attribútum részeként szüksége. 

Például lekérdezése által írt cikkeket a szerző neve, használja a következő lekérdezést:
```
Composite(AA.AuN='mike smith')
```
<br>Az egy adott szerző által írt cikkeket lekérdezése közben a szerző adott intézménynél, használja a következő lekérdezést:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>A Composite() függvény a két részből áll, az összetett attribútum együtt kötelékek. Ez azt jelenti, hogy jelenleg csak a get által írt cikkeket ahol a szerzők egyik "Nagy Smith" közben a következő Harvard volt. 

Az adott intézmény (más) szerzők adott nézeteihez a szerző által írt cikkeket lekérdezéséhez használja a következő lekérdezést:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>Ebben a verzióban Composite() alkalmazott a szerző és kapcsolatot külön-külön előtt And(), mert azt lekérése valamennyi dokumentum, ahol a szerzők egyik "Nagy Smith" és a szerzők nézeteihez egyik "Harvard". Ez az előző lekérdezés példához hasonló hangvételére, de nem ugyanazt jelentik.

Általában, tekintse meg a következő példát: egy összetett attribútumot C, amelyet A és b két részből áll tudunk Egy entitás több érték lehet a c kiszolgálóra. A szervezetek a következők:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>A lekérdezés 
```
Composite(And(C.A=1, C.B=2))
```

<br>csak ha az összetevő C.A 1, az összetevő C.B 2 C értékkel rendelkező entitások megegyezik. Csak E1 felel meg a lekérdezést.

A lekérdezés 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>értékűnek lennie C-hez, ahol C.A az 1 és is olyan értéket c amelyekben C.B 2 entitások megegyezik. E1 és a E2 felel meg a lekérdezést.

Megjegyzés:  
- Egy összetett attribútum kívül Composite() függvény részét nem lehet hivatkozni.
- Az azonos Composite() függvényen belül két különböző összetett attribútumok részei nem lehet hivatkozni.
- Egy attribútum, amely nem része egy összetett attribútum egy Composite() függvényen belül nem lehet hivatkozni.
