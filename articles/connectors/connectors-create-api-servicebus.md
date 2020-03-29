---
title: Exchange-üzenetek az Azure Service Bus használatával
description: Automatikus feladatok és munkafolyamatok létrehozása, amelyek üzeneteket küldenek és fogadnak az Azure Service Bus használatával az Azure Logic Apps-ben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261619"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Üzenetek cseréje a felhőben az Azure Logic Apps és az Azure Service Bus használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és az Azure Service [Bus-összekötő](../service-bus-messaging/service-bus-messaging-overview.md) segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek adatokat továbbítanak, például értékesítési és beszerzési rendeléseket, naplókat és készletmozgásokat a szervezet alkalmazásai között. Az összekötő nem csak figyeli, küldi és kezeli az üzeneteket, hanem műveleteket is hajt végre várólistákkal, munkamenetekkel, témakörökkel, előfizetésekkel és így tovább, például:

* Figyelje, hogy az üzenetek mikor érkeznek (automatikus kiegészítés) vagy mikor érkeznek (betekintés-zárolás) várólistákban, témakörökben és témakör-előfizetésekben.
* Üzeneteket küldeni.
* Témakör-előfizetések létrehozása és törlése.
* Kezelheti a várólistákban és a témakör-előfizetésekben lévő üzeneteket, például a halasztott, a teljes, a halasztást, az elhagyást és a kézbesítetlen leveleket.
* A várólistákban és a témakör-előfizetésekben lévő üzenetek és munkamenetek zárolásának megújítása.
* Munkamenetek bezárása várólistákban és témakörökben.

Olyan eseményindítókat használhat, amelyek válaszokat kapnak a Service Bustól, és elérhetővé teszik a kimenetet a logikai alkalmazások más műveletek számára. Más műveletek is használhatják a Service Bus-műveletek kimenetét. Ha most kezdi meg a Service Bus és a Logic [What is Azure Logic Apps](../logic-apps/logic-apps-overview.md)Apps, tekintse [át, mi az Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A Service Bus névtér és üzenetkezelő entitás, például egy várólista. Ezek az elemek és a logikai alkalmazás kell használni ugyanazt az Azure-előfizetést. Ha nem rendelkezik ezekkel az elemekkel, olvassa el, hogyan [hozhat létre a Service Bus névterét és egy várólistát.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol a Service Bus névtér és üzenetküldésentitás használata. A logikai alkalmazás és a szolgáltatásbusz kell használni ugyanazt az Azure-előfizetést. Ha a munkafolyamatot egy Service Bus-eseményindítóval szeretné elindítani, [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ha egy Service Bus-műveletet szeretne használni a munkafolyamatban, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az [Ismétlődés eseményindítóval.](../connectors/connectors-native-recurrence.md)

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Engedélyek ellenőrzése

Ellenőrizze, hogy a logikai alkalmazás rendelkezik-e a Service Bus-névtér eléréséhez szükséges engedélyekkel.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg a Service Bus *névterét.* A névtér lap **Beállítások**területén válassza a **Megosztott hozzáférési házirendek**lehetőséget. A **Jogcímek csoportban**ellenőrizze, hogy rendelkezik-e a névtér **kezeléséhez** szükséges engedélyekkel.

   ![A Service Bus névteréhez szükséges engedélyek kezelése](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. A Service Bus névterének kapcsolati karakterláncának beszereznie. Szüksége van erre a karakterláncra, amikor megadja a kapcsolatadatait a logikai alkalmazásban.

   1. A **Megosztott hozzáférési házirendek** ablaktáblán válassza a **RootManageSharedAccessKey lehetőséget.**
   
   1. Az elsődleges kapcsolati karakterlánc mellett válassza a másolás gombot. Mentse a kapcsolati karakterláncot későbbi használatra.

      ![A Service Bus névtérbeli kapcsolatának karakterlánca](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Annak ellenőrzéséhez, hogy a kapcsolati karakterlánc a Service Bus névtérrel vagy egy üzenetküldő `EntityPath`  entitással, például egy várólistával van-e társítva, keressen a kapcsolati karakterláncban a paramétert. Ha megtalálja ezt a paramétert, a kapcsolati karakterlánc egy adott entitáshoz, és nem a megfelelő karakterlánc ot a logikai alkalmazással használni.

## <a name="add-service-bus-trigger"></a>Szolgáltatásbusz-eseményindító hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A keresőmezőbe írja be szűrőként az "azure service bus" kifejezést. Az eseményindítók listájában válassza ki a kívánt eseményindítót.

   Például a logikai alkalmazás aktiválásához, amikor egy új elem érkezik egy Service Bus-várólistába, válassza a **Amikor egy üzenet érkezik egy várólistában (automatikus kiegészítés)** eseményindító.

   ![Válassza a Service Bus eseményindítót](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Minden Service Bus eseményindítók *hosszú lekérdezési* eseményindítók. Ez a leírás azt jelenti, hogy amikor az eseményindító aktiválódik, az eseményindító feldolgozza az összes üzenetet, majd 30 másodpercet vár, amíg további üzenetek jelennek meg a várólistában vagy a témakör-előfizetésben. Ha 30 másodpercen belül nem jelenik meg üzenet, a program kihagyja az eseményindító futását. Ellenkező esetben az eseményindító addig folytatja az üzenetek olvasását, amíg a várólista- vagy témakör-előfizetés üres nem lesz. A következő eseményindító lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési időközön alapul.

   Egyes eseményindítók, például a **Amikor egy vagy több üzenet érkezik egy várólistába (automatikus kiegészítés)** eseményindító, visszaadhat egy vagy több üzenetet. Amikor ezek az eseményindítók aktiválódnak, egy és az eseményindító Maximális **üzenetszám** tulajdonsága által megadott üzenetek száma között térnek vissza.

1. Ha az eseményindító először csatlakozik a Service Bus névtérhez, kövesse az alábbi lépéseket, amikor a Logic App Designer csatlakozási adatokat kér.

   1. Adja meg a kapcsolat nevét, és válassza ki a Service Bus névterét.

      ![Service Bus-kapcsolat létrehozása, 1.](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Ha manuálisan szeretné megadni a kapcsolati karakterláncot, válassza **a Kapcsolatadatainak manuális megadása**lehetőséget. Ha nem rendelkezik a kapcsolati karakterlánccal, olvassa el, [hogyan találhatja meg a kapcsolati karakterláncot.](#permissions-connection-string)

   1. Válassza ki a Service Bus házirendet, és válassza a **Létrehozás gombot.**

      ![Service Bus-kapcsolat létrehozása, 2.](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Válassza ki a kívánt üzenetkezelő entitást, például egy várólistát vagy témakört. Ebben a példában válassza ki a Service Bus-várólistát.
   
      ![A Service Bus várólista kiválasztása](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Adja meg a kiválasztott eseményindítóhoz szükséges információkat. Ha további elérhető tulajdonságokat szeretne hozzáadni a művelethez, nyissa meg az **Új paraméter hozzáadása** listát, és jelölje ki a kívánt tulajdonságokat.

   Ebben a példában az eseményindító, válassza ki a lekérdezési időköz és gyakorisága a várólista ellenőrzéséhez.

   ![Lekérdezési időköz beállítása](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Az elérhető eseményindítókról és tulajdonságokról az összekötő [referencialapján](/connectors/servicebus/)talál további információt.

1. Folytassa a logikai alkalmazás létrehozásának folytatását a kívánt műveletek hozzáadásával.

   Hozzáadhat például egy műveletet, amely e-mailt küld, amikor új üzenet érkezik. Amikor az eseményindító ellenőrzi a várólistát, és új üzenetet talál, a logikai alkalmazás futtatja a kiválasztott műveleteket a talált üzenethez.

## <a name="add-service-bus-action"></a>Service Bus hozzáadása művelet

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A művelet hozzáadásának lépése alatt válassza az **Új lépés lehetőséget.**

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), és válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása csoportkereső**mezőjében adja meg szűrőként az "azure service bus" kifejezést. A műveletek listájában jelölje ki a kívánt műveletet. 

   Ebben a példában válassza az **Üzenet küldése** műveletet.

   ![Service Bus művelet kiválasztása](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Ha a művelet első alkalommal csatlakozik a Service Bus névtérhez, kövesse az alábbi lépéseket, amikor a Logic App Designer csatlakozási adatokat kér.

   1. Adja meg a kapcsolat nevét, és válassza ki a Service Bus névterét.

      ![Service Bus-kapcsolat létrehozása, 1.](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Ha manuálisan szeretné megadni a kapcsolati karakterláncot, válassza **a Kapcsolatadatainak manuális megadása**lehetőséget. Ha nem rendelkezik a kapcsolati karakterlánccal, olvassa el, [hogyan találhatja meg a kapcsolati karakterláncot.](#permissions-connection-string)

   1. Válassza ki a Service Bus házirendet, és válassza a **Létrehozás gombot.**

      ![Service Bus-kapcsolat létrehozása, 2.](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Válassza ki a kívánt üzenetkezelő entitást, például egy várólistát vagy témakört. Ebben a példában válassza ki a Service Bus-várólistát.

      ![A Service Bus várólista kiválasztása](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Adja meg a kiválasztott művelethez szükséges részleteket. Ha további elérhető tulajdonságokat szeretne hozzáadni a művelethez, nyissa meg az **Új paraméter hozzáadása** listát, és jelölje ki a kívánt tulajdonságokat.

   Jelölje ki például a **Tartalom-** és **tartalomtípus-tulajdonságokat,** hogy hozzáadja őket a művelethez. Ezután adja meg az elküldeni kívánt üzenet tartalmát.

   ![Üzenet tartalmának és részleteinek biztosítása](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Az elérhető műveletekről és azok tulajdonságairól az összekötő [referencialapján](/connectors/servicebus/)talál további információt.

1. Folytassa a logikai alkalmazás létrehozásának folytatását a kívánt egyéb műveletek hozzáadásával.

   Hozzáadhat például egy műveletet, amely e-mailt küld az üzenet elküldéséhez.

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

## <a name="connector-reference"></a>Összekötő-referencia

A Service Bus-összekötő egyszerre legfeljebb 1500 egyedi munkamenetet takaríthat meg egy szolgáltatásbuszról az összekötő gyorsítótárába. Ha a munkamenetek száma meghaladja ezt a korlátot, a régi munkamenetek törlődnek a gyorsítótárból. További információt az [Üzenetmunkamenetek című témakörben](../service-bus-messaging/message-sessions.md)talál.

Az összekötő OpenAPI (korábbi Swagger) leírása által leírt eseményindítókkal, műveletekkel és korlátokkal kapcsolatos egyéb technikai részletekért tekintse át az összekötő [referenciaoldalát.](/connectors/servicebus/) Az Azure Service Bus Messaging szolgáltatásról a [Mi az Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
