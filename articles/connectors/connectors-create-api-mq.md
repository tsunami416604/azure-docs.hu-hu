---
title: Kapcsolódás az IBM MQ-kiszolgálóhoz
description: Üzenetek küldése és lekérése egy Azure-beli vagy helyszíni IBM MQ-kiszolgálóval és Azure Logic Apps
services: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ef9e91b526055ece58ce283572deb98cff951653
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789578"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Kapcsolódás IBM MQ-kiszolgálóhoz Azure Logic Apps

Az IBM MQ-összekötő a helyszínen vagy az Azure-ban egy IBM MQ-kiszolgálón tárolt üzeneteket küld és kérdez le. Ez az összekötő egy olyan Microsoft MQ-ügyfelet tartalmaz, amely egy távoli IBM MQ-kiszolgálóval kommunikál egy TCP/IP-hálózaton keresztül. Ez a cikk egy alapszintű útmutatót tartalmaz az MQ-összekötő használatához. Először egyetlen üzenetben tallózhat egy várólistán, majd más műveleteket is kipróbálhat.

Az IBM MQ-összekötő tartalmazza ezeket a műveleteket, de nem biztosít eseményindítókat:

- Egyetlen üzenet tallózása az üzenetnek az IBM MQ-kiszolgálóról való törlése nélkül
- Üzenetek egy kötegének tallózása az IBM MQ-kiszolgálóról érkező üzenetek törlése nélkül
- Egyetlen üzenet fogadása és az üzenet törlése az IBM MQ-kiszolgálóról
- Üzenetek kötegének fogadása és az üzenetek törlése az IBM MQ-kiszolgálóról
- Egyetlen üzenet küldése az IBM MQ-kiszolgálónak

## <a name="prerequisites"></a>Előfeltételek

* Ha helyszíni MQ-kiszolgálót használ, [telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) a hálózatán belüli kiszolgálóra. A helyszíni adatátjáró telepítésére szolgáló kiszolgálónak is telepítve kell lennie a .NET-keretrendszer 4,6-es frissítésének az MQ-összekötő működéséhez. A helyszíni adatátjáróhoz is létre kell hoznia egy erőforrást az Azure-ban. További információ: [az adatátjáró-kapcsolatok beállítása](../logic-apps/logic-apps-gateway-connection.md).

  Ha azonban az MQ-kiszolgáló nyilvánosan elérhető, vagy az Azure-on belül elérhető, nem kell használnia az adatátjárót.

* Hivatalosan támogatott IBM WebSphere MQ-verziók:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

* Az a logikai alkalmazás, amelyhez hozzá kívánja adni az MQ-műveletet. A logikai alkalmazásnak ugyanazt a helyet kell használnia, mint a helyszíni adatátjáró-kapcsolatban, és rendelkeznie kell egy olyan triggerrel, amely elindítja a munkafolyamatot. 

  Az MQ-összekötő nem rendelkezik eseményindítókkal, ezért először hozzá kell adnia egy eseményindítót a logikai alkalmazáshoz. Használhatja például az ismétlődési eseményindítót. Ha most ismerkedik a Logic apps szolgáltatással, próbálja ki ezt a rövid útmutatót az [első logikai alkalmazás létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Egyetlen üzenet tallózása

1. A logikai alkalmazás trigger vagy más művelet területén válassza az **új lépés**lehetőséget. 

1. A keresőmezőbe írja be az "MQ" kifejezést, és válassza ki ezt a műveletet: **Tallózás üzenet**

   ![Tallózási üzenet](media/connectors-create-api-mq/Browse_message.png)

1. Ha nem rendelkezik meglévő MQ-kapcsolatban, hozza létre a (z) kapcsolatokat:  

   1. A műveletben válassza a **kapcsolat helyszíni adatátjárón keresztül**lehetőséget.
   
   1. Adja meg az MQ-kiszolgáló tulajdonságait.  

      A **Server**esetében megadhatja az MQ-kiszolgáló nevét, vagy megadhatja az IP-címet, majd egy kettőspontot és a portszámot.
    
   1. Nyissa meg az **átjárók** listáját, amely megjeleníti az előzőleg konfigurált átjárók kapcsolatait. Válassza ki az átjárót.
    
   1. Ha elkészült, kattintson a **Létrehozás** gombra. 
   
      A kapcsolatok a következő példához hasonlóan néz ki:

      ![Kapcsolatok tulajdonságai](media/connectors-create-api-mq/Connection_Properties.png)

1. A művelet tulajdonságainak beállítása:

   * **Üzenetsor**: olyan várólistát ad meg, amely eltér a kapcsolatoktól.

   * **MessageID**, **correlationId**, **GroupID**és egyéb tulajdonságok: a különböző MQ-üzenet tulajdonságai alapján megkeresheti az üzeneteket.

   * **IncludeInfo**: adja meg az **igaz** értéket, hogy a kimenetben szerepeljenek további üzenetek. Vagy adja meg a **Hamis értéket** , ha a kimenetben nem szerepel további üzenet-információ.

   * **Időtúllépés**: adjon meg egy értéket annak meghatározásához, hogy mennyi ideig kell várni, amíg egy üzenet megérkezik egy üres várólistába. Ha nincs megadva, a rendszer beolvassa a várólista első üzenetét, és nem vár időt arra, hogy megjelenjen az üzenet.

     ![Tallózási üzenet tulajdonságai](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Mentse** a módosításokat, majd **futtassa** a logikai alkalmazást.

   ![Mentés és Futtatás](media/connectors-create-api-mq/Save_Run.png)

   A Futtatás befejezése után a Futtatás lépései megjelennek, és áttekintheti a kimenetet.

1. Az egyes lépések részleteinek áttekintéséhez kattintson a zöld pipa jelre. A kimeneti adatokkal kapcsolatos további információkért válassza a **nyers kimenetek megjelenítése**elemet.

   ![Üzenet kimenetének tallózása](media/connectors-create-api-mq/Browse_message_output.png)  

   Íme néhány példa a nyers kimenetre:

   ![Üzenet nyers kimenetének tallózása](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Ha a **IncludeInfo** értéke TRUE (igaz), a következő kimenet jelenik meg:

   ![Tallózási üzenet – ide-információ](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Több üzenet tallózása

Az **üzenetek tallózása** művelet tartalmaz egy **batchSize** beállítást, amely azt jelzi, hogy hány üzenetet kell visszaadni a várólistából.  Ha a **batchSize** nem rendelkezik bejegyzéssel, a rendszer az összes üzenetet visszaadja. A visszaadott kimenet az üzenetek tömbje.

1. Ha hozzáadja a **tallózási üzenetek** műveletet, a rendszer alapértelmezés szerint az első korábban konfigurált csatlakozást választja. Új kapcsolatok létrehozásához válassza a **Kapcsolódás módosítása**lehetőséget. Vagy válasszon másikat.

1. A logikai alkalmazás futtatásának befejeződése után íme néhány példa a **Tallózás üzenetei** műveletből:

   ![Üzenetek kimenetének tallózása](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Egyetlen üzenet fogadása

Az **üzenet fogadása** művelettel azonos bemenetek és kimenetek jelennek meg a **tallózási üzenet** művelettel. A **fogadási üzenet**használatakor a rendszer törli az üzenetet a várólistából.

## <a name="receive-multiple-messages"></a>Több üzenet fogadása

Az **üzenetek fogadása** művelet ugyanazokat a bemeneteket és kimeneteket adja meg, mint a **Tallózás üzenetei** művelet. A **fogadási üzenetek**használatakor az üzenetek törlődnek a várólistából.

Ha a várólistán nem találhatók üzenetek a Tallózás vagy a fogadás során, a lépés a következő kimenettel meghiúsul:  

![MQ – nincs hibaüzenet](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Üzenet küldése

Az **üzenetek küldése** művelet hozzáadásakor az első korábban konfigurált kapcsolat alapértelmezés szerint ki van választva. Új kapcsolatok létrehozásához válassza a **Kapcsolódás módosítása**lehetőséget. Vagy válasszon másikat.

1. Érvényes üzenet típusának kiválasztása: **datagram**, **Válasz**vagy **kérelem**  

   ![Msg-kellékek küldése](media/connectors-create-api-mq/Send_Msg_Props.png)

1. A logikai alkalmazás futásának befejeződése után a következő példa az **üzenet küldése** műveletből származó kimenet:

   ![Msg-kimenet küldése](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Összekötő-referencia

A műveletekkel és korlátokkal kapcsolatos technikai részletekért, amelyeket az összekötő OpenAPI (korábban hencegett) leírása ismertet, tekintse át az összekötő [hivatkozási oldalát](/connectors/mq/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
