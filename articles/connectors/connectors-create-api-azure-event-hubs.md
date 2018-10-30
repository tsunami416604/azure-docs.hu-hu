---
title: Csatlakozás az Azure Event Hubs – Azure Logic Apps |} A Microsoft Docs
description: Felügyelheti és figyelheti az eseményeket az Azure Event Hubs és Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: a91daf08a56470e4d1e112e37b51150c2c5f00ef
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230318"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Figyelheti, fogadhatja és események küldése az Azure Event Hubs és Azure Logic Apps 

Ez a cikk bemutatja, hogyan figyelheti és kezelheti a küldött események [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) a belül egy logikai alkalmazást az Azure Event Hubs-összekötővel. Ezzel a módszerrel, amely a feladatok és ellenőrzése, küldése és események fogadása az Event Hub munkafolyamatok automatizálása a logic apps is létrehozhat. 

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Összekötő-specifikus technikai tudnivalókért tekintse meg a <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">összekötő-referencia az Azure Event Hubs</a>.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure Event Hubs-névtér és Eseményközpont](../event-hubs/event-hubs-create.md)

* A logikai alkalmazás, ahol szeretné elérni az Eseményközpont. A logikai alkalmazás elindításához egy Azure Event Hubs-eseményindítóval kell egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Ellenőrizze az engedélyeit, és a kapcsolati sztring lekérése

A logikai alkalmazás eléréséhez az Eseményközpontok felé ellenőrizze az engedélyeit, és a kapcsolati sztring lekérése az Event Hubs-névtér.

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>. 

2. Nyissa meg az Event Hubs *névtér*, nem egy adott Eseményközpontban. A névtér lapon alatt **beállítások**, válassza a **megosztott elérési házirendek**. Alatt **jogcímek**, ellenőrizze, hogy rendelkezik **kezelés** engedélyeket a névtér számára.

   ![Az Event Hubs-névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Ha szeretné később manuálisan adja meg a kapcsolat adatait, a kapcsolati sztring lekérése az Event Hubs-névtér. 

   1. A **házirend**, válassza a **RootManageSharedAccessKey**. 

   2. Keresse meg az elsődleges kulcs kapcsolati karakterláncot. Válassza a Másolás gombot, és mentse későbbi használatra a kapcsolati karakterláncot.

      ![Az Event Hubs-névtér kapcsolati karakterlánc másolása](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Győződjön meg arról, hogy a kapcsolati karakterlánc társítva, az Event Hubs-névtér, vagy egy adott eseményközpontban, ellenőrizze a kapcsolati karakterlánc nem rendelkezik a `EntityPath`  paraméter. Ha ezt a paramétert, a kapcsolati karakterláncot egy adott eseményközpont "entitás" és nem a megfelelő karakterlánc használata a logikai alkalmazást.

4. Most már folytathatja a [egy Event Hubs-eseményindító hozzáadása](#add-trigger) vagy [az Event Hubs művelet hozzáadása](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Az Event Hubs-eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

Ez a példa bemutatja, hogy miként indítható el a logikai alkalmazás munkafolyamatának új eseményeket az eseményközpontjába elküldésekor. 

1. Az Azure Portalon vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logic Apps Designerben. Ebben a példában az Azure Portalt használja.

2. A Keresés mezőbe írja be "az event hubs" szűrőként. Az eseményindítók listában jelölje ki a kívánt eseményindító. 

   Ebben a példában ez az eseményindító: 
   **Event Hubs – amikor események válnak elérhetővé az Eseményközpontban**

   ![Trigger kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Ha a kapcsolat részleteivel, kér [mostantól az Event Hubs-kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a szükséges információkat az eseményindító.

   1. Az a **Eseményközpont neve** listájához, válassza ki a figyelni kívánt Eseményközpontot.

      ![Adja meg az Eseményközpont vagy felhasználói csoport](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Válassza ki a időközét és gyakoriságát, hogy milyen gyakran szeretné ellenőrizni az Event Hubs az eseményindító.
 
   3. Igény szerint válassza ki, a speciális eseményindító-beállítások némelyikét, válassza a **speciális beállítások megjelenítése**.
   
      ![Speciális beállítások eseményindító](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Tulajdonság | Részletek | 
      |----------|---------| 
      | Tartalom típusa  | Válassza ki az esemény tartalom típusát. Az alapértelmezett érték "application/octet-stream". |
      | A tartalomséma | Adja meg a tartalom séma a JSON-ban az eseményeket, olvassa az Event Hubs a rendszer. |
      | Fogyasztói csoport neve | Adja meg az Event Hubs [fogyasztói csoport neve](../event-hubs/event-hubs-features.md#consumer-groups) az események olvasását. Ha nincs megadva, az alapértelmezett felhasználói csoport használja. |
      | Minimális partíciókulcs | Adja meg a minimális [partíció](../event-hubs/event-hubs-features.md#partitions) olvasható azonosítója. Alapértelmezés szerint az összes partíció olvasható. |
      | Maximális partíciókulcs | Adja meg a maximális [partíció](../event-hubs/event-hubs-features.md#partitions) olvasható azonosítója. Alapértelmezés szerint az összes partíció olvasható. |
      | Események maximális száma | Adjon meg egy értéket az események maximális száma. A trigger között egyet, és ez a tulajdonság által megadott események számát adja vissza. |
      |||

4. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

5. Most már folytathatja, egy vagy több művelet hozzáadása a logikai alkalmazáshoz, a feladatok a trigger eredményekkel végrehajtására vonatkozó szándékát.

> [!NOTE]
> Minden Event Hubs-eseményindítók *hosszú lekérdezéseket* eseményindítók, ami azt jelenti, hogy a csomag trigger akkor aktiválódik, amikor az eseményindító minden eseményt dolgoz fel, és jelennek meg az Eseményközpont további események 30 másodpercet vár, hogy.
> Ha nincsenek események 30 másodperc, a rendszer kihagyta a trigger futtatása. Ellenkező esetben az eseményindító továbbra is fennáll, addig, amíg az Event Hubs szolgáltatás üres események olvasását.
> A következő eseményindító-lekérdezés, amely az eseményindító tulajdonságaiban megadott ismétlődési időköze alapján történik.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Az Event Hubs művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ebben a példában a logikai alkalmazás elindul, ellenőrzi, hogy az eseményközpont új eseményt az Event Hubs eseményindítóval. 

1. Az Azure Portalon vagy a Visual Studióban nyissa meg a logikai alkalmazás a Logic Apps Designerben. Ebben a példában az Azure Portalt használja.

2. A trigger vagy művelet alatt válassza **új lépés** > **művelet hozzáadása**.

   Meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó nyílra. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza **művelet hozzáadása**.

3. A Keresés mezőbe írja be "az event hubs" szűrőként.
Jelölje ki az elvégzendő műveletek listájában. 

   Ebben a példában válassza a következő műveletet: **az Event Hubs - küldési esemény**

   !["Az Event Hubs - küldési esemény" kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Ha a kapcsolat részleteivel, kér [mostantól az Event Hubs-kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a művelet a szükséges információkat.

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------|
   | Eseményközpont neve | Igen | Válassza ki az Event Hubs, ahol szeretné küldeni az esemény | 
   | Események | Nem | A tartalom a küldeni kívánt esemény | 
   | Tulajdonságok | Nem | Az alkalmazás tulajdonságainak és -azonosítókkal való küldése | 
   |||| 

   Példa: 

   ![Válassza ki az Eseményközpont nevét, és adja meg az események](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Az Eseményközpont csatlakozni

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Amikor a kapcsolati adatokat kér, adja meg ezeket az adatokat:

   | Tulajdonság | Szükséges | Érték | Leírás | 
   |----------|----------|-------|-------------|
   | Kapcsolat neve | Igen | <*kapcsolat neve*> | A neve, a kapcsolat létrehozása |
   | Event Hubs-névtér | Igen | <*Event-hubs-névtér*> | Válassza ki az Event Hubs-névtér létrehozásához használni szeretne. | 
   |||||  

   Példa:

   ![Event Hub-kapcsolat létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Manuálisan adja meg a kapcsolati karakterláncot, válassza a **kapcsolatadatok manuális megadása**. 
   Ismerje meg, [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).

2. Az Event Hubs használni kívánt házirendet, válassza ki, ha még nem lenne kiválasztva. Válassza a **Létrehozás** elemet.

   ![Event Hub-kapcsolat, 2. rész létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Miután létrehozta a kapcsolatot, folytassa [hozzáadása az Event Hubs-trigger](#add-trigger) vagy [művelet hozzáadása az Event Hubs](#add-action).

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részleteket, például a triggereket, műveletek és -korlátok, az összekötő Swagger-fájl által leírt: a [összekötő referencialapja](/connectors/eventhubs/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)