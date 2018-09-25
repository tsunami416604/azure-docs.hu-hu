---
title: Az Azure Scheduler áttelepíteni az Azure Logic Apps
description: Ismerje meg, hogyan lecserélheti kivezetjük, az Azure Scheduler-feladatok az Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: c841f29adbe9911193227cced2856d953d820b08
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997281"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Az Azure Scheduler-feladatok migrálása az Azure Logic Apps

> [!IMPORTANT]
> Az Azure Logic Apps kivezetjük, az Azure Scheduler lecseréli. Feladatok ütemezése, hajtsa végre az Azure Logic Apps való áttéréssel, ehelyett Ez a cikk.

Ez a cikk bemutatja, hogyan ütemezheti egyszeri és ismétlődő feladatok automatizált munkafolyamatok létrehozásával az Azure Logic Apps, nem pedig az Azure Scheduler használatával. Amikor logikai alkalmazásokkal hoz létre ütemezett feladatokat, ezeket az előnyöket kap:

* Nem kell aggódnia a fogalmat egy *feladatgyűjtemény* mivel minden egyes logikai alkalmazás egy külön Azure-erőforrás.

* Egyetlen logikai alkalmazás használatával több egyszeri feladatok futtatásához.

* Az Azure Logic Apps szolgáltatás támogatja az időzónát és a nyári időszámításra (cél).

További tudnivalókért lásd: [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md) Próbálja ki az első logikai alkalmazás létrehozása ebben a rövid útmutatóban vagy: [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* A logikai alkalmazás elindításához a HTTP-kérelmek küldésével, használjon egy eszköz például a [Postman asztali alkalmazás](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Egyszeri ütemezés

Csak egyetlen logikai alkalmazás létrehozásával több egyszeri feladatok futtatásához. 

### <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az a [az Azure portal](https://portal.azure.com), hozzon létre egy üres logikai alkalmazás a Logikaialkalmazás-tervezőben. 

   Az alapvető lépéseken, hajtsa végre az [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. A Keresés mezőbe írja be a "Amikor egy http-kérelem" szűrőként. A eseményindítók listáról válassza ki a következő eseményindítót: **amikor egy HTTP-kérés fogadásakor.** 

   !["Request" eseményindító hozzáadása](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. A kérés eseményindító opcionálisan megadhat egy JSON-sémát, amely segít megérteni a bejövő kérelem a bemeneti adatok struktúráját, a Logic App Designerben, és később a munkafolyamatban választható megkönnyíti a kimenetek.

   Adjon meg egy séma, adja meg a sémának a **kérelem Kéréstörzs JSON-sémája** mezőbe, például: 

   ![Kérelem séma](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Ha nem rendelkezik a séma, de egy hasznosadat-minta JSON formátumban van, a sémát, hogy hasznos-is létrehozhat.

   1. A kérelem eseményindítójában válassza **Mintaadat használata séma létrehozásához**.

   1. A **írja vagy illessze be a JSON hasznosadat-minta**, adja meg a hasznosadat-minta, és válassza **kész**, például:

      ![Hasznosadat-minta](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Az eseményindító területén válassza a **következő lépés**. 

1. A Keresés mezőbe írja be a "késleltetés eddig" szűrőként. Műveletek listája alatt válassza a következő műveletet: **késleltetés eddig**

   Ez a művelet felfüggeszti a logikai alkalmazás munkafolyamata egy megadott dátumig és időpontig.

   !["Késleltetés eddig" művelet hozzáadása](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Adja meg az időbélyeg az, amikor elindítja a logikai alkalmazás munkafolyamat. 

   Amikor kattint a **időbélyeg** box, a dinamikus tartalmak listája jelenik meg egy kimeneti igény szerint kiválaszthatja a trigger által.

   ![Adja meg az adatokat "Késleltetés eddig"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Válassza ki a futtatni kívánt bármely egyéb műveletek hozzáadása [~ több mint 200 összekötőt](../connectors/apis-list.md). 

   Megadhat például egy HTTP-művelet, amely egy kérést küld egy URL-címet, vagy műveleteket, amelyeket a tároló-üzenetsorok, Service Bus-üzenetsorok és Service Bus-üzenettémakörök használata: 

   ![HTTP-művelet](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Ha elkészült, mentse a logikai alkalmazást.

   ![A logikai alkalmazás mentése](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   A logikai alkalmazás mentése első alkalommal, amikor a végpont URL-cím a logikai alkalmazás kérelem típusú trigger megjelenik a **HTTP POST URL-címe** mezőbe. 
   Ha azt szeretné, hívja meg a logikai alkalmazást, és feldolgozásra a logikai alkalmazás bemeneti adatokat küldhet, hívás célként használni az URL-címet.

   ![Mentse a kérés eseményindító végpont URL-címe](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Másolja és mentse a végponti URL-cím, hogy később elküldhesse manuális kérelmet, amely elindítja a logikai alkalmazás. 

## <a name="start-a-one-time-job"></a>Egy egyszeri feladat indítása

Manuálisan futtassa, vagy egy egyszeri feladat eseményindítót, küldjön egy hívás a logikai alkalmazás kérelem típusú trigger végpont URL-címéhez. Adja meg a bemenet vagy adattartalom-be való küldéséhez, előfordulhat, hogy rendelkezik korábban leírt adjon meg egy séma, amely ehhez a híváshoz. 

Például a Postman alkalmazással, akkor is hozzon létre egy POST kérést beállításokkal a következő mintához hasonló, és válassza **küldése** a kérés.

| Kérelem metódusa | URL-cím | Törzs | Fejlécek |
|----------------|-----|------|---------| 
| **POST** | <*végpont-URL-címe*> | **nyers** <p>**JSON(Application/JSON)** <p>Az a **nyers** adja meg a hasznos adatokat szeretne küldeni a kérésben. <p>**Megjegyzés:**: ezt a beállítást, automatikusan konfigurálja a **fejlécek** értékeket. | **Kulcs**: Content-Type <br>**Érték**: application/json
 |||| 

![A logikai alkalmazás manuális elindításához kérelem küldése](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Miután elküldte a hívást, a logikai alkalmazás a válasz megjelenik a **nyers** mezőjébe a **törzs** fülre. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Ha szeretné megszakítani a feladatot később, válassza ki a **fejlécek** fülre. Keresse meg és másolja a **x-ms-munkafolyamat-Futtatás-id** fejléc értéke a válaszban. 
>
> ![Válasz](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Egy egyszeri feladat megszakítása

A Logic Apps esetében minden egyes egyszeri feladat példány futtatásához egy logikai alkalmazás végrehajt. Egy egyszeri feladat megszakításához használhatja [- munkafolyamat-futtatások megszakítása](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) a Logic Apps alkalmazások REST API-ban. Ha az eseményindítót hívást küld, adja meg a [munkafolyamat-futtatási azonosító](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Ismétlődő feladatok ütemezése

### <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az a [az Azure portal](https://portal.azure.com), hozzon létre egy üres logikai alkalmazás a Logikaialkalmazás-tervezőben. 

   Az alapvető lépéseken, hajtsa végre az [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. A keresőmezőbe írja be a "recurrence" szűrőként. A eseményindítók listáról válassza ki a következő eseményindítót: **ismétlődés** 

   !["Ismétlődés" eseményindító hozzáadása](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Ha azt szeretné, állítsa be, speciális ütemezés szerint.

   ![Bővített ütemezés](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Speciális ütemezési beállításokkal kapcsolatos további információkért lásd: [létrehozása és az ismétlődő feladatokat futtató és az Azure Logic Apps munkafolyamat](../connectors/connectors-native-recurrence.md)

1. Válassza ki a kívánt egyéb műveletek hozzáadása [több mint 200 összekötőt](../connectors/apis-list.md). Az eseményindító területén válassza a **következő lépés**. Keresse meg és válassza ki a kívánt műveleteket.

   Megadhat például egy HTTP-művelet, amely egy kérést küld egy URL-címet, vagy műveleteket, amelyeket a tároló-üzenetsorok, Service Bus-üzenetsorok és Service Bus-üzenettémakörök használata: 

   ![HTTP-művelet](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Ha elkészült, mentse a logikai alkalmazást.

   ![A logikai alkalmazás mentése](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Speciális telepítési

Az alábbiakban az egyéb módon testre szabhatja a feladatok.

### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

Szabályozhatja az művelet megkísérli újra futtatni a logikai alkalmazás az időszakos meghibásodások esetén, beállíthatja a [újrapróbálkozási szabályzat](../logic-apps/logic-apps-exception-handling.md#retry-policies) minden művelet beállításai, például:

1. Nyissa meg a műveletet (**...** ) menüre, és válassza ki **beállítások**.

   ![Nyissa meg a művelet beállításai](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Válassza ki a kívánt újrapróbálkozási házirendet. Minden egyes házirenddel kapcsolatos további információkért lásd: [újrapróbálkozási szabályzatok](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Válassza ki az újrapróbálkozási szabályzat](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Kivételek és hibák kezelése

Az Azure Schedulerben Ha az alapértelmezett művelet nem sikerül futtatni, egy alternatív műveletet, amely a hibajelzést kiváltó körülmény futtassa. Az Azure Logic Appsben is elvégezheti a ugyanazt a feladatot.

1. A Logikaialkalmazás-tervezőben, a művelet felett szeretné kezelni, a lépések közötti helyezze az egérmutatót a nyílra és válassza ki és **párhuzamos ág hozzáadása**. 

   ![Párhuzamos ág hozzáadása](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Keresse meg és válassza ki a helyette alternatív műveletet futtatni kívánt műveletet.

   ![Párhuzamos művelet hozzáadása](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Az alternatív műveletet, nyissa meg a (**...** ) menüre, és válassza ki **konfigurálása futtassa**.

   ![Esemény utáni futtatás konfigurálása](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Törölje a jelölését a **sikeres** tulajdonság. Válassza ki ezeket a tulajdonságokat: **sikertelen**, **kimarad**, és **túllépte az időkorlátot**

   ![Állítsa be a "Futtatás után" tulajdonságai](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Ha elkészült, válassza ki a **kész**.

Kivételkezelés kapcsolatos további információkért lásd: [hibák és kivételek - RunAfter tulajdonság kezelése](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>GYIK

<a name="retire-date"></a> 

**Q**: Ha az Azure Scheduler kivonás alatt áll? <br>
**A**: az Azure Scheduler ütemezett kivonása a 2019. szeptember 30.

**Q**: Mi történik a Scheduler-feladatgyűjtemények, és a feladatok követően kivonja a szolgáltatás? <br>
**A**: összes Scheduler-feladatgyűjtemények és a feladatok törlődnek a rendszerből.

**Q**: kell biztonsági másolatot, vagy bármely egyéb feladatok végrehajtása a Scheduler-feladatok a Logic Apps-ra migrálás előtt? <br>
**A**: ajánlott eljárásként, mindig készítsen biztonsági másolatot a munkáját. Ellenőrizze, hogy a létrehozott logikai alkalmazások törlése vagy a Scheduler-feladatok letiltása előtt elvárt módon futnak-e. 

**Q**: van van olyan eszköz, amely segítséget kérek a áttelepítés saját feladatok a Scheduler a Logic Apps? <br>
**A**: minden Scheduler-feladat egyediségét, így a dokumentum ajánlásai eszköz nem létezik. Azonban különböző parancsfájlok lesz elérhető az igényei szerint módosíthatja. A parancsfájl rendelkezésre állást próbálkozzon újra később.

**Q**: Hol kaphatok támogatást a Scheduler-feladatok áttelepítéséhez? <br>
**A**: kérhet támogatást a következőképpen: 

**Azure Portal**

Ha az Azure-előfizetés a fizetős támogatási csomagot, létrehozhat egy műszaki támogatási kérést az Azure Portalon. Ellenkező esetben a különböző támogatási lehetőséget is választja.

1. Az a [az Azure portal](https://portal.azure.com) fő menüjéből válassza **súgó + támogatás**.

1. A **támogatja**válassza **új támogatási kérelem**. A kérelem meg az alábbi adatokat:

   | Beállítás | Érték |
   |---------|-------|
   | **Probléma típusa** | **Technikai** | 
   | **Előfizetés** | <*az Azure-előfizetés*> | 
   | **Szolgáltatás** | A **figyelés + felügyelet**válassza **Scheduler**. | 
   ||| 

1. Válassza ki a kívánt támogatása. Ha fizetős támogatási csomaggal rendelkezik, válassza ki a **tovább**.

**Közösség**

* [Az Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>További lépések

* [Rendszeresen futó feladatok és munkafolyamatok létrehozása az Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Oktatóanyag: Forgalom ütemezésalapú logikai alkalmazás ellenőrzése](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)