---
title: Tevékenységek ütemezése összefüggő adatok kezelésére
description: Az azure-beli Logikai alkalmazások csúszóablakainak használatával ismétlődő feladatok létrehozása és futtatása
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786911"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Összefüggő adatok feladatainak ütemezése és futtatása az Azure Logic Apps csúszóablak-eseményindítójával

Ha rendszeresen futtathat olyan feladatokat, folyamatokat vagy feladatokat, amelyeknek összefüggő adattömbökben kell kezelniük az adatokat, a logikai alkalmazás munkafolyamatát a **Csúszó ablak** eseményindítóval indíthatja el. Beállíthat egy dátumot és időt, valamint egy időzónát a munkafolyamat indításához, és ismétlődést a munkafolyamat megismétléséhez. Ha bármilyen okból kimaradnak az ismétlődések, ez az eseményindító feldolgozza a kihagyott ismétlődéseket. Ha például az adatbázis és a biztonsági másolat tárolója közötti adatokat szinkronizálja, használja a Csúszó ablak eseményindítót, hogy az adatok szinkronizálása hézagok nélkül történik. A beépített ütemezési eseményindítókról és -műveletekről az [Ismétlődő automatizált, feladatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps alkalmazásokkal című témakörben](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)talál további információt.

Íme néhány példa, amelyet ez az eseményindító támogat:

* Fuss azonnal, és ismételje meg minden *n* számú másodperc, perc vagy óra.

* Kezdje egy adott dátummal és időpontban, majd futtassa és ismételje meg *az n* számú másodpercet, percet vagy órát. Ezzel az eseményindítóval megadhatja a múltbeli kezdési időpontot, amely az összes korábbi ismétlődést futtatja.

* Futás előtt késleltesse az ismétlődéseket egy adott időtartammal.

Az eseményindító és az Ismétlődés eseményindító közötti különbségekről, illetve az ismétlődő munkafolyamatok ütemezéséről az [Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps alkalmazásokkal című](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)témakörben talál további információt.

> [!TIP]
> Ha azt szeretné, hogy a logikai alkalmazás, és csak egyszer fut a jövőben, lásd: [Feladatok futtatása csak egyszer.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra.](https://azure.microsoft.com/free/)

* Alapvető ismeretek a [logikai alkalmazásokról.](../logic-apps/logic-apps-overview.md) Ha most ismerkedik a logikai alkalmazásokkal, ismerje meg, [hogyan hozhat létre az első logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-sliding-window-trigger"></a>Csúszó ablak eseményindító hozzáadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Üres logikai alkalmazás létrehozása.

1. Miután a Logic App Designer megjelenik, a keresőmezőbe írja be a "csúszó ablak" szűrőként. Az eseményindítók listájából válassza ki ezt az eseményindítót a logikai alkalmazás munkafolyamatának első lépéseként: **Csúszó ablak**

   ![Válassza a "Csúszó ablak" eseményindítót](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában állítsa be ezeket a tulajdonságokat a munkafolyamat minden héten történő futtatásához.

   ![Időköz és gyakoriság beállítása](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Tulajdonság | Kötelező | JSON-név | Típus | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Intervallum** | Igen | interval | Egész szám | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján. A minimális és maximális intervallumok a következők: <p>- Óra: 1-12.000 óra </br>- Perc: 1-72.000 perc </br>- Második: 1-9,999,999 másodperc<p>Ha például az intervallum 6, és a gyakoriság "Óra", akkor az ismétlődés 6 óránként történik. |
   | **Frekvencia** | Igen | frequency | Sztring | Az ismétlődés időegysége: **Második**, **Perc**vagy **Óra** |
   ||||||

   ![Speciális ismétlődési beállítások](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   További ismétlődési beállításokért nyissa meg az **Új paraméter hozzáadása** listát. 
   A kiválasztott beállítások a kijelölés után jelennek meg az eseményindítón.

   | Tulajdonság | Kötelező | JSON-név | Típus | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Késés** | Nem | Késleltetés | Sztring | Az egyes ismétlődések késleltetésének időtartama az [ISO 8601 dátumidő-meghatározás használatával](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Időzóna** | Nem | timeZone | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az eseményindító nem fogadja el [az UTC eltolást.](https://en.wikipedia.org/wiki/UTC_offset) Jelölje ki az alkalmazni kívánt időzónát. |
   | **Kezdési idő** | Nem | startTime | Sztring | Adja meg a kezdési dátumot és az időt ebben a formátumban: <p>YÉÉÉ-HH-DDThh:mm:ss, ha időzónát választ <p>– vagy – <p>YÉÉÉ-HH-DDThh:mm:ssZ, ha nem választ ki időzónát <p>Ha például 2017. Vagy adja meg a "2017-09-18T14:00:00Z" értéket időzóna nélkül. <p>**Megjegyzés:** Ennek a kezdési időpontnak [utc dátumidő-formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)kell követnie az [ISO 8601 dátumidő-meghatározási](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) idejét , de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Ha nem jelöl ki időzónát, a végén szóközök nélkül kell hozzáadnia a "Z" betűt. Ez a "Z" az egyenértékű [hajózási időre](https://en.wikipedia.org/wiki/Nautical_time)vonatkozik . <p>Egyszerű ütemezések esetén a kezdési időpont az első előfordulás, míg a speciális ismétlődések esetén az eseményindító nem aktiválódik hamarabb, mint a kezdési időpont. [*Hogyan használhatom a kezdő dátumot és az időt?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Most más műveletekkel építheti fel a fennmaradó munkafolyamatot. További műveletek, amelyek et hozzáadhat, lásd: [Összekötők az Azure Logic Apps.](../connectors/apis-list.md)

## <a name="workflow-definition---sliding-window"></a>Munkafolyamat-definíció – Csúszó ablak

A logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójában, amely a JSON-t használja, megtekintheti a Csúszó ablak eseményindító-definícióját a kiválasztott beállításokkal. A definíció megtekintéséhez kattintson a tervező eszköztárának **Kód nézetparancsára.** A tervezőhöz való visszatéréshez válassza **Designer**a Tervező eszköztártervező idomára.

Ez a példa bemutatja, hogyan nézhet ki a Csúszó ablak eseményindító-definíciója egy mögöttes munkafolyamat-definícióban, ahol az ismétlődések késleltetése öt másodperc az óránkénti ismétlődéshez:

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

* [A munkafolyamatok következő műveletének késleltetése](../connectors/connectors-native-delay.md)
* [Összekötők a Logic Apps számára](../connectors/apis-list.md)