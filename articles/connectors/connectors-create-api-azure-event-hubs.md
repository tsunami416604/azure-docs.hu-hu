---
title: Csatlakozás az Azure Event hubs - Azure Logic Apps |} Microsoft Docs
description: Kezelni és megfigyelni az Azure Event Hubs és az Azure Logic Apps események
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
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294972"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Figyelheti, fogadására és küldi az eseményeket az Azure Event Hubs és az Azure Logic Apps 

Ez a cikk bemutatja, hogyan figyelheti és kezelheti a küldött események [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) a belül egy logikai alkalmazást az Azure Event Hubs-összekötővel együtt. Ily módon a logic apps, feladatok és a munkafolyamatokat a ellenőrzése, küldése és események fogadása az Eseményközpont automatizáló is létrehozhat. 

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. Ha most ismerkedik a logic apps, tekintse át a [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [gyors üzembe helyezés: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Összekötő-specifikus műszaki információkért lásd: a <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">Azure Event Hubs összekötő hivatkozás</a>.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure Event Hubs-névteret és az Event Hubs](../event-hubs/event-hubs-create.md)

* A logikai alkalmazást, ahová az Eseményközpont eléréséhez. Az Azure Event Hubs eseményindító a Logic Apps alkalmazást elindításához szükséges egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Ellenőrizze az engedélyeit és a kapcsolati karakterlánc beolvasása

A logikai alkalmazásnak az Eseményközpont eléréséhez ellenőrizze az engedélyeit, és a kapcsolati karakterlánc beolvasása az Event Hubs-névtérhez.

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>. 

2. Nyissa meg az Event hubs *névtér*, nem egy adott Eseményközpontban. A névtér lapon alatt **beállítások**, válassza a **megosztott elérési házirendek**. A **jogcímek**, ellenőrizze, hogy rendelkezik **kezelése** engedélyeket a névtérhez.

   ![Az Event Hubs névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Ha azt szeretné, hogy később manuálisan adja meg a kapcsolati adatokat, a kapcsolati karakterlánc beolvasása az Event Hubs névtér. 

   1. A **házirend**, válassza a **RootManageSharedAccessKey**. 

   2. Keresse meg az elsődleges kulcs kapcsolati karakterláncot. Válassza a Másolás gombra, és mentse a kapcsolati karakterlánc későbbi használatra.

      ![Másolja az Event Hubs névtér kapcsolati karakterláncot](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Győződjön meg arról, hogy a kapcsolati karakterlánc tartozik az Event Hubs névtér vagy egy adott eseményközpont, ellenőrizze, hogy a kapcsolati karakterlánc nem rendelkezik a `EntityPath` paraméter. Ha ezt a paramétert, a kapcsolati karakterlánc az egy adott Eseményközpontban "entitás" pedig nem a megfelelő karakterlánc használata a Logic Apps alkalmazást.

4. Most már folytathatja a [adja hozzá az Event Hubs eseményindító](#add-trigger) vagy [új Event Hubs művelet](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Az Event Hubs eseményindító hozzáadása

Az Azure Logic Apps, minden logikai alkalmazást be kell kezdődnie egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor indul, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító következik be, a Logic Apps motor hoz létre egy logic app-példány, az alkalmazás a munkafolyamat futásának indításakor.

Ez a példa bemutatja, hogyan megkezdheti a logic app munkafolyamat új eseményeket az eseményközpontjába elküldésekor. 

1. Az Azure portálon vagy a Visual Studio hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic Apps-tervezőben. A példa az Azure-portálon.

2. A keresési mezőbe írja be az "event hubs" szűrőként. Az eseményindítók listájából válassza ki a kívánt eseményindító. 

   Ez a példa az eseményindító: **Event Hubs - amikor események állnak rendelkezésre az Event Hubs**

   ![Trigger kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Ha a kapcsolat részletes számítógép [most az Event Hubs-kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a szükséges adatokat az eseményindító.

   1. Az a **Eseményközpont nevét** listára, válassza ki a figyelni kívánt Eseményközpontot.

      ![Adja meg az Event Hubs vagy felhasználói csoport](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Válassza ki az időköz és gyakoriságát, hogy milyen gyakran szeretné ellenőrizni az Event Hubs az eseményindító.
 
   3. Bejelölheti a speciális eseményindító beállítások egy része, válassza a **speciális beállítások megjelenítése**.
   
      ![Speciális beállítások eseményindító](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Tulajdonság | Részletek | 
      |----------|---------| 
      | Tartalom típusa  | Válassza ki az esemény tartalom típusát. Az alapértelmezett érték "application/octet-stream". |
      | A tartalomséma | Adja meg a tartalom séma a JSON-ban az eseményeket az eseményközpontból olvasható. |
      | Felhasználói csoport neve | Adja meg az Eseményközpont [fogyasztói csoportnév](../event-hubs/event-hubs-features.md#consumer-groups) események olvasása. Ha nincs megadva, az alapértelmezett felhasználói csoport szolgál. |
      | Minimális partíciókulcs | Adja meg a minimális [partíció](../event-hubs/event-hubs-features.md#partitions) azonosító olvasni. Alapértelmezés szerint minden olyan partíciónak olvasható. |
      | Maximális partíciókulcs | Adja meg azt a maximális [partíció](../event-hubs/event-hubs-features.md#partitions) azonosító olvasni. Alapértelmezés szerint minden olyan partíciónak olvasható. |
      | Események maximális száma | Adjon meg egy értéket események maximális száma. Az eseményindító visszaadása egy és a tulajdonság által megadott események száma. |
      |||

4. Amikor elkészült, a designer eszköztáron válassza **mentése**.

5. Most már folytathatja hozzáadása egy vagy több műveletet szeretne végezni az eseményindító eredményekkel feladatokhoz a Logic Apps alkalmazást.

> [!NOTE]
> Az Event Hubs indítók vannak *hosszú-lekérdezési* eseményindítók, amely azt jelenti, hogy egy eseményindító következik be, amikor az eseményindító dolgozza fel az eseményeket az Eseményközpont megjelenő további események 30 másodpercet várakozik.
> Ha nincsenek események 30 másodperc, a rendszer kihagyja a eseményindító Futtatás. Ellenkező esetben az eseményindító továbbra is fennáll, addig, amíg az Eseményközpont nem üres események olvasását.
> A következő eseményindító lekérdezési az ismétlődési meg az eseményindító tulajdonságait a alapján történik.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Az Event Hubs művelet hozzáadása

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamatban, amely egy eseményindító vagy egy másik művelet a következő lépés. Ebben a példában a logikai alkalmazást az Event Hubs eseményindító, amely ellenőrzi az új eseményeket az Eseményközpont kezdődik. 

1. Az Azure portálon vagy a Visual Studio nyissa meg a Logic Apps alkalmazást Logic Apps-tervezőben. A példa az Azure-portálon.

2. Az eseményindító vagy a művelet alatt válassza ki a **új lépés** > **művelet hozzáadása**.

   Meglévő lépései közötti művelet hozzáadása az egérmutatót a kapcsolódó nyílra. 
   Válassza ki a plusz jelre (**+**), amely akkor jelenik meg, és válassza a **művelet hozzáadása**.

3. A keresési mezőbe írja be az "event hubs" szűrőként.
Válassza ki az elvégzendő műveletek listában. 

   Ehhez a példához válassza ki az ezzel a művelettel: **Event Hubs - küldési esemény**

   ![Válassza ki az "Event Hubs - küldési esemény"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Ha a kapcsolat részletes számítógép [most az Event Hubs-kapcsolat létrehozása](#create-connection). Vagy, ha a kapcsolat már létezik, adja meg a művelet a szükséges információkat.

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------|
   | Eseményközpont neve | Igen | Válassza ki az Event Hubs, ahol szeretné elküldeni a esemény | 
   | Esemény tartalom | Nem | A tartalom a következő eseményhez szeretne küldeni | 
   | Tulajdonságok | Nem | Az alkalmazás tulajdonságainak és -azonosítókkal való küldése | 
   |||| 

   Példa: 

   ![Válassza ki az Eseményközpont nevét, és adja meg az esemény tartalom](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Amikor elkészült, a designer eszköztáron válassza **mentése**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Az Eseményközpont kapcsolódni

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Amikor a rendszer kéri a kapcsolati adatokat, meg az alábbi adatokat:

   | Tulajdonság | Szükséges | Érték | Leírás | 
   |----------|----------|-------|-------------|
   | Kapcsolat neve | Igen | <*kapcsolat-neve*> | A neve a kapcsolat létrehozása |
   | Event Hubs-névtér | Igen | <*az esemény-hubs-névtér*> | Válassza ki a használni kívánt Event Hubs-névteret. | 
   |||||  

   Példa:

   ![Eseményközpont kapcsolatának létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Manuálisan adja meg a kapcsolati karakterláncot, válassza a **manuálisan adja meg a kapcsolati adatokat**. 
   Ismerje meg, [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).

2. Az Event Hubs-házirendet, válassza ki, ha nincs bejelölve. Válassza a **Létrehozás** elemet.

   ![Eseményközpont kapcsolatának, 2. rész létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Miután létrehozta a kapcsolatot, folytassa [indul el, adja hozzá az Event Hubs](#add-trigger) vagy [adja hozzá az Event Hubs művelet](#add-action).

## <a name="connector-reference"></a>Összekötő-referencia

Technikai részletek, például eseményindítók műveletek vagy -korlátok, az összekötő a Swagger-fájl szerint, lásd: a [összekötő referencialapja](/connectors/eventhubs/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Tudnivalók más [Logic Apps-összekötők](../connectors/apis-list.md)