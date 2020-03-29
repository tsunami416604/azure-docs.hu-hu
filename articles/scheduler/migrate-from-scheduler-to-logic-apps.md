---
title: Áttelepítés az Azure Scheduler programból az Azure Logic Apps alkalmazásba
description: Ismerje meg, hogyan cserélheti le a feladatokat az Azure Logic Apps alkalmazásokkal megszüntetett Azure Scheduler alkalmazásban
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 90c3cc2e096b9b58465987bc53f718c5d06c6203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899100"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Az Azure Scheduler-feladatok áttelepítése az Azure Logic Apps alkalmazásba

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb telepítse át az Azure Logic Apps-alkalmazásokba az ezt a cikket követve. 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

Ez a cikk bemutatja, hogyan ütemezheti az egyszeri és ismétlődő feladatokat az Azure Logic Apps automatizált munkafolyamatok létrehozásával, nem pedig az Azure Scheduler használatával. Amikor ütemezett feladatokat hoz létre a Logic Apps alkalmazásokkal, az alábbi előnyöket kapja:

* Több száz szolgáltatás, például az Azure Blob Storage, az Azure Service Bus, az Office 365 Outlook és az SAP vizuális tervezője és [használatra kész összekötőivel](../connectors/apis-list.md) készítheti el a feladatot.

* Az egyes ütemezett munkafolyamatokat első osztályú Azure-erőforrásként kezelheti. Nem kell aggódnia a *feladatgyűjtemény* koncepciója miatt, mert minden logikai alkalmazás egyedi Azure-erőforrás.

* Egyetlen logikai alkalmazás használatával több egyszeri feladatot futtat.

* Állítsa be az időzónákat támogató ütemezéseket, és automatikusan igazodjon a nyári időszámításhoz (DST).

További információ: [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md) vagy próbálja meg létrehozni az első logikai alkalmazást ebben a rövid útmutatóban: [Az első logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás HTTP-kérelmek küldésével történő aktiválásához használjon egy eszközt, például a [Postman asztali alkalmazást.](https://www.getpostman.com/apps)

## <a name="migrate-by-using-a-script"></a>Áttelepítés parancsfájl használatával

Minden ütemező feladat egyedi, így nincs univerzális eszköz az Ütemező-feladatok Azure Logic Apps-be való áttelepítéséhez. Ezt a [parancsfájlt](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) azonban az igényeinek megfelelően szerkesztheti.

## <a name="schedule-one-time-jobs"></a>Egyszeri feladatok ütemezése

Több egyszeri feladatok futtatásához csak egyetlen logikai alkalmazás létrehozásával.

1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a Logic App Designerben.

   Az alapvető lépésekhez kövesse a [Rövid útmutató: Az első logikai alkalmazás létrehozása című útmutatót.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. A keresőmezőbe írja `when a http request` be a Kérelem eseményindító megkereséséhez. Az eseményindítók listájából válassza ezt az eseményindítót: **HTTP-kérelem érkezésekor**

   !["Kérés" eseményindító hozzáadása](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. A kérelem eseményindító, opcionálisan egy JSON-séma, amely segít a Logic App Designer megérteni a szerkezet a bemenetek a kérelem eseményindító, és megkönnyíti a kimenetek kiválasztásához később a munkafolyamatban.

   A **Kérelem törzse JSON-séma** mezőbe írja be a sémát, például:

   ![Séma kérése](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Ha nem rendelkezik sémával, de json formátumú minta hasznos adattal rendelkezik, ebből a hasznos adatból létrehozhat egy sémát.

   1. A kérelem eseményindító, válassza **a Minta hasznos adat létrehozása séma.**

   1. Az **Enter (JSON-tartalom) megadása vagy beillesztése**csoportban adja meg a minta hasznos adatát, és válassza a **Kész**lehetőséget, például:

      ![Minta hasznos teher](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Az eseményindító alatt válassza a **Következő lépés lehetőséget.**

1. A keresőmezőbe írja `delay until` be szűrőként. A műveletek listájában válassza a következő műveletet: **Késleltetés**

   Ez a művelet szünetelteti a logikai alkalmazás munkafolyamatát egy megadott dátumig és időpontig.

   !["Késleltetés-ig" művelet hozzáadása](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Adja meg az időbélyeget, ha el szeretné indítani a logikai alkalmazás munkafolyamatát.

   Amikor az **Időbélyeg** mezőre kattint, megjelenik a dinamikus tartalomlista, így tetszés szerint kiválaszthat egy kimenetet az eseményindítóból.

   ![Adja meg a "Késleltetés a míg" részleteket](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adja hozzá a futtatni kívánt egyéb műveleteket [több száz használatra kész összekötő](../connectors/apis-list.md)közül való kijelöléssel.

   Felvehet például egy HTTP-műveletet, amely kérést küld egy URL-címre, vagy olyan műveleteket, amelyek a tárolási várólistákkal, a Service Bus-várólistákkal vagy a Service Bus-témakörökkel működnek:

   ![HTTP-művelet](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Ha elkészült, mentse a logikai alkalmazást.

   ![A logikai alkalmazás mentése](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Amikor először menti a logikai alkalmazást, a logikai alkalmazás kéréseseményindítójának végponti URL-címe megjelenik a **HTTP POST URL-címe** mezőben. Ha meg szeretné hívni a logikai alkalmazást, és adatokat szeretne küldeni a logikai alkalmazásba feldolgozásra, használja ezt az URL-címet a hívás célhelyeként.

   ![A Kérelem eseményindítójának url-címe](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Másolja és mentse ezt a végpont URL-címet, így később manuális kérelmet küldhet, amely elindítja a logikai alkalmazást.

## <a name="start-a-one-time-job"></a>Egyszeri feladat indítása

Egyszeri feladat manuális futtatásához vagy aktiválásához küldjön hívást a logikai alkalmazás kérelemeseményindítójának végpontURL-címére. Ebben a hívásban adja meg a küldő bemenetet vagy hasznos adatot, amelyet korábban leírt egy séma megadásával.

A Postman alkalmazás használatával például létrehozhat egy POST-kérelmet a mintához hasonló beállításokkal, majd a **Küldés** gombra a kérelem benyújtásához.

| Kérelem módszere | URL-cím | Törzs | Fejlécek |
|----------------|-----|------|---------|
| **POST** | <*végpont-URL*> | **Nyers** <p>**JSON(alkalmazás/json)** <p>A **nyers** mezőbe írja be a kérelmet küldő hasznos adat. <p>**Megjegyzés:** Ez a beállítás automatikusan konfigurálja a **Fejlécek értékeit.** | **Kulcs**: Tartalomtípus <br>**Érték**: alkalmazás/json |
|||||

![A logikai alkalmazás manuális aktiválására irányuló kérelem küldése](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

A hívás elküldése után a logikai alkalmazás válasza a **Törzs** lap **nyers** mezője alatt jelenik meg. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Ha később szeretné megszakítani a feladatot, válassza a **Fejlécek** lapot. **x-ms-workflow-run-id** 
>
> ![Válasz](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Egyszeri feladat megszakítása

A Logic Apps minden egyszeri feladat egyetlen logikai alkalmazás futtatási példányként hajtható végre. Egyszeri feladat megszakításához [használhatja a Munkafolyamat-futtatások – Mégse](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) a Logic Apps REST API-ban. Amikor hívást küld az eseményindítónak, adja meg a [munkafolyamat-futtatási azonosítót.](#workflow-run-id)

## <a name="schedule-recurring-jobs"></a>Ismétlődő feladatok ütemezése

1. Az [Azure Portalon](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a Logic App Designerben.

   Az alapvető lépésekhez kövesse a [Rövid útmutató: Az első logikai alkalmazás létrehozása című útmutatót.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. A keresőmezőbe írja be szűrőként az "ismétlődés" szót. Az eseményindítók listájában jelölje ki ezt az eseményindítót: **Ismétlődés**

   !["Ismétlődés" eseményindító hozzáadása](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Ha szeretné, állítson be egy fejlettebb ütemezést.

   ![Speciális ütemezés](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   A speciális ütemezési beállításokról az [Ismétlődő feladatok és munkafolyamatok létrehozása és futtatása az Azure Logic Apps alkalmazásokkal című témakörben talál](../connectors/connectors-native-recurrence.md)további információt.

1. További műveletek hozzáadásához [válasszon több száz használatra kész](../connectors/apis-list.md). Az eseményindító alatt válassza a **Következő lépés lehetőséget.** Keresse meg és jelölje ki a kívánt műveleteket.

   Felvehet például egy HTTP-műveletet, amely kérést küld egy URL-címre, vagy olyan műveleteket, amelyek a tárolási várólistákkal, a Service Bus-várólistákkal vagy a Service Bus-témakörökkel működnek:

   ![HTTP-művelet](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Ha elkészült, mentse a logikai alkalmazást.

   ![A logikai alkalmazás mentése](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Speciális beállítás

Az alábbiakban további módszereket is testreszabhat a feladatok testreszabásához.

### <a name="retry-policy"></a>Újrapróbálkozási szabályzat

Ha szabályozni szeretné, hogy egy művelet időnkénti hibák esetén hogyan próbálja meg újrafuttatni a logikai alkalmazást, beállíthatja az [újrapróbálkozási szabályzatot](../logic-apps/logic-apps-exception-handling.md#retry-policies) az egyes műveletek beállításaiban, például:

1. Nyissa meg a művelet három pontra (**...**) menüjét, és válassza a **Beállítások lehetőséget.**

   ![Műveletbeállítások megnyitása](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Válassza ki a kívánt újrapróbálkozási házirendet. Az egyes házirendekről az [Újraházirendek](../logic-apps/logic-apps-exception-handling.md#retry-policies)című témakörben talál további információt.

   ![Újrapróbálkozási házirend kiválasztása](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Kivételek és hibák kezelése

Az Azure Scheduler, ha az alapértelmezett művelet nem fut, futtathat egy módosító művelet, amely kikezeli a hiba feltételét. Az Azure Logic Apps, akkor is ugyanazt a feladatot.

1. A Logic App Designer programban a kezelni kívánt művelet felett vigye az egérmutatót a lépések közötti nyíl fölé, és válassza **a Párhuzamos ág hozzáadása**lehetőséget.

   ![Párhuzamos elágazás hozzáadása](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Keresse meg és jelölje ki azt a műveletet, amelyet alternatív műveletként szeretne futtatni.

   ![Párhuzamos művelet hozzáadása](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Az alternatív műveletben nyissa meg a három pont (**...**) menüt, és válassza a **Futtatás beállítása után**lehetőséget.

   ![Futás konfigurálása után](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Törölje a sikeres **tulajdonság** jelölőnégyzetét. Válassza ki a következő tulajdonságokat: **sikertelen,** a program kihagyja a **programot,** és **időkimenőt kapott**

   !["Futtatás után" tulajdonságok beállítása](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Ha elkészült, válassza a **Kész** lehetőséget.

A kivételkezelésről a [Hibák és kivételek kezelése – RunAfter tulajdonság további információja.](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior)

## <a name="faq"></a>GYIK

<a name="retire-date"></a>

**K**: Mikor vonul vissza az Azure Scheduler? <br>
**A**: Az Azure Scheduler a tervek szerint 2019. Az ezen időpont előtt tett fontos lépéseket és a részletes [ütemtervet lásd: Az ütemező nyugdíjazási dátumának meghosszabbítása 2019.](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/) Az általános frissítéseket az [Azure-frissítések – Ütemező témakörben taszják.](https://azure.microsoft.com/updates/?product=scheduler)

**K**: Mi történik a munkagyűjteményeimmel és a feladataimmal a szolgáltatás megszűnése után? <br>
**A**: Az összes ütemező feladatgyűjtemény és feladat futása leáll, és törlődik a rendszerből.

**K:** Biztonsági másolatot kell csinálnom róluk, vagy bármilyen más feladatot kell végrehajtanom, mielőtt áttelepítem az ütemezői feladataimat a Logikai alkalmazásokba? <br>
**V:** Ajánlott eljárásként mindig biztonsági másolatot kell kapnia a munkájáról. Ellenőrizze, hogy a létrehozott logikai alkalmazások a várt módon futnak-e az ütemező feladatok törlése vagy letiltása előtt.

**K**: Van olyan eszköz, amely segíthet áttelepíteni a munkámat az ütemezőről a Logikai alkalmazásokra? <br>
**A**: Minden ütemező feladat egyedi, így egy univerzális eszköz nem létezik. Az igények alapján azonban [szerkesztheti ezt a parancsfájlt az Azure Scheduler-feladatok Azure Logic Apps-be való áttelepítéséhez.](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)

**K**: Hol kaphatok támogatást az ütemezői feladatok áttelepítéséhez? <br>
**A**: Íme néhány módszer a támogatás hozására:

**Azure-portál**

Ha az Azure-előfizetés rendelkezik egy fizetős támogatási csomag, létrehozhat egy technikai támogatási kérelmet az Azure Portalon. Ellenkező esetben másik támogatási lehetőséget is választhat.

1. Az [Azure Portal](https://portal.azure.com) főmenüjében válassza a **Súgó + támogatás**lehetőséget.

1. A **Támogatás** menüben válassza az **Új támogatási kérelem lehetőséget.** Adja meg ezt az információt a kéréshez:

   | Tulajdonság | Érték |
   |---------|-------|
   | **Probléma típusa** | **Műszaki** |
   | **Előfizetés** | <*Azure-előfizetése*> |
   | **Service** | A **Figyelés & kezelése csoportban**válassza **az Ütemező**lehetőséget. Ha nem találja **az Ütemező elemet,** először válassza a **Minden szolgáltatás** lehetőséget. |
   ||| 

1. Válassza ki a kívánt támogatási lehetőséget. Ha fizetős támogatási csomaggal rendelkezik, válassza a **Tovább**gombot.

**Közösség**

* [Az Azure Logic Apps fóruma](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>További lépések

* [Az Azure Logic Apps alkalmazásokkal rendszeresen futó feladatokat és munkafolyamatokat hozhat létre](../connectors/connectors-native-recurrence.md)