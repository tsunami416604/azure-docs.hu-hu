---
title: "Összetett ütemezés és a speciális ismétlődési és Azure-ütemező hozhat létre."
description: "Ismerje meg, hogyan hozhat létre ütemezése összetett és speciális ismétlődési és Azure-ütemező."
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
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Összetett ütemezés és a speciális ismétlődési és Azure-ütemező hozhat létre.

Egy Azure Scheduler feladat mag az ütemezés. Az ütemezés határozza meg, mikor és hogyan Feladatütemező végrehajtja-e a feladatot. 

A Feladatütemező segítségével feladat több egyszeri és ismétlődő ütemezéseket állíthat be. Egyszeri ütemezések érvényesítést egyszer, egy megadott időpontban. Egyszeri ütemezések hatékonyan amelyek ismétlődő ütemezéseket csak egyszer hajtható végre. Ismétlődő ütemezés tűz az előre meghatározott gyakoriságát.

Erre a rugalmasságra Feladatütemező segítségével számos különböző üzleti forgatókönyvek:

* **Rendszeres adat-karbantartási**. Naponta, például az összes Twitter-üzeneteket, amelyek a három hónapnál régebbi törlése.
* **Archiválás**. Például minden hónap, leküldéses számla előzményeinek a biztonsági mentési szolgáltatáshoz.
* **Külső adatokra vonatkozó**. 15 percenként, például egy új ski időjárás-jelentés lekérés a NOAA.
* **Kép feldolgozási**. Minden hétköznap, amikor kevesen, például a lemezképeket, amelyek adott napon töltődtek tömörítendő felhőszámítási használja.

Ebben a cikkben azt ismerteti, hogy a Feladatütemező használatával hozhat létre például feladatok keresztül. Azt adja meg, amely leírja azokat a JSON-adatokat. Ha használja a [Feladatütemező REST API](https://msdn.microsoft.com/library/mt629143.aspx), használhatja ezt a azonos JSON [hozzon létre egy Scheduler feladatot](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Támogatott esetek
Ebben a cikkben szereplő példák bemutatják a forgatókönyvet, amely támogatja a Feladatütemező hardverekről. A példák körben vnetek létrehozásának ütemezése a viselkedés sok kombinációját, beleértve:

* Egyszeri futtatás a egy adott időpontban.
* Futtassa, és meghatározott számú alkalommal megismétlődik.
* Azonnal futtatni, és megismétlődik.
* Futtassa és megismétlődik minden  *n*  perc, óra, nap, hét és hónap, kezdve egy megadott időpontban.
* Futtassa, és heti vagy havi gyakorisággal, de csak a hét meghatározott napjain vagy a hónap adott napjain megismétlődik.
* Futtassa, és időn belül többször ismétlődik. Például, utolsó péntekén és minden hónap vagy Reggel 5:15 és 5:15 előtti minden nap az előző hétfőn.

## <a name="date-and-date-time"></a>Dátum és a dátum-idő
Feladatütemező feladatai kövesse elemben található hivatkozások dátum a [ISO 8601 specification](http://en.wikipedia.org/wiki/ISO_8601), és csak a dátum.

Dátum-idő hivatkozások ütemezőjének kövesse a [ISO 8601 specification](http://en.wikipedia.org/wiki/ISO_8601), és a dátum és idő is. Egy dátum-idő nem adja meg a UTC eltolás UTC adottnak.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Használja a JSON és a REST API ütemezés létrehozása
Használatával egy egyszerű ütemezés létrehozásához a [Feladatütemező REST API](https://msdn.microsoft.com/library/mt629143), első [az előfizetés regisztrálása egy erőforrás-szolgáltató](https://msdn.microsoft.com/library/azure/dn790548.aspx). A szolgáltató neve az ütemező **Microsoft.Scheduler**. Ezt követően [hozzon létre egy feladatgyűjtemény](https://msdn.microsoft.com/library/mt629159.aspx). Végezetül [hozzon létre egy feladatot](https://msdn.microsoft.com/library/mt629145.aspx). 

Egy feladat létrehozásakor megadhatja ütemezés és ismétlődési használatával JSON, például a cikkből:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Feladat séma alapjai
A következő táblázat a fő elemet ismétlődési és az ütemezés beállítása a feladatok használó magas szintű áttekintést nyújt:

| JSON-név | Leírás |
|:--- |:--- |
| **startTime** |Egy dátum-idő értéknek megfelelően. Az alapvető ütemezések **startTime** első van. Az összetett ütemezések a feladat legkorábban elindul **startTime**. |
| **recurrence** |Adja meg a feladatot, és az ismétlődési, amelyen a feladat futtatása ismétlődési szabályait. Az ismétlődési objektum támogatja az elemek **gyakoriság**, **időköz**, **endTime**, **száma**, és **ütemezése**. Ha **ismétlődési** van definiálva, **gyakoriság** szükséges. Más **ismétlődési** elemek egyike sem kötelező. |
| **frequency** |A gyakoriság egységet, amelyen a feladat ismétlési idejét jelölő karakterlánccá. Támogatott értékei a "perc", "hour", "day", "hetente" és "honap". |
| **interval** |Egy pozitív egész szám. **időköz** intervallumát jelöli a **gyakoriság** érték, amely meghatározza, hogy milyen gyakran a feladat futtatásakor. Például ha **időköz** 3 és **gyakoriság** "hét", a feladat minden három hét ismétlődik.<br /><br />A Feladatütemező legfeljebb **időköz** 18 havi gyakoriság, a heti gyakoriság 78 és a napi gyakoriság 548. Az órában és percben gyakoriságát, a támogatott értéktartomány: 1 < = **időköz** < = 1000. |
| **endTime** |A dátum-idő, amelyek a feladat futtatása nem karakterlánc. Egy érték a **endTime** , amely a múltban van. Ha **endTime** és **száma** nincsenek megadva, a feladat futtatása végtelenül. Nem adhat meg mindkét **endTime** és **száma** ugyanazt a feladatot. |
| **Száma** |Egy pozitív egész szám (nullánál nagyobb), amely meghatározza a szám, ahányszor a feladat fut. a befejeződése előtt.<br /><br />**Count** jelenti. a szám, ahányszor a feladat futtatása előtt határozzák meg. Például egy feladatot, amely a naponta végrehajtása egy **száma** 5 és a kezdő dátum hétfő, a feladat befejeződik, péntek végrehajtása után. Ha a kezdő dátuma a múltban, az első végrehajtása kiszámítása a létrehozása óta.<br /><br />Ha nincs **endTime** vagy **száma** van megadva, a feladat futtatása végtelenül. Nem adhat meg mindkét **endTime** és **száma** ugyanazt a feladatot. |
| **schedule** |Egy feladat a megadott gyakorisággal megváltoztatja az ismétlődési a ismétlődési ütemezés szerint. A **ütemezés** érték perc, a üzemideje (óra), a hét nap, a hónap és a hetek száma alapján módosításokat tartalmaz. |

## <a name="job-schema-defaults-limits-and-examples"></a>Feladat séma alapértelmezett értékeket, korlátok és példák
A cikk későbbi részében tárgyaljuk részletesen a következő elemmel:

| JSON-név | Érték típusa | Kötelező? | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |karakterlánc |Nem |Nincs |ISO 8601 dátum-idő |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |objektum |Nem |Nincs |A ütemezésismétlődési objektum |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |karakterlánc |Igen |Nincs |"minute", "hour", "day", "week", "month" |`"frequency" : "hour"` |
| **interval** |szám |Igen |Nincs |1-1000. |`"interval":10` |
| **endTime** |karakterlánc |Nem |Nincs |Egy ideje a jövőben jelölő dátum-idő érték |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Száma** |szám |Nem |None |>= 1 |`"count": 5` |
| **schedule** |objektum |Nem |Nincs |Az ütemterv objektum |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>A startTime részletes bemutatása
A következő táblázat ismerteti, hogyan **startTime** meghatározza, hogy egy feladat futtatása:

| startTime értéke | Nincs ismétlődés. | Ismétlődési ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |:--- |
| **A Kezdés időpontja** |Egyszer, azonnal futtatni. |Egyszer, azonnal futtatni. Futtassa a későbbi végrehajtások során legutóbbi végrehajtásának időpontja számítva. |Egyszer, azonnal futtatni.<br /><br />A későbbi végrehajtások során a ismétlődési ütemezés szerint futtatni. |
| **Kezdő dátuma a múltban** |Egyszer, azonnal futtatni. |Az első jövőbeli végrehajtási idő kiszámítása a kezdési idő utánra, és akkor futnak.<br /><br />Futtassa a későbbi végrehajtások során legutóbbi végrehajtásának időpontja számítva. <br /><br />További információkért lásd a táblázatot követő példában. |Sikertelen feladat indítása *nem sooner mint* a megadott kezdési időponttal. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br /><br />A későbbi végrehajtások során a ismétlődési ütemezés szerint futtatni. |
| **Kezdési idő, a jövőben vagy a jelenlegi időpontnál** |Futtassa egyszer a megadott kezdési időpontban. |Futtassa egyszer a megadott kezdési időpontban.<br /><br />Futtassa a későbbi végrehajtások során legutóbbi végrehajtásának időpontja számítva.|Sikertelen feladat indítása *nem sooner mint* a megadott kezdési időponttal. A első előfordulása az ütemezés kezdési idejét számítva alapul.<br /><br />A későbbi végrehajtások során a ismétlődési ütemezés szerint futtatni. |

Nézzük például mi történik, amikor **startTime** ismétlődési, de nincs ütemezés a múltbeli.  Feltételezik, hogy az aktuális idő 2015-04-08 13:00, **startTime** 2015-04-07 14:00 és **ismétlődési** minden két nap (definiálva **gyakoriság**: nap és **időköz**: 2.) Vegye figyelembe, hogy **startTime** a múltban van, és az aktuális időpont előtt következik be.

Ebben az első végrehajtása 2015-04-09: 14:00\ lesz. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Ebben az esetben **startTime** 2015-04-07 jelenleg 2:00 PM, így a következő példány kezdve, amely 2015-04-09, 2:00 PM két nap.

Vegye figyelembe, hogy az első végrehajtása változatlan marad e **startTime** 2015-04-05 14:00 vagy 2015-04-01 14:00\. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Hogy 2015-04-11, 2:00 PM, majd 2015-04-13 2:00 PM, majd 2015-04-15, 2:00 PM kell, és így tovább.

Végül, ha a feladat rendelkezik egy ütemezést, órákban és percekben nincsenek beállítva az ütemezésben, az értékek alapértelmezett órában és percben az első végrehajtásának, illetve ha.

## <a name="deep-dive-schedule"></a>Részletes bemutatója: ütemezése
Használhat **ütemezés** való *korlát* feladat végrehajtások száma. Például, ha a feladatot egy **gyakoriság** "hónap" ütemezés szerint csak a 31 nap futtatja, akkor a feladat fut, csak egy harminc-első napon hónapokban.

Is **ütemezés** való *bontsa ki a* feladat végrehajtások száma. Például, ha a feladatot egy **gyakoriság** "hónap" egy ütemezést, amely a hónap napjain 1 és 2 fut, akkor a feladat futtatása helyett csak egyszer a hónap első és második napjain egy hónap.

Ha több ütemezést elemet ad meg, a kiértékelés sorrendjét van legnagyobbaktól a legkisebbekig: hetek száma, hónap és nap, hét nap, óra és perc.

A következő táblázat részletesen ismerteti a schedule elemeit:

| JSON-név | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **minutes** |A feladat futtatása az óra perc. |Egy számokból álló tömb. |
| **hours** |A feladat futtatása napot. |Egy számokból álló tömb. |
| **weekDays** |A feladat futtatása a hét nap. Csak a heti gyakorisággal adható meg. |Tömb (maximális tömb mérete 7) a következő értékek egyikét sem:<br />-"Hétfő"<br />-"Kedd"<br />-"Szerda"<br />-"Csütörtök"<br />-"Péntek"<br />-"Szombat"<br />-"Vasárnap"<br /><br />Nem kis-és nagybetűket. |
| **monthlyOccurrences** |Meghatározza, hogy mely hónap napjai a feladat futtatásakor. Csak a havi gyakorisággal adható meg. |A tömb **monthlyOccurrences** objektumok:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **nap** a feladat futtatása a hét napja. Például *{vasárnap}* a hónap minden vasárnap. Kötelező.<br /><br />**előfordulás** a napot a hónapban előfordulása. Például *{vasárnap, -1}* a hónap utolsó vasárnap. Választható. |
| **monthDays** |A feladat fut. a hónap napjának. Csak a havi gyakorisággal adható meg. |A következő értékek tömbje:<br />-Bármely értéke < = -1 és > =-31<br />-Bármely érték > = 1 és < = 31|

## <a name="examples-recurrence-schedules"></a>Példák: Ismétlődési ütemezés
Az alábbi példák bemutatják a különböző ismétlődési ütemezés. A példák összpontosítanak az ütemterv objektum és a alelemeiket mutatják be.

Ezeket az ütemezéseket feltételeztük, hogy **időköz** értéke 1\. A példákban is feltételezzük a megfelelő **gyakoriság** értékek az értékek **ütemezés**. Például nem használható egy **gyakoriság** "nap", és egy **monthDays** módosításra **ütemezés**. Azt ismertetik, hogy ezek a korlátozások, a cikk korábbi részében.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` |Futtatás minden nap 5 órakor.<br /><br />Ütemező másolatot minden egyes érték felel meg a "hours" minden érték a "minutes", egyenként, a feladat fut, amelyen az összes idő a lista létrehozásához. |
| `{"minutes":[15], "hours":[5]}` |Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` |Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` |Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` |15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Óránként fut le.<br /><br />Ez a feladat óránként fut. A percet értéke által szabályozott **startTime**, ha van megadva. Ha nincs **startTime** érték van megadva, a perc vezérli a létrehozásának ideje. Például, ha a kezdési ideje vagy létrehozásának idejét (amelyik vonatkozik) du. 12:25, a feladat fut. a 00:25, 01:25, 02:25,..., 23:25.<br /><br />Az ütemezés értéke megegyezik egy feladatot a **gyakoriság** "hour", az egy **időköz** 1, és nem **ütemezés** érték. A különbség az, hogy az ütemezés használható másik **gyakoriság** és **időköz** értékek más feladatok létrehozására. Például ha **gyakoriság** van "honap", az ütemezés futtatása helyett naponta csak egyszer a hónap (Ha **gyakoriság** van "day"). |
| `{minutes:[0]}` |Minden óra kezdetén fut le.<br /><br />Ez a feladat is fut, óránként, de egész (12 óra, Reggel 1, 2 AM és így tovább). Ez megegyezik a feladatot egy **gyakorisága** "hour", a egy **startTime** értéke nulla perc, és nem **ütemezés**, ha a gyakoriság értéke "day". Azonban ha a **gyakoriság** van "hét" vagy "honap", az ütemezés egy hét vagy havi, egy nap csak egy nap végrehajtja a kulcsattribútumokkal. |
| `{"minutes":[15]}` |Minden órában 15 perc futtatni.<br /><br />Kezdő pozíció: 00:15 AM, 1:15 AM, 2:15 AM, óránként fut és így tovább. A 11:15 előtti végződik. |
| `{"hours":[17], "weekDays":["saturday"]}` |Futtassa a szombat délután 5 óra minden héten. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten futtatni hétfőn, szerdán és pénteken délután 5 óra. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten, hétfőn, szerdán és pénteken 5 óra és 5 futtassa. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Futtatás éjfélre (5:15), 5 óra 45 Perckor, 5:15 előtti, és délután 5:45 óra a hétfőn, szerdán és pénteken minden héten. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |15 percenként futtatható létrehozását, Reggel 9 és du. 4:45 között. |
| `{"weekDays":["sunday"]}` |Minden vasárnap futni kezdési időpontban. |
| `{"weekDays":["tuesday", "thursday"]}` |Futtassa a keddi és csütörtöki napokon kezdési időpontban. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Minden hónap erdőtopológia kezelésre-nyolcadik napon 6 órakor futtassa (feltéve, hogy egy **gyakoriság** "hónap"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Futtatás a hónap utolsó napján 6 órakor.<br /><br />Ha azt szeretné, a feladat futtatásához a hónap utolsó napján, használja a -1 nap 28, 29, 30 és 31 helyett. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Minden hónap első és utolsó napján reggel 6 óra futtatni. |
| `{monthDays":[1,-1]}` |Futtatás kezdési időpontban minden hónap első és utolsó napján. |
| `{monthDays":[1,14]}` |Futtatás kezdési időpontban minden hónap első és tizennegyedik napján. |
| `{monthDays":[2]}` |Futtassa a második napot a hónapban kezdési időpontban. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Futtassa a 5 órakor minden hónap első péntekig. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Futtatás kezdési időpontban minden hónap első péntekén. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Futtatás a hónap minden hónapban, kezdési időpontban végén harmadik péntekén. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Az első és utolsó péntekén legyen minden hónap Futtatás kezdési időpontban. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Futtatás kezdési időpontban havonta ötödik péntekén.<br /><br />Ha nincs ötödik péntek hónap, a feladat nem futtatható. Érdemes a-1 érték 5 helyett a előfordulásakor Ha azt szeretné, hogy fusson a feladat az utolsó bekövetkező hónap péntekig. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="see-also"></a>Lásd még

- [A Scheduler ismertetése](scheduler-intro.md)
- [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)
- [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)
- [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)
- [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)
- [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
- [Az Azure Scheduler magas rendelkezésre állás és a megbízhatóság](scheduler-high-availability-reliability.md)
- [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
- [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)

