---
title: Csatlakozás az IBM MQ kiszolgálóhoz
description: Üzenetek küldése és beolvasása Azure-ban vagy helyszíni IBM MQ-kiszolgálóval és Azure Logic Apps alkalmazásokkal
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650947"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Csatlakozás IBM MQ-kiszolgálóhoz az Azure Logic Apps alkalmazásból

Az IBM MQ-összekötő a helyszínen vagy az Azure-ban egy IBM MQ-kiszolgálón tárolt üzeneteket küld és olvas le. Ez az összekötő egy Microsoft MQ-ügyfelet tartalmaz, amely egy távoli IBM MQ-kiszolgálóval kommunikál tcp/IP hálózaton keresztül. Ez a cikk egy kezdő útmutatót tartalmaz az MQ-csatlakozó használatához. Először egy üzenetet böngészhet egy várólistán, majd próbálkozzon más műveletekkel.

Az IBM MQ-összekötő tartalmazza ezeket a műveleteket, de nem biztosít eseményindítókat:

- Egyetlen üzenet böngészése az IBM MQ-kiszolgálóról való üzenet törlése nélkül
- Üzenetköteg böngészése az IBM MQ-kiszolgálóról érkező üzenetek törlése nélkül
- Egyetlen üzenet fogadása és az üzenet törlése az IBM MQ kiszolgálóról
- Üzenetek kötegének fogadása és az IBM MQ-kiszolgálóról érkező üzenetek törlése
- Egyetlen üzenet küldése az IBM MQ szerverre

## <a name="prerequisites"></a>Előfeltételek

* Ha helyszíni MQ-kiszolgálót használ, [telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) a hálózaton belüli kiszolgálóra. Annak a kiszolgálónak, ahol a helyszíni adatátjáró telepítve van, az MQ-összekötő működéséhez is telepítve kell lennie .NET Framework 4.6 rendszerrel. A helyszíni adatátjáróhoz is létre kell hoznia egy erőforrást az Azure-ban. További információt [az Adatátjáró-kapcsolat beállítása](../logic-apps/logic-apps-gateway-connection.md)című témakörben talál.

  Ha azonban az MQ-kiszolgáló nyilvánosan elérhető vagy elérhető az Azure-ban, nem kell használnia az adatátjárót.

* Hivatalosan támogatott IBM WebSphere MQ verziók:

  * MQ 7,5
  * MQ 8.0
  * MQ 9.0

* Az a logikai alkalmazás, amelyhez hozzá szeretné adni az MQ-műveletet. Ennek a logikai alkalmazásnak ugyanazt a helyet kell használnia, mint a helyszíni adatátjáró-kapcsolatnak, és már rendelkeznie kell egy eseményindítóval, amely elindítja a munkafolyamatot. 

  Az MQ-összekötő nem rendelkezik eseményindítókkal, ezért először hozzá kell adnia egy eseményindítót a logikai alkalmazáshoz. Használhatja például az Ismétlődés eseményindítót. Ha most jön a logikai alkalmazások, próbálja meg ezt a [rövid útmutatót az első logikai alkalmazás létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="browse-a-single-message"></a>Egyetlen üzenet böngészése

1. A logikai alkalmazásban az eseményindító vagy egy másik művelet alatt válassza az **Új lépés lehetőséget.** 

1. A keresőmezőbe írja be az "mq" parancsot, és válassza a műveletet: **Üzenet tallózása**

   ![Üzenet tallózása](media/connectors-create-api-mq/Browse_message.png)

1. Ha nem rendelkezik meglévő MQ-kapcsolattal, hozza létre a kapcsolatot:  

   1. A műveletben válassza a **Csatlakozás helyszíni adatátjárón keresztül**lehetőséget.
   
   1. Adja meg az MQ-kiszolgáló tulajdonságait.  

      Server esetén **megadhatja**az MQ-kiszolgáló nevét, vagy megadhatja az IP-címet, amelyet kettőspont és a portszám követ.
    
   1. Nyissa meg az **átjárólistát,** amely a korábban konfigurált átjárókapcsolatokat jeleníti meg. Válassza ki az átjárót.
    
   1. Ha elkészült, kattintson a **Létrehozás** gombra. 
   
      A kapcsolat a következő példához hasonlóan néz ki:

      ![Kapcsolat tulajdonságai](media/connectors-create-api-mq/Connection_Properties.png)

1. A művelet tulajdonságainak beállítása:

   * **Várólista**: Adjon meg egy, a kapcsolattól eltérő várólistát.

   * **MessageId**, **CorrelationId**, **GroupId**és egyéb tulajdonságok: Üzenet tallózása az MQ-üzenetek különböző tulajdonságai alapján

   * **IncludeInfo**: Adja meg **a True** értéket, ha további üzenetadatokat szeretne felvenni a kimenetbe. Vagy adja meg a **False** értéket, ha nem szeretne további üzenetinformációkat felvenni a kimenetbe.

   * **Időtúltöltés**: Adjon meg egy értéket annak meghatározásához, hogy mennyi ideig várjon az üzenet üres várólistába érkezésére. Ha semmit sem ad meg, a rendszer beolvassa a várólista első üzenetét, és nincs idő arra, hogy megvárja az üzenet megjelenését.

     ![Üzenet tulajdonságainak tallózása](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Mentse** a módosításokat, majd **futtassa** a logikai alkalmazást.

   ![Mentés és futtatás](media/connectors-create-api-mq/Save_Run.png)

   A futtatás befejezése után a futtatás lépései megjelennek, és áttekintheti a kimenetet.

1. Az egyes lépcsők részleteinek áttekintéséhez jelölje be a zöld pipát. A kimeneti adatokkal kapcsolatos további információk áttekintéséhez válassza a **Nyers kimenetek megjelenítése**lehetőséget.

   ![Üzenet kimenetének tallózása](media/connectors-create-api-mq/Browse_message_output.png)  

   Itt van néhány minta nyers kimenet:

   ![Üzenet nyers kimenetének tallózása](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Ha **az IncludeInfo értéket** igaz értékre állítja, a következő kimenet jelenik meg:

   ![Tallózás üzenet információkat tartalmaz](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Több üzenet böngészése

Az **Üzenetek tallózása** művelet tartalmaz egy **BatchSize** beállítást, amely jelzi, hogy hány üzenetet kell visszaküldeni a várólistából.  Ha **a BatchSize** nem rendelkezik bejegyzéssel, a rendszer az összes üzenetet visszaadja. A visszaadott kimenet üzenetek tömbje.

1. A **Tallózás üzenetek** művelet hozzáadásakor alapértelmezés szerint az első, korábban konfigurált kapcsolat lesz kijelölve. Új kapcsolat létrehozásához válassza a **Kapcsolat módosítása**lehetőséget. Vagy válasszon másik kapcsolatot.

1. A logikai alkalmazás futásának befejezése után az üzenetek **tallózása** művelet ből néhány mintakimenet látható:

   ![Üzenetek kimenetének tallózása](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Egyetlen üzenet fogadása

Az **Üzenet fogadása** művelet ugyanazokat a bemeneteket és kimeneteket, mint a **Tallózás üzenet** művelet. A **Fogadás üzenet**használatakor az üzenet törlődik a várólistából.

## <a name="receive-multiple-messages"></a>Több üzenet fogadása

Az **Üzenetek fogadása** művelet ugyanazokat a bemeneteket és kimeneteket tartalmaz, mint az **Üzenetek tallózása** művelet. Az **Üzenetek fogadása**használatakor az üzenetek törlődnek a várólistából.

Ha böngészés vagy fogadás közben nincsenek üzenetek a várólistában, a lépés ezzel a kimenettel sikertelen lesz:  

![MQ nincs üzenethiba](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Üzenet küldése

Az Üzenetek **küldése** művelet hozzáadásakor alapértelmezés szerint az első, korábban konfigurált kapcsolat lesz kijelölve. Új kapcsolat létrehozásához válassza a **Kapcsolat módosítása**lehetőséget. Vagy válasszon másik kapcsolatot.

1. Érvényes üzenettípus kiválasztása: **Datagram**, **Válasz**vagy **Kérés**  

   ![Msg kellékek küldése](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Miután a logikai alkalmazás futása befejeződik, itt van néhány minta kimenet az **Üzenet küldése** műveletből:

   ![Msg kimenet küldése](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részleteket, például az eseményindítókat, műveleteket és korlátokat az összekötő Swagger-fájlja szerint lásd az [összekötő referencialapján.](https://docs.microsoft.com/connectors/mq/)

> [!NOTE]
> [Az integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lévő logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója az [ISE-üzenetkorlátokat](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja.

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
