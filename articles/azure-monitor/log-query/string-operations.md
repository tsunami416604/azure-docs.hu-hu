---
title: Karakterláncok kezelése az Azure Monitor naplólekérdezéseiben | Microsoft dokumentumok
description: Bemutatja, hogyan szerkesztheti, hasonlíthatja össze, kereshet és hajthat végre számos más műveletet az Azure Monitor naplólekérdezéseiben a karakterláncokon.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397468"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Karakterláncok kezelése az Azure Monitor naplólekérdezéseiben


> [!NOTE]
> Az oktatóanyag befejezése előtt el kell [végeznie az Azure Monitor loganalytics](get-started-portal.md) és [az Azure Monitor naplólekérdezéseinek első lépéseit.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk bemutatja, hogyan szerkeszti, hasonlítsa össze, keressen és hajtson végre számos más műveleteket a karakterláncokon.

A karakterlánc minden karakterének van egy indexszáma, a helyének megfelelően. Az első karakter index 0, a következő karakter 1, és így tovább. A különböző karakterlánc-függvények indexszámokat használnak a következő szakaszokban látható módon. Az alábbi példák közül sok a **nyomtatási** paranccsal mutatja be a karakterláncok manipulálását egy adott adatforrás használata nélkül.


## <a name="strings-and-escaping-them"></a>Húrok és menekülés őket
A karakterlánc-értékek et egy- vagy dupla idézőjelekkel csomagolják. A fordított\\perjel ( ) a karakterek kikerülésére szolgál az azt követő karakterhez, például \t laphoz, \n új sorhoz és \" magához az idézőkarakterhez.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Ha meg\\szeretné akadályozni, hogy a\@" " " escape karakterként működjön, adja hozzá a " " " előtagot a karakterlánchoz:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Karakterlánc-összehasonlítások

Művelet       |Leírás                         |Kis- és nagybetűk megkülönböztetése|Példa (hozamok) `true`
---------------|------------------------------------|--------------|-----------------------
`==`           |Egyenlő                              |Igen           |`"aBc" == "aBc"`
`!=`           |Nem egyenlő                          |Igen           |`"abc" != "ABC"`
`=~`           |Egyenlő                              |Nem            |`"abc" =~ "ABC"`
`!~`           |Nem egyenlő                          |Nem            |`"aBc" !~ "xyz"`
`has`          |A jobb oldali kifejezés egy egész kifejezés a bal oldali |Nem|`"North America" has "america"`
`!has`         |A jobb oldal nem teljes kifejezés a bal oldalon.       |Nem            |`"North America" !has "amer"` 
`has_cs`       |A jobb oldali kifejezés egy egész kifejezés a bal oldali |Igen|`"North America" has_cs "America"`
`!has_cs`      |A jobb oldal nem teljes kifejezés a bal oldalon.       |Igen            |`"North America" !has_cs "amer"` 
`hasprefix`    |A jobb oldali kifejezés a bal oldali         |Nem            |`"North America" hasprefix "ame"`
`!hasprefix`   |A jobb oldal nem egy kifejezés előtag a bal oldali     |Nem            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |A jobb oldali kifejezés a bal oldali         |Igen            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |A jobb oldal nem egy kifejezés előtag a bal oldali     |Igen            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |A jobb oldali kifejezés utótag a bal oldali         |Nem            |`"North America" hassuffix "ica"`
`!hassuffix`   |A jobb oldal nem egy kifejezés utótag a bal oldali     |Nem            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |A jobb oldali kifejezés utótag a bal oldali         |Igen            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |A jobb oldal nem egy kifejezés utótag a bal oldali     |Igen            |`"North America" !hassuffix_cs "icA"`
`contains`     |A jobb oldali a bal oldali részsorozatként  |Nem            |`"FabriKam" contains "BRik"`
`!contains`    |A jobb oldali nem fordul elő a bal oldali           |Nem            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |A jobb oldali a bal oldali részsorozatként  |Igen           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |A jobb oldali nem fordul elő a bal oldali           |Igen           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |A jobb oldali a bal oldali|Nem            |`"Fabrikam" startswith "fab"`
`!startswith`  |A jobb oldal nem a bal oldali részsorozat.|Nem        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |A jobb oldali a bal oldali|Igen            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |A jobb oldal nem a bal oldali részsorozat.|Igen        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |A jobb oldali a bal oldali oldal záró alszekvenciája|Nem             |`"Fabrikam" endswith "Kam"`
`!endswith`    |A jobb oldal nem a bal oldali záró részsorozat.|Nem         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |A jobb oldali a bal oldali oldal záró alszekvenciája|Igen             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |A jobb oldal nem a bal oldali záró részsorozat.|Igen         |`"Fabrikam" !endswith "brik"`
`matches regex`|A bal oldali egyezést tartalmaz a jobb oldali        |Igen           |`"Fabrikam" matches regex "b.*k"`
`in`           |Egyenlő az egyik elem       |Igen           |`"abc" in ("123", "345", "abc")`
`!in`          |Nem egyenlő az elemek bármelyikével   |Igen           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>gróf

Karakterlánc-részkarakterlánc előfordulásait számolja meg. Egyezik az egyszerű karakterláncokkal, vagy használhatja a regex-et. Az egyszerű karakterlánc-egyezések átfedésben lehetnek, míg a regex-egyezések nem.

### <a name="syntax"></a>Szintaxis
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Érvek:
- `text`- A bemeneti karakterlánc 
- `search`- Egyszerű karakterlánc vagy reguláris kifejezés a szövegbelsejében való egyeztetéshez.
- `kind` - _normál_ | _regex_ (alapértelmezett: normál).

### <a name="returns"></a>Visszatérési érték

Azon alkalmak száma, ahányszor a keresési karakterlánc egyeztethető a tárolóban. Az egyszerű karakterlánc-egyezések átfedésben lehetnek, míg a regex-egyezések nem.

### <a name="examples"></a>Példák

#### <a name="plain-string-matches"></a>Egyszerű karakterlánc-egyezések

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex mérkőzések

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Kivonat

Egy adott karakterlánc reguláris kifejezésére vonatkozó egyezést kap. Tetszés szerint a kibontott karakterláncot a megadott típusra konvertálja.

### <a name="syntax"></a>Szintaxis

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentumok

- `regex`- Egy szabályos kifejezés.
- `captureGroup`- Pozitív egész szám állandó, amely a kibontandó rögzítési csoportot jelzi. 0 a teljes egyezésre, 1 a reguláris kifejezésben az első "("zárójel")"-hez illeszkedő értékre, a későbbi zárójelek esetében 2 vagy több.
- `text`- Egy madzag, amit meg kell keresni.
- `typeLiteral`- Egy választható típusú konstans (például typeof(long)). Ha meg van adva, a kinyert karakterláncrész erre a típusra lesz konvertálva.

### <a name="returns"></a>Visszatérési érték
A részkarakterlánc egyeztetett a jelzett rögzítési csoport captureGroup, opcionálisan konvertált típusLiteral.
Ha nincs egyezés, vagy a típuskonvertálás sikertelen, null értéket ad vissza.

### <a name="examples"></a>Példák

A következő példa a *ComputerIP* utolsó oktettét bontja ki egy szívverésrekordból:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

A következő példa kinyeri az utolsó oktettet, *valódi* típusra (számra) veti, és kiszámítja a következő IP-értéket.
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Az alábbi példában a *nyomkövetés* karakterlánca az "Időtartam" definíciójára keres. A mérkőzés *valósra* van vetve, és megszorozva egy időállandóval (1 s), *amely az Időtartam ot írja be az időtartományba*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *az isempty* igaz értéket ad vissza, ha az argumentum üres karakterlánc vagy null (lásd még *is null*).
- *az isnotempty* igaz értéket ad vissza, ha az argumentum nem üres karakterlánc vagy null (lásd még *is isnotnull*). alias: *notempty*.

### <a name="syntax"></a>Szintaxis

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Példák

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>elemzési cím

Az URL-címet a részeire osztja fel (protokoll, állomás, port stb.), és karakterláncként a részeket tartalmazó szótárobjektumot ad vissza.

### <a name="syntax"></a>Szintaxis

```
parseurl(urlstring)
```

### <a name="examples"></a>Példák

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Az eredmény a következő lesz:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>Helyettesít

Az összes regex egyezést lecseréli egy másik karakterláncra. 

### <a name="syntax"></a>Szintaxis

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentumok

- `regex`- A reguláris kifejezés, ami passzol. Tartalmazhat rögzítési csoportokat a '('parentheses)"-ben.
- `rewrite`- A helyettesítő regex minden mérkőzés által megfelelő regex. A\0 kapcsolóval hivatkozhat a teljes egyezésre, \1 az első rögzítési csoportra, \2, és így tovább a következő rögzítési csoportokra.
- `input_text`- A bemeneti karakterlánc, amiben keresni lehet.

### <a name="returns"></a>Visszatérési érték
A szöveg cseréje után minden mérkőzés regex értékelése átírása. A gyufa nem fedi egymást.

### <a name="examples"></a>Példák

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Lehet a következő eredményeket:

Tevékenység                                        |Helyébe
------------------------------------------------|----------------------------------------------------------
4663 - Kísérlet történt egy objektum elérésére  |4663-as tevékenységazonosító: Kísérlet történt egy objektum elérésére.


## <a name="split"></a>felosztás

Egy adott karakterláncfelosztása egy megadott határolójel szerint, és az eredményül kapott részkarakterláncok tömbjét adja vissza.

### <a name="syntax"></a>Szintaxis
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Érvek:

- `source`- A megadott határolójel nek megfelelően felosztandó karakterlánc.
- `delimiter`- A forráskarakterlánc felosztásához használt határoló jel.
- `requestedIndex`- Egy opcionális nulla-alapú index. Ha meg van adva, a visszaadott karakterlánctömb csak az adott elemet fogja tárolni (ha létezik).


### <a name="examples"></a>Példák

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat között

Összefűzi a karakterlánc argumentumait (támogatja az 1-16 argumentumot).

### <a name="syntax"></a>Szintaxis
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Példák
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>Strlen

Egy karakterlánc hosszát adja eredményül.

### <a name="syntax"></a>Szintaxis
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Példák
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>Substring

Egy adott forráskarakterlánc részkarakterláncát bontja ki a megadott indextől kezdve. Szükség esetén a kért részkarakterlánc hossza megadható.

### <a name="syntax"></a>Szintaxis
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Érvek:

- `source`- Az a forráskarakterlánc, amelyből a karakterlánc részkarakterláncát veszi a rendszer.
- `startingIndex`- A kért részkarakterlánc nulla-alapú kezdőkarakter-pozíciója.
- `length`- Egy választható paraméter, amely a visszaadott karakterlánc kért hosszának megadására használható.

### <a name="examples"></a>Példák
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>alower, toupper

Adott karakterláncot alakít át az összes kis- vagy nagybetűssé.

### <a name="syntax"></a>Szintaxis
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Példák
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>További lépések
Folytassa a speciális oktatóanyagokkal:
* [Aggregátumfüggvények](aggregations.md)
* [Speciális aggregátumok](advanced-aggregations.md)
* [Diagramok](charts.md)
* [A JSON és az adatstruktúrák együttműködése](json-data-structures.md)
* [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
* [Illesztések - keresztelemzés](joins.md)
