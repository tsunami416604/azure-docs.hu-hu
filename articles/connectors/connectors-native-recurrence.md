---
title: Ismétlődő feladatok és munkafolyamatok ütemezése
description: Ismétlődő automatizált feladatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps ismétlődési eseményindítójával
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445863"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az Azure Logic Apps ismétlődési eseményindítójával

A feladatok, folyamatok vagy feladatok rendszeres futtatásához adott ütemezés szerint, elindíthatja a logikai alkalmazás munkafolyamata a beépített **Ismétlődés – Ütemezés** eseményindítóval. Beállíthat egy dátumot és időt, valamint egy időzónát a munkafolyamat indításához, és ismétlődést a munkafolyamat megismétléséhez. Ha az ismétlődések bármilyen okból kimaradnak, ez az eseményindító a következő ütemezett időközönként ismétlődik. A beépített ütemezési eseményindítókról és -műveletekről az [Ismétlődő automatizált, feladatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps alkalmazásokkal című témakörben](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)talál további információt.

Íme néhány példa, amelyet ez az eseményindító támogat a fejlettebb ismétlődésekkel és az összetett ütemezésekkel együtt:

* Fuss azonnal, és ismételje meg minden *n* számú másodperc, perc, óra, nap, hét, vagy hónap.

* Kezdje egy adott dátummal és időpontban, majd futtassa és ismételje meg minden *n* számú másodpercet, percet, órát, napot, hetet vagy hónapot.

* Fusson és ismételje meg naponta egy vagy több alkalommal, például 8:00 és 17:00 órakor.

* Fusson és ismételje meg minden héten, de csak bizonyos napokon, például szombaton és vasárnap.

* Fusson és ismételje meg minden héten, de csak bizonyos napokon és időpontokban, például hétfőtől péntekig 8:00-kor és 17:00-kor.

Az eseményindító és a Csúszóablak eseményindító közötti különbségekről, illetve az ismétlődő munkafolyamatok ütemezéséről az [Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps alkalmazásokkal című témakörben](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)talál további információt.

> [!TIP]
> Ha azt szeretné, hogy a logikai alkalmazás, és csak egyszer fut a jövőben, lásd: [Feladatok futtatása csak egyszer.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek a [logikai alkalmazásokról.](../logic-apps/logic-apps-overview.md) Ha most ismerkedik a logikai alkalmazásokkal, ismerje meg, [hogyan hozhat létre az első logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-recurrence-trigger"></a>Ismétlődés eseményindító hozzáadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Üres logikai alkalmazás létrehozása.

1. Miután a Logic App Designer megjelenik, a keresőmezőbe írja be `recurrence` a szűrőt. Az eseményindítók listájából válassza ki ezt az eseményindítót a logikai alkalmazás munkafolyamatának első lépéseként: **Ismétlődés**

   ![Válassza az "Ismétlődés" eseményindítót](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában állítsa be ezeket a tulajdonságokat a munkafolyamat minden héten történő futtatásához.

   ![Időköz és gyakoriság beállítása](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | **Intervallum** | `interval` | Igen | Egész szám | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján. A minimális és maximális intervallumok a következők: <p>- Hónap: 1-16 hónap </br>- Nap: 1-500 nap </br>- Óra: 1-12.000 óra </br>- Perc: 1-72.000 perc </br>- Második: 1-9,999,999 másodperc<p>Ha például az intervallum 6, és a gyakoriság "Hónap", akkor az ismétlődés 6 havonta történik. |
   | **Frekvencia** | `frequency` | Igen | Sztring | Az ismétlődés időegysége: **Második,** **Perc,** **Óra,** **Nap,** **Hét**vagy **Hónap** |
   ||||||

   > [!IMPORTANT]
   > Ha az ismétlődések nem adnak meg speciális ütemezési beállításokat, a jövőbeli ismétlődések az utolsó futtatási időalapján történnek.
   > Ezek az ismétlődések kezdési időpontjai elsodródhatnak olyan tényezők miatt, mint például a tárolási hívások során a késés. Annak érdekében, hogy a logikai alkalmazás ne maradjon le az ismétlődésről, különösen akkor, ha a gyakoriság napokban vagy hosszabb ideig tart, használja az alábbi lehetőségek egyikét:
   > 
   > * Adja meg az ismétlődés kezdési idejét.
   > 
   > * Adja meg az órákat és perceket, hogy mikor kell futtatni az ismétlődést az Ezekben az **órákban** és ezekben a **percekben** tulajdonságok használatával.
   > 
   > * Az Ismétlődés eseményindító helyett használja a [Csúszó ablak eseményindítót.](../connectors/connectors-native-sliding-window.md)

1. A speciális ütemezési beállítások beállításához nyissa meg az **Új paraméter hozzáadása** listát. A kiválasztott beállítások a kijelölés után jelennek meg az eseményindítón.

   ![Speciális ütemezési beállítások](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Tulajdonság | JSON-név | Kötelező | Típus | Leírás |
   |----------|-----------|----------|------|-------------|
   | **Időzóna** | `timeZone` | Nem | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az eseményindító nem fogadja el [az UTC eltolást.](https://en.wikipedia.org/wiki/UTC_offset) Jelölje ki az alkalmazni kívánt időzónát. |
   | **Kezdési idő** | `startTime` | Nem | Sztring | Adja meg a kezdési dátumot és az időt ebben a formátumban: <p>YÉÉÉ-HH-DDThh:mm:ss, ha időzónát választ <p>– vagy – <p>YÉÉÉ-HH-DDThh:mm:ssZ, ha nem választ ki időzónát <p>Ha például 2017. Vagy adja meg a "2017-09-18T14:00:00Z" értéket időzóna nélkül. <p>**Megjegyzés:** Ennek a kezdési időpontnak a jövőben legfeljebb 49 éve van, és [utc dátumidő-formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kell követnie az [ISO 8601 dátumidő-meghatározási](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) szintet , de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül . Ha nem jelöl ki időzónát, a végén szóközök nélkül kell hozzáadnia a "Z" betűt. Ez a "Z" az egyenértékű [hajózási időre](https://en.wikipedia.org/wiki/Nautical_time)vonatkozik . <p>Egyszerű ütemezések esetén a kezdési időpont az első előfordulás, míg az összetett ütemezések esetén az eseményindító nem aktiválódik hamarabb, mint a kezdési időpont. [*Hogyan használhatom a kezdő dátumot és az időt?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Ezeken a napokon** | `weekDays` | Nem | Karakterlánc- vagy karakterlánctömb | Ha a "Hét" lehetőséget választja, akkor egy vagy több napot választhat, amikor futtatni szeretné a munkafolyamatot: **hétfő,** **kedd,** **szerda,** **csütörtök,** **péntek,** **szombat**és **vasárnap** |
   | **Ezekben az órákban** | `hours` | Nem | Egész vagy egész tömb | Ha a "Nap" vagy a "Hét" lehetőséget választja, akkor egy vagy több 0 és 23 év között választhat jaatot a munkafolyamat futtatásának idejére. <p><p>Ha például a "10", "12" és "14" értéket adja meg, akkor a nap óráiban 10,13, 12 és 14 óra lesz, de a nap perceinek kiszámítása az ismétlődés kezdetétől függ. A nap perceinek beállításához adja meg az **At these minutes** tulajdonság értékét. |
   | **Ezekben a percekben** | `minutes` | Nem | Egész vagy egész tömb | Ha a "Nap" vagy a "Hét" lehetőséget választja, akkor egy vagy több 0 és 59 év között választhat jattot az óra azon perceként, amikor futtatni szeretné a munkafolyamatot. <p>Megadhatja például a "30" szót percjelként, és az előző példát használva a nap óráiban 10:30, 12:30 és 14:30 óra kor között. |
   |||||

   Tegyük fel például, hogy ma hétfő, szeptember 4, 2017. A következő ismétlődési eseményindító nem *aktiválódik hamarabb,* mint a kezdő dátum és az idő, amely 2017. Az ismétlődési ütemezés azonban csak hétfőnként 10:30-ra, 12:30-ra és 14:30-ra van beállítva. Így az első alkalommal, amikor az eseményindító aktiválódik, és létrehoz egy logikai alkalmazás munkafolyamat-példány a 10:30. Ha többet szeretne megtudni a kezdési idők működéséről, tekintse meg az alábbi [kezdési időpontokra vonatkozó példákat.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)

   A jövőbeli futások ugyanazon a napon 12:30-kor és 14:30-kor történnek. Minden ismétlődés saját munkafolyamat-példányt hoz létre. Ezt követően, az egész menetrend ismétlődik újra jövő hétfőn. [*Milyen egyéb példaelőfordulások?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Példa speciális ütemezésre](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Az eseményindító csak akkor jeleníti meg a megadott ismétlődés előnézetét, ha a "Nap" vagy a "Hét" lehetőséget választja gyakoriságként.

1. Most más műveletekkel építheti fel a fennmaradó munkafolyamatot. További műveletek, amelyek et hozzáadhat, lásd: [Összekötők az Azure Logic Apps.](../connectors/apis-list.md)

## <a name="workflow-definition---recurrence"></a>Munkafolyamat-definíció - Ismétlődés

A logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójában, amely a JSON-t használja, megtekintheti az [Ismétlődés eseményindító definícióját](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) a kiválasztott beállításokkal. A definíció megtekintéséhez kattintson a tervező eszköztárának **Kód nézetparancsára.** A tervezőhöz való visszatéréshez válassza **Designer**a Tervező eszköztártervező idomára.

Ez a példa bemutatja, hogyan nézhet ki az Ismétlődés eseményindító-definíció egy mögöttes munkafolyamat-definícióban:

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
