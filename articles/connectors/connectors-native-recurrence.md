---
title: Ismétlődő feladatok és munkafolyamatok ütemezett időpontja
description: Ismétlődő automatizált feladatok és munkafolyamatok ütemezése és futtatása az ismétlődési eseményindítóval Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 3618e6d3fe919bc06496d5b41d162bd211c3d404
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231934"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval Azure Logic Apps

Ha a feladatokat, folyamatokat vagy feladatokat egy adott ütemterven szeretné rendszeresen futtatni, elindíthatja a logikai alkalmazás munkafolyamatát a beépített **Ismétlődés-ütemezése** eseményindítóval. Megadhat egy dátumot és időpontot, valamint egy időzónát a munkafolyamat elindításához és a munkafolyamat ismétlődésének megismétléséhez. Ha bármilyen okból kimaradnak az ismétlődések, ez az eseményindító a következő ütemezett időszakban ismétlődően folytatódik. A beépített ütemezett eseményindítókkal és műveletekkel kapcsolatos további információkért lásd: [ismétlődő automatizált, feladatok és munkafolyamatok ütemezett és futtatott Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Íme néhány olyan minta, amelyet ez az eseményindító támogat, valamint fejlettebb ismétlődéseket és összetett ütemezéseket:

* Futtassa azonnal, és ismételje meg az *n* másodpercenkénti számát, a perceket, az órákat, a napokat, a heteket és a hónapokat.

* Kezdjen egy adott dátummal és időponttal, majd futtasson és ismételje meg az *n* másodpercenkénti számát, perc, óra, nap, hét vagy hónap.

* Futtasson és ismételje meg az egyes napokon, például 8:00-kor és 5:00 PM-ben.

* Minden héten futtasson és ismételje meg a műveletet, de csak adott napokon, például szombaton és vasárnap.

* Minden héten futtasson és ismételje meg a műveletet, de csak adott napokra és időpontokra, például hétfő – péntek, 8:00 és 5:00 órakor.

A trigger és a csúszó ablak trigger közötti különbségek, illetve az ismétlődő munkafolyamatok ütemezésével kapcsolatos további információkért lásd: [ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása a Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Ha szeretné elindítani a logikai alkalmazást, és csak egyszer kell futtatnia a jövőben, tekintse meg a [feladatok futtatása csak](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)egyszer című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A [Logic apps](../logic-apps/logic-apps-overview.md)alapszintű ismerete. Ha most ismerkedik a Logic apps szolgáltatással, Ismerje meg, [hogyan hozhatja létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Ismétlődési eseményindító hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. A Logic app Designer megjelenése után a keresőmezőbe írja be `recurrence` szűrőként a kifejezést. Az eseményindítók listából válassza ki ezt az eseményindítót a logikai alkalmazás munkafolyamatának első lépéseként: **Ismétlődés**

   ![Az "Ismétlődés" eseményindító kiválasztása](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában ezeket a tulajdonságokat úgy állítsa be, hogy hetente futtassák a munkafolyamatot.

   ![Az intervallum és a gyakoriság beállítása](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | **Időköz** | `interval` | Igen | Egész szám | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján. Itt láthatók a minimális és a maximális intervallumok: <p>-Hónap: 1-16 hónap </br>-Nap: 1-500 nap </br>-Óra: 1 – 12000 óra </br>Perc: 1 – 72000 perc </br>-Másodperc: 1 – 9999999 másodperc<p>Ha például az intervallum 6, és a gyakoriság értéke "Month", akkor az ismétlődés 6 havonta történik. |
   | **Gyakoriság** | `frequency` | Igen | Sztring | Az ismétlődés időegysége: **másodperc**, **perc**, **óra**, **nap**, **hét**vagy **hónap** |
   ||||||

   > [!IMPORTANT]
   > Ha az ismétlődések nem határoznak meg speciális ütemezési beállításokat, a jövőbeli ismétlődések az utolsó futási időn alapulnak.
   > Ezeknek az ismétlődéseknek az indítási időpontja a tárolási hívások során felmerülő tényezők, például a késés miatt eltérhet. A következő lehetőségek egyikének használatával győződjön meg arról, hogy a logikai alkalmazás nem hagyja ki az ismétlődést, különösen akkor, ha a gyakoriság napokban vagy már nem érhető el.
   > 
   > * Adja meg az ismétlődés kezdési idejét.
   > 
   > * Adja meg az órákat és a perceket, hogy mikor futtassa az ismétlődést a **ezen órákon** és a **percekben** megadott tulajdonságok alapján.
   > 
   > * Az ismétlődési eseményindító helyett használja a [csúszó ablak eseményindítóját](../connectors/connectors-native-sliding-window.md).

1. A speciális ütemezési beállítások megadásához nyissa meg az **új paraméterek hozzáadása** listát. A kiválasztott lehetőségek megjelennek az triggeren a kijelölés után.

   ![Speciális ütemezési beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | **Időzóna** | `timeZone` | Nem | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az trigger nem fogad el [UTC-eltolást](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az alkalmazni kívánt időzónát. |
   | **Kezdési idő** | `startTime` | Nem | Sztring | Adja meg a kezdő dátumot és időpontot a következő formátumban: <p>ÉÉÉÉ-hh-NNTóó: PP: mm, ha időzónát választ <p>– vagy – <p>ÉÉÉÉ-hh-NNTóó: PP: ssZ, ha nem jelöl ki időzónát <p>Így például, ha a szeptember 18., 2017 at 2:00 PM-t szeretné használni, adja meg a "2017-09-18T14:00:00" parancsot, és válasszon ki egy időzónát, például a csendes-óceáni téli időpontot. Vagy a "2017-09-18T14:00:00Z" érték megadásával időzóna nélkül. <p>**Megjegyzés:** Ez a kezdési időpont legfeljebb 49 évvel későbbi, és az [ISO 8601 dátum-idő specifikációt](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) kell követnie [UTC dátum és idő formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Ha nem ad meg időzónát, a végén fel kell vennie a "Z" betűt szóközök nélkül. Ez a "Z" a megfelelő [tengeri időpontra](https://en.wikipedia.org/wiki/Nautical_time)hivatkozik. <p>Az egyszerű ütemtervek esetében a kezdési időpont az első előfordulás, míg a komplex ütemtervek esetében az trigger nem a kezdési időpontnál hamarabb következik be. [*Milyen módon használhatom a kezdő dátumot és időt?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Ezeken a napokon** | `weekDays` | Nem | Karakterlánc-vagy karakterlánc-tömb | Ha a "hét" lehetőséget választja, kiválaszthat egy vagy több napot, amikor futtatni szeretné a munkafolyamatot: **hétfő**, **kedd**, **szerda**, **csütörtök**, **péntek**, **szombat**és **vasárnap** |
   | **Ezekben az órákban** | `hours` | Nem | Egész vagy egész tömb | Ha a "nap" vagy a "hét" lehetőséget választja, akkor 0 és 23 közötti egész számot is kiválaszthat a nap azon órájára, amikor futtatni kívánja a munkafolyamatot. <p><p>Ha például a "10", a "12" és a "14" lehetőséget választja, akkor a nap órájában 10 ÓRAKOR, 12 ÓRAKOR és 2 óráig lesz kiszámítva, de a nap perce az ismétlődés megkezdése alapján történik. A nap adott percének megadásához például: 10:00, 12:00 PM és 2:00 PM, adja meg ezeket az értékeket a **következő perc** tulajdonság használatával. |
   | **Ezekben a percekben** | `minutes` | Nem | Egész vagy egész tömb | Ha a "nap" vagy a "hét" lehetőséget választja, akkor 0 és 59 közötti egész számot is kiválaszthat, ha a munkafolyamatot futtatni szeretné az óra percében. <p>Megadhatja például a "30" percet a perces jelölésként, és az előző példában a nap órájában a következőt kapja: 10:30, 12:30 PM és 2:30 PM. |
   |||||

   Tegyük fel például, hogy ma hétfő, szeptember 4., 2017. A következő ismétlődési eseményindító nem *any sooner* indul el a kezdési dátumnál és az időpontnál, ami a 2017-as hétfő, szeptember 18., 8:00 am PST. Az ismétlődési ütemterv azonban 10:30, 12:30 PM és 2:30 ÓRAKOR van beállítva hétfőnként. Így az eseményindító első indításakor a logikai alkalmazás munkafolyamat-példánya a 10:30-es időpontban jön létre. A kezdési idő működésével kapcsolatos további tudnivalókért tekintse meg a következő [példákat](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   A jövőbeli futtatások megtörténnek a 12:30 ÓRAKOR és a 2:30 PM-től ugyanazon a napon. Minden ismétlődés létrehozza a saját munkafolyamat-példányát. Ezután a teljes ütemterv ismét ismétlődik a következő hétfőn. [*Milyen más példák is előfordulnak?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Speciális ütemezési példa](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Az eseményindító csak akkor jeleníti meg a megadott ismétlődés előnézetét, ha a gyakoriság beállításnál a "nap" vagy a "hét" lehetőséget választja.

1. Most hozza létre a hátralévő munkafolyamatot más műveletekkel. További felvehető műveletekért lásd: [összekötők Azure Logic Appshoz](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Munkafolyamat-definíció – ismétlődés

A logikai alkalmazás mögöttes munkafolyamat-definíciójában, amely JSON-t használ, megtekintheti az [ismétlődési eseményindító definícióját](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) a kiválasztott beállításokkal. A definíció megtekintéséhez a tervező eszköztárán válassza a **kód nézet**lehetőséget. A tervezőhöz való visszatéréshez válassza a tervező eszköztárán a **tervező elemet.**

Ez a példa azt szemlélteti, hogyan lehet megtekinteni egy ismétlődési eseményindító definícióját egy mögöttes munkafolyamat-definícióban:

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

* [Munkafolyamatok szüneteltetése késleltetési műveletekkel](../connectors/connectors-native-delay.md)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)
