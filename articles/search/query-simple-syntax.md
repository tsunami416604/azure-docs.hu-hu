---
title: Egyszerű lekérdezés szintaxisa
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search teljes szöveges keresési lekérdezéséhez használt egyszerű lekérdezési szintaxis referenciája.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: fb98be9975de38ec9f65e723e078a1db8755b4ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792553"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search

Az Azure Cognitive Search két Lucene-alapú lekérdezési nyelvet implementál: az [egyszerű lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Az Azure Cognitive Search az egyszerű lekérdezési szintaxis kizárja a fuzzy/lejtő beállításait.  

> [!NOTE]  
>  Az Azure Cognitive Search egy alternatív [Lucene lekérdezési szintaxist](query-lucene-syntax.md) biztosít az összetettebb lekérdezésekhez. Ha többet szeretne megtudni a lekérdezés-elemzési architektúra és az egyes szintaxisok előnyeiről, tekintse meg a [teljes szöveges keresés működése az Azure Cognitive Search-ban](search-lucene-query-architecture.md)című témakört.

## <a name="how-to-invoke-simple-parsing"></a>Egyszerű elemzések meghívása

Az egyszerű szintaxis az alapértelmezett. A hívás csak akkor szükséges, ha a szintaxist teljesről egyszerűre állítja alaphelyzetbe. A szintaxis explicit beállításához használja a `queryType` keresési paramétert. Az érvényes értékek a következők: `simple|full`, az alapértelmezett `simple`, a Lucene pedig `full`. 

## <a name="query-behavior-anomalies"></a>A lekérdezés viselkedési rendellenességei

Egy vagy több kifejezéssel rendelkező szöveg érvényes kiindulási pontnak tekintendő a lekérdezés végrehajtásához. Az Azure Cognitive Search a feltételek bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezteti, beleértve a szöveg elemzése során talált módosításokat is. 

Ahogy ez hangzik, az Azure Cognitive Search lekérdezés-végrehajtásának egyik aspektusa *, amely váratlan eredményeket eredményezhet,* és a keresési eredmények csökkentése helyett egyre nagyobb mértékben bővül, és a rendszer hozzáadja a bemeneti karakterlánchoz az operátorokat. Ha ez a bővítés ténylegesen bekövetkezik, attól függ, hogy egy nem operátor bekerül-e egy `searchMode` paraméterrel, amely azt határozza meg, hogy a rendszer hogyan értelmezi a használati feltételeket és a viselkedést. Az alapértelmezett, a `searchMode=Any`és a nem operátor miatt a műveletet egy vagy műveletként számítja ki a rendszer, így `"New York" NOT Seattle` visszaadja az összes olyan várost, amely nem Seattle.  

Általában nagyobb valószínűséggel tekintheti meg ezeket a viselkedéseket a felhasználói interakciós mintákban a tartalmat kereső alkalmazások esetében, ahol a felhasználók nagyobb valószínűséggel vehetik igénybe az operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel. További információ: [not operátor](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logikai operátorok (és, vagy nem) 

A lekérdezési karakterláncban lévő operátorok beágyazásával számos feltételt hozhat létre, amelyekkel a megfelelő dokumentumok találhatók. 

### <a name="and-operator-"></a>ÉS operátor `+`

A és az operátor egy plusz jel. `wifi+luxury` például a `wifi` és a `luxury`egyaránt tartalmazó dokumentumokat fogja keresni.

### <a name="or-operator-"></a>VAGY operátor `|`

A vagy operátor egy függőleges sáv vagy cső karakter. A `wifi | luxury` például `wifi` vagy `luxury` vagy mindkettőt tartalmazó dokumentumokat keres.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NEM operátor `-`

A NOT operátor egy mínusz jel. A `wifi –luxury` például megkeresi azokat a dokumentumokat, amelyek `wifi` kifejezéssel rendelkeznek, és/vagy amelyek nem rendelkeznek `luxury`tal (és/vagy `searchMode`által vezérelt).

> [!NOTE]  
>  A `searchMode` beállítás azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel `+` vagy `|` operátor hiányában. Ne felejtse el, hogy `searchMode` beállítható `any` (alapértelmezett) vagy `all`ra. Ha `any`használ, a további eredményekkel együtt növeli a lekérdezések visszahívását, és alapértelmezés szerint a `-` "vagy nem"-ként lesz értelmezve. Például a `wifi -luxury` olyan dokumentumokhoz fog illeszkedni, amelyek tartalmazzák a `wifi` kifejezést, vagy a `luxury`kifejezést nem tartalmazó dokumentumokat. Ha `all`használ, a kevesebb eredményt is magában foglaló lekérdezések pontosságát növeli, és alapértelmezés szerint a "és nem" értéket fogja értelmezni. A `wifi -luxury` például a `wifi` kifejezést tartalmazó dokumentumokat fogja egyeztetni, és nem tartalmazza a "luxus" kifejezést. Ez vitathatatlanul intuitív viselkedés a `-` operátor számára. Ezért érdemes megfontolnia `searchMode=all` használatát `searchMode=any` helyett, ha a nem visszahívás helyett a pontosságot szeretné keresni, *és* a felhasználók gyakran használják az `-` operátort a keresésekben.

## <a name="suffix-operator"></a>Utótag operátora

Az utótag operátor egy csillag `*`. A `lux*` például olyan dokumentumokra keres rá, amelyek olyan kifejezéssel rendelkeznek, amely a `lux`vel kezdődik, és figyelmen kívül hagyja a kis-és nagybetűket.  

## <a name="phrase-search-operator"></a>Kifejezés keresési operátora

A kifejezés operátor a kifejezést idézőjelek közé `" "`. Előfordulhat például, hogy `Roach Motel` (idézőjelek nélkül) a `Roach`t és/vagy `Motel`t tartalmazó dokumentumokat bármilyen sorrendben keresi, `"Roach Motel"` (idézőjelekkel együtt) csak a teljes kifejezést tartalmazó dokumentumokat fogja tartalmazni, és ebben a sorrendben (a szöveg elemzése továbbra is fennáll. érvényes).

## <a name="precedence-operator"></a>Elsőbbségi operátor

A sorrend operátor a karakterláncot zárójelek közé `( )`. `motel+(wifi | luxury)` például megkeresi a Motel kifejezést tartalmazó dokumentumokat, valamint `wifi` vagy `luxury`t (vagy mindkettőt).  

## <a name="escaping-search-operators"></a>Keresési operátorok elől  

 Ahhoz, hogy a fenti szimbólumokat a keresett szöveg tényleges részeként lehessen használni, el kell menekülniük a perjelekkel való előrögzítéssel. `luxury\+hotel` például a `luxury+hotel`kifejezést fogja eredményezni. Ahhoz, hogy a dolgok egyszerűbbek legyenek a tipikus esetekben, a szabálynak két kivétele van, ahol nincs szükség a szökésre:  

- A nem operátort `-` csak akkor kell megszökni, ha az első karakter a szóköz után, nem, ha egy kifejezés közepén van. `wi-fi` például egyetlen kifejezés; míg a GUID azonosítók (például `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) egyetlen jogkivonatként vannak kezelve.
- Az utótag operátorának `*` csak akkor kell megszöknie, ha az utolsó karakter a szóköz előtt, nem, ha egy kifejezés közepén van. A `wi*fi` például egyetlen jogkivonatként kezeli a rendszer.

> [!NOTE]  
>  Bár a Escapes megtartja a tokeneket, a szöveg elemzése az elemzési módtól függően feldarabolhatja őket. A részletekért lásd a [ &#40;Language&#41; support Azure Cognitive Search REST API](index-add-language-analyzers.md) című témakört.  

## <a name="see-also"></a>Lásd még:  

+ [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md) 
