---
title: Csatlakozhat az IBM MQ server – Azure Logic Apps
description: Elküldhetők és beolvashatók az Azure Logic Apps és az Azure-ban vagy a helyszíni IBM MQ server üzenetek
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273119"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Csatlakozhat az IBM MQ server kiszolgálóhoz, az Azure Logic Apps

Az IBM MQ-összekötő küld, és beolvassa a egy helyszíni IBM MQ Server vagy az Azure-ban tárolja. Ez az összekötő tartalmazza a Microsoft MQ-ügyfél, amely a TCP/IP-hálózaton keresztül egy távoli kiszolgálóval IBM MQ-val kommunikál. Ebben a cikkben egy alapszintű útmutató az MQ-összekötő használatára. Első lépésként keresse az üzenetsorba egy üzenet, és ismételje meg az egyéb műveleteket.

Az IBM MQ-összekötő ezeket a műveleteket tartalmaz, de nincsenek eseményindítók biztosít:

- Keresse meg egy üzenet az IBM MQ-kiszolgálóról az üzenet törlése nélkül
- Keresse meg egy üzenetköteget az IBM MQ-kiszolgálóról az üzenetek törlése nélkül
- Egyetlen üzenetet kap, és törli az üzenetet az IBM MQ server kiszolgálóhoz
- Kötegelt üzenetek fogadásához, és törli az üzeneteket az IBM MQ server kiszolgálóhoz
- Egy üzenet küldéséhez az IBM MQ server kiszolgálóhoz

## <a name="prerequisites"></a>Előfeltételek

* Ha egy helyszíni MQ server használ [a helyszíni adatátjáró telepítése](../logic-apps/logic-apps-gateway-install.md) a hálózaton belüli kiszolgálón. A kiszolgáló, ahol a helyszíni átjáró telepítve van a .NET Framework 4.6-os az MQ-összekötő működéséhez is rendelkeznie kell. Meg kell is erőforrás létrehozása az Azure-ban a helyszíni adatátjáróhoz. További információkért lásd: [a data gateway kapcsolat beállításához](../logic-apps/logic-apps-gateway-connection.md).

  Azonban ha az MQ server nyilvánosan elérhető vagy Azure-ban érhető el, nem kell az átjáró használatára.

* Hivatalosan támogatott IBM WebSphere MQ-verziók:

  * 7\.5 MQ
  * MQ 8.0
  * MQ 9.0

* A logikai alkalmazást, amelyre az MQ-művelet hozzáadása. Ez a logikai alkalmazás ugyanazon a helyen kell használnia, mint a helyszíni data gateway-kapcsolat, és már rendelkeznie kell egy eseményindítót, amely elindítja a munkafolyamatot. 

  Az MQ-összekötő nem tartozhat eseményindító, hogy hozzá kell adnia egy eseményindítót a logikai alkalmazás első. Ha például az ismétlődési eseményindító is használhatja. Ha most ismerkedik a logic apps, próbálkozzon a következővel [gyors útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Keresse meg egy adott üzenet

1. A logikai alkalmazást, az eseményindító vagy egy másik művelet alatt válassza **új lépés**. 

1. A Keresés mezőbe írja be a "mq", és válassza a következő műveletet: **Keresse meg az üzenet**

   ![Keresse meg az üzenet](media/connectors-create-api-mq/Browse_message.png)

1. Ha nem rendelkezik meglévő MQ-kapcsolat, a kapcsolat létrehozása:  

   1. A működés közben, válassza ki a **kapcsolódás helyszíni adatátjárón keresztül**.
   
   1. Adja meg a tulajdonságokat az MQ server kiszolgálóhoz.  

      A **kiszolgáló**, adja meg az MQ-kiszolgáló nevét, vagy adja meg az IP-cím egy kettőspontot és a port számát.
    
   1. Nyissa meg a **átjáró** lista, amely megjeleníti a korábban beállított gateway-kapcsolatok. Válassza ki az átjáró.
    
   1. Ha elkészült, kattintson a **Létrehozás** gombra. 
   
      A kapcsolat a példához hasonlóan néz ki:

      ![Kapcsolat tulajdonságai](media/connectors-create-api-mq/Connection_Properties.png)

1. A művelet tulajdonságainak beállítása:

   * **várólista**: Adjon meg egy üzenetsorba, amely eltér a kapcsolatot.

   * **Üzenetazonosító**, **CorrelationId**, **GroupId**, és egyéb tulajdonságok: Keresse meg a különböző MQ-üzenet tulajdonságai alapján üzenet

   * **IncludeInfo**: Adja meg **igaz** újabb üzenetet információval a kimenetben. Vagy adjon meg **hamis** nem újabb üzenetet információval a kimenetben.

   * **Időtúllépés**: Adjon meg egy értéket, mennyi ideig kell várni egy üzenet érkezik egy üres üzenetsorhoz meghatározásához. Ha semmit nem adott meg, a rendszer lekéri a várólista első üzenetébe, és nincs várakozás egy hibaüzenet jelenik meg a fordított idő.

     ![Keresse meg az üzenet tulajdonságai](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Mentés** a módosításokat, majd **futtatása** a logikai alkalmazást.

   ![Mentés és Futtatás](media/connectors-create-api-mq/Save_Run.png)

   A Futtatás befejeződése után jelennek meg a lépéseket a futtatható, és áttekintheti a kimenetet.

1. Tekintse át az egyes lépéseihez szükséges részleteket, válasszon zöld pipa. További információ a kimeneti adatok felülvizsgálatához válassza **nyers kimenetek megjelenítése**.

   ![Keresse meg a kimeneti üzenet](media/connectors-create-api-mq/Browse_message_output.png)  

   Íme néhány példa nyers kimenetét:

   ![Keresse meg az üzenet nyers kimenet](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Ha **IncludeInfo** TRUE értéket kap, a következő kimenet jelenik meg:

   ![Tallózás üzenet adatait tartalmazza](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Több üzenetek tallózása

A **keresse meg az üzenetek** művelet tartalmaz egy **BatchSize** beállítás jelzi, hogy hány üzenetet a rendszer visszalépteti az üzenetsorból.  Ha **BatchSize** nem tartozik bejegyzés, minden üzenetet adja vissza. A visszaadott kimenete egy tömb, az üzenetek.

1. Amikor hozzáadja a **keresse meg az üzenetek** művelet, az első korábban konfigurált kapcsolati alapértelmezés szerint ki van választva. Új kapcsolat létrehozásához válassza a **kapcsolat módosítása**. Vagy válasszon ki egy másik kapcsolatot.

1. A logikai alkalmazás futtatása befejeződik, után Íme néhány példa kimenetében a **keresse meg az üzenetek** művelet:

   ![Keresse meg a kimeneti üzenetek](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Egyetlen üzenet fogadása

A **Receive message** művelet van ugyanazon a forráson, és kiírja a **Tallózás üzenet** művelet. Használata esetén **Receive message**, az üzenet törlődik az üzenetsorból.

## <a name="receive-multiple-messages"></a>Több üzenetek fogadása

A **üzenetek fogadása** művelet van ugyanazon a forráson, és kiírja a **keresse meg az üzenetek** művelet. Használata esetén **üzenetek fogadása**, az üzenetek törlése az üzenetsorból.

Ha nincsenek üzenetek az üzenetsorban a Tallózás gombra, vagy a fogadó oldalon, a lépés sikertelen lesz, és ez a kimenet:  

![MQ nem jelenik meg hibaüzenet](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Üzenet küldése

Amikor hozzáadja a **üzenetküldés** művelet, az első korábban konfigurált kapcsolati alapértelmezés szerint ki van választva. Új kapcsolat létrehozásához válassza a **kapcsolat módosítása**. Vagy válasszon ki egy másik kapcsolatot.

1. Válasszon egy érvényes üzenet típusa: **Datagram**, **válasz**, vagy **kérése**  

   ![Msg Kellékek küldése](media/connectors-create-api-mq/Send_Msg_Props.png)

1. A logikai alkalmazás futtatásának befejezését követően Íme néhány példa kimenetében a **üzenetküldés** művelet:

   ![Msg kimenet](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek műveleteket és korlátait, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/mq/).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
