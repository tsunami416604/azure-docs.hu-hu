---
title: Az Azure Logic Apps automatizált feladatok és a munkafolyamatok futtatásához és ütemezéséhez |} A Microsoft Docs
description: Az Azure Logic Appsben az ismétlődési összekötővel ütemezett és ismétlődő feladatok automatizálása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
tags: connectors
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: eb22539d1f433e396935f82e4cb3786d5699d21a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083954"
---
# <a name="create-and-run-recurring-tasks-and-workflows-with-azure-logic-apps"></a>Hozzon létre és ismétlődő feladatok és a munkafolyamatok futtatásához az Azure Logic Apps

Műveletek, a számítási feladatok vagy a rendszeresen futó folyamatok ütemezése, hozzon létre egy logikai alkalmazás munkafolyamatának kezdődik a **ütemezés – ismétlődés** [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts). Dátum és idő indítása a munkafolyamat és a egy ismétlődési ütemezést, mint az ezekben a példákban a feladatok végrehajtásához állíthatja be:

* Belső adatok lekérése: [SQL tárolt eljárás futtatása](../connectors/connectors-create-api-sqlazure.md) minden nap.
* Külső adatok beolvasása: Lekérés időjárás-előrejelzéseket kaphat a NOAA 15 percenként.
* Jelentés adatai: Az elmúlt héten az e-mail-nagyobb, mint egy adott mennyiség megrendeléseket összegzését.
* Dolgozza fel az adatokat: A mai feltöltött képek minden csúcsidőn weekday tömörítése.
* Adatok törlése: Törölje a három hónapnál régebbi összes tweeteket.
* Archivált adatok: Küldje le számlák egy biztonsági mentési szolgáltatás minden hónapban.

Az eseményindító támogatja az sok mintát, például:

* Azonnal futtatni, és ismételje meg minden *n* másodperc, perc, óra, nap, hét vagy hónapok száma.
* Egy adott időpontban el, majd futtassa, és ismételje meg minden *n* másodperc, perc, óra, nap, hét vagy hónapok száma.
* Futtassa, és ismételje meg egy vagy több alkalommal minden nap, például: 8:00-kor és 17:00-kor.
* Futtassa, és ismételje meg minden héten, de csak az adott napon, például a hétvégén.
* Futtassa, és ismételje meg minden héten, de csak az adott napokon és időpontokban, például a munkanapokon 8:00-kor és 17:00-kor.

Minden alkalommal, amikor az ismétlődési eseményindító indulásakor a Logic Apps hoz létre, és a egy új példányát a logikai alkalmazás munkafolyamatának futtatása. 

A logikai alkalmazás elindításához és a jövőben csak egyetlen alkalommal fussanak: [Futtatás feladat csak egyszer](#run-once) jelen témakör későbbi részében.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Adjon hozzá egy ismétlődési eseményindítót a logikai alkalmazáshoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Hozzon létre egy üres logikai alkalmazás, vagy további [üres logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Miután a Logic Apps Designerben jelenik meg, a keresőmező alatt válassza ki **összes**. A keresőmezőbe írja be a "recurrence" szűrőként. Az eseményindítók listában jelölje ki az eseményindító: **Ismétlődés - ütemezés** 

   !["Ismétlődési – ütemezés" trigger kiválasztása](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Ez az eseményindító már az első lépés a logikai alkalmazásban.

3. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában állítsa be ezeket a tulajdonságokat a munkafolyamatot hetente futtatni. 

   ![Set időközét és gyakoriságát](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. További ütemezési beállításokat, válassza a **speciális beállítások megjelenítése**. 

   ![További beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Mostantól ezek a beállítások adhatók meg: 

   * Állítsa be a kezdő dátum és idő aktiválja az eseményindítót. 
   Ha megad egy kezdési dátumot és időpontot, időzónát is alkalmazhatók. 

   * Ha a gyakoriság "Week" vagy a "Day" lehetőséget választja, kiválaszthatja az ismétlődés megadott időpontok. 

   * Ha a "Week" lehetőséget választja, a hét meghatározott napjain túl is kiválaszthatja.
   
   ![Speciális ütemezési beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Tegyük fel például, hogy a mai dátum 2017. szeptember 4., hétfő. 
   A következő ismétlődési eseményindító nem indul el *minden korábban* , mint a kezdő dátum és idő, amely 2017. szeptember 18., hétfő jelenleg 8:00 Csendes-ÓCEÁNI. 
   Azonban van beállítva az ismétlődési ütemezés 10:30-kor, 12:30 = 1997031213, és 2:30-kor hétfőjén csak. Ezért az első alkalom, hogy az eseményindító aktiválódik, és létrehoz egy logikaialkalmazás-példányt a munkafolyamat jelenleg 10:30-kor. 
   Hogyan indítása túllépi az munkahelyi kapcsolatos további információkért lásd: ezek [idő példák a start](#start-time).
   12:30 = 1997031213 és 2:30-kor későbbi futtatások ugyanarra a napra fordulhat elő. 
   Minden egyes ismétlődési saját munkafolyamat-példányhoz hoz létre. Ezt követően a teljes ütemezés ismétlődik minden újra következő hétfőn keresztül. 
   [*Mik azok néhány további példa előfordulásait?*](#example-recurrences)

   ![Speciális ütemezési példa](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Az eseményindító a megadott ismétlődés előnézetét jeleníti meg, csak ha "Day" vagy "Week" jelöli meg a gyakoriságot.
   
6. Most már létrehozhatja a fennmaradó munkafolyamatot műveletekhez, vagy vezérlő utasítások folyamatot. Hozzáadhat további műveleteket, lásd: [összekötők](../connectors/apis-list.md). 

## <a name="trigger-details"></a>A trigger részletei

Ezeket a tulajdonságokat az ismétlődési eseményindító konfigurálhatja.

| Name (Név) | Szükséges | Tulajdonság neve | Typo | Leírás | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Gyakoriság** | Igen | frequency | String | Az időegység, az Ismétlődés: **Második**, **perc**, **óra**, **nap**, **hét**, vagy **hónap** | 
| **Intervallum** | Igen | interval | Egész szám | Pozitív egész szám, amely leírja, hogy milyen gyakran a munkafolyamat futtatása gyakorisága alapján. <p>Az alapértelmezett időköz: 1. Az alábbiakban a minimális és maximális időközönként: <p>– Hónap: 1 – 16 hónap </br>– Nap: 1-500 nap </br>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például ha a időköz 6, és a gyakoriság "Month", akkor az ismétlődés nem minden hatodik hónapban. | 
| **Időzóna** | Nem | timeZone | String | Csak amikor Ön megadja a kezdési időt, mert ez az eseményindító nem fogadja el érvényes [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az időzónát, amely a alkalmazni szeretné. | 
| **Kezdési idő** | Nem | startTime | String | Adja meg a kezdési időt a következő formátumban: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha egy időzóna <p>– vagy – <p>ÉÉÉÉ-hh-DDThh:mm:ssZ, ha nem adja meg a időzóna <p>Így például, ha azt szeretné, 2017. szeptember 18., 2:00-kor, majd adja meg "2017-09-18T14:00:00", és válassza ki az időzónát, például a csendes-óceáni idő. Másik lehetőségként adja meg "2017-09-18T14:00:00Z" időzóna nélkül. <p>**Megjegyzés:** Hajtsa végre a kezdő időpont a [ISO 8601 dátum-idő specifikáció](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC idő dátumformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Ne válassza ki az időzónát, ha hozzá kell adnia a levél "Z", a végén szóközök nélkül. A "Z" hivatkozik az azzal egyenértékű [hajózási idő](https://en.wikipedia.org/wiki/Nautical_time). <p>Egyszerű ütemezések esetében a kezdési időpont az első előfordulás a összetett ütemezéseknél az eseményindító nem indul el minden korábban, a kezdési időpontnál. [*Mik azok a módon, hogy a kezdő dátum és idő használható?*](#start-time) | 
| **Ezeken a napokon** | Nem | weekDays | Karakterláncot vagy karakterlánc-tömbben | Ha "Week", ha a munkafolyamatot futtatni kívánt egy vagy több napot választhat: **Hétfő**, **kedd**, **szerda**, **csütörtök**, **péntek**, **szombat**, és **Vasárnap** | 
| **Ezekben az órákban** | Nem | hours | Egész szám vagy az egész számok tömbje | Ha "Day" vagy "Week", választhat egy vagy több egész számok 0 és 23 legyen az óra, a nap, amikor a munkafolyamatot futtatni szeretné. <p>Például ha megadja a "10", "12" és "14", kap, az óra együtt 2 óra, 10 Órakor, 12 PM. | 
| **Ezekben a percekben** | Nem | minutes | Egész szám vagy az egész számok tömbje | Ha "Day" vagy "Week", választhat egy vagy több egész számok 0 és 59 közötti legyen a perc, az óra, ha a munkafolyamatot futtatni szeretné. <p>Például "30" megadhatja a percenkénti be van jelölve, és megjelenik az előző példa a nap, óra, 10:30-kor, 12:30 = 1997031213, és 2:30-kor. | 
||||| 

## <a name="json-example"></a>Példa JSON

Íme egy példa [ismétlődési eseményindító meghatározása](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="faq"></a>GYIK

<a name="run-once"></a>

**K:** Mi történik, ha szeretnék futtatni egy logikai alkalmazást a későbbiekben csak egyszer? </br>
**V:** A logikai alkalmazás elindításához, és futtassa egyszer ismétlődés nélkül, használhatja a **Scheduler: Egyszeri futtatás feladatok** sablont. Új logikai alkalmazás létrehozása után, de a Logic Apps Designerben a megnyitása előtt a **sablonok** szakaszban, az a **kategória** listáról válassza ki **ütemezés**, majd válassza ki a sablon:

![Válassza "a Scheduler: Feladatok futtatása után"sablon](./media/connectors-native-recurrence/choose-run-once-template.png)

Vagy egy üres logikaialkalmazás-sablon használata, indítsa el a logikai alkalmazás a **amikor egy HTTP-kérés érkezik - kérelem** eseményindító. Paraméterként adja át a trigger kezdő időpontja. Adja hozzá a következő lépés a **késleltetés eddig – ütemezése** műveletet, és adja meg az idő, amikor elindul a következő művelet.

<a name="example-recurrences"></a>

**K:** Mik a többi példában ismétlődésütemezésekre? </br>
**V:** Az alábbiakban további példák:

| Ismétlődés | Intervallum | Gyakoriság | Kezdő időpont | E napokon | Órák | Percek | Megjegyzés |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| 15 percenként fut le (nincs kezdő dátum és idő) | 15 | Perc | {nincs} | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés azonnal elindítja, majd a jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. | 
| 15 percenként fut le (a kezdő dátum és idő) | 15 | Perc | *startDate*T*startTime*Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* , mint a megadott kezdő dátum és idő, jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. | 
| Futtassa a minden óra kezdetén (a kezdő dátum és idő) | 1 | Óra | *startDate*Thh:00:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* a megadott kezdési dátuma és időpontja. Jövőbeli ismétlődések minden órában futtatva a "00" minute be van jelölve. <p>Ha a gyakoriság "Week" vagy "Month", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. | 
| Óránként fut le, minden nap (nincs kezdő dátum és idő) | 1 | Óra | {nincs} | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés azonnal elindul, és a jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. <p>Ha a gyakoriság "Week" vagy "Month", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. | 
| Óránként fut le, minden nap (kezdő dátum és idő) | 1 | Óra | *startDate*T*startTime*Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* , mint a megadott kezdő dátum és idő, jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. <p>Ha a gyakoriság "Week" vagy "Month", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. | 
| Futtassa az elmúlt óra 15 percenként, óránként (a kezdő dátum és idő) | 1 | Óra | *startDate*T00:15:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* , mint a megadott kezdő dátum és idő, futtató: 00:15-kor, 1:15-kor, 2:15-kor, és így tovább. | 
| 15 percenként fut le egy óra, minden órában (nincs kezdő dátum és idő) | 1 | Nap | {nincs} | {nem} érhető el | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ez az ütemezés lefuttat 00:15-kor, 1:15-kor, 2:15-kor, és így tovább. Ez az ütemezés is egyenértékűnek gyakoriságot, "Hour" és a egy kezdési időpontot "15" perc. | 
| 15 percenként fut le, a 15 perces mark (nincs kezdő dátum és idő) | 1 | Nap | {nincs} | {nem} érhető el | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés nem indul el, mindaddig, amíg a következő megadott 15 perces be van jelölve. | 
| Futtatás 8:00 órakor (kezdő dátum és idő nem) naponta | 1 | Nap | {nincs} | {nem} érhető el | 8 | {nincs} | Ez az ütemezés 8:00 órakor minden nap, fut, a megadott ütemezés alapján. | 
| Futtatás 8:00 órakor minden nap (kezdő dátum és idő) | 1 | Nap | *startDate*T08:00:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés 8:00-kor minden nap, fut, a megadott kezdési időpont alapján. | 
| Futtatás reggel 8:30-kor minden nap (nincs kezdő dátum és idő) | 1 | Nap | {nincs} | {nem} érhető el | 8 | 30 | Ez az ütemezés futtatja, reggel 8:30-kor minden nap, a megadott ütemezés alapján. | 
| Futtatás reggel 8:30-kor minden nap (kezdő dátum és idő) | 1 | Nap | *startDate*T08:30:00Z | {nem} érhető el | {nincs} | {nincs} | Ez az ütemezés a megadott kezdési időpontban reggel 8:30-kor kezdődik. | 
| 8:30-kor és du. 4:30 naponta futtatása | 1 | Nap | {nincs} | {nem} érhető el | 8, 16 | 30 | | 
| Futtatás reggel 8:30-kor, 8:45-kor, 4:30 = 1997031213 és 4:45-kor minden nap | 1 | Nap | {nincs} | {nem} érhető el | 8, 16 | 30, 45 | | 
| Futtat minden szombat hajnali 17: 00 (nincs kezdő dátum és idő) | 1 | Hét | {nincs} | "Szombat" | 17 | 00 | Az ütemezés futtat minden szombat délután 5:00-kor. | 
| Futtat minden szombat hajnali 17: 00 (a kezdő dátum és idő) | 1 | Hét | *startDate*T17:00:00Z | "Szombat" | {nincs} | {nincs} | Ez az ütemezés nem indul el *minden korábban* a megadott kezdési dátum és idő, ebben az esetben 2017. szeptember 9., 17:00-kor. Jövőbeli ismétlődések futtat minden szombat hajnali 17:00-kor. | 
| Minden kedden, csütörtök, 17: 00 | 1 | Hét | {nincs} | "Kedd", "Thursday" | 17 | {nincs} | Ez az ütemezés futtatása minden kedden és csütörtökön délután 5:00-kor. | 
| Óránként fut le munkaidőben | 1 | Hét | {nincs} | Válassza ki a hétvégén kivételével minden nap. | Adja meg a kívánt nap. | Válassza ki bármelyik kívánt órák perc. | Például ha a munkaórák 8:00-kor, 5:00 Órakor, majd válassza ki "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" szerint az órák, a nap. <p>Ha a munkaórák, 5:30 = 1997031213 8:30-kor, válassza ki az előző órában a napot plusz "30" óra perc. | 
| Naponta egyszer futtatni hétvégén | 1 | Hét | {nincs} | "Szombat", "Sunday értékkel" | Adja meg a kívánt nap. | Válassza ki bármelyik óra perc szükség szerint. | Ez az ütemezés a megadott ütemezés szerint minden szombat és vasárnap futtatja. | 
| 15 percenként fut le Kétheti hétfőn csak | 2 | Hét | {nincs} | "Hétfő" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés lefuttat minden más hétfő minden 15 perces be van jelölve. | 
| Óránként fut le egy nap / hó | 1 | Hónap | {Lásd a megjegyzést} | {nem} érhető el | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Lásd a megjegyzést} | Ha nem adja meg a kezdő dátum és idő, az ütemezés használja a létrehozásának dátuma és időpontja. A perc az ismétlődési ütemezés beállításához adja meg az órák, a kezdési időt percben, vagy azt az időpontot használja. Például ha a kezdési és a létrehozás ideje 8:25-kor, ez az ütemezés fut reggel 8:25-kor, 9:25-kor, 10:25-kor, és így tovább. | 
||||||||| 

<a name="start-time"></a>

**K:** Mik azok a módon, hogy a kezdő dátum és idő használható? </br>
**V:** Íme néhány a minták azt mutatják be, hogy miként szabályozható a kezdő dátum és idő ismétlődési, és hogyan a Logic Apps-motor hajtja végre ezek ismétlődések:

| Kezdő időpont | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel | 
| ---------- | --------------------------- | ------------------------ | 
| {nincs} | Az első számítási feladat azonnal futtatja. <p>A legutóbbi futtatás ideje alapján jövőbeli számítási feladatot futtat. | Az első számítási feladat azonnal futtatja. <p>Jövőbeli számítási feladatok a megadott ütemezés alapján fut. | 
| Múltbeli kezdési időpontja | Futtatási időt a megadott kezdési időpontban és elvetések elmúlt futtassa többször alapján számítja ki. Az első számítási feladat fut, a következő jövő futási idő. <p>A legutóbbi futtatás ideje kiszámítása alapján jövőbeli számítási feladatot futtat. <p>További magyarázat tekintse meg a táblázat példa. | Az első számítási feladatot futtat *nem hamarabb* a kezdési időpontnál, a kezdési időpontból kiszámított ütemezésen alapul. <p>Jövőbeli számítási feladatok a megadott ütemezés alapján fut. <p>**Megjegyzés:** Ha adjon meg egy ismétlődési ütemezés, de nem adja meg az óra vagy perc alatt az ütemezés, majd jövőbeli futtatási idő kiszámítása az óra vagy perc, az első futtatásának időpontját, használatával. | 
| Jelenlegi vagy jövőbeni kezdő időpontja | Az első számítási feladatok a megadott kezdési időpontban fut. <p>A legutóbbi futtatás ideje kiszámítása alapján jövőbeli számítási feladatot futtat. | Az első számítási feladatot futtat *nem hamarabb* a kezdési időpontnál, a kezdési időpontból kiszámított ütemezésen alapul. <p>Jövőbeli számítási feladatok a megadott ütemezés alapján fut. <p>**Megjegyzés:** Ha adjon meg egy ismétlődési ütemezés, de nem adja meg az óra vagy perc alatt az ütemezés, majd jövőbeli futtatási idő kiszámítása az óra vagy perc, az első futtatásának időpontját, használatával. | 
||||

**Példa egy múltbeli kezdési időpont, ismétlődéssel, de ütemezés nélkül** 

| Kezdő időpont | Aktuális idő | Ismétlődés | Ütemezés |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Minden 2 nap | {nincs} | 
||||| 

Ebben a forgatókönyvben a futtatási idejének alapján a kezdési időpont alapján kiszámítja a Logic Apps korábbi futtatási ideje, és használja a következő jövő kezdési idő az első futtatáskor elveti. Követően, hogy az első használatkor későbbi futtatások a kezdési időpontból kiszámított ütemezésen alapul. Itt látható, hogyan néz ki az Ismétlődés:

| Kezdő időpont | Első futtatás ideje | A jövőben futtatási idejének | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** , 2:00-kor | 2017-09 -**09** , 2:00-kor | 2017-09 -**11** , 2:00-kor </br>2017-09 -**13** , 2:00-kor </br>2017-09 -**15** , 2:00-kor </br>és így tovább...
||||

Tehát ebben a forgatókönyvben függetlenül attól, hogy milyen a múltban, adja meg a kezdési idő, például 2017-09 -**05** 2:00-kor, vagy 2017-09 -**01** 2:00 Órakor, az első futtassa a következő alkalommal ugyanaz.

## <a name="next-steps"></a>További lépések

* [Munkafolyamat-műveletek és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Összekötők](../connectors/apis-list.md)
