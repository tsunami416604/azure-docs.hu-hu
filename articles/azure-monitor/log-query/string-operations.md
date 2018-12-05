---
title: Az Azure Log Analytics-lekérdezéseket a karakterláncokkal való munka |} A Microsoft Docs
description: Ez a cikk nyújt segítséget az Analytics-portál használatával kell lekérdezéseket írni a Log Analyticsben.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: d8dec2b056107962e3e8abd4aa7073adb1ae898d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52883185"
---
# <a name="working-with-strings-in-log-analytics-queries"></a>Log Analytics-lekérdezéseket a karakterláncokkal való munka


> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-portal.md) és [Ismerkedés a lekérdezések](get-started-queries.md) Ez az oktatóanyag elvégzése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk bemutatja, hogyan szerkesztheti, összehasonlítása, a Keresés és számos egyéb karakterláncokat a művelet végrehajtása. 

Egy karakterláncban szereplő karakterek egyenként indexszáma, a helye szerint. Az első karakter 0. indexnél, a következő karakter 1, és így egy. Különböző karakterlánc-függvények indexszámok használja, az alábbiakban látható módon. Az alábbi példák közül használ a **nyomtatása** karakterlánc adatkezelési bemutatása egy adott adatforrás használata nélkül parancsot.


## <a name="strings-and-escaping-them"></a>Karakterláncok és escape-karaktersorozat őket
Karakterlánc-értékek egyikét egy vagy két idézőjel karakterrel burkolja. Fordított perjel (\) karaktereket escape-karakterrel, a következő lap, a soremelés, \n \t például szolgál, és \" maga idézőjelet.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Elkerülése érdekében "\\"nem tudja karakteréhez escape-karakter, a hozzáadása"\@" karakterláncot előtagjaként:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>A karakterlánc-összehasonlítások

Művelet       |Leírás                         |Kis-és nagybetűket|Példa (poskytne `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Egyenlő                              |Igen           |`"aBc" == "aBc"`
`!=`           |Nem egyenlő                          |Igen           |`"abc" != "ABC"`
`=~`           |Egyenlő                              |Nem            |`"abc" =~ "ABC"`
`!~`           |Nem egyenlő                          |Nem            |`"aBc" !~ "xyz"`
`has`          |Oldali jobb oldali bal oldalon található egy teljes kifejezés |Nem|`"North America" has "america"`
`!has`         |Oldali jobb oldali bal oldalon található egy teljes kifejezés nem       |Nem            |`"North America" !has "amer"` 
`has_cs`       |Oldali jobb oldali bal oldalon található egy teljes kifejezés |Igen|`"North America" has_cs "America"`
`!has_cs`      |Oldali jobb oldali bal oldalon található egy teljes kifejezés nem       |Igen            |`"North America" !has_cs "amer"` 
`hasprefix`    |Oldali jobb oldali bal kifejezés előtag         |Nem            |`"North America" hasprefix "ame"`
`!hasprefix`   |Oldali jobb oldali bal kifejezés előtag nem     |Nem            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Oldali jobb oldali bal kifejezés előtag         |Igen            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Oldali jobb oldali bal kifejezés előtag nem     |Igen            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Oldali jobb oldali bal oldalon található kifejezés utótag         |Nem            |`"North America" hassuffix "ica"`
`!hassuffix`   |Oldali jobb oldali bal oldalon található kifejezés utótag nem     |Nem            |A(z) "Észak-Amerika"! "americ" hassuffix
`hassuffix_cs`    |Oldali jobb oldali bal oldalon található kifejezés utótag         |Igen            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Oldali jobb oldali bal oldalon található kifejezés utótag nem     |Igen            |A(z) "Észak-Amerika"! "icA" hassuffix_cs
`contains`     |Oldali jobb oldali bal oldalon, a későbbi mint történik  |Nem            |`"FabriKam" contains "BRik"`
`!contains`    |A oldali bal oldali jobb nem következik be           |Nem            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Oldali jobb oldali bal oldalon, a későbbi mint történik  |Igen           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |A oldali bal oldali jobb nem következik be           |Igen           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Oldali jobb oldali bal, egy kezdeti részsorozat|Nem            |`"Fabrikam" startswith "fab"`
`!startswith`  |Oldali jobb oldali bal, egy kezdeti részsorozat nem|Nem        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Oldali jobb oldali bal, egy kezdeti részsorozat|Igen            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Oldali jobb oldali bal, egy kezdeti részsorozat nem|Igen        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Oldali jobb oldali bal oldalon, a záró részsorozat|Nem             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Oldali jobb oldali bal oldalon, a záró részsorozat nem|Nem         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Oldali jobb oldali bal oldalon, a záró részsorozat|Igen             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Oldali jobb oldali bal oldalon, a záró részsorozat nem|Igen         |`"Fabrikam" !endswith "brik"`
`matches regex`|oldali bal oldali jobb esetén tartalmazza        |Igen           |`"Fabrikam" matches regex "b.*k"`
`in`           |Egy-egy elem nagyobb, mint       |Igen           |`"abc" in ("123", "345", "abc")`
`!in`          |Az elemek nem egyenlő   |Igen           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Egy karakterlánc részkarakterláncot előfordulását számolja. Egyszerű karakterláncok egyeznie vagy regex használata is. Egyszerű karakterlánc-egyezés átfedésbe, míg egyezés reguláris kifejezés nem.

### <a name="syntax"></a>Szintaxis
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumenty:
- `text` – A bemeneti karakterlánc 
- `search` – Egyszerű karakterlánc- vagy reguláris kifejezésnek megfelelő szöveg belül.
- `kind` - _Normál_ | _regex_ (alapértelmezett: normál).

### <a name="returns"></a>adja vissza

A tároló a keresési karakterlánc nem egyező száma. Egyszerű karakterlánc-egyezés átfedésbe, amíg megfelel a reguláris kifejezés nem.

### <a name="examples"></a>Példák

#### <a name="plain-string-matches"></a>Egyszerű karakterlánc megfelel

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Megfelel reguláris kifejezés

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Kinyerés

Egyezés reguláris kifejezést olvas be egy adott karakterlánccal. Igény szerint is alakíthatók át egymásba a kinyert karakterláncrészletre a megadott típusra.

### <a name="syntax"></a>Szintaxis

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentumok

- `regex` -A reguláris kifejezés.
- `captureGroup` – Egy pozitív egész állandót vár, bontsa ki a rögzítési csoport jelzi. 0: a teljes egyezik, az első '("zárójelet')' a reguláris kifejezés, 2 vagy több, az azt követő zárójelek egyező érték 1.
- `text` – Egy karakterlánc keresése.
- `typeLiteral` – Egy nem kötelező típusú konstans (például typeof(long)). Ha meg van adva, a kinyert karakterláncrészletet ilyen alakítja át.

### <a name="returns"></a>adja vissza
A jelzett rögzítési csoport captureGroup összeveti a karakterláncrészletet typeLiteral igény szerint konvertálja.
Ha nem egyezik meg, vagy a típus átalakítás sikertelen, nesmí vracet hodnotu null.

### <a name="examples"></a>Példák

Az alábbi példa az utolsó oktettet, kinyeri *ComputerIP* a szívverés rekordból:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

A következő példa az utolsó oktettet ad eredményül, kerül, hogy egy *valós* írja be a (szám), és kiszámítja a következő IP-értéket
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

A példában a karakterlánc az alábbi *nyomkövetési* "Időtartama" definíciójának kell keresni. A match konvertálni *valós* és idő állandónak megszorozza (1 s) *amely kerül be timespan időtartama*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, notempty

- *IsEmpty* igaz, ha az argumentum értéke egy üres karakterlánc vagy null értéket ad vissza (lásd még: *isnull*).
- *isnotempty* igaz, ha az argumentum nem üres karakterlánc vagy null értéket ad vissza (lásd még: *isnotnull*). alias: *notempty*.

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

Egy URL-cím részre bontja, (protokoll, host, port, stb.), és a egy karakterláncként részeit tartalmazó szótár objektumot ad vissza.

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


## <a name="replace"></a>cserélje le

Minden regex megfelel egy másik karakterláncra cseréli. 

### <a name="syntax"></a>Szintaxis

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentumok

- `regex` – A reguláris kifejezés által megfelelően. Rögzítési csoportok zárójelben"(" ")" tartalmazhat.
- `rewrite` – A megfelelő regex bármilyen egyezés helyettesítő regex. \0 használatával tekintse meg a teljes egyezik, az első rögzítési csoport \1, \2, és így tovább a következő rögzítési csoportok.
- `input_text` – A Keresés a bemeneti karakterlánc.

### <a name="returns"></a>adja vissza
A szöveg írja újra az értékelést az összes regex-egyezés lecserélése után. Egyezések nincsenek átfedésben.

### <a name="examples"></a>Példák

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

A következő eredményeket veheti fel:
Tevékenység                                        |cseréje
------------------------------------------------|----------------------------------------------------------
4663 – kísérlet történt egy olyan objektumot  |4663. Tevékenységazonosító: Kísérlet történt egy olyan objektumot.


## <a name="split"></a>felosztás

Felosztja a megadott karakterlánc megadott elválasztó alapján, és az eredményül kapott karakterláncrészleteket tömbjét adja vissza.

### <a name="syntax"></a>Szintaxis
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumenty:

- `source` – A karakterlánc kell osztani, a megadott elválasztó alapján.
- `delimiter` – A annak érdekében, hogy a forrás-karakterlánc felosztása használandó elválasztó.
- `requestedIndex` – Nem kötelező nulla alapú index. Ha meg van adva, a visszaadott karakterlánc-tömbben feladatelemeket csak az adott elem (ha létezik).


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

(1 – 16 támogatja argumentumot) karakterlánc argumentumok fűz össze.

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


## <a name="substring"></a>karakterláncrészlet

Egy karakterláncrészletet kiolvassa a megadott forrás karakterláncot, a megadott indextől kezdődő. Szükség esetén a kért karakterláncrész hossza adható meg.

### <a name="syntax"></a>Szintaxis
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumenty:

- `source` -A forrás karakterláncot, amely a substring veszi át.
- `startingIndex` – A nulláról induló karakter kezdőpozícióját a kért karakterláncrészletet.
- `length` – Egy nem kötelező paraméter, amely segítségével adja meg a kért a visszaadott karakterláncrész hossza.

### <a name="examples"></a>Példák
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, toupper

Alsó vagy a nagybetűs alakít egy adott karakterláncot.

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
A speciális oktatóanyagok folytatásához:
* [Összesítésfüggvények](aggregations.md)
* [Speciális összesítések](advanced-aggregations.md)
* [Diagramok és ábrák](charts.md)
* [JSON és adatstruktúrák használata](json-data-structures.md)
* [Speciális lekérdezés írása](advanced-query-writing.md)
* [Csatlakoztatja - közötti elemzése](joins.md)
