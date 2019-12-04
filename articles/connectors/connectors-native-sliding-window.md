---
title: Feladatok ütemezett végrehajtása a folytonos adatok kezeléséhez
description: Összefüggő adatok kezelésére szolgáló ismétlődő feladatok létrehozása és futtatása a Azure Logic Apps a csúszó ablakok használatával
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786911"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>A folytonos adatokhoz tartozó feladatok ütemezett és futtatásához használja a csúszó ablakos triggert Azure Logic Apps

Ahhoz, hogy rendszeresen futtasson olyan feladatokat, folyamatokat vagy feladatokat, amelyeknek összefüggő adattömbökben kell kezelnie az adatok kezelését, elindíthatja a logikai alkalmazás munkafolyamatát a **csúszó ablakos** trigger használatával. Megadhat egy dátumot és időpontot, valamint egy időzónát a munkafolyamat elindításához és a munkafolyamat ismétlődésének megismétléséhez. Ha az ismétlődések valamilyen okból kimaradnak, az eseményindító feldolgozza ezeket a kihagyott ismétlődéseket. Ha például szinkronizálja az adatokat az adatbázis és a biztonsági mentési tároló között, akkor a csúszó ablak triggert használva, hogy az adatokat a rendszer hézagok nélkül szinkronizálja. A beépített ütemezett eseményindítókkal és műveletekkel kapcsolatos további információkért lásd: [ismétlődő automatizált, feladatok és munkafolyamatok ütemezett és futtatott Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Íme néhány példa, amelyet ez az trigger támogat:

* Azonnal fusson, és ismételje meg az *n* másodpercenkénti számát, percet vagy órát.

* Kezdjen egy adott dátummal és időponttal, majd futtasson és ismételje meg az *n* másodpercenkénti számát, percet vagy órát. Ezzel az eseményindítóval megadhatja a múltbeli kezdési időt, amely minden korábbi ismétlődést futtat.

* Egy adott időtartamra vonatkozó ismétlődések késleltetése a Futtatás előtt.

Az eseményindító és az ismétlődési eseményindító közötti különbségek, illetve az ismétlődő munkafolyamatok ütemezésével kapcsolatos további információkért lásd: [ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása a Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Ha szeretné elindítani a logikai alkalmazást, és csak egyszer kell futtatnia a jövőben, tekintse meg a [feladatok futtatása csak](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)egyszer című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* A [Logic apps](../logic-apps/logic-apps-overview.md)alapszintű ismerete. Ha most ismerkedik a Logic apps szolgáltatással, Ismerje meg, [hogyan hozhatja létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Csúszó ablakos trigger hozzáadása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. A Logic app Designer megjelenése után a keresőmezőbe írja be szűrőként a "csúszó ablak" kifejezést. Az eseményindítók listából válassza ki ezt az eseményindítót a logikai alkalmazás munkafolyamatának első lépéseként: **ablak** behúzása

   ![Válassza a "csúszó ablak" triggert](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Adja meg az ismétlés időközét és gyakoriságát. Ebben a példában ezeket a tulajdonságokat úgy állítsa be, hogy hetente futtassák a munkafolyamatot.

   ![Az intervallum és a gyakoriság beállítása](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Tulajdonság | Szükséges | JSON-név | Type (Típus) | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Intervallum** | Igen | interval | Egész szám | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján. Itt láthatók a minimális és a maximális intervallumok: <p>-Óra: 1 – 12000 óra </br>Perc: 1 – 72000 perc </br>-Másodperc: 1 – 9999999 másodperc<p>Ha például az intervallum 6, és a gyakoriság értéke "Hour", akkor az ismétlődés 6 óránként történik. |
   | **Gyakoriság** | Igen | frequency | Sztring | Az ismétlődés időegysége: **másodperc**, **perc**vagy **óra** |
   ||||||

   ![Speciális ismétlődési beállítások](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   További ismétlődési lehetőségekért nyissa meg az **új paraméterek hozzáadása** listát. 
   A kiválasztott lehetőségek megjelennek az triggeren a kijelölés után.

   | Tulajdonság | Szükséges | JSON-név | Type (Típus) | Leírás |
   |----------|----------|-----------|------|-------------|
   | **Késedelem** | Nem | késedelem | Sztring | Az egyes ismétlődések késleltetésének időtartama az [ISO 8601 dátum-idő specifikációjának](https://en.wikipedia.org/wiki/ISO_8601#Durations) használatával |
   | **Időzóna** | Nem | timeZone | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az trigger nem fogad el [UTC-eltolást](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az alkalmazni kívánt időzónát. |
   | **Kezdési idő** | Nem | startTime | Sztring | Adja meg a kezdő dátumot és időpontot a következő formátumban: <p>ÉÉÉÉ-hh-NNTóó: PP: mm, ha időzónát választ <p>– vagy – <p>ÉÉÉÉ-hh-NNTóó: PP: ssZ, ha nem jelöl ki időzónát <p>Így például, ha a szeptember 18., 2017 at 2:00 PM-t szeretné használni, adja meg a "2017-09-18T14:00:00" parancsot, és válasszon ki egy időzónát, például a csendes-óceáni téli időpontot. Vagy a "2017-09-18T14:00:00Z" érték megadásával időzóna nélkül. <p>**Megjegyzés:** Ennek a kezdési időpontnak meg kell felelnie az [ISO 8601 dátum-idő specifikációjának](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) [UTC dátum és idő formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Ha nem ad meg időzónát, a végén fel kell vennie a "Z" betűt szóközök nélkül. Ez a "Z" a megfelelő [tengeri időpontra](https://en.wikipedia.org/wiki/Nautical_time)hivatkozik. <p>Az egyszerű ütemezések esetében a kezdő időpont az első előfordulás, míg a speciális ismétlődések esetében az eseményindító nem indul el a kezdési időpontnál hamarabb. [*Milyen módon használhatom a kezdő dátumot és időt?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Most hozza létre a hátralévő munkafolyamatot más műveletekkel. További felvehető műveletekért lásd: [összekötők Azure Logic Appshoz](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Munkafolyamat-definíció – ablak csúsztatása

A logikai alkalmazás mögöttes munkafolyamat-definíciójában, amely JSON-t használ, megtekintheti a csúszó ablak trigger definícióját a kiválasztott beállításokkal. A definíció megtekintéséhez a tervező eszköztárán válassza a **kód nézet**lehetőséget. A tervezőhöz való visszatéréshez válassza a tervező eszköztárán a **tervező elemet.**

Ebből a példából megtudhatja, hogyan jelenhet meg a csúszó ablak eseményindítójának definíciója egy mögöttes munkafolyamat-definícióban, ahol az ismétlődések késése öt másodperc az óránkénti ismétlődésnél:

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

## <a name="next-steps"></a>Következő lépések

* [A következő művelet késleltetése a munkafolyamatokban](../connectors/connectors-native-delay.md)
* [Logic Apps-összekötők](../connectors/apis-list.md)