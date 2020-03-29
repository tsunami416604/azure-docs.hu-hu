---
title: Speciális feladatütemezések és ismétlődések létrehozása
description: Megtudhatja, hogy miként hozhat létre speciális ütemezéseket és ismétlődéseket az Azure Scheduler-ben lévő feladatokhoz
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: b85932bf0d4fd080afadef2bc28d6a218b2d627a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898589"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Speciális ütemezések és ismétlődések létrehozása az Azure Scheduler-ben

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

Egy [Azure Scheduler](../scheduler/scheduler-intro.md) feladaton belül az ütemezés az a mag, amely meghatározza, hogy az ütemező szolgáltatás mikor és hogyan futtatja a feladatot. Az Ütemezővel több egyszeri és ismétlődő ütemezést is beállíthat egy feladathoz. Az egyszeri ütemezések csak egyszer futnak egy adott időpontban, és alapvetően ismétlődő ütemezések, amelyek csak egyszer futnak. Az ismétlődő ütemezések egy megadott gyakorisággal futnak. Ezzel a rugalmassággal az Ütemező különböző üzleti forgatókönyvekhez használható, például:

* **Rendszeresen töröljön adatokat:** Hozzon létre egy napi feladatot, amely törli az összes három hónapnál régebbi tweetet.

* **Archiválási adatok:** Hozzon létre egy havi feladatot, amely leküldéses számlaelőzmények egy biztonsági mentési szolgáltatás.

* **Külső adatok kérése:** Hozzon létre egy feladatot, amely 15 percenként fut, és új időjárás-jelentést kér a NOAA-tól.

* **Képek feldolgozása:** Hozzon létre egy hétköznap munkát, amely csúcsidőn kívül fut, és felhőalapú számítástechnikát használ a nap folyamán feltöltött képek tömörítéséhez.

Ez a cikk ismerteti a scheduler és az [Azure Scheduler REST API](/rest/api/scheduler)használatával létrehozható példafeladatokat, és tartalmazza az egyes ütemezések JavaScript-objektumnotúcés (JSON) definícióját. 

## <a name="supported-scenarios"></a>Támogatott esetek

Ezek a példák az Azure Scheduler által támogatott forgatókönyvek tartományát mutatják be, és például a különböző viselkedésminták ütemezésének létrehozásáról:

* Futtatás egyszer egy adott napon és időpontban.
* Meghatározott számú futtatása és ismétlődése.
* Fuss azonnal, és ismétlődik.
* Fusson és ismétlődjen *n* percenként, órában, naponként, hétenként vagy hónapban, egy adott időponttól kezdve.
* Fusson és ismétlődjenek hetente vagy havonta, de csak a hét meghatározott napjain vagy a hónap adott napjain.
* Futtassa és ismétlődhet meg egynél többször egy adott időszakban. Például minden hónapban az utolsó pénteken és hétfőn, vagy naponta 5:15-kor és 17:15-kor.

Ez a cikk később részletesebben ismerteti ezeket a forgatókönyveket.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Ütemezés létrehozása rest api-val

Ha alapütemezést szeretne létrehozni az [Azure Scheduler REST API-val,](/rest/api/scheduler)kövesse az alábbi lépéseket:

1. Regisztrálja Azure-előfizetését egy erőforrás-szolgáltatónál a [Register művelet - Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/providers)használatával. Az Azure Scheduler szolgáltatás szolgáltatóneve a **Microsoft.Scheduler**. 

1. Hozzon létre egy feladatgyűjteményt a Létrehozás vagy frissítés művelet használatával a [Feladatgyűjtemények számára](https://docs.microsoft.com/rest/api/scheduler/jobcollections) a Scheduler REST API-ban. 

1. Hozzon létre egy feladatot a [feladatok létrehozása vagy frissítése művelettel.](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate) 

## <a name="job-schema-elements"></a>Feladatséma elemei

Ez a táblázat magas szintű áttekintést nyújt a főbb JSON-elemekről, amelyeket az ismétlődések és a feladatok ütemezésének beállításakor használhat. 

| Elem | Kötelező | Leírás | 
|---------|----------|-------------|
| **startTime** | Nem | ISO [8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601) megadott DateTime karakterlánc-érték, amely meghatározza, hogy a feladat mikor indul el először egy alapütemezésben. <p>Összetett ütemezések esetén a feladat legkorábban **elindul.** | 
| **recurrence** | Nem | A feladat futtatásakor vonatkozó ismétlődési szabályok. Az **ismétlődési** objektum támogatja a következő elemeket: **gyakoriság**, **intervallum**, **ütemezés**, **darabszám**és **endTime**. <p>Ha az **ismétlődési** elemet használja, akkor a **gyakorisági** elemet is használnia kell, míg más **ismétlődési** elemek nem kötelezőek. |
| **frequency** | Igen, **ismétlődés** használataesetén | Az előfordulások közötti időegység, és támogatja ezeket az értékeket: "Perc", "Óra", "Nap", "Hét", "Hónap" és "Év" | 
| **interval** | Nem | Pozitív egész szám, amely a **gyakoriság**alapján határozza meg az események közötti időegységek számát. <p>Ha például az **intervallum** 10, **a gyakoriságpedig** "Hét", a feladat 10 hetente ismétlődik. <p>Itt van a legtöbb időközönként minden frekvencián: <p>- 18 hónap <br>- 78 hét <br>- 548 nap <br>- Órákés percek esetén a tartomány 1 <= <*időköz*> <= 1000. | 
| **Ütemezése** | Nem | Az ismétlődés módosításait a megadott percjelek, órajelek, a hét napjai és a hónap napjai alapján határozza meg. | 
| **Számít** | Nem | Pozitív egész szám, amely megadja, hogy a feladat hányszor fut a befejezés előtt. <p>Ha például egy napi **feladat száma** 7-re van állítva, és a kezdő dátum hétfő, a feladat vasárnap fejeződik be. Ha a kezdési dátum már lejárt, az első futtatás a létrehozás iódéből kerül kiszámításra. <p>**EndTime** vagy **count**nélkül a feladat végtelenül fut. Nem használhatja a **count** és **a endTime** ugyanabban a feladatban, de a szabály, amely befejezi az első tiszteletben tartják. | 
| **endTime** | Nem | ISO [8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601) megadott Dátum vagy DateTime karakterlánc érték, amely meghatározza, hogy a feladat mikor fut. Beállíthat egy értéket a múltban lévő **endTime** értékhez. <p>**EndTime** vagy **count**nélkül a feladat végtelenül fut. Nem használhatja a **count** és **a endTime** ugyanabban a feladatban, de a szabály, amely befejezi az első tiszteletben tartják. |
|||| 

Ez a JSON-séma például egy feladat alapvető ütemezését és ismétlődését írja le: 

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

*Dátumok és DateTime értékek*

* Az Ütemező feladatok ban lévő dátumok csak a dátumot tartalmazzák, és követik az [ISO 8601 specifikációt.](https://en.wikipedia.org/wiki/ISO_8601)

* Az ütemezőfeladatok dátum-időpontjai tartalmazzák a dátumot és az időt, az [ISO 8601 specifikációt](https://en.wikipedia.org/wiki/ISO_8601)követik, és UTC-nek tekintendők, ha nincs megadva UTC-eltolás. 

További információ: [Fogalmak, terminológia és entitások.](../scheduler/scheduler-concepts-terms.md)

<a name="start-time"></a>

## <a name="details-starttime"></a>Részletek: startTime

Ez a táblázat azt ismerteti, hogy a **startTime** hogyan szabályozza a feladat futási módját:

| startTime | Nincs ismétlődés | Ismétlődés, nincs ütemezés | Ismétlődés ütemezéssel |
|-----------|---------------|-------------------------|--------------------------|
| **Nincs kezdési időpont** | Fusson azonnal. | Fusson azonnal. Futtassa a későbbi végrehajtásokat az utolsó végrehajtási időpontból számítva. | Fusson azonnal. Későbbi végrehajtások futtatása ismétlődési ütemezés alapján. | 
| **Kezdési időpont a múltban** | Fusson azonnal. | Számítsa ki az első jövőbeli futási időt a kezdési időpont után, és fusson az adott időpontban. <p>Futtassa a későbbi végrehajtásokat az utolsó végrehajtási időpontból számítva. <p>Tekintse meg a táblázat utáni példát. | A feladat *indítása legkorábban* a megadott kezdési időpontban. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul. <p>Későbbi végrehajtások futtatása ismétlődési ütemezés alapján. | 
| **Kezdési időpont a jövőben vagy az aktuális időpontban** | Futtassa egyszer a megadott kezdési időpontban. | Futtassa egyszer a megadott kezdési időpontban. <p>Futtassa a későbbi végrehajtásokat az utolsó végrehajtási időpontból számítva. | A feladat *indítása legkorábban* a megadott kezdési időpontban. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul. <p>Későbbi végrehajtások futtatása ismétlődési ütemezés alapján. |
||||| 

Tegyük fel, hogy ez a példa a következő feltételekkel: a kezdési időpont a múltban ismétlődés, de nincs ütemezés.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Az aktuális dátum és idő 2015.

* A kezdési dátum és időpont 2015.

* Az ismétlődés kétnaponta.

1. Ilyen körülmények között az első végrehajtás 2015. 

   Az ütemező a kezdési időpont alapján számítja ki a végrehajtási előfordulásokat, elveti a múltban lévő példányokat, és a jövőben a következő példányt használja. 
   Ebben az esetben a **startTime** 2015.

   Az első végrehajtás ugyanaz, hogy **a startTime** 2015-04-05 14:00 vagy 2015-04-01 14:00. Az első végrehajtás után a későbbi végrehajtások az ütemezés alapján kerülnek kiszámításra. 
   
1. A kivégzések ezután a következő sorrendben következnek: 
   
   1. 2015-04-11 at 14:00
   1. 2015-04-13 at 14:00 
   1. 2015-04-15 at 14:00
   1. És így tovább...

1. Végül, ha egy feladat ütemezése, de nincs megadva óra és perc, ezek az értékek alapértelmezés szerint az óra és perc az első végrehajtás, illetve.

<a name="schedule"></a>

## <a name="details-schedule"></a>Részletek: menetrend

Az **ütemezés** segítségével *korlátozhatja* a feladat-végrehajtások számát. Ha például egy "hónap" **gyakorisággal** rendelkező feladat ütemezése csak a 31.

Az **ütemezés** segítségével *kibővítheti* a feladat-végrehajtások számát. Ha például egy "hónap" **gyakorisággal** rendelkező feladat ütemezése az 1.

Ha egynél több ütemezési elemet ad meg, a kiértékelés sorrendje a legnagyobbtól a legkisebbig: hétszám, hónapnap, hétköznap, óra és perc.

A következő táblázat részletesen ismerteti a schedule elemeit:

| JSON-név | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **minutes** |A feladat futásának órájának percei. |Egész számok bólika. |
| **hours** |A feladat futásának napi munkaideje. |Egész számok bólika. |
| **weekDays** |A hét napjai, amikor a feladat fut. Csak heti gyakorisággal adható meg. |Az alábbi értékek bármelyikének tömbje (a tömb maximális mérete 7):<br />- "Hétfő"<br />- "Kedd"<br />- "Szerda"<br />- "Csütörtök"<br />- "Péntek"<br />- "Szombat"<br />- "Vasárnap"<br /><br />Nem a kis- és nagybetűket. |
| **monthlyOccurrences** |Azt határozza meg, hogy a feladat mely napjait futja. Csak havi gyakorisággal adható meg. |**Havi előfordulási** objektumok tömbje:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **nap** a hét napja a feladat fut. A *(z) {Sunday}* például a hónap minden vasárnapja. Kötelező.<br /><br />**előfordulása** a nap előfordulása a hónap során. *A(z) {Sunday, -1}* például a hónap utolsó vasárnapja. Választható. |
| **monthDays** |A hónap napja, amikor a feladat fut. Csak havi gyakorisággal adható meg. |A következő értékek tömbje:<br />– Bármilyen érték -1 és -31 között<br />– Bármilyen érték 1 és 31 között|

## <a name="examples-recurrence-schedules"></a>Példák: Ismétlődési ütemezések

A következő példák különböző ismétlődési ütemezéseket mutatnak be. A példák az ütemezési objektumra és annak alelemeire összpontosítanak.

Ezek az ütemezések feltételezik, hogy az **időköz** 1-re van állítva\. A példák az ütemezésben lévő értékek helyes **gyakorisági** értékeit is **feltételezik.** Például nem használhatja a "nap" **gyakoriságát,** és **haviNapok** módosítással rendelkezik az **ütemezésben.** Ezeket a korlátozásokat a cikk korábbi szakaszában ismertetjük.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` |Fuss on 05:00 minden nap.<br /><br />Az ütemező minden értéket "órában" egyeztet, minden egyes értékkel "percben", egyenként, hogy listát hozzon létre a feladat futásának minden időpontja között. |
| `{"minutes":[15], "hours":[5]}` |Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` |Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` |Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` |15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Óránként fut le.<br /><br />Ez a feladat óránként fut. A perc értékét a **startTime**értéke szabályozza, ha meg van adva. Ha nincs **megadva startTime** érték, a percet a létrehozási idő szabályozza. Ha például a kezdési időpont vagy a létrehozás időpontja (amelyik érvényes) 12:25, a feladat 00:25, 01:25, 02:25, ..., 23:25 órakor fut.<br /><br />Az ütemezés megegyezik az "óra" **gyakorisággal,** az **1-es időközzel** és az **ütemezési** értékkel nem rendelkező feladattal. A különbség az, hogy ezt az ütemezést különböző **gyakorisági** és **intervallumértékekkel** használhatja más feladatok létrehozásához. Ha például a **gyakoriság** "hónap", az ütemezés csak havonta egyszer fut, nem pedig minden nap (ha a **gyakoriság** "nap"). |
| `{minutes:[0]}` |Minden óra kezdetén fut le.<br /><br />Ez a feladat is fut óránként, de az óra (12:00, 1:00, 2:00, és így tovább). Ez az ütemezés megegyezik az "óra" **gyakorisággal** rendelkező feladatokkal, a **startTime** nulla perces értékével, és nincs **ütemezés,** ha a gyakoriság "nap". Ha azonban a **gyakoriság** "hét" vagy "hónap", az ütemezés csak heti vagy egy napot hajt végre. |
| `{"minutes":[15]}` |Fuss 15 perccel az óra után minden órában.<br /><br />Óránként fut, 00:15-től, 1:15-től, 2:15-től és így tovább. 23:15-kor ér véget. |
| `{"hours":[17], "weekDays":["saturday"]}` |Fuss on 5:00 szombaton minden héten. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Fuss 17:00-kor hétfőn, szerdán és pénteken minden héten. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken 5:00-kor és 17:00-kor fut. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Fuss on 5:15, 5:45, 5:15, és 5:45 hétfőn, szerdán és pénteken minden héten. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Hétköznap 9:00 és 16:45 között 15 percenként fusson. |
| `{"weekDays":["sunday"]}` |Fuss vasárnap a kezdési időpontban. |
| `{"weekDays":["tuesday", "thursday"]}` |Fuss kedden és csütörtökön a kezdési időpontban. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Minden hónap 28-án reggel 6-kor fusson (feltételezve a **"hónap" gyakoriságát).** |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Fuss on 6 am a hónap utolsó napján.<br /><br />Ha egy feladatot a hónap utolsó napján szeretne futtatni, használja a -1-et a 28., 29., 30. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Fuss on 06:00 az első és az utolsó nap minden hónapban. |
| `{monthDays":[1,-1]}` |Fuss minden hónap első és utolsó napján a kezdési időpontban. |
| `{monthDays":[1,14]}` |Fuss minden hónap első és tizennegyedik napján a kezdési időpontban. |
| `{monthDays":[2]}` |Futtatás a hónap második napján a kezdési időpontban. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Fuss minden hónap első péntekén 05:00 órakor. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Minden hónap első péntekén futanak a kezdéskor. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Fuss a hónap végétől a harmadik pénteken, minden hónapban, a kezdési időpontban. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Fuss minden hónap első és utolsó péntekén a kezdéskor. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Fuss minden hónap ötödik péntekén a kezdéskor.<br /><br />Ha egy hónapban nincs ötödik péntek, a feladat nem működik. Érdemes lehet a -1 helyett 5 az esemény, ha szeretné futtatni a feladatot az utolsó előforduló péntekén a hónap. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)