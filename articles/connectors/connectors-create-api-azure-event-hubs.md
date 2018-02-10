---
title: "Esemény figyelése az Azure Event Hubs Azure Logic Apps |} Microsoft Docs"
description: "Az adatfolyamok események fogadásához, és a logic apps események küldése az Azure Event Hubs használatával figyelése"
services: logic-apps
keywords: "az adatfolyam, eseményfigyelő, az event hubs"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 076f7dd11ca8c153046727861ecb755e88f32b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Figyelése, fogadására és küldi az eseményeket az Event Hubs-összekötő használatával

Az eseményfigyelő beállítása, hogy a Logic Apps alkalmazást képesek észlelni események, képes eseményeket fogadni, és elküldje az eseményeket, csatlakoznia kell egy [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) a logikai alkalmazás. További információ [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) és [árképzési működése Logic Apps-összekötők](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Előfeltételek

Az Event Hubs-összekötő használata előtt ezeket az elemeket kell tartalmaznia:

* Egy [Azure Event Hubs-névteret és az Event Hubs](../event-hubs/event-hubs-create.md)
* A [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Csatlakozás az Azure Event hubs

A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, létre kell hoznia egy [ *kapcsolat* ](./connectors-overview.md) között a Logic Apps alkalmazást és a szolgáltatást, ha még nem tette meg. Ez a kapcsolat engedélyezi a Logic Apps alkalmazást adatok eléréséhez. A logikai alkalmazásnak az Eseményközpont eléréséhez ellenőrizze az engedélyeit, és a kapcsolati karakterlánc beolvasása az Event Hubs-névtérhez.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") 

2.  Nyissa meg az Event hubs *névtér*, nem egy adott Eseményközpontban. A névtér lapon alatt **beállítások**, válassza a **megosztott elérési házirendek**. A **jogcímek**, ellenőrizze, hogy rendelkezik **kezelése** engedélyeket a névtérhez.

    ![Az Event Hubs névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Ha azt szeretné, hogy később manuálisan adja meg a kapcsolati adatokat, a kapcsolati karakterlánc beolvasása az Event Hubs névtér. Choose **RootManageSharedAccessKey**. Mellett az elsődleges kulcs kapcsolati karakterláncot válassza a Másolás gombra. Későbbi használatra a kapcsolódási karakterlánc mentése.

    ![Másolja az Event Hubs névtér kapcsolati karakterláncot](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Győződjön meg arról, hogy a kapcsolati karakterlánc tartozik az Event Hubs névtér vagy egy adott eseményközpont, ellenőrizze a kapcsolati karakterláncot a `EntityPath` paraméter. Ha ezt a paramétert, a kapcsolati karakterlánc az egy adott Eseményközpontban "entitás", pedig nem a megfelelő karakterlánc használata a Logic Apps alkalmazást.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Vált a munkafolyamatot, ha az Eseményközpont lekérdezi az új események

A [ *eseményindító* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) olyan esemény, amely a Logic Apps alkalmazást a munkamenet indítása. Indítani egy munkafolyamatot, ha új események küldhetők az Eseményközpontba, kövesse az alábbi lépéseket az eseményindító által észlelt Ez az esemény felvételéhez.

1. Az a [Azure-portálon](https://portal.azure.com "Azure-portálon"), látogasson el a meglévő Logic Apps alkalmazást, vagy hozzon létre egy üres logikai alkalmazást.

2. A Logic Apps Designer írja be a keresőmezőbe a szűrő "event hubs". Válassza ki az ehhez az eseményindítóhoz: **mikor események érhetők el az Event Hubs**

   ![Válassza ki a eseményindítója a következőnek: Ha az Eseményközpont kap új események](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Ha még nem rendelkezik a kapcsolat az Event Hubs névtérhez, felszólítást hozzon létre most ezt a kapcsolatot. Nevezze el a kapcsolatot, és jelölje ki a használni kívánt az Event Hubs-névteret.

      ![Eseményközpont kapcsolatának létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      Vagy manuálisan adja meg a kapcsolati karakterláncot, válassza a **manuálisan adja meg a kapcsolati adatokat**. 
      Ismerje meg, [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).

   2. Most válassza ki a Event Hubs házirendet használja, és válassza a **létrehozása**.

      ![Eseményközpont kapcsolatának, 2. rész létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Válassza ki az Event Hubs figyelésére, és állítsa be az időköz és mikor ellenőrizze az Event Hubs gyakorisága.

    ![Adja meg az Event Hubs vagy felhasználói csoport](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

    > [!TIP]
    > Opcionálisan válassza ki a fogyasztói csoportot események olvasása, válassza a **speciális beállítások megjelenítése**.

4. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Most ha a logikai alkalmazás ellenőrzi a kijelölt Eseményközpont, és új esemény talál, az eseményindító fut a műveletek a Logic Apps alkalmazást a tényleges esemény.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Események küldése az Eseményközpont a logikai alkalmazás

A [*művelet*](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás munkafolyamata által végrehajtott feladat. Miután hozzáadott egy eseményindítót a logikai alkalmazáshoz, hozzáadhat egy műveletet is, amely további műveleteket végez el az eseményindító által előállított adatokkal. Esemény küldése az Eseményközpont a logic App, kövesse az alábbi lépéseket.

1. A Logic Apps Designer alapján az eseményindító válasszon **új lépés** > **művelet hozzáadása**.

2. A keresési mezőbe írja be az "event hubs" szűrőként.
Ez a művelet kiválasztása: **Event Hubs - küldési esemény**

   ![Válassza ki az "Event Hubs - küldési esemény"](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. Válassza ki az Eseményközpont kiválasztásával hova küldje a esemény. Ezután írja be a esemény tartalom és bármely egyéb részleteket.

   ![Válassza ki az Eseményközpont nevét, és adja meg az esemény tartalom](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. Mentse a logikai alkalmazást.

Most létrehozott események küldi a logikai alkalmazás művelettel. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

További információ a eseményindítók és műveletek határozzák meg a Swagger-fájl, és semmilyen határnak további tudnivalókért tekintse át a [connector részleteket](/connectors/eventhubs/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ [más összekötők, az Azure Logic Apps alkalmazások](../connectors/apis-list.md)