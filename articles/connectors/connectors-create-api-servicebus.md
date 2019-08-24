---
title: Üzenetek küldése és fogadása Azure Service Bus-Azure Logic Appssal | Microsoft Docs
description: Nagyvállalati Felhőbeli üzenetkezelés beállítása Azure Service Busekkel Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 944bac44c1fc6504dfe1a93df5760ccf4ee46fa0
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982212"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Exchange-üzenetek a felhőben Azure Service Bus és Azure Logic Apps

Az Azure Logic Appsszel és az Azure Service Bus-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek szervezetének adatait továbbítják, például értékesítési és vásárlási megrendeléseket, a naplókat vagy a leltármozgásokat. Az összekötő nem csak az üzenetek figyelését, küldését és kezelését végzi, hanem a várólistákkal, munkamenetekkel, témakörökkel, előfizetésekkel és egyéb műveletekkel is végrehajtja a műveleteket, például:

* A várólisták, témakörök és témakör-előfizetések fogadásának figyelése, amikor az üzenetek érkeznek (automatikusan befejeződik) vagy érkeznek (betekintési zárolás). 
* Üzenetek küldése.
* Témakör-előfizetések létrehozása és törlése.
* Kezelheti a várólisták és a témakör-előfizetések üzeneteit, például a Get, a késleltetett, a teljes, a késleltetés, a lemondás és a kézbesítetlen levelek.
* Üzenetek és munkamenetek zárolásának megújítása a várólistákban és a témakör-előfizetésekben.
* Munkamenetek lezárása a várólistákban és témakörökben.

Olyan eseményindítókat is használhat, amelyek Service Bus válaszokat kapnak, és elérhetővé teszik a kimenetet a logikai alkalmazások más műveletei számára. Más műveletek is használhatók Service Bus műveletek kimenetének használatával. Ha most ismerkedik Service Bus és Logic Apps, tekintse át a [Mi az Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) és [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Egy Service Bus névtér és üzenetküldési entitás, például egy üzenetsor. Ha nem rendelkezik ezekkel az elemekkel, megtudhatja, hogyan [hozhatja létre a Service Bus névteret és a várólistát](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Ezeknek az elemeknek ugyanabban az Azure-előfizetésben kell lenniük, mint az ezeket az elemeket használó logikai alkalmazások.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyben a Service Bus kívánja használni. A logikai alkalmazásnak ugyanabban az Azure-előfizetésben kell lennie, mint a Service Bus-nek. Ha Service Bus triggert szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Service Bus művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Engedélyek keresése

Győződjön meg arról, hogy a logikai alkalmazás rendelkezik a Service Bus névtér eléréséhez szükséges engedélyekkel. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Lépjen a Service Bus *névtérhez*. A névtér lap **Beállítások**területén válassza a **megosztott elérési házirendek**elemet. Ajogcímek területen győződjön meg arról, hogy rendelkezik az adott névtérhez tartozó jogosultságokkal.

   ![Service Bus névtér engedélyeinek kezelése](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. A Service Bus névtérhez tartozó kapcsolatok karakterláncának beolvasása. Erre a karakterláncra akkor van szükség, amikor megadja a kapcsolódási adatait a logikai alkalmazásban.

   1. Válassza ki **RootManageSharedAccessKey**. 
   
   1. Az elsődleges kapcsolódási karakterlánc mellett kattintson a Másolás gombra. Mentse a kapcsolatok karakterláncát későbbi használatra.

      ![Service Bus névtérbeli kapcsolatok karakterláncának másolása](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Annak ellenőrzéséhez, hogy a kapcsolódási karakterlánc társítva van-e a Service Bus névteréhez vagy egy üzenetküldési entitáshoz, például egy várólistához, keresse meg a `EntityPath`  paraméterhez tartozó kapcsolódási karakterláncot. Ha megtalálta ezt a paramétert, a kapcsolódási karakterlánc egy adott entitásra vonatkozik, és nem a megfelelő karakterláncot használja a logikai alkalmazáshoz.

## <a name="add-trigger-or-action"></a>Trigger vagy művelet hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Ha egy *triggert* üres logikai alkalmazáshoz szeretne adni, a keresőmezőbe írja be a "Azure Service Bus" kifejezést a szűrőként. Válassza ki a kívánt eseményindítót az eseményindítók listából. 

   Például a logikai alkalmazás aktiválásához, amikor új elem érkezik egy Service Bus várólistára, válassza ki ezt az triggert: **Üzenet várólistára érkezésekor (automatikus Befejezés)**

   ![Service Bus trigger kiválasztása](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Egyes eseményindítók egy vagy több üzenetet is visszaadhatnak, például az eseményindítót, **Amikor egy vagy több üzenet érkezik egy várólistába (automatikusan befejeződött)** . Ha ezek az eseményindítók elindítják a tüzet, az adott érték egy és az eseményindítók **maximális** számának tulajdonsága által megadott üzenetek számát adja vissza.

   Az *összes Service Bus eseményindító hosszú lekérdezési eseményindítók*, ami azt jelenti, hogy amikor az eseményindító tüzek, az eseményindító feldolgozza az összes üzenetet, majd 30 másodpercet vár, hogy további üzenetek jelenjenek meg a várólista vagy a témakör előfizetésében. 
   Ha 30 másodpercen belül nem jelenik meg üzenet, a rendszer kihagyja a trigger futtatását. 
   Ellenkező esetben az trigger folytatja az üzenetek olvasását, amíg a várólista vagy a témakör-előfizetés üres. A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési intervallumon alapul.

1. Ha *műveletet* szeretne hozzáadni egy meglévő logikai alkalmazáshoz, kövesse az alábbi lépéseket: 

   1. Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés**lehetőséget. 

      A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. 
      Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

   1. A keresőmezőbe írja be a "Azure Service Bus" kifejezést a szűrőként. 
   A műveletek listában válassza ki a kívánt műveletet. 
 
      Válassza például a következő műveletet: **Üzenet küldése**

      ![Service Bus művelet kiválasztása](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Ha első alkalommal csatlakoztatja a logikai alkalmazást a Service Bus-névtérhez, a Logic app Designer most kéri a kapcsolati adatok megadását. 

   1. Adja meg a kapcsolatok nevét, és válassza ki a Service Bus névteret.

      ![Service Bus kapcsolat létrehozása, 1. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Ha ehelyett manuálisan szeretné megadni a kapcsolódási karakterláncot, válassza a **kapcsolódási adatok manuális megadása**lehetőséget. 
      Ha nem rendelkezik a kapcsolódási karakterlánccal, Ismerje meg, [hogyan keresheti meg a kapcsolódási karakterláncot](#permissions-connection-string).

   1. Most válassza ki a Service Bus szabályzatot, majd válassza a **Létrehozás**lehetőséget.

      ![Service Bus kapcsolat létrehozása, 2. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Ebben a példában válassza ki a kívánt üzenetküldési entitást, például egy várólistát vagy egy témakört. Ebben a példában válassza ki a Service Bus várólistát. 
   
   ![Service Bus üzenetsor kiválasztása](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Adja meg az triggerhez vagy a művelethez szükséges adatokat. Ebben a példában kövesse az trigger vagy a művelet megfelelő lépéseit: 

   * **A minta trigger esetében**: Adja meg a lekérdezési időközt és a gyakoriságot a várólista ellenőrzéséhez.

     ![Lekérdezési időköz beállítása](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Ha elkészült, folytassa a logikai alkalmazás munkafolyamatának létrehozásával a kívánt műveletek hozzáadásával. Hozzáadhat például egy olyan műveletet, amely e-mailt küld, amikor új üzenet érkezik.
     Amikor a trigger ellenőrzi a várólistát, és új üzenetet talál, a logikai alkalmazás futtatja az észlelt üzenethez kiválasztott műveleteket.

   * **A minta művelethez**: Adja meg az üzenet tartalmát és a többi adatot. 

     ![Üzenet tartalmának és részleteinek megadása](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Ha elkészült, folytassa a logikai alkalmazás munkafolyamatának összeállításával a kívánt egyéb műveletek hozzáadásával. Hozzáadhat például egy olyan műveletet, amely e-mailt küld, amely megerősíti az üzenet küldését.

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

## <a name="connector-reference"></a>Összekötő-referencia

A Service Bus-összekötő egyszerre akár 1 500 egyedi munkamenetet is megtakaríthat a Service Bus és az összekötő gyorsítótára között. Ha a munkamenetek száma meghaladja ezt a korlátot, a rendszer eltávolítja a régi munkameneteket a gyorsítótárból. Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos egyéb technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/servicebus/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
