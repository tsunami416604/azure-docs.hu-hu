---
title: Karakterláncok használata Azure Monitor log-lekérdezésekben | Microsoft Docs
description: Leírja, hogyan szerkesztheti, hasonlíthatja össze, keresheti meg és végezheti el számos más műveletet a sztringeken Azure Monitor a naplók lekérdezéseit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75397468"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Karakterláncok használata Azure Monitor naplózási lekérdezésekben


> [!NOTE]
> Az oktatóanyag elvégzése előtt fejezze be a [Azure Monitor log Analytics](get-started-portal.md) és az [első lépéseket a Azure monitor log-lekérdezések](get-started-queries.md) használatába.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk leírja, hogyan szerkesztheti, hasonlíthatja össze, keresheti meg és végezheti el számos más műveletet a karakterláncokon.

A karakterlánc minden karakterének indexe a helye alapján történik. Az első karakter a 0. index, a következő karakter 1, és így tovább. A különböző karakterlánc-függvények az indexelési számokat az alábbi részekben látható módon használják. Az alábbi példák többsége a **Print** parancs használatával mutatja be a karakterlánc-manipulációt egy adott adatforrás használata nélkül.


## <a name="strings-and-escaping-them"></a>Karakterláncok és Escape-szövegek
A karakterlánc-értékek egy vagy két idézőjeles karakterrel vannak becsomagolva. A fordított perjel (\\) a karakterek a következő karakterrel való Escape-karakterekkel való elírására szolgál: például \t for Tab, \n for sortörés, és maga az idézőjel karakter \".

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

A "\\" Escape-karakterként való működésének megakadályozásához adja hozzá a "\@" előtagot a következő sztringhez:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Karakterlánc-összehasonlítások

Művelet       |Leírás                         |Kis-és nagybetűk megkülönböztetése|Példa (hozamok `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Egyenlő                              |Igen           |`"aBc" == "aBc"`
`!=`           |Nem egyenlő                          |Igen           |`"abc" != "ABC"`
`=~`           |Egyenlő                              |Nem            |`"abc" =~ "ABC"`
`!~`           |Nem egyenlő                          |Nem            |`"aBc" !~ "xyz"`
`has`          |A jobb oldali teljes kifejezés a bal oldali oldalon |Nem|`"North America" has "america"`
`!has`         |A jobb oldali nem teljes kifejezés a bal oldali oldalon       |Nem            |`"North America" !has "amer"` 
`has_cs`       |A jobb oldali teljes kifejezés a bal oldali oldalon |Igen|`"North America" has_cs "America"`
`!has_cs`      |A jobb oldali nem teljes kifejezés a bal oldali oldalon       |Igen            |`"North America" !has_cs "amer"` 
`hasprefix`    |A jobb oldali egy kifejezés-előtag a bal oldali oldalon         |Nem            |`"North America" hasprefix "ame"`
`!hasprefix`   |A jobb oldali nem egy kifejezés előtagja a bal oldali oldalon     |Nem            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |A jobb oldali egy kifejezés-előtag a bal oldali oldalon         |Igen            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |A jobb oldali nem egy kifejezés előtagja a bal oldali oldalon     |Igen            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |A jobb oldali egy kifejezés utótagja a bal oldali oldalon         |Nem            |`"North America" hassuffix "ica"`
`!hassuffix`   |A jobb oldali nem egy kifejezés utótagja a bal oldali oldalon     |Nem            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |A jobb oldali egy kifejezés utótagja a bal oldali oldalon         |Igen            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |A jobb oldali nem egy kifejezés utótagja a bal oldali oldalon     |Igen            |`"North America" !hassuffix_cs "icA"`
`contains`     |A jobb oldali a bal oldali alsorozatként jelenik meg.  |Nem            |`"FabriKam" contains "BRik"`
`!contains`    |A jobb oldali nem jelenik meg a bal oldali oldalon           |Nem            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |A jobb oldali a bal oldali alsorozatként jelenik meg.  |Igen           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |A jobb oldali nem jelenik meg a bal oldali oldalon           |Igen           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |A jobb oldali a bal oldali első alsorozata|Nem            |`"Fabrikam" startswith "fab"`
`!startswith`  |A jobb oldali nem a bal oldali rész kezdeti alsorozata|Nem        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |A jobb oldali a bal oldali első alsorozata|Igen            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |A jobb oldali nem a bal oldali rész kezdeti alsorozata|Igen        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |A jobb oldali a bal oldali záró alsorozata|Nem             |`"Fabrikam" endswith "Kam"`
`!endswith`    |A jobb oldali nem a bal oldali záró alsorozata|Nem         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |A jobb oldali a bal oldali záró alsorozata|Igen             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |A jobb oldali nem a bal oldali záró alsorozata|Igen         |`"Fabrikam" !endswith "brik"`
`matches regex`|a bal oldali oldalon a jobb oldali egyezés szerepel        |Igen           |`"Fabrikam" matches regex "b.*k"`
`in`           |Az egyik elemmel egyenlő       |Igen           |`"abc" in ("123", "345", "abc")`
`!in`          |Nem egyenlő az elemek bármelyikével   |Igen           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Egy karakterláncban lévő alsztring előfordulásainak megszámlálása. Képes az egyszerű karakterláncok egyeztetésére vagy a regex használatára. Az egyszerű karakterlánc-egyezések átfedésben lehetnek, amíg a regex-egyezések nem.

### <a name="syntax"></a>Szintaxis
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentumok
- `text` – a bemeneti sztring 
- `search` – egyszerű karakterlánc vagy reguláris kifejezés, amely a szövegen belüli egyezést adja meg.
- `kind` - _normál_ | _regex_ (alapértelmezett: normál).

### <a name="returns"></a>Visszatérési érték

Az a szám, ahányszor a keresési karakterlánc összehasonlítható a tárolóban. Az egyszerű karakterlánc-egyezések átfedésben lehetnek, amíg a regex-egyezések nem.

### <a name="examples"></a>Példák

#### <a name="plain-string-matches"></a>Egyszerű karakterlánc-egyezések

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex-egyezések

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>kivonat

Egy adott sztringből származó reguláris kifejezés egyezésének beolvasása. Opcionálisan a kinyert alsztringet is átalakítja a megadott típusra.

### <a name="syntax"></a>Szintaxis

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentumok

- `regex` – reguláris kifejezés.
- `captureGroup` – a kinyerni kívánt rögzítési csoportot jelző pozitív egész konstans. 0 a teljes egyezés esetében 1 a reguláris kifejezésben szereplő első "(" zárójel ")" értékkel egyeztetve, a későbbi zárójelek esetében pedig 2 vagy több.
- `text` – A keresendő karakterlánc.
- `typeLiteral` – nem kötelező típusú literál (például typeof (Long)). Ha meg van adni, a kibontott alkarakterlánc erre a típusra lesz konvertálva.

### <a name="returns"></a>Visszatérési érték
Az alkarakterlánc egyeztetve lett a jelzett rögzítési csoport captureGroup, és igény szerint typeLiteral konvertálható.
Ha nincs egyezés, vagy a típus konvertálása sikertelen, a null értéket adja vissza.

### <a name="examples"></a>Példák

A következő példa egy szívverési rekord utolsó oktettjét kibontja a *ComputerIP* :
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Az alábbi példa kibontja az utolsó oktettet, a *valós* típusra (számra), és kiszámítja a következő IP-értéket
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Az alábbi példában a karakterlánc- *nyomkövetés* az "időtartam" definícióját keresi. A mérkőzés *valós* értékre van leképezve, és egy idő állandó (1 s) szorzata, *amely időtartamot vet fel a TimeSpan típusra*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, nem csábító

- a *IsEmpty* igaz értéket ad vissza, ha az argumentum egy üres sztring vagy Null (lásd még: *IsNull*).
- a *isnotempty* igaz értéket ad vissza, ha az argumentum nem üres karakterlánc vagy NULL értékű (lásd még: *isnotnull*). alias: nem *csábító*.

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


## <a name="parseurl"></a>parseurl

Feldarabol egy URL-címet a részeire (protokoll, gazdagép, Port stb.), és egy olyan szótár objektumot ad vissza, amely tartalmazza a részeket karakterláncként.

### <a name="syntax"></a>Szintaxis

```
parseurl(urlstring)
```

### <a name="examples"></a>Példák

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Az eredmény a következőket eredményezi:
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


## <a name="replace"></a>csere

Lecseréli az összes regex egyezést egy másik karakterláncra. 

### <a name="syntax"></a>Szintaxis

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentumok

- `regex` – az egyeztetendő reguláris kifejezés. Tartalmazhat rögzítési csoportokat a (z) "(zárójelek") ".
- `rewrite` – a behelyettesítési regex, amely a megfelelő regexnek felel meg. A \ 0 paranccsal hivatkozhat a teljes egyezésre, \ 1 az első rögzítési csoporthoz, \ 2 és így tovább a következő rögzítési csoportokhoz.
- `input_text` – a keresendő bemeneti karakterlánc.

### <a name="returns"></a>Visszatérési érték
A regex összes egyezésének az újraírás értékelését követő szövege. A egyezések nem fedik át egymást.

### <a name="examples"></a>Példák

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

A következő eredményekkel rendelkezhet:

Tevékenység                                        |helyébe
------------------------------------------------|----------------------------------------------------------
4663 – kísérlet történt egy objektum elérésére  |4663-as AZONOSÍTÓJÚ tevékenység: kísérlet történt egy objektum elérésére.


## <a name="split"></a>felosztás

Egy adott karakterláncot egy megadott elválasztó alapján feldarabol, és az eredményül kapott alsztringek tömbjét adja vissza.

### <a name="syntax"></a>Szintaxis
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentumok

- `source` – a megosztva kívánt karakterlánc a megadott elválasztó karakternek megfelelően.
- `delimiter` – a forrás sztring felosztásához használandó elválasztó karakter.
- `requestedIndex` – nem kötelező nulla alapú index. Ha meg van jelölve, a visszaadott karakterlánc-tömb csak az adott elem (ha létezik) marad.


### <a name="examples"></a>Példák

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Karakterlánc-argumentumok összefűzése (támogatja a 1-16 argumentumot).

### <a name="syntax"></a>Szintaxis
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Példák
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Egy karakterlánc hosszát adja vissza.

### <a name="syntax"></a>Szintaxis
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Példák
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Egy adott forrás sztringből származó alsztring kibontása a megadott indextől kezdődően. Opcionálisan megadhatja a kért alkarakterlánc hosszát is.

### <a name="syntax"></a>Szintaxis
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentumok

- `source` – az a forrás sztring, amelyet az alsztring el fog venni.
- `startingIndex` – a kért alsztring nulla alapú kiindulási karakterének pozíciója.
- `length` – nem kötelező paraméter, amely a visszaadott alsztring kért hosszának megadására használható.

### <a name="examples"></a>Példák
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, ToUpper

Egy adott sztringet konvertál az összes alsó vagy nagybetű értékre.

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



## <a name="next-steps"></a>Következő lépések
Folytassa a speciális oktatóanyagokkal:
* [Összesítési függvények](aggregations.md)
* [Speciális összesítések](advanced-aggregations.md)
* [Diagramok és diagramok](charts.md)
* [JSON-és adatstruktúrák használata](json-data-structures.md)
* [Speciális lekérdezés írása](advanced-query-writing.md)
* [Illesztések – több elemzés](joins.md)
