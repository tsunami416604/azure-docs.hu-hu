---
title: Speciális feladatok ütemezését és ismétlődések – Azure Scheduler szolgáltatásával
description: Speciális ütemezések és ismétlődések-feladatok létrehozása az Azure Schedulerben
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: f5a8b929cf5af6e4e43c6003e6b622d04a50b93e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980940"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Az Azure Scheduler-feladatok az ismétlődések és a speciális ütemezések létrehozása

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) kivezetjük, az Azure Scheduler lecseréli. Feladatok ütemezése [helyette próbálkozzon az Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Belül egy [Azure Scheduler](../scheduler/scheduler-intro.md) feladat, az ütemezés a következő alapvető fontosságú, amely meghatározza, mikor és hogyan a Scheduler szolgáltatás fut-e a feladatot. A Scheduler állíthat be egy feladat több egyszeri és ismétlődő ütemezéseket. Egyszeri ütemezés futtatása csak egyszer, egy megadott időpontban, és alapvetően ismétlődő ütemezések csak egyszer futtatott. A megadott gyakorisággal ismétlődő ütemezések szerint futtatni. Az ezt a rugalmasságot is használhat Scheduler különböző üzleti forgatókönyvekben, például:

* **Távolítsa el az adatokat rendszeresen**: hozzon létre egy napi feladatot, amely törli a három hónapnál régebbi összes tweeteket.

* **Adatok archiválása**: hozzon létre, hogy a leküldés előzményeit, hogy egy biztonsági mentési szolgáltatás számlázása havi feladat.

* **Külső adatok kérése**: hozzon létre egy feladatot, amely 15 percenként fut le, és a NOAA időjárási új jelentés lekéri.

* **Dolgozhassa**: hozzon létre egy hét napja feladatot, amikor kevesen fut, és a felhő-számítástechnika a tömörítés során a naponta feltöltött képek.

Ez a cikk azt ismerteti, például feladatokat a Scheduler használatával hozhat létre és és a [Azure Scheduler REST API](https://docs.microsoft.com/rest/api/schedule), és minden egyes ütemezés JavaScript Object Notation (JSON) definícióját tartalmazza. 

## <a name="supported-scenarios"></a>Támogatott esetek

A példákból látható, amely támogatja az Azure Scheduler forgatókönyvek és ütemezése a különböző viselkedési mintákat, például létrehozása:

* Egy adott dátummal és időponttal, futtassa egyszer.
* Futtassa, és a egy adott számú ismétlődés.
* Azonnal futtatni, és az ismétlődés.
* Futtassa, és az Ismétlődés minden *n* perc, óra, nap, hét vagy egy adott időpontban hónapig.
* Futtassa, és hetente vagy havonta, de csak a hét meghatározott napjain vagy a hónap meghatározott napjain ismétlődés.
* Futtassa, és a egy adott időszakra vonatkozó többször ismétlődés. Ha például minden hónap utolsó péntekjén és hétfő, vagy a napi 05:15-kor és 17:15-kor.

Ez a cikk későbbi ismerteti részletesebben ezeket a forgatókönyveket.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Ütemezés létrehozása a REST API-val

Az alapszintű ütemezés létrehozása a [Azure Scheduler REST API](https://docs.microsoft.com/rest/api/schedule), kövesse az alábbi lépéseket:

1. Azure-előfizetés regisztrálása az erőforrás-szolgáltató használatával a [művelet – Resource Manager REST API regisztrálása](https://docs.microsoft.com/rest/api/resources/providers#Providers_Register). A szolgáltató neve, az Azure Scheduler szolgáltatás **Microsoft.Scheduler**. 

1. Feladatgyűjtemények létrehozása használatával a [létrehozási vagy frissítési műveletben a feladatgyűjtemények](https://docs.microsoft.com/rest/api/scheduler/jobcollections#JobCollections_CreateOrUpdate) a Scheduler REST API-ban. 

1. Hozzon létre egy feladatot a használatával a [létrehozási vagy frissítési műveletben feladatok](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Feladat séma elemei

Ez a táblázat magas szintű áttekintést nyújt a fő JSON-elemek ismétlődések és a feladatok ütemezésének beállításakor használható. 

| Elem | Szükséges | Leírás | 
|---------|----------|-------------|
| **startTime** | Nem | A dátum/idő karakterlánc-érték [ISO 8601 formátumú](http://en.wikipedia.org/wiki/ISO_8601) , amely meghatározza a feladat első indításakor az alapszintű ütemezés szerint. <p>Komplex ütemezések a feladat nem indul korábban, mint **startTime**. | 
| **recurrence** | Nem | A feladat futtatásakor tartozó ismétlődési szabályokat. A **ismétlődési** objektum támogatja ezeket az elemeket: **gyakorisága**, **időköz**, **ütemezés**, **száma**, és **endTime**. <p>Ha használja a **ismétlődési** elemben is használnia kell a **gyakorisága** elem, míg más **ismétlődési** elemek egyike sem kötelező. |
| **frequency** | Igen, használatakor **ismétlődés** | Az időegység előfordulások között, és ezeket az értékeket támogatja: "Perces", "Hour", "Day", "Week", "Month" és "Year" | 
| **interval** | Nem | Határozza meg, hogy időegységek előfordulások közötti pozitív egész szám alapján **gyakorisága**. <p>Például ha **időköz** 10 és **gyakorisága** pedig "Week", a feladat 10 hetente ismétlődik. <p>Az alábbiakban időközönként minden gyakoriságának maximális száma: <p>– 18 hónap <br>-78 hét <br>-548 nap <br>– A óra és perc, a tartomány: 1 < = <*időköz*>< = 1000. | 
| **schedule** | Nem | Határozza meg a módosítások a megadott perc-jelek, óra-jelek, a hét azon napjai, és a hónap napjain alapuló az ismétlődés | 
| **Száma** | Nem | Pozitív egész szám, amely meghatározza, hogy a feladat futtatása a befejezés előtt hányszor. <p>Például, ha napi feladat, **száma** 7 beállítása, és a kezdő dátum hétfő, a feladat futásának vasárnap. Ha már elhagyta a kezdő dátum, az első futtatásakor kiszámítása a létrehozás ideje. <p>Nélkül **endTime** vagy **száma**, a feladat korlátlanul futtatása. Nem használhatja mindkettő **száma** és **endTime** ugyanazt a feladatot, de a szabályt, hogy befejezi először van figyelembe véve. | 
| **endTime** | Nem | Egy dátum vagy dátum/idő karakterlánc értéket [ISO 8601 formátumú](http://en.wikipedia.org/wiki/ISO_8601) , amely meghatározza, amikor a feladat leáll futtatása. Beállíthatja a egy értéke **endTime** , amely a múltban van. <p>Nélkül **endTime** vagy **száma**, a feladat korlátlanul futtatása. Nem használhatja mindkettő **száma** és **endTime** ugyanazt a feladatot, de a szabályt, hogy befejezi először van figyelembe véve. |
|||| 

A JSON-sémájában például egy egyszerű ütemezés és a egy feladat ismétlődése ismerteti: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Dátum- és dátum/idő értékek*

* A Scheduler-feladatok dátumok csak a dátum, és kövesse a [ISO 8601-specifikáció](http://en.wikipedia.org/wiki/ISO_8601).

* Dátum-idő a Scheduler-feladatok közé tartozik a dátum- és, hajtsa végre a [ISO 8601-specifikáció](http://en.wikipedia.org/wiki/ISO_8601), és adottnak tekintik (UTC), ha nem az UTC-eltérés van megadva. 

További információkért lásd: [alapfogalmai, terminológiája és entitásai](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Részletek: startTime

Ez a táblázat azt ismerteti, hogyan **startTime** befolyásolja a feladat fut:

| startTime | Nincs ismétlődés. | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|-----------|---------------|-------------------------|--------------------------|
| **A Kezdés időpontja** | Ha azonnal futtatni. | Ha azonnal futtatni. Futtassa a következő végrehajtási időpontokat az utolsó végrehajtási időpont alapján számítja ki. | Ha azonnal futtatni. Futtassa az azt követő végrehajtások az ismétlődési ütemezés alapján. | 
| **Múltbeli kezdési időpontja** | Ha azonnal futtatni. | Kiszámíthatja az első jövőbeli futtatáskor a kezdési idő után, és jelenleg futtatható. <p>Futtassa a következő végrehajtási időpontokat az utolsó végrehajtási időpont alapján számítja ki. <p>Tekintse meg a példát a táblázat után. | Feladat indítása *nem indulhat hamarabb* a megadott kezdési időpontban. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul. <p>Futtassa az azt követő végrehajtások az ismétlődési ütemezés alapján. | 
| **Kezdő időpont jövőbeli, illetve az aktuális időpont** | Egyszer, a megadott kezdési időpontban fut le. | Egyszer, a megadott kezdési időpontban fut le. <p>Futtassa a következő végrehajtási időpontokat az utolsó végrehajtási időpont alapján számítja ki. | Feladat indítása *nem indulhat hamarabb* a megadott kezdési időpontban. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul. <p>Futtassa az azt követő végrehajtások az ismétlődési ütemezés alapján. |
||||| 

Tegyük fel, hogy ebben a példában az alábbi feltételek: egy kezdő dátuma a múltban ismétlődéssel, de ütemezés nélkül.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Az aktuális dátum és idő "2015-04-08 13:00".

* A kezdő dátum és idő "2015-04-07 14:00", azaz az aktuális dátum és idő előtt.

* Az ismétlődés kétnaponta van.

1. Ezen feltételek mellett az első végrehajtási időpont a 2015-04-09 14:00-kor. 

   A Scheduler a végrehajtási alkalmakat, a kezdési időpont alapján példányok elveti a múltban, és a jövőben használja a következő alkalom számítja ki. 
   Ebben az esetben **startTime** jelenleg a 2015-04-07 2:00 Órakor, így a következő alkalom két napra esik ettől, amely 2015-04-09: 2:00-kor.

   Az első végrehajtási időpont megegyezik-e **startTime** 2015-04-05 14:00 vagy 2015-04-01 14:00. Az első végrehajtás után az azt követő végrehajtások kiszámítása alapján az ütemezést. 
   
1. A végrehajtás majd kövesse az itt látható sorrendben: 
   
   1. 2015-04-11: 2:00-kor
   1. 2015-04-13: 2:00-kor 
   1. 2015-04-15: 2:00-kor
   1. és így tovább...

1. Végül ha egy feladat ütemezés szerint, de nem rendelkezik megadott óra és perc, óra és perc az első végrehajtás ezen értékek alapértelmezett jelölik.

<a name="schedule"></a>

## <a name="details-schedule"></a>Részletek: ütemezés

Használhat **ütemezés** való *korlát* a feladat végrehajtásainak számát. Például, ha a feladat egy **gyakorisága** "Month" csak a 31. napon futó ütemezés van, a feladat csak a harmincegy napos hónapokban futtatható.

Is **ütemezés** való *bontsa ki a* a feladat végrehajtásainak számát. Például, ha a feladat egy **gyakorisága** "Month" 1. és 2 futó ütemezés van, a feladat futtatása helyett csak egyszer a hónap első és második napjain egy hónapban.

Ha több ütemezési elem adja meg, a kiértékelési sorrend a legnagyobb van-e a legkisebb: hét száma, hónap napja, hét napja, óra és perc.

A következő táblázat részletesen ismerteti a schedule elemeit:

| JSON-név | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **minutes** |A feladat futtatása óra perc. |Egész számok tömbje. |
| **hours** |A feladat futtatása napot. |Egész számok tömbje. |
| **weekDays** |A feladat futtatása a hét napjait. Csak heti gyakorisággal adható meg. |(A tömb maximális mérete 7) a következő értékek bármelyikének tömbje:<br />– "Hétfő"<br />– "Kedd"<br />– "Szerda"<br />– "Thursday"<br />– "Péntek"<br />– "Saturday"<br />– "Sunday értékkel"<br /><br />Nem kis-és nagybetűket. |
| **monthlyOccurrences** |Meghatározza, hogy a hónap mely napjain a feladat futtatásakor. Csak havi gyakoriság mellett adható meg. |Egy tömbjét **monthlyOccurrences** objektumok:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **nap** a feladat futtatása a hét napja. Ha például *{vasárnap}* a hónap minden vasárnapja. Kötelező.<br /><br />**előfordulás** előfordulása a hónapban a nap. Ha például *{vasárnap, a -1}* a hónap utolsó vasárnapja. Választható. |
| **monthDays** |A feladat futtatása a hónap napját. Csak havi gyakoriság mellett adható meg. |Egy tömb, a következő értékeket:<br />– Bármilyen érték -1 és -31 között<br />– Bármilyen érték 1 és 31 között|

## <a name="examples-recurrence-schedules"></a>Példák: Ismétlődésütemezések

Az alábbi példák bemutatják a különböző ismétlődésütemezésekre. A példák az ütemezési objektumra és annak alelemeire koncentrálhat.

Ezeket az ütemezéseket feltételeztük, hogy **időköz** értéke 1\. A példák is feltételezik a megfelelő **gyakorisága** értékek az értékek **ütemezés**. Például nem használható egy **gyakorisága** "Day", és egy **monthDays** módosítás **ütemezés**. Ezek a korlátozások a cikk korábbi részében ismertetünk.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` |Minden nap 05-kor fut.<br /><br />A Scheduler minden értéket felel meg az "hours", "minutes" szereplő összes értékhez, egyenként, hozzon létre egy listát, ahol minden alkalommal a feladat futtatása. |
| `{"minutes":[15], "hours":[5]}` |Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` |Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` |Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` |15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Óránként fut le.<br /><br />Ez a feladat óránként fut le. A perc értéke által szabályozott **startTime**, ha a célgyűjtemény meg van adva. Ha nincs **startTime** érték van megadva, a percnek a létrehozás ideje szabályozza. Például ha a Kezdés időpontja vagy a Létrehozás időpontja (amelyik alkalmazható) 12:25-kor, a feladat fut, 00:25-kor, 01:25-kor, 02:25-kor,..., 23:25-kor.<br /><br />Az ütemezés megegyezik egy feladathoz egy **gyakorisága** "Hour", egy **időköz** az 1- es nem **ütemezés** értéket. A különbség az, hogy ez az ütemezés használható különböző **gyakorisága** és **időköz** értékeket más feladatok létrehozásához. Például ha **gyakorisága** van "month", az ütemezés fut, nem naponta egy hónapban csak egyszer (Ha **gyakorisága** "Day"). |
| `{minutes:[0]}` |Minden óra kezdetén fut le.<br /><br />Ez a feladat szintén óránként fut az adott óra kezdetén (12 AM, 1 AM, 2 AM és így tovább). Ez az egy feladathoz egyenértékű egy **gyakorisága** "Hour", egy **startTime** értéke nulla perc, és nincs **ütemezés**, ha a gyakoriság "day". Azonban ha a **gyakorisága** van "week" vagy "month", az ütemezés egyszer fut csak egy héten egyszer vagy egy hónapban, illetve. |
| `{"minutes":[15]}` |Minden óra 15 perc, futtassa.<br /><br />00:15-kor, 1:15-kor, 2:15-kor, díjtól óránként fut és így tovább. Este 11:15-kor ér véget. |
| `{"hours":[17], "weekDays":["saturday"]}` |Minden héten szombaton 17: 00-kor fut. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken 17: 00-kor fut. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |05-kor és 17: 00, hétfőn, szerdán és pénteken futtatása minden héten. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Futtatás: 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor hétfőn, szerdán és pénteken, minden héten. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Hétköznapokon 15 percenként, 9: 00 és 16:45 között. |
| `{"weekDays":["sunday"]}` |Vasárnap futtassa a megadott kezdési időpontban. |
| `{"weekDays":["tuesday", "thursday"]}` |Keddenként és csütörtökönként futtassa a megadott kezdési időpontban. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Minden hónap huszonnyolcadik napján 6-kor fut (feltéve, hogy egy **gyakorisága** "Month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |A hónap utolsó napján 6: 00-kor fut le.<br /><br />Ha azt szeretné, a feladat futtatásához a hónap utolsó napján, -1 nap helyett használjon 28, 29, 30 és 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Minden hónap első és utolsó napján 6: 00-kor fut le. |
| `{monthDays":[1,-1]}` |Futtassa a megadott kezdési időpontban minden hónap első és utolsó napján. |
| `{monthDays":[1,14]}` |Futtassa a megadott kezdési időpontban minden hónap első és tizennegyedik nap. |
| `{monthDays":[2]}` |A második nap a hónapban megadott kezdési időpontban fut. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Futtassa az első péntekjén, 05-kor, havonta. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Futtassa a megadott kezdési időpontban minden hónap első péntekjén. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |A harmadik pénteken, a hónap, minden hónapban, a megadott kezdési időpontban fut. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Első és utolsó péntekjén, havonta, futtassa a megadott kezdési időpontban. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Futtassa a megadott kezdési időpontban minden hónap ötödik péntek.<br /><br />Ha az adott hónapban nincs ötödik péntek, akkor a feladat nem fut le. Érdemes a -1 5 helyett inkább az előfordulás esetében ha szeretné futtatni a feladatot az utolsó bekövetkező péntekjén, a hónap. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)