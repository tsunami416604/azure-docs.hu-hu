---
title: Csatlakozás az Azure Event Hubs – Azure Logic Apps
description: Felügyelheti és figyelheti az eseményeket az Azure Event Hubs és Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 882bae14678d8bfff15b35c63c666a20aeee3d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720042"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Figyelheti, fogadhatja és események küldése az Azure Event Hubs és Azure Logic Apps

Ez a cikk bemutatja, hogyan figyelheti és kezelheti a küldött események [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) a belül egy logikai alkalmazást az Azure Event Hubs-összekötővel. Ezzel a módszerrel, amely a feladatok és ellenőrzése, küldése és események fogadása az Event Hub munkafolyamatok automatizálása a logic apps is létrehozhat. Összekötő-specifikus technikai tudnivalókért tekintse meg a [összekötő-referencia az Azure Event Hubs](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Egy [Azure Event Hubs-névtér és Eseményközpont](../event-hubs/event-hubs-create.md)

* A logikai alkalmazás, ahol szeretné elérni az Eseményközpont. A logikai alkalmazás elindításához egy Azure Event Hubs-eseményindítóval kell egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Ellenőrizze az engedélyeit, és a kapcsolati sztring lekérése

A logikai alkalmazás eléréséhez az Eseményközpontok felé ellenőrizze az engedélyeit, és a kapcsolati sztring lekérése az Event Hubs-névtér.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg az Event Hubs *névtér*, nem egy adott Eseményközpontban. 

1. A névtér menü alatt **beállítások**válassza **megosztott elérési házirendek**. Alatt **jogcímek**, ellenőrizze, hogy rendelkezik **kezelés** engedélyeket a névtér számára.

   ![Az Event Hubs-névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Ha szeretné később manuálisan adja meg a kapcsolat adatait, a kapcsolati sztring lekérése az Event Hubs-névtér.

   1. A **házirend**, válassza a **RootManageSharedAccessKey**.

   1. Keresse meg az elsődleges kulcs kapcsolati karakterláncot. Válassza a Másolás gombot, és mentse későbbi használatra a kapcsolati karakterláncot.

      ![Az Event Hubs-névtér kapcsolati karakterlánc másolása](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Győződjön meg arról, hogy a kapcsolati karakterlánc társítva, az Event Hubs-névtér, vagy egy adott eseményközpontban, ellenőrizze a kapcsolati karakterlánc nem rendelkezik a `EntityPath`  paraméter. Ha ezt a paramétert, a kapcsolati karakterláncot egy adott eseményközpont "entitás" és nem a megfelelő karakterlánc használata a logikai alkalmazást.

1. Most már folytathatja a [egy Event Hubs-eseményindító hozzáadása](#add-trigger) vagy [az Event Hubs művelet hozzáadása](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Az Event Hubs-eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

Ez a példa bemutatja, hogy miként indítható el a logikai alkalmazás munkafolyamatának új eseményeket az eseményközpontjába elküldésekor. 

1. Az Azure Portalon vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logic Apps Designerben. Ebben a példában az Azure Portalt használja.

1. A Keresés mezőbe írja be "az event hubs" szűrőként. Az eseményindítók listában jelölje ki az eseményindító: **Amikor események válnak elérhetővé az Eseményközpontban – Event Hubs**

   ![Trigger kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Ha a kapcsolat részleteivel, kér [mostantól az Event Hubs-kapcsolat létrehozása](#create-connection). 

1. Az eseményindító az Eseményközpontba, amely a figyelni kívánt információt nyújtanak. Nyissa meg a további tulajdonságok a **új paraméter hozzáadása** listája. A trigger kártyán paraméter kiválasztása hozzáadja a tulajdonságot.

   ![Eseményindító tulajdonságai](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Eseményközpont neve** | Igen | Az Event Hubs, amely a figyelni kívánt nevét |
   | **Tartalom típusa** | Nem | A tartalom eseménytípus. A mező alapértelmezett értéke: `application/octet-stream`. |
   | **Fogyasztói csoport neve** | Nem | A [az Eseményközpontbeli fogyasztói csoport neve](../event-hubs/event-hubs-features.md#consumer-groups) használandó események olvasását. Ha nincs megadva, az alapértelmezett felhasználói csoport használja. |
   | **Események maximális száma** | Nem | Események maximális száma. A trigger között egyet, és ez a tulajdonság által megadott események számát adja vissza. |
   | **Intervallum** | Igen | Pozitív egész szám, amely leírja, hogy milyen gyakran a munkafolyamat futtatása gyakorisága alapján. |
   | **Gyakoriság** | Igen | Az ismétlődés időegység |
   ||||

   **További tulajdonságok**

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **A tartalomséma** | Nem | A JSON tartalom sémáját az Event Hubs olvasni az eseményeket. Például ha megadja a tartalomséma, is beállíthat a logikai alkalmazás csak a séma megfelelő események. |
   | **Minimális partíciókulcs** | Nem | Adja meg a minimális [partíció](../event-hubs/event-hubs-features.md#partitions) olvasható azonosítója. Alapértelmezés szerint az összes partíció olvasható. |
   | **Maximális partíciókulcs** | Nem | Adja meg a maximális [partíció](../event-hubs/event-hubs-features.md#partitions) olvasható azonosítója. Alapértelmezés szerint az összes partíció olvasható. |
   | **Időzóna** | Nem | Érvényes csak akkor, ha megadja a kezdési időt, mert ez az eseményindító nem fogadja el az UTC-eltérés. Válassza ki az időzónát, amely a alkalmazni szeretné. <p>További információkért lásd: [létrehozása és az ismétlődő feladatokat futtató és az Azure Logic Apps munkafolyamat](../connectors/connectors-native-recurrence.md). |
   | **Kezdési idő** | Nem | Adja meg a kezdési időt a következő formátumban: <p>ÉÉÉÉ-hh-nnTóó: pp: Ha egy időzóna<p>– vagy –<p>ÉÉÉÉ-hh-DDThh:mm:ssZ, ha nem adja meg a időzóna<p>További információkért lásd: [létrehozása és az ismétlődő feladatokat futtató és az Azure Logic Apps munkafolyamat](../connectors/connectors-native-recurrence.md). |
   ||||

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

1. Most már folytathatja, egy vagy több művelet hozzáadása a logikai alkalmazáshoz, a feladatok a trigger eredményekkel végrehajtására vonatkozó szándékát. 

   Például használatával szűrhetők az események egy adott értéket, például egy kategória alapján adhat hozzá egy feltételt, hogy a **esemény - Küldés az Event Hubs** művelet csak olyan események, amelyek megfelelnek a feltételnek küld. 

> [!NOTE]
> Minden Event Hubs-eseményindítók *hosszú lekérdezéseket* eseményindítók, ami azt jelenti, hogy a csomag trigger akkor aktiválódik, amikor az eseményindító minden eseményt dolgoz fel, és jelennek meg az Eseményközpont további események 30 másodpercet vár, hogy.
> Ha nincsenek események 30 másodperc, a rendszer kihagyta a trigger futtatása. Ellenkező esetben az eseményindító továbbra is fennáll, addig, amíg az Event Hubs szolgáltatás üres események olvasását.
> A következő eseményindító-lekérdezés, amely az eseményindító tulajdonságaiban megadott ismétlődési időköze alapján történik.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Az Event Hubs-művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ebben a példában a logikai alkalmazás elindul, ellenőrzi, hogy az eseményközpont új eseményt az Event Hubs eseményindítóval.

1. Az Azure Portalon vagy a Visual Studióban nyissa meg a logikai alkalmazás a Logic Apps Designerben. Ebben a példában az Azure Portalt használja.

1. A trigger vagy művelet alatt válassza **új lépés**.

   Meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó nyílra. 
   Válassza a plusz jelre ( **+** ), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A Keresés mezőbe írja be "az event hubs" szűrőként.
A műveletek listából válassza a következő műveletet: **Esemény - Küldés az Event Hubs**

   !["Esemény küldése" művelet kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Ha a kapcsolat részleteivel, kér [mostantól az Event Hubs-kapcsolat létrehozása](#create-connection). 

1. A művelet a küldeni kívánt események adatainak megadása. Nyissa meg a további tulajdonságok a **új paraméter hozzáadása** listája. A művelet kártya kiválasztása egy paraméter hozzáadja a tulajdonságot.

   ![Válassza ki az Eseményközpont nevét, és adja meg az események](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Tulajdonság | Szükséges | Leírás |
   |----------|----------|-------------|
   | **Eseményközpont neve** | Igen | Az Event Hubs, ahol szeretné küldeni az esemény |
   | **Tartalom** | Nem | A tartalom a küldeni kívánt esemény |
   | **Tulajdonságok** | Nem | Az alkalmazás tulajdonságainak és -azonosítókkal való küldése |
   | **Partíciókulcs** | Nem | A [partíció](../event-hubs/event-hubs-features.md#partitions) , amelyre az esemény azonosítója |
   ||||

   Például elküldheti a kimenetet az Event Hubs-trigger által az Eseményközpontba egy másik:

   ![Példa esemény küldése](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Az Eseményközpont csatlakozni

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Amikor a kapcsolati adatokat kér, adja meg ezeket az adatokat:

   | Tulajdonság | Szükséges | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A neve, a kapcsolat létrehozása |
   | **Event Hubs-Namespace** | Igen | <*event-hubs-namespace*> | Válassza ki az Event Hubs-névtér létrehozásához használni szeretne. |
   |||||  

   Példa:

   ![Event Hub-kapcsolat létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Manuálisan adja meg a kapcsolati karakterláncot, válassza a **kapcsolatadatok manuális megadása**. 
   Ismerje meg, [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).

2. Az Event Hubs használni kívánt házirendet, válassza ki, ha még nem lenne kiválasztva. Válassza a **Létrehozás** elemet.

   ![Event Hub-kapcsolat, 2. rész létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Miután létrehozta a kapcsolatot, folytassa [hozzáadása az Event Hubs-trigger](#add-trigger) vagy [művelet hozzáadása az Event Hubs](#add-action).

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részletekért, például a triggereket, műveletek és -korlátok, leírtak szerint az összekötő OpenAPI (korábbi nevén Swagger) fájl, tekintse meg a [összekötő referenciájának oldalát](/connectors/eventhubs/).

## <a name="next-steps"></a>További lépések

További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)