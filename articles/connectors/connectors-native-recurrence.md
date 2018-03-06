---
title: "Feladatok ütemezése és rendszeresen futó munkafolyamatok - Azure Logic Apps |} Microsoft Docs"
description: "Hozzon létre, és rendszeresen futó tevékenységek, műveletek, munkafolyamatok, folyamatok és a logic apps munkaterhelések ütemezése"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0dead955f9eb723dfa232d3ce751498a09ce1b29
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="create-and-schedule-regularly-running-tasks-with-azure-logic-apps"></a>Hozzon létre, és az Azure Logic Apps rendszeresen futó feladatok ütemezése

Ütemezett feladatok, a műveletek, a munkaterhelés vagy a rendszeresen futtatott folyamatokat, kezdetű logic app munkafolyamat hozhat létre a **ütemezés - ismétlődési** [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts). Az ehhez az eseményindítóhoz állíthatja be egy dátumot és időpontot a ismétlődési és feladatokat, mint az ezekben a példákban ismétlődés ütemezésének indítása:

* Belső adatok lekérése: [egy SQL tárolt eljárás futtatására](../connectors/connectors-create-api-sqlazure.md) minden nap.
* Külső adatok beolvasása: időjárás-jelentések lekérés NOAA a 15 percenként.
* A jelentés adatait: e-mailben összes rendelés nagyobb, mint a megadott összeg összesítő az elmúlt héten.
* Adatok feldolgozása: Compress ma feltöltötte a képek minden hétköznap végezze alacsony forgalmú időszakban.
* Adatok karbantartása: törölje a három hónapnál régebbi összes Twitter-üzeneteket.
* Archivált adatok: a biztonsági mentési szolgáltatás számlák leküldéssel minden hónapban.

Ehhez az eseményindítóhoz sok kombinációját, például támogatja:

* Azonnal futtatni, és ismételje meg minden *n* másodperc, perc, óra, nap, hét vagy hónapok száma.
* Indítsa el a megadott időpontban, majd futtassa, majd ismételje meg minden *n* másodperc, perc, óra, nap, hét, vagy hónapok száma.
* Futtassa, és ismételje meg egy vagy több minden nap időpontban, például: 8:00-kor és Délután 5.
* Futtassa, és ismételje meg minden héten, de csak az adott napokra, például a szombat és vasárnap.
* Futtassa, és ismételje meg minden héten, de csak adott napját és idejét, például hétfőtől péntekig 8:00-kor és Délután 5.

Az ismétlődési eseményindító minden alkalommal, amikor következik be, amikor a Logic Apps hoz létre, és a logic app munkafolyamat új példányát futtatja.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Ismétlődés eseményindító hozzáadása a logikai alkalmazás

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Hozzon létre egy üres logikai alkalmazást, vagy további [egy üres logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Miután Logic Apps Designer megjelenik, a keresési mezőbe, írja be a "ismétlődési" szűrőként. Válassza ki a **ütemezés - ismétlődési** eseményindító. 

   ![Ütemezés - ismétlődési eseményindító](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Az eseményindító már az első lépés a Logic Apps alkalmazást.

3. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában állítsa be ezeket a tulajdonságokat a munkafolyamatot futtatni minden héten. 

   ![Set időköz és gyakorisága](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. További ütemezési beállítások kiválasztása **speciális beállítások megjelenítése**. 

   ![További beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Mostantól megadhatja ezeket a beállításokat: 

   * Állítsa be a kezdési dátummal és idővel a kiváltó az eseményindító. 
   A kezdő dátum és idő megadása esetén is alkalmazhat időzónát. 

   * Ha bejelöli a "Day" vagy "Hetente", a gyakoriság, kiválaszthatja az ismétlődés meghatározott alkalommal. 

   * Ha "Hetente" lehetőséget választja, a hét meghatározott napjain túl is választhat.
   
   ![Speciális ütemezési beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Tegyük fel például, hogy ma 2017. szeptember 4., hétfőtől. 
   A következő ismétlődési eseményindító nem érvényesítést *bármely hamarabb* a kezdő dátum és idő, mint ami 2017. szeptember 18., hétfőtől jelenleg 8:00 AM csendes-óceáni TÉLI. 
   Azonban az ismétlődés ütemezésének be van állítva 10:30 AM, 12:30 PM és 2:30 PM csak hétfőn. Ezért az első alkalom, hogy az eseményindító következik be, és létrehoz egy logic app munkafolyamat-példány jelenleg 10:30-kor. 
   Hogyan start alkalommal munkahelyi kapcsolatos további információkért lásd: ezek [idő példák a start](#start-time).
   Későbbi futtatásakor fordulhat elő: 12:30 PM és 2:30 PM ugyanarra a napra esnek. 
   Minden egyes ismétlődési saját munkafolyamat-példányhoz hoz létre. Ezt követően a teljes ütemezési ismétlődik minden újra következő hétfő keresztül. 
   [*Mik azok a néhány más példa eseményeket?*](#example-recurrences)

   ![Speciális ütemezési – példa](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Az eseményindító egy előzetes verzióját a megadott ismétlődési jeleníti meg, csak ha "Day" vagy "Hetente" jelöli meg a gyakorisága.
   
6. Most már a fennmaradó munkafolyamat műveletek összeállítását, vagy flow vezérlő utasítások. További műveletek, amelyeket felvehet, lásd: [összekötők](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Eseményindító részletei

Az ismétlődési eseményindító tulajdonságait konfigurálhatja.

| Name (Név) | Szükséges | Tulajdonság neve | Típus | Leírás | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Gyakoriság** | Igen | frequency | Karakterlánc | Az ismétlődés időegység: **második**, **perc**, **óra**, **nap**, **hét**, vagy  **Hónap** | 
| **Intervallum** | Igen | interval | Egész szám | Egy pozitív egész szám, amely leírja, milyen gyakran a munkafolyamat fut, a gyakoriság alapján. <p>Az alapértelmezett időköz: 1. Az alábbiakban a minimális és maximális intervallumok: <p>-Hónap: 1-16 hónap </br>-Nap: 1-500 nap </br>– Óra: 1-12 000 üzemideje (óra) </br>-Perc: 1-72,000 perc </br>-Második: 1-9,999,999 másodpercben<p>Például ha az intervallum értéke 6, és a gyakoriság "Honap", akkor az ismétlődés értéke minden hatodik hónapban. | 
| **Időzóna** | Nem | timeZone | Karakterlánc | Vonatkozik, csak ha a kezdési időpontot, mert az eseményindító nem fogadja el [UTC eltolás](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az alkalmazni kívánt időzónát. | 
| **Kezdési idő** | Nem | startTime | Karakterlánc | Adja meg a kezdési idő, a következő formátumban: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha egy időzóna <p>– vagy – <p>ÉÉÉÉ-hh-SSz, ha nem adja meg a időzóna <p>Így például, ha 2017. szeptember 18., 2:00 PM, majd adja meg "2017-09-18T14:00:00", és válassza ki például a csendes-óceáni idő időzóna. Másik lehetőségként adja meg "2017-09-18T14:00:00Z" időzóna nélkül. <p>**Megjegyzés:** hajtsa végre a kezdési ideje a [ISO 8601 dátuma a megadott időpont](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC dátum időformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [UTC eltolás](https://en.wikipedia.org/wiki/UTC_offset). Ne válassza ki egy időzónát, ha hozzá kell adnia a levél "Z" szóközt nélkül végén. A "Z" hivatkozik a egyenértékű [navigációs idő](https://en.wikipedia.org/wiki/Nautical_time). <p>Egyszerű ütemezés, a kezdési idő nem az első olyan, az összetett ütemezéseket, az eseményindító a kezdési időpontnál bármilyen hamarabb nem érvényesítést. [*Milyen módon használható a kezdő dátum és idő is?*](#start-time) | 
| **Ezeken a napokon** | Nem | weekDays | Karakterlánc vagy karakterlánctömb | Ha "Hetente", ha azt szeretné, a munkafolyamat futtatásához egy vagy több napot kiválaszthatja: **hétfő**, **kedd**, **szerda**, **csütörtök** , **Péntek**, **szombat**, és **vasárnap** | 
| **Ezekben az órákban** | Nem | hours | Egész szám vagy az egész tömb | Ha bejelöli a "Day" vagy "Hetente", választhat egy vagy több egész számok 0 – 23 a nap, ha azt szeretné, a munkafolyamat futtatási óra. <p>Például ha megadja a "10", "12" és "14", kapott 2 PM, az óra közötti, de 10, 12 PM. | 
| **Ezekben a percekben** | Nem | minutes | Egész szám vagy az egész tömb | Ha bejelöli a "Day" vagy "Hetente", választhat egy vagy több egész számok 0 és 59 az óra, ha azt szeretné, a munkafolyamat futtatási perc. <p>Például megadhatja a perc megjelölés "30", és elérhetővé az előző példa óra, nap, 10:30 AM, 12:30 PM és 2:30 PM. | 
||||| 

## <a name="json-example"></a>JSON-példa

Példa [ismétlődési eseményindító definícióját](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
{
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
}
```

## <a name="faq"></a>GYIK

<a name="example-recurrences"></a>

**K:** Mik azok a más példa ismétlődés ütemezésének? </br>
**V:** további példa:

| Ismétlődés | Időköz | Gyakoriság | Kezdés időpontja | E napokon | Órák | Percek | Megjegyzés |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Futtassa a 15 percenként (nem kezdő dátum és idő) | 15 | Perc | {nincs} | {nem érhető el} | {nincs} | {nincs} | Az ütemezés azonnal elindul, majd későbbi ismétlődések a legutóbbi futtatás ideje alapján számítja ki. | 
| 15 percenként futtatni (a kezdő dátum és idő) | 15 | Perc | *startDate*T*startTime*Z | {nem érhető el} | {nincs} | {nincs} | Az ütemezés nem indul el *bármely hamarabb* a megadott kezdési dátum és idő, mint jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. | 
| Minden órában, futtassa az óra (a kezdő dátum és idő) | 1 | Óra | *startDate*Thh:00:00Z | {nem érhető el} | {nincs} | {nincs} | Az ütemezés nem indul el *bármely hamarabb* mint a megadott kezdési dátuma és időpontja. Jövőbeli ismétlődések óránként futtassa a "00" minute be van jelölve. <p>Ha a gyakoriság "Hét" vagy "Honap", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. | 
| Futtassa a óránként, naponta (nem kezdő dátum és idő) | 1 | Óra | {nincs} | {nem érhető el} | {nincs} | {nincs} | Az ütemezés azonnal elindul, és későbbi ismétlődések a legutóbbi futtatás ideje alapján számítja ki. <p>Ha a gyakoriság "Hét" vagy "Honap", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. | 
| Futtassa a óránként, naponta (a kezdő dátum és idő) | 1 | Óra | *startDate*T*startTime*Z | {nem érhető el} | {nincs} | {nincs} | Az ütemezés nem indul el *bármely hamarabb* a megadott kezdési dátum és idő, mint jövőbeli ismétlődések a legutóbbi futtatás ideje alapján számítja ki. <p>Ha a gyakoriság "Hét" vagy "Honap", az ütemezés rendre fut csak egy naponta, hetente vagy havonta egy nap. | 
| Futtassa a óránként túlra 15 percenként, minden órában (a kezdő dátum és idő) | 1 | Óra | *startDate*T00:15:00Z | {nem érhető el} | {nincs} | {nincs} | Az ütemezés nem indul el *bármely hamarabb* mint a megadott kezdési dátuma és időpontja, éjfélre (00:15), 1:15 AM, 2:15 AM, fut, és így tovább. | 
| Futtatás minden 15 perc, minden órában (nem kezdő dátum és idő) | 1 | Nap | {nincs} | {nem érhető el} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Az ütemezés futtatására éjfélre (00:15), 1:15 AM, 2:15 AM, és így tovább. Ezt az ütemezést is, megegyezik a gyakoriság "Hour" és "15" perc a kezdési időpontot. | 
| 15 percenként futtassa a 15 perces jel (nem kezdő dátum és idő) | 1 | Nap | {nincs} | {nem érhető el} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Az ütemezés nem indul el, amíg a következő megadott 15 perces be van jelölve. | 
| 8:00 órakor minden nap (nem kezdő dátum és idő) futtatása | 1 | Nap | {nincs} | {nem érhető el} | 8 | {nincs} | Az ütemezés futtatására 8:00 órakor naponta, megadott ütemezés szerint. | 
| 8:00 órakor minden nap (a kezdő dátum és idő) futtatása | 1 | Nap | *startDate*T08:00:00Z | {nem érhető el} | {nincs} | {nincs} | Az ütemezés 8:00-kor naponta, fut, a megadott kezdési időponttal alapján. | 
| 8:30 AM minden nap (nem kezdő dátum és idő) futtatása | 1 | Nap | {nincs} | {nem érhető el} | 8 | 30 | Az ütemezés lefuttat 8:30 AM naponta, a megadott ütemezés alapján. | 
| 8:30 AM minden nap (a kezdő dátum és idő) futtatása | 1 | Nap | *startDate*T08:30:00Z | {nem érhető el} | {nincs} | {nincs} | Az ütemezés a megadott kezdési dátum: 8:30 AM elindítja. | 
| 8:30 AM du. 4:30 naponta futtatását | 1 | Nap | {nincs} | {nem érhető el} | 8, 16 | 30 | | 
| 8:30 AM, 8 óra 45 Perckor, futtassa du. 4:30 és du. 4:45 minden nap | 1 | Nap | {nincs} | {nem érhető el} | 8, 16 | 30, 45 | | 
| Futtat minden szombat hajnali (nem kezdő dátum és idő) délután 5 óra | 1 | Hét | {nincs} | "Szombat" | 17 | 00 | Az ütemezés minden szombaton 5:00 PM lefut. | 
| Futtat minden szombat hajnali (a kezdő dátum és idő) délután 5 óra | 1 | Hét | *startDate*T17:00:00Z | "Szombat" | {nincs} | {nincs} | Az ütemezés nem indul el *bármely hamarabb* mint a megadott kezdési dátuma és időpontja, ebben az esetben 2017. szeptember 9., 5:00 PM. Jövőbeli ismétlődések, 5:00 PM futtat minden szombat. | 
| Futtatás minden keddjén, csütörtök, délután 5 óra | 1 | Hét | {nincs} | "Kedd", "Csütörtök" | 17 | {nincs} | Ezt az ütemezést, 5:00 PM minden keddi és csütörtöki futtatja. | 
| Munkaidőben minden órában futtatva | 1 | Hét | {nincs} | Válassza ki a szombat és vasárnap kivételével minden nap. | Válassza ki a kívánt nap óra. | Válassza ki az óra, amelyet minden perc. | Például ha a munkaidőt a 5:00 PM 8:00-kor, majd jelöljön ki "8, 9, 10, 11, 12, 13, 14, 15, 16, 17", az óra, nap. <p>Ha a munkaidőt a 5:30 PM 8:30 AM, válassza ki az előző óra, nap összege "30" az óra perc. | 
| Naponta egyszer hétvégén futtatása | 1 | Hét | {nincs} | "Szombat", "Vasárnap" | Válassza ki a kívánt nap óra. | Válassza ki az óra bármely perc szükség szerint. | Az ütemezés a megadott ütemezés szerint futtatja az minden szombat és vasárnap. | 
| Kétheti hétfőn csak 15 percenként futtatása | 2 | Hét | {nincs} | "Hétfő" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Az ütemezés lefuttat minden második hétfő minden 15 perces be van jelölve. | 
| Havonta egy napig minden órában futtatva | 1 | Hónap | {Lásd: a Megjegyzés} | {nem érhető el} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Lásd: a Megjegyzés} | Ha nem adja meg a kezdési dátummal és idővel, ezt az ütemezést használja a létrehozásának dátuma és időpontja. A perc az ismétlődési ütemezés beállításához adja meg a percek száma, az óra, a kezdő időpont, vagy használjon a létrehozásának ideje. Például ha a kezdési idő vagy a létrehozás időpontjában 8:25 óra, az ütemezés fut órakor 8:25, 9:25 óra, 10:25 óra, és így tovább. | 
||||||||| 

<a name="start-time"></a>

**K:** milyen módon az, hogy a kezdő dátum és idő használata? </br>
**V:** az alábbiakban néhány minta, hogy miként szabályozható a kezdő dátum és idő ismétlődési, és hogyan a Logic Apps motor végrehajtja ezeket az Ismétlődések megjelenítése:

| Kezdés időpontja | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel | 
| ---------- | --------------------------- | ------------------------ | 
| {nincs} | Az első munkaterhelés azonnal futtatja. <p>A legutóbbi futtatás ideje alapján jövőbeni munkaterhelések fut. | Az első munkaterhelés azonnal futtatja. <p>A megadott ütemezés alapján jövőbeni munkaterhelések fut. | 
| Kezdő dátuma a múltban | Futtatási időt a megadott kezdési időponttal és elvetések korábbi futtatása alkalommal alapján számítja ki. Az első munkaterhelés lefut a következő jövőbeli futási időt. <p>A legutóbbi futtatása óta számítások alapján jövőbeni munkaterhelések fut. <p>További magyarázatát tekintse meg ezt a táblázatot követő példában. | Futtatja az első munkaterhelés *egyes* a kezdési időpontnál, kezdési idejét számítva ütemezés szerint. <p>A megadott ütemezés alapján jövőbeni munkaterhelések fut. <p>**Megjegyzés:** Ha adjon meg egy ismétlődési ütemezés, de nem adja meg, órában vagy percben, az ütemezés, majd későbbi futtatási időt kiszámítása a órában vagy percben, az első futtatásának időpontját, használatával. | 
| Jelenleg vagy a jövőben kezdési ideje | A megadott kezdési időpontban lefut az első munkaterhelés. <p>A legutóbbi futtatása óta számítások alapján jövőbeni munkaterhelések fut. | Futtatja az első munkaterhelés *egyes* a kezdési időpontnál, kezdési idejét számítva ütemezés szerint. <p>A megadott ütemezés alapján jövőbeni munkaterhelések fut. <p>**Megjegyzés:** Ha adjon meg egy ismétlődési ütemezés, de nem adja meg, órában vagy percben, az ütemezés, majd későbbi futtatási időt kiszámítása a órában vagy percben, az első futtatásának időpontját, használatával. | 
||||

**Korábbi kezdési időpontot az ismétlődési, de nincs ütemezés a következő példa** 

| Kezdés időpontja | Aktuális idő | Ismétlődés | Ütemezés |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Minden 2 nap | {nincs} | 
||||| 

Ebben a forgatókönyvben a futtatási idejének kezdési ideje alapján motor kiszámolja a Logic Apps elveti a múltbeli futási ideje, és használja a következő jövőbeli kezdő időpont első alkalommal történő futtatásakor. Követően, hogy első alkalommal történő futtatásakor a jövőbeli futtatása az ütemezés kezdési idejét számítva alapulnak. Az ismétlődés megjelenésének itt található:

| Kezdés időpontja | Első futási idő | Futtatási idejének jövőbeli | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** 2:00 PM: | 2017-09 -**09** 2:00 PM: | 2017-09 -**11** 2:00 PM: </br>2017-09 -**13** 2:00 PM: </br>2017-09 -**15** 2:00 PM: </br>és így tovább...
||||| 

Ezért a jelen esetben függetlenül attól, hogy milyen távolságban a múltban, adja meg a kezdési idő, például 2017-09 -**05** , 2:00 PM vagy 2017-09 -**01** , 2:00 PM, az első futásidejű azonos.

## <a name="next-steps"></a>További lépések

* [Munkafolyamat-műveletek és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Összekötők](../connectors/apis-list.md)
