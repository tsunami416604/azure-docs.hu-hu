---
title: "Az Azure Event Hubs eseményfigyelő beállítása az Azure Logic Apps |} Microsoft Docs"
description: "Az adatfolyamok események fogadásához, és elküldje az eseményeket az Azure Logic Apps az Azure Event Hubs figyelése"
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
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Figyelése, fogadására és küldi az eseményeket az Event Hubs-összekötő használatával

Az eseményfigyelő beállítása, hogy a Logic Apps alkalmazást képesek észlelni események, képes eseményeket fogadni, és elküldje az eseményeket, csatlakoznia kell egy [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) a logikai alkalmazás. További információ [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

## <a name="requirements"></a>Követelmények

* Rendelkeznie kell egy [Event Hubs-névteret és Eseményközpont](../event-hubs/event-hubs-create.md) az Azure-ban. Ismerje meg, [az Event Hubs névtér és az Eseményközpont létrehozása](../event-hubs/event-hubs-create.md). 

* Használandó [a csatlakozókat](https://docs.microsoft.com/azure/connectors/apis-list) a Logic Apps alkalmazást fel kell először hozzon létre egy logikai alkalmazást. Ismerje meg, [logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md).

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Ellenőrizze az Event Hubs névtér engedélyeit, és keresse meg a kapcsolati karakterláncot

A logikai alkalmazásnak, a szolgáltatás eléréséhez, létre kell hoznia egy [ *kapcsolat* ](./connectors-overview.md) között a Logic Apps alkalmazást és a szolgáltatást, ha még nem tette meg. Ez a kapcsolat engedélyezi a Logic Apps alkalmazást adatok eléréséhez.
A logikai alkalmazásnak az Eseményközpont eléréséhez, akkor szükség **kezelése** engedélyek és a kapcsolati karakterlánc az Event Hubs-névtérhez.

Ellenőrizze az engedélyeit, és a kapcsolati karakterláncot, kövesse az alábbi lépéseket.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") 

2.  Nyissa meg az Event hubs *névtér*, nem az adott Eseményközpontban. A névtér panelen a **beállítások**, válassza a **megosztott elérési házirendek**. A **jogcímek**, ellenőrizze, hogy rendelkezik **kezelése** engedélyeket a névtérhez.

    ![Az Event Hubs névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Másolja a kapcsolati karakterláncot az Event Hubs névtérhez, válassza a **RootManageSharedAccessKey**. Mellett az elsődleges kulcs kapcsolati karakterláncot válassza a Másolás gombra.

    ![Másolja az Event Hubs névtér kapcsolati karakterláncot](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Győződjön meg arról, hogy a kapcsolati karakterlánc tartozik az Event Hubs névtér vagy egy adott Eseményközpontban, ellenőrizze a kapcsolati karakterláncot a `EntityPath` paraméter. Ha ezt a paramétert, a kapcsolati karakterlánc az egy adott Eseményközpontban "entitás", pedig nem a megfelelő karakterlánc használata a Logic Apps alkalmazást.

4.  Most amikor a rendszer kéri a hitelesítő adatokat az Event Hubs eseményindító vagy a művelet a Logic Apps alkalmazást való hozzáadását követően, csatlakozhat az Event Hubs névtér. Nevezze el a kapcsolatot, adja meg a kapcsolati karakterláncot, amely a másolt, és válassza a **létrehozása**.

    ![Adja meg a kapcsolati karakterlánc az Event Hubs névtér](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Miután létrehozta a kapcsolatot, a kapcsolat nevét meg kell jelennie az Eseménynapló hubok eseményindító vagy műveletet. 
    Majd folytathatja a többi lépés a a Logic Apps alkalmazást.

    ![Event Hubs névtér kapcsolat létre](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Ha az Eseményközpont kap új események munkafolyamat indítása

A [ *eseményindító* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) olyan esemény, amely a Logic Apps alkalmazást a munkamenet indítása. Indítani egy munkafolyamatot, ha új események küldhetők az Eseményközpontba, kövesse az alábbi lépéseket az eseményindító által észlelt Ez az esemény felvételéhez.

1.  Az a [Azure-portálon](https://portal.azure.com "Azure-portálon"), látogasson el a meglévő Logic Apps alkalmazást, vagy hozzon létre egy üres logikai alkalmazást.

2.  Írja be a keresőmezőbe a Logic App Designer `event hubs` a szűrőhöz. Válassza ki az ehhez az eseményindítóhoz: **mikor események érhetők el az Event Hubs**

    ![Válassza ki a eseményindítója a következőnek: Ha az Eseményközpont kap új események](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Ha még nem rendelkezik a kapcsolat az Event Hubs névtérhez, felszólítást hozzon létre most ezt a kapcsolatot. Nevezze el a kapcsolatot, és adja meg a kapcsolati karakterláncot az Event Hubs névtérhez. 
    Szükség esetén további [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).

    ![Adja meg a kapcsolati karakterlánc az Event Hubs névtér](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Miután létrehozta a kapcsolatot, a beállításait a **Ha olyan esemény az elérhető eseményközpontban** eseményindító jelennek meg.

    ![Ha az Eseményközpont kap új események eseményindító beállításai](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  Adja meg, vagy válassza ki a figyelni kívánt Eseményközpont nevét. Válassza ki a gyakoriságát és milyen gyakran az Event Hubs ellenőrizni kívánja intervallumát.

    > [!TIP]
    > Opcionálisan válassza ki a fogyasztói csoportot események olvasása, válassza a **speciális beállítások megjelenítése**. 

    ![Adja meg az Event Hubs vagy felhasználói csoport](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    Most már beállította egy eseményindító indítani egy munkafolyamatot a logikai alkalmazásnak. 
    A Logic Apps alkalmazást az adott Event Hubs beállított ütemezés szerint ellenőrzi. 
    Ha az alkalmazás megkeresi az új eseményeket az Eseménynapló Hub, az eseményindító más műveletek futtatása vagy elindítja a Logic Apps alkalmazást a.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Események küldése az Eseményközpont a logikai alkalmazás

A [*művelet*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) a logikai alkalmazás munkafolyamata által végrehajtott feladat. Miután hozzáadott egy eseményindítót a logikai alkalmazáshoz, hozzáadhat egy műveletet is, amely további műveleteket végez el az eseményindító által előállított adatokkal. Esemény küldése az Eseményközpont a logic App, kövesse az alábbi lépéseket.

1.  Logic App tervezőben, a logic app eseményindító alatt válassza a **új lépés** > **művelet hozzáadása**.

    ![Válassza a "Új lépés", majd "Művelet hozzáadása"](./media/connectors-create-api-azure-event-hubs/add-action.png)

    Most keresse meg és jelölje ki a végrehajtani kívánt műveletet. 
    Azonban bármely művelet, például kiválaszthatja azt szeretnénk, az Event Hubs művelet is küldi az eseményeket.

2.  A keresési mezőbe, írja be a `event hubs` a szűrőhöz.
Ez a művelet kiválasztása: **küldési esemény**

    ![Válassza ki az "Event Hubs - küldési esemény" művelet](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  Az esemény, például az Event Hubs, ha szeretne küldeni az esemény nevét adja meg a szükséges adatokat. Adja meg az esemény, például tartalom esemény bármely egyéb választható részleteit.

    > [!TIP]
    > Igény szerint adja meg az Eseményközpont-partíción hova küldje a esemény, válassza a **speciális beállítások megjelenítése**. 

    ![Adja meg az Eseményközpont nevét és opcionális esemény részletei](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  Mentse a módosításokat.

    ![A logikai alkalmazás mentése](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    Most már beállította művelet események küldése a Logic Apps alkalmazást. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/eventhubs/). 

## <a name="get-help"></a>Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretne segíteni a Logic Apps és összekötők fejlesztésében, szavazzon vagy küldje el javaslatait a [Logic Apps felhasználói visszajelzések oldalon](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

*  [Más összekötők keresése Azure Logic Apps alkalmazások](./apis-list.md)