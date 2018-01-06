---
title: "Hogyan hozhat létre ütemezése összetett és speciális ismétlődési az Azure Schedulerrel"
description: "Hogyan hozhat létre ütemezése összetett és speciális ismétlődési az Azure Schedulerrel"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: e1e45d394a4c442a4fb255ed6d838a589e98860e
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Hogyan hozhat létre ütemezése összetett és speciális ismétlődési az Azure Schedulerrel
## <a name="overview"></a>Áttekintés
Egy Azure Scheduler középpontjában van a *ütemezés*. Az ütemezés határozza meg, mikor és hogyan az ütemező végrehajtja-e a feladatot.

Azure Schedulerrel lehetővé teszi különböző egyszeri ütemezések és az ismétlődő feladat megadását. *Egyszeri* egyszer, egy megadott időpontban – hatékonyan érvényesítést ütemezéseket, olyan *ismétlődő* ütemezéseket csak egyszer hajtható végre. Ismétlődő ütemezés tűz az előre meghatározott gyakoriságát.

Ez rugalmasságot Azure Scheduler lehetővé teszi a üzleti forgatókönyvek széles választékát támogatja:

* Rendszeres adat-karbantartási – például a minden nap 3 hónapnál régebbi összes Twitter-üzeneteket törlése
* Archiválási – például minden hónap leküldése számla előzményeinek a biztonsági mentési szolgáltatás
* A kéréseket a külső adatokat – például 15 percenként, lekérés új ski időjárás-jelentés a NOAA
* Kép feldolgozás – pl. minden hétköznap, amikor kevesen, használja a felhőalapú számítástechnika tömörítendő képek feltöltése a nap

Ez a cikk azt bízná példa feladatokat hozhat létre Azure Scheduler is. Azt adja meg, amely leírja azokat a JSON-adatokat. Ha használja a [Feladatütemező REST API](https://msdn.microsoft.com/library/mt629143.aspx), használhat ez az azonos JSON [egy Azure Scheduler-feladat létrehozása](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Támogatott helyzetek
Ebben a témakörben szereplő számos példák bemutatják a forgatókönyvet, amely támogatja az Azure Scheduler hardverekről. Széles körű ezek a példák vnetek létrehozásának ütemezése a viselkedés sok kombinációját, többek között az alábbi megfelelően:

* Egy adott dátummal és időponttal egyszeri futtatás
* Futtassa, és explicit többször ismétlődik.
* Azonnal futtatni, és ismétlődéshez
* Futtassa és megismétlődik minden  *n*  perc, óra, nap, hét és hónap, egy adott időpontban indítása
* Futtatás és ismétlődéshez heti vagy havi gyakorisággal, hanem csak az adott napokra, a hét meghatározott napjain vagy a hónap adott napjaira
* Futtassa, és a – például utolsó péntek és minden hónapban, vagy 5 15 perckor és délután 5:15 óra naponta hétfő időn belül többször ismétlődik

## <a name="dates-and-datetimes"></a>A dátumok és időpontok
Hajtsa végre az Azure ütemezőjének dátumokra a [ISO-8601 specification](http://en.wikipedia.org/wiki/ISO_8601) és csak a dátum.

Hajtsa végre az Azure ütemezőjének dátum idejű hivatkozások a [ISO-8601 specification](http://en.wikipedia.org/wiki/ISO_8601) , és tartalmazzák a dátum és idő is részei. Egy dátum-idő nem adja meg a UTC eltolás UTC adottnak.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Útmutató: JSON és a REST API használata az ütemezés létrehozása
Létrehozni egy egyszerű ütemezés használata a [Azure Scheduler REST API](https://msdn.microsoft.com/library/mt629143), első [az előfizetés regisztrálása egy erőforrás-szolgáltató](https://msdn.microsoft.com/library/azure/dn790548.aspx) (a szolgáltató neve az ütemező *Microsoft.Scheduler* ), majd [hozzon létre egy feladatgyűjtemény](https://msdn.microsoft.com/library/mt629159.aspx), és végül [hozzon létre egy feladatot](https://msdn.microsoft.com/library/mt629145.aspx). Amikor feladatot hoz létre, megadhatja a ütemezését és az ismétlődési JSON használata hasonló az alább látható:

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // how often to fire
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>Áttekintés: Feladat séma alapjai
A következő táblázat a fő elemet a feladatok ütemezése és ismétlődési kapcsolódó magas szintű áttekintést nyújt:

| **JSON-név** | **Leírás** |
|:--- |:--- |
| ***Kezdő időpont*** |*startTime* egy dátum-idő. Az egyszerű ütemezés *startTime* első és az összetett ütemezések a feladat legkorábban elindul *startTime*. |
| ***Ismétlődés*** |A *ismétlődési* objektum határozza meg a feladat ismétlődési szabályok és az ismétlődési a feladat végrehajtja a. Az ismétlődési objektum támogatja az elemek *gyakorisága, időköz, endTime, count,* és *ütemezés*. Ha *ismétlődési* van definiálva, *gyakoriság* szükséges; egyéb elemeinek *ismétlődési* megadása nem kötelező. |
| ***gyakoriság*** |A *gyakoriság* karakterlánc, amely a gyakoriság egységet, amelyen a feladat ismétlési idejét. Támogatott értékek a következők *"perc", "hour", "day", "hetente"* vagy *"honap."* |
| ***időköz*** |A *időköz* pozitív egész szám, és azt jelzi, az időközt a *gyakoriság* , amely meghatározza, hogy a feladat futási gyakoriságát. Például ha *időköz* 3 és *gyakoriság* "hét", a feladat minden 3 hét ismétlődik. Azure Schedulerrel legfeljebb *időköz* 18 hónapig havi gyakoriság 78 hét heti gyakoriság, vagy a napi gyakoriságú 548 nap. Az órában és percben gyakoriságát, a támogatott értéktartomány: 1 < = *időköz* < = 1000. |
| ***Befejezés időpontja*** |A *endTime* karakterláncot határozza meg, amely kell nem hajtható végre: a feladat dátuma múltbeli. Érvénytelen, így nem kell rendelkeznie egy *endTime* múltbeli. Ha nincs *endTime* vagy szám van megadva, a feladat futtatása végtelenül. Mindkét *endTime* és *száma* része a ugyanazt a feladatot nem lehet. |
| ***száma*** |<p>A *száma* pozitív egész szám (nullánál nagyobb), amely meghatározza a szám, ahányszor a feladat végrehajtása előtt fusson.</p><p>A *száma* jelenti. a szám, ahányszor a feladat futtatása előtt befejeződött határozzák meg. Például egy feladatot, amely a naponta végrehajtása *száma* 5 és kezdő dátuma hétfő, péntek végrehajtása után a feladat befejeződik. Ha a kezdő dátuma a múltban, az első végrehajtása kiszámítása a létrehozása óta.</p><p>Ha nincs *endTime* vagy *száma* van megadva, a feladat futtatása végtelenül. Mindkét *endTime* és *száma* része a ugyanazt a feladatot nem lehet.</p> |
| ***ütemezés*** |Egy feladat a megadott gyakorisággal megváltoztatja az ismétlődési a ismétlődési ütemezés szerint. A *ütemezés* perc, a üzemideje (óra), a hét nap, a hónap és a hetek száma alapján módosításokat tartalmazza. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Áttekintés: A feladat alapértelmezett séma, korlátok és példák
Ez az áttekintés után most tárgyalják részletesen ezeket az elemeket.

| **JSON-név** | **Érték típusa** | **Kötelező megadni?** | **Alapértelmezett érték** | **Érvényes értékek** | **Példa** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***Kezdő időpont*** |Karakterlánc |Nem |None |ISO-8601 dátum-idők |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***Ismétlődés*** |Objektum |Nem |Nincs |Recurrence objektum |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***gyakoriság*** |Karakterlánc |Igen |Nincs |"perc", "hour", "day", "hetente", "honap" |<code>"frequency" : "hour"</code> |
| ***időköz*** |Szám |Igen |Nincs |1 és 1000 között. |<code>"interval":10</code> |
| ***Befejezés időpontja*** |Karakterlánc |Nem |Nincs |Egy jövőbeli időpontot jelölő dátum-idő |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***száma*** |Szám |Nem |None |>= 1 |<code>"count": 5</code> |
| ***ütemezés*** |Objektum |Nem |None |Schedule objektum |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Részletes bemutatója: *kezdő időpont*
A következő táblázat rögzítésekre hogyan *startTime* egy feladat futtatásának módját szabályozza.

| **startTime érték** | **Nincs ismétlődés.** | **Ismétlődés. Ütemezés nélkül** | **Ismétlődés ütemezéssel** |
|:--- |:--- |:--- |:--- |
| **A Kezdés időpontja** |Egyszer, azonnal futtatni |Egyszer, azonnal futtatni. Futtassa a későbbi végrehajtások során a legutóbbi végrehajtásának időpontja kiszámítása alapján |<p>Egyszer, azonnal futtatni</p><p>Az azt követő végrehajtások az ismétlődési ütemezés alapján futnak</p> |
| **Kezdési időpontja múltbeli** |Egyszer, azonnal futtatni |<p>Első jövőbeli végrehajtási idő kiszámítása a kezdési idő utánra, és akkor futnak</p><p>Futtassa a későbbi végrehajtások során alapú oncalculating legutóbbi végrehajtásának időpontja</p><p>Lásd a példát kapcsolódó további tájékoztatást a táblázat után</p> |<p>Sikertelen feladat indítása *nem sooner mint* a megadott kezdési időponttal. A első előfordulása alapul, az ütemezés kezdési idejét számítva</p><p>Az azt követő végrehajtások az ismétlődési ütemezés alapján futnak</p> |
| **Kezdési idő későbbi vagy a jelenlegi** |Futtassa egyszer a megadott kezdési időpontban |<p>Futtassa egyszer a megadott kezdési időpontban</p><p>Futtassa a későbbi végrehajtások során a legutóbbi végrehajtásának időpontja kiszámítása alapján</p> |<p>Sikertelen feladat indítása *nem sooner mint* a megadott kezdési időponttal. A első előfordulása alapul, az ütemezés kezdési idejét számítva</p><p>Az azt követő végrehajtások az ismétlődési ütemezés alapján futnak</p> |

Nézzük meg, mi történik, például ha *startTime* a múltban van *ismétlődési* , de nincs *ütemezés*.  Feltételezik, hogy az aktuális idő 2015-04-08 13:00, *startTime* 2015-04-07 14:00 és *ismétlődési* minden 2 nap (definiálva *gyakoriság*: nap és *időköz*: 2.) Vegye figyelembe, hogy a *startTime* a múltban van, és akkor fordul elő, az aktuális időpont előtt

Ezek a feltételek mellett a *első végrehajtási* 2015-04-09 lesz 14:00\. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat.  A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja.  Így ebben az esetben *startTime* 2015-04-07 jelenleg 2:00 pm, így a következő példány kezdve, amely 2015-04-09, 2:00 pm 2 nap.

Vegye figyelembe, hogy az első végrehajtása kellene lennie a azonos még akkor is, ha a startTime 2015-04-05 14:00 vagy 2015-04-01 14:00\. Az első végrehajtása után a későbbi végrehajtások során kiszámítása használatával ütemezett –, így azok lenne 2015-04-11, 2:00 pm, majd 2015-04-13 2:00 pm, majd 2015-04-15 2:00 pm, stb.

Végül ha a feladat ütemezés szerint, ha óra és/vagy perc nincsenek beállítva az ütemezésben szereplő, azok alapértelmezés szerint az óra és/vagy a percek száma, az első végrehajtása rendre.

## <a name="deep-dive-schedule"></a>Részletes bemutatója: *ütemezése*
Egyrészről a egy *ütemezés* is *korlát* feladat végrehajtások száma.  Például ha egy feladatot az "honap" gyakoriságát rendelkezik egy *ütemezés* , hogy csak a 31 napra esnek fut, a feladat futtatása csak a, amelyek rendelkeznek egy 31 hónapban<sup>st</sup> nap.

Másrészről egy *ütemezés* is *bontsa ki a* feladat végrehajtások száma. Például ha egy feladatot az "honap" gyakoriságát rendelkezik egy *ütemezés* , hogy fut a hónap napjain 1 és 2, a feladat fut a 1<sup>st</sup> és 2<sup>nd</sup> helyett csak egyszer a hónap a hónap napjait.

Ha több ütemezést elem meg van adva, a kiértékelés sorrendjét legnagyobbaktól a van-e a legkisebb – hét number, hónap és nap, hét nap, óra és perc.

A következő táblázat ismerteti *ütemezés* elemek részletei.

| **JSON-név** | **Leírás** | **Érvényes értékek** |
|:--- |:--- |:--- |
| **perc** |A feladat fut, amelyen az óra perc |<ul><li>Egész számok tömbje</li></ul> |
| **üzemideje (óra)** |A feladat fut, amelyen napot |<ul><li>Egész számok tömbje</li></ul> |
| **hétköznapokon** |A feladat fut a hét nap. Csak heti gyakoriság mellett adható meg. |<ul><li>Bármely tömb az alábbi értékek (maximális tömbméret 7)<ul><li>"Hétfő"</li><li>"Kedd"</li><li>"Szerda"</li><li>"Csütörtök"</li><li>"Péntek"</li><li>"Szombat"</li><li>"Vasárnap"</li></ul></li></ul>*Nem* kis-és nagybetűket |
| **monthlyOccurrences** |Meghatározza, hogy a feladat a hónap melyik napjain fog futni. Csak havi gyakoriság mellett adható meg. |<ul><li>MonthlyOccurrence objektumokból álló tömb:</li></ul> <pre>{ "day": *day*,<br />  "occurrence": *occurrence*<br />}</pre><p> *nap* -e a hét napja, a feladat fut, pl. {vasárnap} van-e a hónap minden vasárnap. Kötelező.</p><p>Az előfordulási érték *előfordulási* során a hónap napjának pl. {vasárnap, -1} hónap utolsó vasárnap. Választható.</p> |
| **monthDays** |A feladat fut a hónap napja. Csak havi gyakoriság mellett adható meg. |<ul><li>Értékek alatt álló tömb</li><ul><li>Bármely értéke < = -1 és > =-31.</li><li>Bármely érték > = 1 és < = 31.</li></ul></ul> |

## <a name="examples-recurrence-schedules"></a>Példák: Ismétlődési ütemezés
A következő példák különböző ismétlődés ütemezésének – az ütemterv objektum és az alárendelt elemei.

Az alábbi összes ütemezések feltételeztük, hogy a *időköz* értéke 1\. Emellett egy részlegnek feltételeznie kell a megfelelő gyakoriságát összhangban a a *ütemezés* – például egy nem használ gyakoriság "day", és rendelkezik a "monthDays" módosítása az ütemezésben. Az ilyen korlátozások fent ismerteti.

| **Példa** | **Leírás** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |5 órakor Futtatás minden nap. Azure Schedulerrel másolatot minden egyes értéknek felel meg a "hours", "minutes" szereplő értékekhez, egyenként, hozzon létre egy lista, amely minden alkalommal a feladat futtatását. |
| <code>{"minutes":[15], "hours":[5]}</code> |5:15 órakor Futtatás minden nap |
| <code>{"minutes":[15], "hours":[5,17]}</code> |5:15 Reggel 5:15 előtti futtatását minden nap |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Futtatás éjfélre (5:15), 5 óra 45 Perckor, 5:15 előtti, és 5:45 PM minden nap |
| <code>{"minutes":[0,15,30,45]}</code> |15 percenként futtatása |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Minden órában futtatva. Ez a feladat óránként fut. A percet vezérli a *startTime*, ha egy meg van adva, vagy ha nincs megadva, a létrehozásának ideje szerint. Például, ha a kezdési ideje vagy létrehozásának idejét (amelyik vonatkozik) du. 12:25, a feladat futni fog 00:25, 01:25, 02:25,..., 23:25. Az ütemezés értéke megegyezik a munkája *gyakoriság* "hour", az egy *időköz* 1, és nem *ütemezés*. A különbség az, hogy az ütemezés használható másik *gyakoriság* és *időköz* túl egyéb feladatok létrehozására. Például ha a *gyakoriság* "honap" volt, az ütemezés futna helyett minden nap havonta egyszer csak ha *gyakoriság* volt "day" |
| <code>{minutes:[0]}</code> |Minden órában futtatva egész. Ez a feladat is fut, óránként, de egész (pl. 12 AM, 13: 00, hajnali 2 Órakor, stb.) Ez megegyezik egy feladatot, a gyakoriság "hour", a nulla perc, és nincs ütemezés a startTime Ha gyakoriságát a "day" volt, de ha a gyakoriság "hét" vagy "honap", az ütemezés egy hét vagy havi, egy nap csak egy nap hajtaná végre kulcsattribútumokkal. |
| <code>{"minutes":[15]}</code> |15 percenként futtatásra óránként túlra óránként. Óránként fut le, 0:15-kor, 01:15-kor. 02:15-kor stb., egészen 22:15-ig és 23:15-ig. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Szombaton délután 5 óra futtatni minden héten |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Futtassa a következő hétfőn, szerdán és pénteken délután 5 óra minden héten |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |5:15 előtti 5:45 PM hétfőn, szerdán és pénteken futtatását minden héten |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |5 óra és 5 hétfőn, szerdán és pénteken futtatni minden héten |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Futtatás éjfélre (5:15), 5 óra 45 Perckor, 5:15 előtti, és délután 5:45 óra hétfőn, szerdán és pénteken minden héten |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Hétköznapokon 15 percenként fut le |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |15 percenként futtatnak hétköznapokon Reggel 9 és du. 4:45 között |
| <code>{"weekDays":["sunday"]}</code> |Futtassa a kezdési időpontban vasárnap |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Futtassa a keddi és csütörtöki napokon kezdési időpontban |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |6 órakor futtassa a 28 nap az minden hónap (feltéve, hogy a gyakoriság hónap) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Futtatás a hónap utolsó napján 6 órakor. Ha azt szeretné, a feladat futtatásához a hónap utolsó napján, használja a -1 nap 28, 29, 30 és 31 helyett. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Minden hónap első és utolsó napján 6 órakor futtassa |
| <code>{monthDays":[1,-1]}</code> |Kezdési időpontban minden hónap első és utolsó napján |
| <code>{monthDays":[1,14]}</code> |Kezdési időpontban minden hónap első és tizennegyedik napján |
| <code>{monthDays":[2]}</code> |A kezdési időpontban hónap második napján |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |5 órakor minden hónap első péntekén futtatása |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Futtassa a kezdési időpontban minden hónap első péntek |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Futtassa a hónap végét harmadik péntek minden hónapban, kezdési időpontban |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Futtassa az első és utolsó péntekén legyen havonta 5:15 órakor |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Első és utolsó péntekén legyen havonta Futtatás kezdési időpontban |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Minden hónap kezdési időpontban ötödik péntek futtatható. Ha nincs ötödik péntek hónap, ez nem fut, mivel csak ötödik péntekenként futtatásra ütemezve van. Használhatja a -1 5 helyett a előfordulásakor Ha azt szeretné, hogy fusson a feladat az utolsó bekövetkező hónap péntekig. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |15 percenként Futtatás a hónap utolsó péntekén |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Futtatás éjfélre (5:15), 5 óra 45 Perckor, 5:15 előtti, és 5:45 du. 3. szerdáján minden hónapban |

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)

