---
title: Állítsa be az Azure Logic Apps Azure Service Bus üzenetkezelés |} Microsoft Docs
description: Üzenetek küldése és fogadása a logic Apps alkalmazások az Azure Service Bus használatával
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: d5a4760e1e0f38fd81fd779786985f5753d77eab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Az Azure Service Bus-összekötővel üzeneteket küldjön és fogadjon

Üzenetek küldése és fogadása és a Logic Apps alkalmazást, csatlakozzon [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Annak a várólistára küldés műveleteket is végrehajthat küldhet a témakör, fogadása egy üzenetsorból, és előfizetésből. További információ [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) és [hogyan működik a Logic Apps árképzési elindítja](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Előfeltételek

A Service Bus-összekötő használata előtt ezeket az elemeket, amelyek az azonos Azure-előfizetés már léteznie kell, hogy azokat látja-e egymással kell rendelkeznie:

* A [Service Bus-névtér és üzenetküldési entitás, például egy várólistát](../service-bus-messaging/service-bus-create-namespace-portal.md)
* A [logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Csatlakozás Azure Service Bus

A Logic Apps alkalmazást bármely szolgáltatás hozzáférni, létre kell hoznia egy [ *kapcsolat* ](./connectors-overview.md) között a Logic Apps alkalmazást és a szolgáltatást, ha még nem tette meg. Ez a kapcsolat engedélyezi a Logic Apps alkalmazást adatok eléréséhez. A logikai alkalmazásnak hozzáférhetnek a Service Bus-fiókjához ellenőrizze az engedélyeit.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") 

2. Nyissa meg a Service Bus *névtér*, nem egy adott "üzenetküldési entitásra". A névtér lapon alatt **beállítások**, válassza a **megosztott elérési házirendek**. A **jogcímek**, ellenőrizze, hogy rendelkezik **kezelése** engedélyeket a névtérhez.

   ![A Service Bus-névtér engedélyeinek kezelése](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Ha azt szeretné, hogy később manuálisan adja meg a kapcsolati adatokat, a kapcsolati karakterlánc beolvasása a Service Bus-névtér. Válasszon **RootManageSharedAccessKey**. Mellett az elsődleges kulcs kapcsolati karakterláncot válassza a Másolás gombra. Későbbi használatra a kapcsolódási karakterlánc mentése.

   ![Másolja a Service Bus-névtér kapcsolati karakterláncot](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Győződjön meg arról, hogy a kapcsolati karakterlánc: a Service Bus-névtér vagy az adott entitáshoz kapcsolódó, ellenőrizze a kapcsolati karakterláncot a `EntityPath` paraméter. Ha ezt a paramétert, a kapcsolati karakterlánc egy adott entitás, pedig nem a megfelelő karakterlánc használata a Logic Apps alkalmazást.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>A Service Bus lekérdezi az új üzenetek akkor vált munkafolyamatot

A [ *eseményindító* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) olyan esemény, amely a Logic Apps alkalmazást a munkamenet indítása. Indítani egy munkafolyamatot, ha új üzeneteket küld a Service Bus, kövesse az alábbi lépéseket az eseményindítót, hogy ezek az üzenetek észlelt hozzáadásához.

1. Az a [Azure-portálon](https://portal.azure.com "Azure-portálon"), látogasson el a meglévő Logic Apps alkalmazást, vagy hozzon létre egy üres logikai alkalmazást.

2. A Logic Apps Designer írja be a keresőmezőbe a szűrő "service bus". Válassza ki a **Service Bus** összekötő. 

   ![Jelölje ki a Service Bus-összekötőt](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Válassza ki a használni kívánt eseményindító. Logikai alkalmazás futtatását a Service Bus-üzenetsorba küldi el egy új elemet, jelölje ki például az eseményindító: **Service Bus - üzenet fogadásakor a várólistában egyszerre várakozó (automatikusan hajthat végre)**

   ![Válassza ki a Service Bus eseményindító](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Néhány váltja ki egy visszatérési, illetve az üzeneteket, például a *Service Bus - egy vagy több üzenet a várólistában egyszerre várakozó (automatikusan hajthat végre) érkezésekor* eseményindító.
   > Ezek az eseményindítók érvényesítést, akkor lépjen vissza egy és az eseményindító által megadott üzenetek száma között **maximális üzenetek száma** tulajdonság.

   1. A Service Bus-névtér már nincs kapcsolat, ha a program kéri, most már a kapcsolat létrehozásához. Nevezze el a kapcsolatot, és válassza ki a használni kívánt Service Bus-névtér.

      ![Service Bus-kapcsolat létrehozása](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Vagy manuálisan adja meg a kapcsolati karakterláncot, válassza a **manuálisan adja meg a kapcsolati adatokat**. 
      Ismerje meg, [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).
      

   2. Most válassza ki a Service Bus házirendet használja, és válassza a **létrehozása**.

      ![Hozzon létre a Service Bus-kapcsolat, 2. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Válassza ki a Service Bus-üzenetsorba, és állítsa be az időköz és mikor ellenőrizze a várólista gyakorisága.

   ![Válassza ki a Service Bus-üzenetsorba, és állítsa be a lekérdezési intervallum](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

   > [!NOTE]
   > A Service Bus indítók vannak **hosszú-lekérdezési** eseményindítók, ami azt jelenti, hogy egy eseményindító következik be, amikor az eseményindító összes üzenetet feldolgozza majd megvárja-e a további üzeneteket az üzenetsor vagy témakör előfizetés megjelenő 30 másodperc.
   > Ha nem érkezik üzenet 30 másodperc, a rendszer kihagyja a eseményindító Futtatás. Ellenkező esetben az eseményindító továbbra is fennáll, addig, amíg az üzenetsor vagy témakör előfizetés nem üres üzenetek olvasásához.
   > A következő eseményindító lekérdezési az eseményindító tulajdonságaiban megadott ismétlődési alapul.

5. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

Amikor a Logic Apps alkalmazást a kiválasztott várólistához ellenőrzi, és megállapítja, hogy új üzenetet, az eseményindító futtatja a műveletek a Logic Apps alkalmazást a tényleges üzenet található.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>A Logic Apps alkalmazást üzeneteket küldeni a Service Bus

A [*művelet*](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás munkafolyamata által végrehajtott feladat. Miután hozzáadott egy eseményindítót a logikai alkalmazáshoz, hozzáadhat egy műveletet is, amely további műveleteket végez el az eseményindító által előállított adatokkal. Üzenet küldése a Service Bus üzenetkezelés entitásának a Logic Apps alkalmazást, kövesse az alábbi lépéseket.

1. A Logic Apps Designer alapján az eseményindító válasszon **+ új lépés** > **művelet hozzáadása**.

2. A keresési mezőbe írja be a "service bus" szűrőként. Válassza ki ezt az összekötőt: **Service Bus**

   ![Jelölje ki a Service Bus-összekötőt](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Ez a művelet kiválasztása: **Service Bus - üzenet küldése**

   ![Válassza ki a "Service Bus - üzenet küldése"](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Válassza ki a üzenetküldési entitás, amely az üzenetsor vagy témakör neve, hol szeretné elküldeni az üzenetet. Ezt követően adja meg az üzenet tartalma és bármely egyéb részleteket.

   ![Válassza ki az üzenetküldési entitás, és adja meg a üzenet adatai](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Mentse a logikai alkalmazást. 

Egy műveletet, amely üzeneteket küld a Logic Apps alkalmazást a most létrehozott. 

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

További információ a eseményindítók és műveletek határozzák meg a Swagger-fájl, és semmilyen határnak további tudnivalókért tekintse át a [connector részleteket](/connectors/servicebus/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információ [más összekötők, az Azure Logic Apps alkalmazások](../connectors/apis-list.md)