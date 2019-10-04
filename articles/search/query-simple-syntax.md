---
title: Egyszerű lekérdezési szintaxis – Azure Search
description: A Azure Search teljes szöveges keresési lekérdezéséhez használt egyszerű lekérdezési szintaxisra mutató hivatkozás.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: e6c5ea86534001e0e5de2b02c4151af70631e4ef
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650015"
---
# <a name="simple-query-syntax-in-azure-search"></a>Egyszerű lekérdezési szintaxis a Azure Searchban
Azure Search két Lucene-alapú lekérdezési nyelvet implementál: [Egyszerű lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Azure Search az egyszerű lekérdezési szintaxis kizárja a fuzzy/lejtő beállításait.  

> [!NOTE]  
>  Azure Search egy alternatív [Lucene lekérdezési szintaxist](query-lucene-syntax.md) biztosít az összetettebb lekérdezésekhez. Ha többet szeretne megtudni a lekérdezés-elemzési architektúra és az egyes szintaxisok előnyeiről, tekintse meg a [teljes szöveges keresés működése a Azure Searchban](search-lucene-query-architecture.md)című témakört.

## <a name="how-to-invoke-simple-parsing"></a>Egyszerű elemzések meghívása

Az egyszerű szintaxis az alapértelmezett. A hívás csak akkor szükséges, ha a szintaxist teljesről egyszerűre állítja alaphelyzetbe. A szintaxis explicit beállításához használja a `queryType` keresési paramétert. Az érvényes értékek `simple|full`a következők `simple` :, az alapértelmezett és `full` a Lucene esetében. 

## <a name="query-behavior-anomalies"></a>A lekérdezés viselkedési rendellenességei

Egy vagy több kifejezéssel rendelkező szöveg érvényes kiindulási pontnak tekintendő a lekérdezés végrehajtásához. A Azure Search a feltételek bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezteti, beleértve a szöveg elemzése során talált változatokat is. 

Ahogy ez hangzik, az Azure Search lekérdezés-végrehajtásának egyik aspektusa, amely váratlan eredményeket eredményezhet, és a keresési eredmények csökkentése helyett egyre nagyobb mértékben növekszik, és a rendszer hozzáadja a bemeneti karakterlánchoz az operátorokat. Ha ez a bővítés ténylegesen bekövetkezik, attól függ, hogy egy nem operátor bekerül `searchMode` -e, és egy olyan paraméter-beállítással együtt, amely meghatározza, hogy a rendszer hogyan értelmezi a használati feltételeket és a viselkedést. Az alapértelmezett, `searchMode=Any`és egy not operátor miatt a művelet egy vagy műveletként van kiszámítva, amely `"New York" NOT Seattle` az összes Seattle-ből származó várost visszaadja.  

Általában nagyobb valószínűséggel tekintheti meg ezeket a viselkedéseket a felhasználói interakciós mintákban a tartalmat kereső alkalmazások esetében, ahol a felhasználók nagyobb valószínűséggel vehetik igénybe az operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel. További információ: [not operátor](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logikai operátorok (és, vagy nem) 

A lekérdezési karakterláncban lévő operátorok beágyazásával számos feltételt hozhat létre, amelyekkel a megfelelő dokumentumok találhatók. 

### <a name="and-operator-"></a>ÉS operátor`+`

A és az operátor egy plusz jel. Például `wifi+luxury` megkeresi a `wifi` és a- `luxury`t tartalmazó dokumentumokat.

### <a name="or-operator-"></a>VAGY operátor`|`

A vagy operátor egy függőleges sáv vagy cső karakter. Például `wifi | luxury` megkeresi a `wifi` vagy `luxury` a vagy a mindkettőt tartalmazó dokumentumokat.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NEM operátor`-`

A NOT operátor egy mínusz jel. Például `wifi –luxury` megkeresi azokat a dokumentumokat, amelyek esetében `wifi` a kifejezés és/vagy nem rendelkezik `luxury` (és/vagy a vezérlését `searchMode`).

> [!NOTE]  
>  A `searchMode` beállítás azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel egy `+` vagy `|` operátor hiányában. Visszahívás, `searchMode` amely beállítható `any` a következőre: vagy ( `all`alapértelmezett) vagy. A használata `any`esetén a több eredmény hozzáadásával növeli a lekérdezések visszahívását, és alapértelmezés `-` szerint "vagy nem" lesz értelmezve. Például `wifi -luxury` megegyeznek azok `wifi` a dokumentumok, amelyek tartalmazzák a kifejezést, vagy amelyek nem tartalmazzák a kifejezést `luxury`. A használatakor `all`a lekérdezés a rövidebb eredményekkel együtt növeli a lekérdezések pontosságát, és alapértelmezés szerint a "és nem" értéket fogja értelmezni. Például a a `wifi -luxury` kifejezést `wifi` tartalmazó dokumentumokat fogja egyeztetni, és nem tartalmazza a "luxus" kifejezést. Ez vitathatatlanul intuitív viselkedést biztosít a `-` kezelő számára. `searchMode=all` Ezért érdemes megfontolni, `searchMode=any` hogy ahelyett, hogy visszahívást szeretne használni a pontossághoz, *és* a felhasználók gyakran használják a keresésekben `-` a kezelőt.

## <a name="suffix-operator"></a>Utótag operátora

Az utótag operátor egy csillag `*`. Például `lux*` megkeresi azokat a dokumentumokat, amelyek egy kifejezéssel `lux`kezdődnek, figyelmen kívül hagyva az esetet.  

## <a name="phrase-search-operator"></a>Kifejezés keresési operátora

A kifejezés operátor idézőjelek `" "`közé foglal egy kifejezést. Például amíg `Roach Motel` (idézőjelek nélkül) megkeresi az összes olyan dokumentumot, amely `Motel` bármilyen sorrendben tartalmaz `Roach` és/ `"Roach Motel"` vagy bárhol, (idézőjelekkel), csak az egész kifejezést tartalmazó dokumentumokat fogja össze sorrend (a szöveg elemzése továbbra is érvényes).

## <a name="precedence-operator"></a>Elsőbbségi operátor

A sorrend operátor zárójelben `( )`zárja be a karakterláncot. Például `motel+(wifi | luxury)` megkeresi a Motel kifejezést tartalmazó dokumentumokat `wifi` és a vagy `luxury` (vagy mindkettőt).  

## <a name="escaping-search-operators"></a>Keresési operátorok elől  

 Ahhoz, hogy a fenti szimbólumokat a keresett szöveg tényleges részeként lehessen használni, el kell menekülniük a perjelekkel való előrögzítéssel. A kifejezés például `luxury\+hotel` a kifejezést `luxury+hotel`fogja eredményezni. Ahhoz, hogy a dolgok egyszerűbbek legyenek a tipikus esetekben, a szabálynak két kivétele van, ahol nincs szükség a szökésre:  

- A nem operátort `-` csak akkor kell megszökni, ha az első karakter a szóköz után, nem, ha egy kifejezés közepén van. Például `wi-fi` egy kifejezés, míg a GUID azonosítók ( `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`például) egyetlen jogkivonatként vannak kezelve.
- Az utótag operátort `*` csak akkor kell megszökni, ha az utolsó karakter a szóköz előtt, nem, ha egy kifejezés közepén van. Például egyetlen jogkivonatként kezeli a `wi*fi` rendszer.

> [!NOTE]  
>  Bár a Escapes megtartja a tokeneket, a szöveg elemzése az elemzési módtól függően feldarabolhatja őket. A részletekért tekintse meg a [Language support &#40;Azure Search Service REST API&#41; ](index-add-language-analyzers.md) című témakört.  

## <a name="see-also"></a>Lásd még  

+ [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md) 
