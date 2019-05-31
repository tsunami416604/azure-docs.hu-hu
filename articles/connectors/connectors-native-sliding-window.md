---
title: Késleltetett ablakos eseményindító – Azure Logic Apps az ismétlődő feladatok ütemezése
description: Az Azure Logic Appsben a késleltetett ablak eseményindítóval ismétlődő automatikus feladatokkal és munkafolyamatokkal futtatásához és ütemezéséhez
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299502"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Létrehozhatja, ütemezheti és ismétlődő feladatok és munkafolyamatok futtatása az Azure Logic Apps késleltetett ablak eseményindítót a

Feladatok, folyamatok és feladatok, amelyek kell kezelni a folytonos adattömbökre az adatokat rendszeresen futtatásához, a logikai alkalmazás munkafolyamatának a megkezdése a **késleltetett ablak - ütemezés** eseményindító. Dátum és idő, valamint az időzóna indítása a munkafolyamat és a egy ismétlődési, hogy a munkafolyamat ismétlődő állíthatja be. Ismétlődések valamilyen okból nem talált a rendszer, ha ez az eseményindító ezeket kihagyott ismétlődések dolgozza fel. Például az adatbázis és a biztonsági mentési tár közötti szinkronizálásakor használja a késleltetett eseményindító, hogy az adatok beolvasása szinkronizálása, hézagok nélkül. További információ a beépített ütemezési eseményindítók és műveletek: [ütemezés és Futtatás ismétlődő, automatizált, feladatok és az Azure Logic Apps munkafolyamat](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Az alábbiakban néhány mintákat, amelyek az eseményindító támogatja:

* Azonnal futtatni, és ismételje meg minden *n* másodperc, perc vagy órák száma.

* Indítsa el a megadott dátummal és időponttal, majd futtassa, és ismételje meg minden *n* másodperc, perc vagy órák száma. A triggerrel egy kezdő időpontja múltbeli időpont, amely futtatja az összes korábbi ismétlődések is megadhat.

* Késleltetés az egyes ismétlődési konkrét időtartamot futtatása előtt.

Ez az eseményindító és az ismétlődési eseményindító közötti különbségek vagy további információt a munkafolyamat ismétlődő ütemezés esetén lásd: [ütemezése és futtatása ismétlődő, automatizált feladatokat, folyamatokat és munkafolyamatokat az Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Ha a logikai alkalmazás elindításához, és csak egyetlen alkalommal fussanak a jövőbeli, tekintse meg a kívánt [Futtatás feladat csak egyszer](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, akkor az [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* Alapvető ismeretek szerezhetők [a logic apps](../logic-apps/logic-apps-overview.md). Ha most ismerkedik a logic apps, [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Késleltetett eseményindító hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. Miután Logikaialkalmazás-Tervező jelenik meg, a Keresés mezőbe írja be a "csúszóablakban" szűrőként. Az eseményindítók listában jelölje ki a logikai alkalmazás munkafolyamatának első lépéseként erre az eseményindítóra: **Csúszóablakszerűen történik**

   ![Válassza ki a "késleltetett" eseményindító](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában állítsa be ezeket a tulajdonságokat a munkafolyamatot hetente futtatni.

   ![Set időközét és gyakoriságát](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Tulajdonság | Kötelező | JSON-név | Típus | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Intervallum** | Igen | interval | Integer | Pozitív egész szám, amely leírja, hogy milyen gyakran a munkafolyamat futtatása gyakorisága alapján. Az alábbiakban a minimális és maximális időközönként: <p>– Óra: 1 – 12 000 óra </br>– Perc: 1 – 72,000 perc </br>-Másodperc: 1 – 9,999,999 másodperc<p>Például ha a időköz 6, és a gyakoriság "Hour", majd az ismétlődést, 6 óránként. |
   | **Gyakoriság** | Igen | frequency | String | Az időegység, az Ismétlődés: **Második**, **perc**, vagy **óra** |
   ||||||

   ![Speciális ismétlődési beállítások](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   További ismétlődési beállítások, nyissa meg a **új paraméter hozzáadása** listája. 
   Minden kiválasztott beállítások kiválasztása után jelennek meg az eseményindító.

   | Tulajdonság | Kötelező | JSON-név | Típus | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Delay** | Nem | delay | String | Egyes ismétlődési használatával késleltetés idejére a [ISO 8601 dátum-idő specifikáció](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Időzóna** | Nem | timeZone | String | Csak amikor Ön megadja a kezdési időt, mert ez az eseményindító nem fogadja el érvényes [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az időzónát, amely a alkalmazni szeretné. |
   | **Kezdési idő** | Nem | startTime | String | Adja meg a kezdő dátum és idő a következő formátumban: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha egy időzóna <p>– vagy – <p>ÉÉÉÉ-hh-DDThh:mm:ssZ, ha nem adja meg a időzóna <p>Így például, ha azt szeretné, 2017. szeptember 18., 2:00-kor, majd adja meg "2017-09-18T14:00:00", és válassza ki az időzónát, például a csendes-óceáni téli idő. Másik lehetőségként adja meg "2017-09-18T14:00:00Z" időzóna nélkül. <p>**Megjegyzés:** Hajtsa végre a kezdő időpont a [ISO 8601 dátum-idő specifikáció](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) a [UTC idő dátumformátum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de egy [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Ne válassza ki az időzónát, ha hozzá kell adnia a levél "Z", a végén szóközök nélkül. A "Z" hivatkozik az azzal egyenértékű [hajózási idő](https://en.wikipedia.org/wiki/Nautical_time). <p>Egyszerű ütemezések esetében a kezdési időpont az első előfordulás a speciális ismétlődések, az eseményindító nem indul el minden korábban, a kezdési időpontnál. [*Mik azok a módon, hogy a kezdő dátum és idő használható?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Most már létrehozhatja a fennmaradó munkafolyamatot más műveletekkel. Hozzáadhat további műveleteket, lásd: [Azure Logic Apps összekötői](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Munkafolyamat-definíció – késleltetett ablak

A logikai alkalmazás alapjául szolgáló munkafolyamat-definíciót, amely JSON-t használ, a késleltetett ablak eseményindító meghatározása a választott beállításokkal is megtekintheti. Ez a definíció megtekintéséhez a Tervező eszköztárán válassza **Kódnézet**. Térjen vissza a tervezőben, válassza az entitástervező eszköztár **Designer**.

Ez a példa megmutatja, hogy milyen egy késleltetett ablak eseményindító meghatározása előfordulhat, hogy az alapul szolgáló munkafolyamat-definíció ahol minden egyes ismétlődés késleltetési idő legyen-e öt másodpercenként egy óránkénti Ismétlődés:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>További lépések

* [A munkafolyamatokban a következő művelet késleltetése](../connectors/connectors-native-delay.md)
* [A Logic Apps összekötői](../connectors/apis-list.md)