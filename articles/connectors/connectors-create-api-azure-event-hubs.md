---
title: Csatlakozás az Azure Event Hubs szolgáltatáshoz
description: Automatizált feladatok és munkafolyamatok létrehozása, amelyek az Azure Event Hubs és az Azure Logic Apps használatával figyelik és kezelik az eseményeket
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 32fa54ef0d8eccaf8745ee37cb028d4f3c6d73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247292"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Események figyelése, fogadása és küldése az Azure Event Hubs és az Azure Logic Apps segítségével

Ez a cikk bemutatja, hogyan figyelheti és kezelheti az [Azure Event Hubs-ba](../event-hubs/event-hubs-what-is-event-hubs.md) küldött eseményeket egy logikai alkalmazáson belülről az Azure Event Hubs-összekötővel. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják az események ellenőrzési, küldési és fogadási feladatait és munkafolyamatait az Event Hubon. Az összekötő-specifikus technikai információk az [Azure Event Hubs-összekötő referencia.](https://docs.microsoft.com/connectors/eventhubs/)</a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [Az Azure Event Hubs névtere és az Event Hub](../event-hubs/event-hubs-create.md)

* A logikai alkalmazás, ahol szeretné elérni az Event Hub. A logikai alkalmazás Azure Event Hubs eseményindítóval való elindításához [egy üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.
Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a Rövid útmutató: Az első logikai alkalmazás létrehozása című ismertetése című ismertetése című ismertetése. [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Engedélyek ellenőrzése és kapcsolati karakterlánc bekése

Győződjön meg arról, hogy a logikai alkalmazás hozzáférhet az Event Hub, ellenőrizze az engedélyeket, és az Event Hubs névtér kapcsolati karakterláncának beolvassa.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg az Event Hubs *névterét,* nem pedig egy adott Eseményközpontot. 

1. A Névtér menü **Beállítások**területén válassza a **Megosztott hozzáférési házirendek**lehetőséget. A **Jogcímek csoportban**ellenőrizze, hogy rendelkezik-e a névtér **kezeléséhez** szükséges engedélyekkel.

   ![Az Event Hub névteréhez szükséges engedélyek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Ha később manuálisan szeretné megadni a kapcsolatadatait, az Event Hubs névtér kapcsolati karakterláncát.

   1. A **Házirend csoportban**válassza a **RootManageSharedAccessKey lehetőséget.**

   1. Keresse meg az elsődleges kulcs kapcsolati karakterláncát. Válassza ki a másolás gombot, és mentse a kapcsolati karakterláncot későbbi használatra.

      ![Az Event Hubs névtérbeli kapcsolatkarakterláncának másolása](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Annak ellenőrzéséhez, hogy a kapcsolati karakterlánc az Event Hubs névtérrel vagy egy adott `EntityPath`  eseményközponttal van-e társítva, győződjön meg arról, hogy a kapcsolati karakterlánc nem rendelkezik a paraméterrel. Ha megtalálja ezt a paramétert, a kapcsolati karakterlánc egy adott Event Hub "entitás", és nem a megfelelő karakterláncot használni a logikai alkalmazással.

1. Most folytassa [az Eseményközpontok hozzáadása eseményindítóval](#add-trigger) vagy [az Eseményközpontok hozzáadása művelettel.](#add-action)

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Eseményközpontok hozzáadása eseményindító

Az Azure Logic Apps minden logikai alkalmazás kell kezdeni egy [eseményindító,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely egy adott esemény bekövetkezésekor, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító aktiválódik, a Logic Apps motor létrehoz egy logikai alkalmazáspéldányt, és megkezdi az alkalmazás munkafolyamatának futtatását.

Ez a példa bemutatja, hogyan indíthat el egy logikai alkalmazás munkafolyamatot, amikor új eseményeket küld az Event Hub. 

1. Az Azure Portalon vagy a Visual Studio,hozzon létre egy üres logikai alkalmazás, amely megnyitja a Logic Apps Designer. Ez a példa az Azure Portalt használja.

1. A keresőmezőbe írja be szűrőként az "eseményközpontok" kifejezést. Az eseményindítók listájában válassza ki ezt az eseményindítót: **Ha események érhetők el az Event Hub – Event Hubs alkalmazásban**

   ![Eseményindító kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre az Event Hubs-kapcsolatot](#create-connection)most. 

1. Az eseményindítóban adja meg a figyelni kívánt eseményközpontadatait. További tulajdonságok esetén nyissa meg az **Új paraméter hozzáadása** listát. Egy paraméter kiválasztása hozzáadja a tulajdonságot az eseményindító kártyához.

   ![Eseményindító tulajdonságai](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Az eseményközpont neve** | Igen | A figyelni kívánt eseményközpont neve |
   | **Tartalom típusa** | Nem | Az esemény tartalomtípusa. A mező alapértelmezett értéke: `application/octet-stream`. |
   | **Fogyasztói csoport neve** | Nem | Az [Event Hub-fogyasztói csoport neve](../event-hubs/event-hubs-features.md#consumer-groups) az események olvasásához. Ha nincs megadva, a rendszer az alapértelmezett fogyasztói csoportot használja. |
   | **Események maximális száma** | Nem | Az események maximális száma. Az eseményindító egy és a tulajdonság által megadott események száma között ad vissza értéket. |
   | **Intervallum** | Igen | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján |
   | **Frekvencia** | Igen | Az ismétlődés időegysége |
   ||||

   **További tulajdonságok**

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tartalomséma** | Nem | A JSON-tartalomséma az események et az Event Hub. Ha például megadja a tartalomsémát, a logikai alkalmazást csak a sémának megfelelő eseményekhez indíthatja el. |
   | **Minimális partíciókulcs** | Nem | Adja meg az olvasandó [partíció](../event-hubs/event-hubs-features.md#partitions) minimális azonosítóját. Alapértelmezés szerint a rendszer az összes partíciót beolvassa. |
   | **Maximális partíciókulcs** | Nem | Adja meg a [maximálisan](../event-hubs/event-hubs-features.md#partitions) olvasandó partícióazonosítót. Alapértelmezés szerint a rendszer az összes partíciót beolvassa. |
   | **Időzóna** | Nem | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az eseményindító nem fogadja el az UTC eltolását. Jelölje ki az alkalmazni kívánt időzónát. <p>További információ: [Ismétlődő feladatok és munkafolyamatok létrehozása és futtatása az Azure Logic Apps alkalmazásokkal című témakörben.](../connectors/connectors-native-recurrence.md) |
   | **Kezdési idő** | Nem | Adja meg a kezdési időpontot ebben a formátumban: <p>YÉÉÉ-HH-DDThh:mm:ss, ha időzónát választ<p>– vagy –<p>YÉÉÉ-HH-DDThh:mm:ssZ, ha nem választ ki időzónát<p>További információ: [Ismétlődő feladatok és munkafolyamatok létrehozása és futtatása az Azure Logic Apps alkalmazásokkal című témakörben.](../connectors/connectors-native-recurrence.md) |
   ||||

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

1. Most folytassa egy vagy több művelet hozzáadásával a logikai alkalmazáshoz az eseményindító eredményekkel végrehajtani kívánt feladatokhoz. 

   Ha például egy adott érték, például egy kategória alapján szeretné szűrni az eseményeket, hozzáadhat egy feltételt, hogy az **esemény küldése** művelet csak az adott feltételnek megfelelő eseményeket küldhesse. 

> [!NOTE]
> Az Event Hub minden eseményindítója *hosszú lekérdezési* eseményindító, ami azt jelenti, hogy amikor egy eseményindító aktiválódik, az eseményindító feldolgozza az összes eseményt, majd 30 másodpercet vár, amíg további események jelennek meg az Event Hubban.
> Ha 30 másodpercen belül nem érkezik esemény, az eseményindító futtatása kimarad. Ellenkező esetben az eseményindító addig folytatja az események olvasását, amíg az Event Hub üres nem lesz.
> A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési időköz alapján történik.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Eseményközpontok hozzáadása művelet

Az Azure Logic Apps egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy lépés a munkafolyamatban, amely követi az eseményindító vagy egy másik művelet. Ebben a példában a logikai alkalmazás egy Eseményközpontok eseményindítóval kezdődik, amely új eseményeket keres az Event Hubban.

1. Az Azure Portalon vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic Apps Designer. Ez a példa az Azure Portalt használja.

1. Az eseményindító vagy művelet alatt válassza az **Új lépés lehetőséget.**

   Ha műveletet szeretne hozzáadni a meglévő lépések közé, vigye az egeret a csatlakozó nyíl fölé. 
   Válassza ki a**+** megjelenő pluszjelet ( ), majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként az "eseményközpontok" kifejezést.
A műveletlistából válassza a következő műveletet: **Esemény küldése – Eseményközpontok**

   ![Válassza az "Esemény küldése" műveletet](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre az Event Hubs-kapcsolatot](#create-connection)most. 

1. A műveletben adjon meg információt az elküldeni kívánt eseményekről. További tulajdonságok esetén nyissa meg az **Új paraméter hozzáadása** listát. Egy paraméter kiválasztása hozzáadja a tulajdonságot a műveletkártyához.

   ![Válassza ki az Eseményközpont nevét, és adja meg az esemény tartalmát](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Az eseményközpont neve** | Igen | Az Eseményközpont, ahová el szeretné küldeni az eseményt |
   | **Tartalom** | Nem | Az elküldeni kívánt esemény tartalma |
   | **Tulajdonságok** | Nem | A küldő alkalmazás tulajdonságai és értékei |
   | **Partíciókulcs** | Nem | A [partíció](../event-hubs/event-hubs-features.md#partitions) azonosítója, hogy hová küldje az eseményt |
   ||||

   Elküldheti például a kimenetet az Eseményközpontok eseményindítójából egy másik eseményközpontba:

   ![Példa az esemény küldése](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Csatlakozás az Eseményközponthoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Amikor a rendszer csatlakozási adatokat kér, adja meg az alábbi adatokat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Igen | <*kapcsolat neve*> | A kapcsolathoz létrehozandó név |
   | **Eseményközpontok névtere** | Igen | <*esemény-hubok-névtér*> | Jelölje ki a használni kívánt Eseményközpontok névteret. |
   |||||  

   Példa:

   ![Eseményközpont-kapcsolat létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   A kapcsolati karakterlánc manuális megadásához válassza a **Kapcsolatadatainak manuális megadása**lehetőséget. 
   További információ [a kapcsolati karakterlánc megkereséséhez.](#permissions-connection-string)

2. Válassza ki a használni kívánt Eseményközpontok házirendet, ha még nincs kijelölve. Válassza a **Létrehozás** elemet.

   ![Event Hub-kapcsolat létrehozása, 2.](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. A kapcsolat létrehozása után folytassa az [Eseményközpontok hozzáadása eseményindítóval](#add-trigger) vagy [az Eseményközpontok hozzáadása művelettel.](#add-action)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő Swagger-fájljában leírt technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán](https://docs.microsoft.com/connectors/eventhubs/)találja.

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)