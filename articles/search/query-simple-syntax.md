---
title: Egyszerű lekérdezési szintaxis – Azure Search
description: A teljes szöveges keresési lekérdezésekben az Azure Search szolgáltatásban használt egyszerű lekérdezési szintaxis referenciája.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 99729141e5e1478f45ad385cf671c44a8e08f21a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437492"
---
# <a name="simple-query-syntax-in-azure-search"></a>Az Azure Search egyszerű lekérdezési szintaxis
Az Azure Search két Lucene-alapú lekérdezési nyelv valósít meg: [Egyszerű Lekérdezéselemzőt](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene lekérdezéselemző](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Az Azure Search szolgáltatásban az egyszerű lekérdezési szintaxis nem tartalmazza az intelligens/olajsár lehetőségek.  

> [!NOTE]  
>  Az Azure Search alternatívát kínál [Lucene lekérdezési szintaxis](query-lucene-syntax.md) összetettebb lekérdezésekhez. Lekérdezéselemzési architektúra és minden egyes szintaxis előnyeivel kapcsolatos további információkért lásd: [teljes szöveges keresés működése az Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Való meghívásának egyszerű elemzése

Egyszerű szintaxis az alapértelmezett érték. Meghívás csak akkor szükség, ha alaphelyzetbe állítja a szintaxist a teljes modellről egyszerű. A szintaxist explicit módon beállításához használja a `queryType` keresési paramétert. Érvényes értékek a következők `simple|full`, a `simple` az alapértelmezett, és `full` Lucene számára. 

## <a name="query-behavior-anomalies"></a>Lekérdezési viselkedés anomáliák

Az egy vagy több feltételeinek szöveg számít a lekérdezés-végrehajtáshoz érvényes kiindulási pontot. Az Azure Search egyezni fog a feltételeket, beleértve minden módosítást, a szöveg elemzése során talált egy részének vagy egészének tartalmazó dokumentumokat. 

A hangok szerint egyértelmű nincs lekérdezés végrehajtása az Azure Search egyik tulajdonsága, amely *előfordulhat, hogy* nem várt eredmények, növelje a helyett, további feltételek és operátorok csökkentésével a keresési eredmények a bemeneti adatok kerülnek a termék karakterlánc. A NOT operátor szerinti szűrése, kombinálva felvételét függ attól, hogy az ilyen történik-e ténylegesen egy `searchMode` paraméter beállítása, amely meghatározza, hogy nem értelmezi, hogy és vagy vagy viselkedéseket. Az alapértelmezett megadott `searchMode=Any`, és a egy NOT operátor szerinti szűrése, a művelet számítja ki, hogy vagy a művelet úgy, hogy `"New York" NOT Seattle` adja vissza, amelyek nem Seattle minden város.  

Általában Kedvet hol találhatók az operátornak foglalandó lekérdezés, ellentétben, amelyek több beépített navigációs struktúrát e-kereskedelmi helyek nagy valószínűséggel felhasználók ezek viselkedést, a felhasználói interakció minták az alkalmazásokat, amelyek a tartalomhoz, keressen a megtekintéséhez. További információkért lásd: [NOT operátor](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logikai operátorokat (AND, OR, NOT) 

Operátorok feltételek, amelyek az egyező dokumentumok találhatók széles skáláját hozhat létre egy lekérdezési karakterláncban ágyazhatók be. 

### <a name="and-operator-"></a>ÉS operátor `+`

Az AND operátor nem plusz jelre. Ha például `wifi+luxury` egyaránt tartalmazó dokumentumokat fogja keresni `wifi` és `luxury`.

### <a name="or-operator-"></a>VAGY a kezelő `|`

A vagy operátor egy függőleges oszlopra vagy egy függőleges vonal. Például `wifi | luxury` vagy tartalmazó dokumentumokat fogja keresni `wifi` vagy `luxury` vagy mindkettőt.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT operátor `-`

A NOT operátor mínuszjel. Például `wifi –luxury` dokumentumokon, amelyek meg fogja keresni az `wifi` távú és/vagy nem rendelkezik `luxury` (és/vagy által szabályozott `searchMode`).

> [!NOTE]  
>  A `searchMode` vezérlők lehetőséget, hogy egy kifejezés, és a NOT operátor műveletet vagy végbe hiányában a lekérdezésben az egyéb feltételeket egy `+` vagy `|` operátor. Emlékeztetnek arra, hogy `searchMode` is megadni `any` (alapértelmezés) vagy `all`. Ha `any`, növeli a lekérdezések visszahívása fel további találatok, és alapértelmezés szerint `-` értelmezi "Vagy NOT". Ha például `wifi -luxury` egyezni fog a dokumentumok, hogy vagy tartalmazza a kisbetűs `wifi` vagy azokkal, amelyek nem tartalmaznak az előfizetési időszak `luxury`. Ha `all`, nő a pontosság, a lekérdezések kevesebb találatot többek között, és alapértelmezés szerint - értelmezi "És a NOT". Ha például `wifi -luxury` egyezni fog a dokumentumok, amely tartalmazza a kisbetűs `wifi` , és nem tartalmaz "engedélyezhető" kifejezés. Ez valószínűleg a egy intuitívabb működés esetén a `-` operátor. Ezért érdemes használni `searchMode=all` helyett `searchMode=any` szeretné optimalizálni keresi meg a pontosság helyett már ismert, ha *és* gyakran használnak a felhasználók a `-` operátor, keresi.

## <a name="suffix-operator"></a>Utótag-operátor

Az utótag operátor nem csillag `*`. Ha például `lux*` dokumentumokon, amelyek egy kifejezés, amely kezdődik meg fogja keresni `lux`,-és nagybetűktől.  

## <a name="phrase-search-operator"></a>A kifejezés keresési operátor

A kifejezés operátor elfedi kifejezést idézőjelek `" "`. Előfordulhat például `Roach Motel` (idézőjelek nélkül) tartalmazó dokumentumok lenne keresése `Roach` és/vagy `Motel` bárhol bármilyen sorrendben, `"Roach Motel"` (az együtt) csak egyezni fog együttesen, amely a teljes kifejezést tartalmazó dokumentumok rendelés (a szövegelemzés továbbra is vonatkozik).

## <a name="precedence-operator"></a>Elsőbbségi operátor

A sorrend operátor elfedi a karakterlánc zárójelben `( )`. Ha például `motel+(wifi | luxury)` dokumentumot tartalmazó, amelyben kifejezés, és meg fogja keresni `wifi` vagy `luxury` (vagy mindkettő).  

## <a name="escaping-search-operators"></a>Escape-karaktersorozat operátorokat  

 A fenti szimbólum a keresési szöveg tényleges részeként használatához, érdemes lehet escape-karakterrel fordított perjelet illesztésével. Ha például `luxury\+hotel` eredményez az előfizetési időszak `luxury+hotel`. Annak érdekében, hogy a több tipikus esetek egyszerű dolog, alól két kivétel van ez a szabály, ahol escape-karaktersorozat nincs szükség:  

- A NOT operátor `-` csupán meg kell jelölni legyen az első karakter utáni szóközt, ha nem, ha egy időszak közepén. Például `wi-fi` egyetlen kifejezés; van, mivel a GUID-azonosítói (például `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) az egyes számít.
- Az utótag operátor `*` meg kell jelölni csak akkor, ha az utolsó karakter a szóköz, mielőtt nem, ha egy időszak közepén. Ha például `wi*fi` az egyes számít.

> [!NOTE]  
>  Bár együtt kijutását tartja jogkivonatok, szövegelemzés előfordulhat, hogy osztott őket, elemzési módtól függően. Lásd: [nyelvi támogatás &#40;Azure Search szolgáltatás REST API&#41; ](index-add-language-analyzers.md) részleteiről.  

## <a name="see-also"></a>Lásd még  

+ [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md) 
