---
title: Kapcsolódás az Azure Event Hubshoz – Azure Logic Apps
description: Események kezelése és figyelése az Azure Event Hubs és Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 24f66782821f372f5c045dbb82db24fa8b6ad482
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051077"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Események figyelése, fogadása és küldése az Azure Event Hubs és Azure Logic Apps

Ez a cikk bemutatja, hogyan figyelheti és kezelheti az [azure Event Hubsnak](../event-hubs/event-hubs-what-is-event-hubs.md) eljuttatott eseményeket egy logikai alkalmazásból az Azure Event Hubs Connector használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják az események ellenőrzési, küldési és fogadási feladatait és munkafolyamatait az Event Hubon. Az összekötő-specifikus technikai információk az [Azure Event Hubs-összekötő dokumentációjában](https://docs.microsoft.com/connectors/eventhubs/)</a>olvashatók.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [Azure Event Hubs névtér és Event hub](../event-hubs/event-hubs-create.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni az Event hub-t. A logikai alkalmazás Azure Event Hubs triggerrel való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és [a gyors útmutató: Hozza létre az első logikai](../logic-apps/quickstart-create-first-logic-app-workflow.md)alkalmazását.

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Engedélyek keresése és a kapcsolatok karakterláncának beolvasása

Győződjön meg arról, hogy a logikai alkalmazás hozzáfér az Event hub-hoz, ellenőrizze az engedélyeket, és szerezze be a Event Hubs névtérhez tartozó kapcsolati karakterláncot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg aEvent Hubs névteret, nem egy adott Event hub-t. 

1. A névtér menü **Beállítások**területén válassza a **megosztott elérési házirendek**elemet. Ajogcímek területen győződjön meg arról, hogy rendelkezik az adott névtérhez tartozó jogosultságokkal.

   ![Az Event hub-névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Ha később manuálisan szeretné megadni a kapcsolódási adatokat, szerezze be a Event Hubs névtérhez tartozó kapcsolódási karakterláncot.

   1. A **házirend**területen válassza a **RootManageSharedAccessKey**lehetőséget.

   1. Keresse meg az elsődleges kulcshoz tartozó kapcsolatok sztringjét. Válassza a másolás gombot, és mentse a kapcsolatok karakterláncát későbbi használatra.

      ![Event Hubs névtérbeli kapcsolatok karakterláncának másolása](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Annak ellenőrzéséhez, hogy a kapcsolati karakterlánc társítva van-e a Event Hubs névteréhez vagy egy adott Event hub-hoz, győződjön `EntityPath`meg arról, hogy a kapcsolati karakterlánc nem rendelkezik a  paraméterrel. Ha ezt a paramétert találja, a kapcsolati karakterlánc egy adott Event hub "Entity" értékre vonatkozik, és nem a logikai alkalmazáshoz használandó helyes sztring.

1. Most folytassa a [Event Hubs-trigger hozzáadásával](#add-trigger) vagy [egy Event Hubs művelet hozzáadásával](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Event Hubs trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie [](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

Ebből a példából megtudhatja, hogyan indíthat el egy logikai alkalmazás-munkafolyamatot, amikor új eseményeket továbbítanak az Event hub-nak. 

1. A Azure Portal vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja Logic Apps designert. Ez a példa a Azure Portal használja.

1. A keresőmezőbe írja be szűrőként az "Event hubok" kifejezést. Az eseményindítók listából válassza ki a következő eseményindítót: **Ha események érhetők el az Event hub-Event Hubs**

   ![Trigger kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a Event Hubs](#create-connection)-kapcsolatát. 

1. Adja meg a figyelni kívánt Event hub adatait az Eseményindítóban. A további tulajdonságok megjelenítéséhez nyissa meg az **új paraméterek hozzáadása** listát. Egy paraméter kiválasztásával hozzáadja ezt a tulajdonságot az trigger kártyához.

   ![Eseményindító tulajdonságai](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Eseményközpont neve** | Igen | A figyelni kívánt Event hub neve |
   | **Tartalom típusa** | Nem | Az esemény tartalomtípusa. A mező alapértelmezett értéke: `application/octet-stream`. |
   | **Fogyasztói csoport neve** | Nem | Az események olvasásához használandó [Event hub-beli fogyasztói csoport neve](../event-hubs/event-hubs-features.md#consumer-groups) . Ha nincs megadva, a rendszer az alapértelmezett fogyasztói csoportot használja. |
   | **Események maximális száma** | Nem | Az események maximális száma. Az trigger az egyik és a tulajdonság által megadott események száma közötti értéket adja vissza. |
   | **Intervallum** | Igen | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján |
   | **Gyakoriság** | Igen | Az ismétlődés időegysége |
   ||||

   **További tulajdonságok**

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tartalmi séma** | Nem | Az Event hub-ból beolvasni kívánt események JSON-tartalmának sémája. Ha például megadja a tartalmi sémát, a logikai alkalmazást csak azokhoz az eseményekhez aktiválhatja, amelyek megfelelnek a sémának. |
   | **Minimális partíciós kulcs** | Nem | Adja meg az olvasni kívánt [partíció](../event-hubs/event-hubs-features.md#partitions) -azonosító minimális értéket. Alapértelmezés szerint a rendszer az összes partíciót beolvassa. |
   | **Partíció maximális kulcsa** | Nem | Adja meg az olvasni kívánt [partíció](../event-hubs/event-hubs-features.md#partitions) maximális azonosítóját. Alapértelmezés szerint a rendszer az összes partíciót beolvassa. |
   | **Időzóna** | Nem | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az trigger nem fogad el UTC-eltolást. Válassza ki az alkalmazni kívánt időzónát. <p>További információ: [ismétlődő feladatok és munkafolyamatok létrehozása és futtatása Azure Logic Appssal](../connectors/connectors-native-recurrence.md). |
   | **Kezdési idő** | Nem | Adja meg a kezdő időpontot a következő formátumban: <p>ÉÉÉÉ-hh-NNTóó: PP: mm, ha időzónát választ<p>– vagy –<p>ÉÉÉÉ-hh-NNTóó: PP: ssZ, ha nem jelöl ki időzónát<p>További információ: [ismétlődő feladatok és munkafolyamatok létrehozása és futtatása Azure Logic Appssal](../connectors/connectors-native-recurrence.md). |
   ||||

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

1. Most folytassa a logikai alkalmazáshoz egy vagy több művelet hozzáadását azokkal a feladatokkal, amelyeket el szeretne végezni az trigger eredményeivel. 

   Ha például egy adott érték (például egy kategória) alapján szeretne eseményeket szűrni, hozzáadhat egy feltételt, hogy az **esemény küldése** művelet csak azokat az eseményeket küldje el, amelyek megfelelnek a feltételnek. 

> [!NOTE]
> Az Event hub összes eseményindítója *hosszú lekérdezési* eseményindítókat használ, ami azt jelenti, hogy amikor egy eseményindító aktiválódik, az eseményindító feldolgozza az összes eseményt, majd 30 másodpercig várakozik, hogy további események jelenjenek meg az Event hub-ban.
> Ha 30 másodpercen belül nem érkezik esemény, a rendszer kihagyja a trigger futtatását. Ellenkező esetben az eseményindító addig folytatja az eseményeket, amíg az Event hub üres nem lesz.
> A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési időköz alapján történik.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Event Hubs művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás egy Event Hubs eseményindítóval kezdődik, amely az Event hub új eseményeit ellenőrzi.

1. A Azure Portal vagy a Visual Studióban nyissa meg a logikai alkalmazást Logic Apps Designerben. Ez a példa a Azure Portal használja.

1. Az trigger vagy a művelet alatt válassza az **új lépés**lehetőséget.

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. 
   Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "Event hubok" kifejezést.
A műveletek listából válassza ki ezt a műveletet: **Esemény küldése – Event Hubs**

   ![Az "esemény küldése" művelet kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a Event Hubs](#create-connection)-kapcsolatát. 

1. A műveletben adja meg az elküldeni kívánt eseményekkel kapcsolatos információkat. A további tulajdonságok megjelenítéséhez nyissa meg az **új paraméterek hozzáadása** listát. Egy paraméter kiválasztásával hozzáadja ezt a tulajdonságot a műveleti kártyához.

   ![Válassza ki az Event hub nevét, és adja meg az esemény tartalmát](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Eseményközpont neve** | Igen | Az Event hub, ahová el szeretné küldeni az eseményt |
   | **Tartalom** | Nem | Az elküldeni kívánt esemény tartalma |
   | **Tulajdonságok** | Nem | A küldendő alkalmazás tulajdonságai és értékei |
   | **Partíciós kulcs** | Nem | Az esemény küldési helyének [partíció](../event-hubs/event-hubs-features.md#partitions) -azonosítója |
   ||||

   Elküldheti például a kimenetet a Event Hubs eseményindítóból egy másik Event hubhoz:

   ![Példa küldése eseményre](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Kapcsolódás az Event hub-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Ha a rendszer a kapcsolódási adatok megadását kéri, adja meg a következő adatokat:

   | Tulajdonság | Kötelező | Value | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | <*kapcsolattípus*> | A kapcsolódáshoz létrehozandó név |
   | **Event Hubs névtér** | Igen | <*event-hubs-namespace*> | Válassza ki a használni kívánt Event Hubs névteret. |
   |||||  

   Példa:

   ![Event hub-kapcsolat létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   A kapcsolódási karakterlánc manuális megadásához válassza a **kapcsolódási adatok manuális megadása**lehetőséget. 
   Megtudhatja [, hogyan keresheti meg a kapcsolódási karakterláncot](#permissions-connection-string).

2. Válassza ki a használni kívánt Event Hubs szabályzatot, ha még nincs kiválasztva. Válassza a **Létrehozás** lehetőséget.

   ![Event hub-kapcsolat létrehozása, 2. rész](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. A kapcsolat létrehozása után folytassa a [Event Hubs trigger hozzáadása](#add-trigger) vagy [Event Hubs művelet hozzáadása](#add-action)lehetőséggel.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/eventhubs/).

## <a name="next-steps"></a>További lépések

További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése