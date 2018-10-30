---
title: Az Azure Service Bus – Azure Logic Apps üzenetek küldése és fogadása |} A Microsoft Docs
description: Állítsa be a vállalati felhőalapú üzenetkezelés az Azure Service Bus, az Azure Logic Appsben
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
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232715"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>A felhőben az Azure Service Bus és az Azure Logic Apps, az Exchange-üzenetek

Az Azure Logic Apps és az Azure Service Bus-összekötő automatikus feladatokkal és munkafolyamatokkal, például a sales adatátvitelt, és rendeléseket, naplók és a készlet áthelyezések száma – megvásárlása a szervezet alkalmazások hozhat létre. Az összekötő nem csupán figyeli, küld, és kezeli az üzeneteket, de összehasonlítást is végez műveleteket a várólisták, munkamenetek, témakörök, előfizetések és így tovább, például:

* Figyelő, ha az üzenetek érkezésekor (automatikus lezárás), vagy fogadott (betekintési zárolás) az üzenetsorok, témakörök és előfizetések. 
* Üzenetek küldése.
* Hozzon létre, és előfizetések törlése.
* Üzenet-üzenetsorok és -előfizetések kezelése, például beolvasása, késleltetett beolvasása, végezze el, késleltetése, megszakítási vagy a kézbesítetlen levelek.
* Újítsa meg az üzeneteket és az üzenetsorok és -előfizetések munkamenetek a zárolását.
* Zárja be az üzenetsorok és témakörök-munkamenetekben.

Eseményindítókat, amelyek választ kaphat a Service Bus, és elérhetővé a kimenetben egyéb műveleteket hajthat végre a logic Apps is használhatja. A Service Bus-műveletek kimenetét használják más műveletek is rendelkezhet. Ha most ismerkedik a Service Bus és a Logic Apps, tekintse át [Mi az Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) és [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A Service Bus-névtér és üzenetküldési entitás, például egy üzenetsorba. Ha ezek az elemek nincs, megtudhatja, hogyan [létrehozása a Service Bus-névtér és üzenetsor](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Ezek az elemek léteznie kell az Azure-előfizetéshez, a logic apps, amely ezekre az adatokra.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol a Service Bus szeretné. A logikai alkalmazás az Azure-előfizetéshez, a service bus léteznie kell. Szeretne kezdeni egy Service Bus-trigger [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). Egy Service Bus-műveletet használja, indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Ellenőrizze az engedélyeket

Győződjön meg róla, hogy a logikai alkalmazás a Service Bus-névtér való hozzáférésre vonatkozó engedélyeket. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Nyissa meg a Service Bus *névtér*. A névtér lapon alatt **beállítások**válassza **megosztott elérési házirendek**. Alatt **jogcímek**, ellenőrizze, hogy rendelkezik **kezelés** engedélyeket a névtér számára

   ![A Service Bus-névtér engedélyeinek kezelése](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. A kapcsolati sztring lekérése a Service Bus-névtér. Ez a karakterlánc a logikai alkalmazás a kapcsolati adatok beírásakor kell.

   1. Válassza ki **RootManageSharedAccessKey**. 
   
   1. Az elsődleges kapcsolati karakterlánc mellett válassza a Másolás gombra. A kapcsolati karakterlánc későbbi használatra mentse.

      ![A Service Bus-névtér kapcsolati karakterlánc másolása](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Győződjön meg arról, hogy a kapcsolati karakterlánc vagy társítva a Service Bus-névtér egy üzenetküldési entitásra, például egy üzenetsort, keresse meg a kapcsolati karakterláncát a `EntityPath`  paraméter. Ha ezt a paramétert, a kapcsolati karakterláncot egy adott entitás van, és nem a megfelelő karakterlánc, a logikai alkalmazás használata.

## <a name="add-trigger-or-action"></a>Adja hozzá az eseményindítót vagy műveletet

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Hozzáadása egy *eseményindító* üres logikai alkalmazás, a Keresés mezőbe írja be "Az Azure Service Bus" szűrőként. Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   A logikai alkalmazás elindításához, ha új elem a Service Bus-üzenetsorba küldi, jelölje be például a trigger: **amikor üzenet érkezik egy üzenetsorban (automatikus lezárás)**

   ![A Service Bus-trigger kiválasztása](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Egyes eseményindítók egy visszaadó vagy üzenetek, például az eseményindító **egy vagy több üzenet érkezésekor (automatikus lezárás) üzenetsor**. Ezek az eseményindítók aktiválódik, amikor az eseményindító által megadott üzenetek száma és a egy között vissza **maximális üzenetszám** tulajdonság.

   *Az összes Service Bus-eseményindítók hosszú lekérdezéseket eseményindítók*, ami azt jelenti, ha akkor aktiválódik, a trigger az összes üzenetet feldolgozza, és megjelenik az üzenetsor vagy témakör-előfizetésben található további üzenetek 30 másodpercet vár, majd. 
   Ha nem jelenik meg üzenet 30 másodperc, a rendszer kihagyta a trigger futtatása. 
   Ellenkező esetben az eseményindító továbbra is fennáll, az üzenetek olvasásához, amíg az üzenetsor vagy témakör-előfizetés nem üres. A következő eseményindító-lekérdezésének az eseményindító tulajdonságaiban megadott ismétlődési időköze alapul.

1. Hozzáadása egy *művelet* egy meglévő logikai alkalmazást, tegye a következőket: 

   1. Válassza ki az utolsó lépésnél, ahová a művelet hozzáadása, **új lépés**. 

      Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
      Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

   1. A keresőmezőbe írja be szűrőként "Az Azure Service Bus". 
   Műveletek listája alatt válassza ki a kívánt művelet. 
 
      Jelölje be például, hogy ez a művelet: **üzenet küldése**

      ![Válassza ki a Service Bus-műveletet](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Ha a logikai alkalmazás a Service Bus-névteret első alkalommal csatlakoztatja, a Logic App Designerben most kéri a kapcsolati adatokat. 

   1. Adja meg a kapcsolat nevét, és válassza ki a Service Bus-névtér.

      ![Hozzon létre a Service Bus-kapcsolat, 1. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Inkább manuálisan adja meg a kapcsolati karakterláncot, válassza a **kapcsolatadatok manuális megadása**. 
      Ha nem rendelkezik a kapcsolati karakterláncot, további [a kapcsolati karakterlánc megkeresése](#permissions-connection-string).

   1. Most válassza ki a Service Bus-szabályzat, és válassza **létrehozás**.

      ![Hozzon létre a Service Bus-kapcsolat, a 2. rész](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Ebben a példában válassza ki az üzenetkezelési entitás azt szeretné, például egy üzenetsorba vagy témakörbe. Ebben a példában válassza ki a Service Bus-üzenetsorba. 
   
   ![Válassza ki a Service Bus-üzenetsorba](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Adja meg a szükséges adatokat az eseményindítót vagy műveletet. Ebben a példában a trigger vagy művelet esetén a megfelelő lépéseket kövesse: 

   * **A minta eseményindító**: lekérdezési időköz és az üzenetsor-ellenőrzési gyakoriságának beállítása.

     ![Lekérdezési időköz beállítása](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Ha elkészült, folytassa a logikai alkalmazás munkafolyamat létrehozásához adja hozzá a kívánt műveleteket. Például hozzáadhat egy műveletet, amely e-mailt küld, ha új üzenet érkezik.
     Ha a trigger ellenőrzi az üzenetsor, és egy új üzenetet talál, a logikai alkalmazás fut a kiválasztott műveletek az található üzenet.

   * **A mintául szolgáló művelet**: Adja meg az üzenet tartalmának és összes többi részletet. 

     ![Adja meg az üzenet tartalma és a részletek](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Ha elkészült, folytassa a hozzáadásával bármely más műveleteket, amelyeket a logikai alkalmazás munkafolyamat létrehozásához. Például hozzáadhat egy műveletet, amely megerősíti, hogy az üzenet el lett küldve e-mailt küld.

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/servicebus/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)