---
title: Ismétlődési eseményindító – Azure Logic Apps az ismétlődő feladatok ütemezése
description: Az Azure Logic Apps ismétlődési trigger ismétlődő automatizált feladatok és a munkafolyamatok futtatásához és ütemezéséhez
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297541"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Létrehozhatja, ütemezheti és ismétlődő feladatok és a munkafolyamatok futtatásához az Azure Logic Apps ismétlődési trigger

A feladatok, folyamatok és feladatok rendszeresen meghatározott ütemezés szerint fut, elkezdheti a logikai alkalmazás munkafolyamatának a beépített **ismétlődés - ütemezés** eseményindító. Dátum és idő, valamint az időzóna indítása a munkafolyamat és a egy ismétlődési, hogy a munkafolyamat ismétlődő állíthatja be. Ismétlődések bármilyen okból nem talált a rendszer, ha ez az eseményindító továbbra is a következő ütemezett időközönként ismétlődő. További információ a beépített ütemezési eseményindítók és műveletek: [ütemezés és Futtatás ismétlődő, automatizált, feladatok és az Azure Logic Apps munkafolyamat](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Az alábbiakban néhány, az eseményindító támogatja az összetettebb ismétlődések és a komplex ütemezések a minták:

* Azonnal futtatni, és ismételje meg minden *n* másodperc, perc, óra, nap, hét vagy hónapok száma.

* Indítsa el a megadott dátummal és időponttal, majd futtassa, és ismételje meg minden *n* másodperc, perc, óra, nap, hét vagy hónapok száma.

* Futtassa, és ismételje meg egy vagy több alkalommal minden nap, például: 8:00-kor és 17:00-kor.

* Futtassa, és ismételje meg minden héten, de csak az adott napon, például a hétvégén.

* Futtassa, és ismételje meg minden héten, de csak az adott napokon és időpontokban, például a munkanapokon 8:00-kor és 17:00-kor.

Erre az eseményindítóra, és a késleltetett eseményindító közötti különbségek vagy további információt a munkafolyamat ismétlődő ütemezés esetén lásd: [ütemezése és futtatása ismétlődő, automatizált feladatokat, folyamatokat és munkafolyamatokat az Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Ha a logikai alkalmazás elindításához, és csak egyetlen alkalommal fussanak a jövőbeli, tekintse meg a kívánt [Futtatás feladat csak egyszer](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, akkor az [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* Alapvető ismeretek szerezhetők [a logic apps](../logic-apps/logic-apps-overview.md). Ha most ismerkedik a logic apps, [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Ismétlődési trigger hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. Miután Logikaialkalmazás-Tervező jelenik meg, a Keresés mezőbe írja be a "recurrence" szűrőként. Az eseményindítók listában jelölje ki a logikai alkalmazás munkafolyamatának első lépéseként erre az eseményindítóra: **Ismétlődés**

   ![Válassza ki az "Ismétlődés" eseményindító](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában állítsa be ezeket a tulajdonságokat a munkafolyamatot hetente futtatni.

   ![Set időközét és gyakoriságát](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Tulajdonság | Kötelező | JSON-név | Típus | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Intervallum** | Igen | interval | Integer | Pozitív egész szám, amely leírja, hogy milyen gyakran a munkafolyamat futtatása gyakorisága alapján. Az alábbiakban a minimális és maximális időközönként: <p>– Hónap: 1 – 16 hónap </br>– Nap: 1-500 nap </br>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például ha a időköz 6, és a gyakoriság "Month", akkor az ismétlődés nem minden hatodik hónapban. |
   | **Gyakoriság** | Igen | frequency | String | Az időegység, az Ismétlődés: **Második**, **perc**, **óra**, **nap**, **hét**, vagy **hónap** |
   ||||||

   További ütemezési beállításokat, nyissa meg a **új paraméter hozzáadása** listája. 
   Minden kiválasztott beállítások kiválasztása után jelennek meg az eseményindító.

   ![Speciális ütemezési beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Tulajdonság | Kötelező | JSON-név | Típus | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Időzóna** | Nem | timeZone | String | Csak amikor Ön megadja a kezdési időt, mert ez az eseményindító nem fogadja el érvényes [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az időzónát, amely a alkalmazni szeretné. |
   | **Kezdési idő** | Nem | startTime | String | Adja meg a kezdő dátum és idő a következő formátumban: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha egy időzóna <p>– vagy – <p>ÉÉÉÉ-hh-DDThh:mm:ssZ, ha nem adja meg a időzóna <p>Így például, ha azt szeretné, 2017. szeptember 18., 2:00-kor, majd adja meg "2017-09-18T14:00:00", és válassza ki az időzónát, például a csendes-óceáni téli idő. Másik lehetőségként adja meg "2017-09-18T14:00:00Z" időzóna nélkül. <p>**Megjegyzés:** Hajtsa végre a kezdő időpont a [ISO 8601 dátum-idő specifikáció](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC idő dátumformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Ne válassza ki az időzónát, ha hozzá kell adnia a levél "Z", a végén szóközök nélkül. A "Z" hivatkozik az azzal egyenértékű [hajózási idő](https://en.wikipedia.org/wiki/Nautical_time). <p>Egyszerű ütemezések esetében a kezdési időpont az első előfordulás a összetett ütemezéseknél az eseményindító nem indul el minden korábban, a kezdési időpontnál. [*Mik azok a módon, hogy a kezdő dátum és idő használható?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Ezeken a napokon** | Nem | weekDays | Karakterláncot vagy karakterlánc-tömbben | Ha "Week", ha a munkafolyamatot futtatni kívánt egy vagy több napot választhat: **Hétfő**, **kedd**, **szerda**, **csütörtök**, **péntek**, **szombat**, és **Vasárnap** |
   | **Ezekben az órákban** | Nem | hours | Egész szám vagy az egész számok tömbje | Ha "Day" vagy "Week", választhat egy vagy több egész számok 0 és 23 legyen az óra szeretné, hogy a munkafolyamat futtatásához a napon. <p><p>Például ha megadja a "10", "12" és "14", 2 PM az a nap, óra, 10 Órakor, 12 PM kap, de a perc, a nap alapján számítjuk, amikor elindul az ismétlődés. Adja meg, hogy az a nap percben, értékét a **ezekben a percekben** tulajdonság. |
   | **Ezekben a percekben** | Nem | minutes | Egész szám vagy az egész számok tömbje | Ha "Day" vagy "Week", választhat egy vagy több egész számok 0 és 59 közötti legyen a perc, az óra, ha a munkafolyamatot futtatni szeretné. <p>Például "30" megadhatja a percenkénti be van jelölve, és megjelenik az előző példa a nap, óra, 10:30-kor, 12:30 = 1997031213, és 2:30-kor. |
   |||||

   Tegyük fel például, hogy a mai dátum 2017. szeptember 4., hétfő. A következő ismétlődési eseményindító nem indul el *minden korábban* , mint a kezdő dátum és idő, amely 2017. szeptember 18., hétfő jelenleg 8:00 Csendes-ÓCEÁNI. Azonban van beállítva az ismétlődési ütemezés 10:30-kor, 12:30 = 1997031213, és 2:30-kor hétfőjén csak. Ezért az első alkalom, hogy az eseményindító aktiválódik, és létrehoz egy logikaialkalmazás-példányt a munkafolyamat jelenleg 10:30-kor. Hogyan indítása túllépi az munkahelyi kapcsolatos további információkért lásd: ezek [idő példák a start](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   12:30 = 1997031213 és 2:30-kor későbbi futtatások ugyanarra a napra fordulhat elő. Minden egyes ismétlődési saját munkafolyamat-példányhoz hoz létre. Ezt követően a teljes ütemezés ismétlődik minden újra következő hétfőn keresztül. [*Mik azok néhány további példa előfordulásait?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Speciális ütemezési példa](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Az eseményindító a megadott ismétlődés előnézetét jeleníti meg, csak ha "Day" vagy "Week" jelöli meg a gyakoriságot.

1. Most már létrehozhatja a fennmaradó munkafolyamatot más műveletekkel. Hozzáadhat további műveleteket, lásd: [Azure Logic Apps összekötői](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Munkafolyamat-definíció - ismétlődés

A logikai alkalmazás alapjául szolgáló munkafolyamat-definíciót, amely JSON-t használ, megtekintheti a [ismétlődési eseményindító meghatározása](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) a választott beállításokkal. Ez a definíció megtekintéséhez a Tervező eszköztárán válassza **Kódnézet**. Térjen vissza a tervezőben, válassza az entitástervező eszköztár **Designer**.

Ez a példa bemutatja, hogyan nézhet ki egy ismétlődési eseményindító meghatározása az alapul szolgáló munkafolyamat-definíció:

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
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>További lépések

* [Munkafolyamatok felfüggesztése késleltetés műveletek](../connectors/connectors-native-delay.md)
* [A Logic Apps összekötői](../connectors/apis-list.md)
