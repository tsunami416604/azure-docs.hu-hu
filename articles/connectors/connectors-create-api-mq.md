---
title: Kapcsolódás az IBM MQ-kiszolgálóhoz
description: Üzenetek küldése és lekérése egy Azure-beli vagy helyszíni IBM MQ-kiszolgálóval és Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80410272"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Kapcsolódás IBM MQ-kiszolgálóhoz Azure Logic Apps

Az IBM MQ-összekötő a helyszínen vagy az Azure-ban egy IBM MQ-kiszolgálón tárolt üzeneteket küld és kérdez le. Ez az összekötő egy olyan Microsoft MQ-ügyfelet tartalmaz, amely egy távoli IBM MQ-kiszolgálóval kommunikál egy TCP/IP-hálózaton keresztül. Ez a cikk egy alapszintű útmutatót tartalmaz az MQ-összekötő használatához. Először egyetlen üzenetben tallózhat egy várólistán, majd más műveleteket is kipróbálhat.

Az IBM MQ-összekötő tartalmazza ezeket a műveleteket, de nem biztosít eseményindítókat:

- Egyetlen üzenet tallózása az üzenetnek az IBM MQ-kiszolgálóról való törlése nélkül.
- Az IBM MQ-kiszolgálóról érkező üzenetek törlése nélkül böngészhet egy üzenetben.
- Egyetlen üzenetet kap, és törli az üzenetet az IBM MQ-kiszolgálóról.
- Egy köteg üzenetet fogad, és törli az üzeneteket az IBM MQ-kiszolgálóról.
- Egyetlen üzenet küldése az IBM MQ-kiszolgálónak.

Itt láthatók a hivatalosan támogatott IBM WebSphere MQ-verziók:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0

## <a name="prerequisites"></a>Előfeltételek

* Ha helyszíni MQ-kiszolgálót használ, [telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) a hálózatán belüli kiszolgálóra. A helyszíni adatátjáró telepítésére szolgáló kiszolgálónak is telepítve kell lennie a .NET-keretrendszer 4,6-es frissítésének az MQ-összekötő működéséhez.

  Az átjáró telepítésének befejezése után létre kell hoznia egy erőforrást az Azure-ban a helyszíni adatátjáróhoz. További információ: [az adatátjáró-kapcsolatok beállítása](../logic-apps/logic-apps-gateway-connection.md).

  Ha az MQ-kiszolgáló nyilvánosan elérhető, vagy az Azure-on belül elérhető, nem kell használnia az adatátjárót.

* Az a logikai alkalmazás, amelyhez hozzá kívánja adni az MQ-műveletet. A logikai alkalmazásnak ugyanazt a helyet kell használnia, mint a helyszíni adatátjáró-kapcsolatban, és rendelkeznie kell egy olyan triggerrel, amely elindítja a munkafolyamatot.

  Az MQ-összekötő nem rendelkezik eseményindítókkal, ezért először hozzá kell adnia egy eseményindítót a logikai alkalmazáshoz. Használhatja például az ismétlődési eseményindítót. Ha most ismerkedik a Logic apps szolgáltatással, próbálja ki ezt a rövid útmutatót az [első logikai alkalmazás létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ-kapcsolatok létrehozása

Ha egy MQ-művelet hozzáadásakor még nem rendelkezik MQ-kapcsolattal, a rendszer felszólítja a kapcsolat létrehozására, például:

![Adja meg a kapcsolatok adatait](media/connectors-create-api-mq/connection-properties.png)

1. Ha helyszíni MQ-kiszolgálóhoz csatlakozik, válassza **a csatlakozás helyszíni adatátjárón keresztül**lehetőséget.

1. Adja meg az MQ-kiszolgáló elérhetőségi adatait.

   * A **Server**esetében megadhatja az MQ-kiszolgáló nevét, vagy megadhatja az IP-címet, majd egy kettőspontot és a portszámot.

   * SSL (SSL) használatához válassza az **SSL engedélyezése lehetőséget?**.

     Az MQ-összekötő jelenleg csak kiszolgálói hitelesítést támogat, nem az ügyfél-hitelesítést. További információ: [kapcsolódási és hitelesítési problémák](#connection-problems).

1. Az **átjáró** szakaszban hajtsa végre az alábbi lépéseket:

   1. Az **előfizetés** listából válassza ki az Azure Gateway-erőforráshoz hozzárendelt Azure-előfizetést.

   1. A **kapcsolódási átjáró** listából válassza ki a használni kívánt Azure Gateway-erőforrást.

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Kapcsolódási és hitelesítési problémák

Ha a logikai alkalmazás megpróbál csatlakozni a helyszíni MQ-kiszolgálóhoz, a következő hibaüzenet jelenhet meg:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Ha közvetlenül az Azure-ban használja az MQ-összekötőt, az MQ-kiszolgálónak egy megbízható [hitelesítésszolgáltató](https://www.ssl.com/faqs/what-is-a-certificate-authority/)által kiadott tanúsítványt kell használnia.

* Ha a helyszíni adatátjárót használja, próbáljon meg egy megbízható [hitelesítésszolgáltató](https://www.ssl.com/faqs/what-is-a-certificate-authority/) által kiadott tanúsítványt használni, ha lehetséges. Ha azonban ez a lehetőség nem lehetséges, használhat önaláírt tanúsítványt, amelyet nem megbízható [hitelesítésszolgáltató](https://www.ssl.com/faqs/what-is-a-certificate-authority/) állít ki, és kevésbé biztonságosnek tekinti.

  A kiszolgáló önaláírt tanúsítványának telepítéséhez használhatja a **Windows Certification Manager** (certmgr. msc) eszközt. Ebben az esetben azon a helyi számítógépen, amelyen a helyszíni adatátjáró szolgáltatás fut, telepítenie kell a tanúsítványt a **helyi számítógép** tanúsítványtárolójában a **megbízható legfelső szintű hitelesítésszolgáltatók** szintjén.

  1. Azon a számítógépen, amelyen a helyszíni adatátjáró szolgáltatás fut, nyissa meg a Start menüt, keresse meg és válassza a **felhasználói tanúsítványok kezelése**elemet.

  1. A Windows hitelesítésszolgáltató eszköz megnyitása után nyissa meg a **tanúsítványok – helyi számítógép** >  **megbízható legfelső szintű hitelesítésszolgáltatók** mappáját, és telepítse a tanúsítványt.

     > [!IMPORTANT]
     > Ügyeljen arra, hogy a tanúsítványokat a **tanúsítványok – helyi számítógép** > **megbízható legfelső szintű hitelesítésszolgáltatók** tárolójába telepítse.

* Az MQ-kiszolgáló megköveteli az SSL-kapcsolatokhoz használni kívánt titkosítási specifikáció megadását. A .NET-beli SsLStream azonban nem teszi lehetővé a titkosítási specifikációk megadását. A korlátozás megkerüléséhez módosítsa az MQ-kiszolgáló konfigurációját úgy, hogy az megfeleljen az összekötő által az SSL-egyeztetésben elküldött csomag első titkosítási specifikációjának.

  A kapcsolat kipróbálásakor az MQ-kiszolgáló naplóz egy olyan eseményt, amely jelzi, hogy a kapcsolat nem sikerült, mert a másik végén a helytelen titkosítási specifikációt használta. Az esemény üzenet tartalmazza a listában elsőként megjelenő titkosítási specifikációt. Frissítse a titkosítási specifikációt a csatorna konfigurációjában, hogy az megfeleljen a titkosítási specifikációnak az esemény üzenetében.

## <a name="browse-single-message"></a>Egyetlen üzenet tallózása

1. A logikai alkalmazás trigger vagy más művelet területén válassza az **új lépés**lehetőséget.

1. A keresőmezőbe írja be `mq`a kifejezést, majd válassza ki a **tallózási üzenet** műveletet.

   ![Az "üzenet tallózása" művelet kiválasztása](media/connectors-create-api-mq/browse-message.png)

1. Ha még nem hozott létre MQ-kapcsolatokat, a rendszer kéri, hogy [hozza létre ezt a kapcsolódást](#create-connection).

1. A kapcsolat létrehozása után állítsa be a **tallózási üzenet** műveletének tulajdonságait:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Várólista** | Ha eltér a kapcsolatban megadott sorból, adja meg a várólistát. |
   | **MessageID**, **correlationId**, **GroupID**és egyéb tulajdonságok | A különböző MQ-üzenet tulajdonságain alapuló üzenet keresése |
   | **IncludeInfo** | Ha további üzeneteket szeretne szerepeltetni a kimenetben, válassza az **igaz**lehetőséget. Ha további üzeneteket szeretne kihagyni a kimenetben, válassza a **false (hamis**) lehetőséget. |
   | **Időtúllépés** | Adja meg azt az értéket, amely meghatározza, hogy a rendszer mennyi ideig várjon, amíg egy üzenet beérkezik egy üres várólistába. Ha nincs megadva, a rendszer beolvassa a várólista első üzenetét, és nem vár időt arra, hogy megjelenjen az üzenet. |
   |||

   Például:

   ![A "Tallózás üzenet" művelet tulajdonságai](media/connectors-create-api-mq/browse-message-properties.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget. Az alkalmazás teszteléséhez válassza a **Futtatás**lehetőséget.

   A Futtatás befejezése után a tervező megjeleníti a munkafolyamat lépéseit és az állapotukat, hogy áttekintse a kimenetet.

1. Az egyes lépések részleteinek megtekintéséhez kattintson a Step címsorára. A lépés kimenetével kapcsolatos további információkért válassza a **nyers kimenetek megjelenítése**elemet.

   ![Üzenet kimenetének tallózása](media/connectors-create-api-mq/browse-message-output.png)

   Íme néhány példa a nyers kimenetre:

   ![Üzenet nyers kimenetének tallózása](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Ha a **IncludeInfo** értéke **true (igaz**), további kimenet jelenik meg:

   ![Tallózási üzenet – ide-információ](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Több üzenet tallózása

Az **üzenetek tallózása** művelet tartalmaz egy **batchSize** beállítást, amely azt jelzi, hogy hány üzenetnek kell visszaadnia a várólistából. Ha a **batchSize** nem rendelkezik értékkel, a rendszer az összes üzenetet visszaadja. A visszaadott kimenet az üzenetek tömbje.

1. Kövesse az előző lépéseket, de ehelyett adja hozzá a **tallózási üzenetek** műveletet.

1. Ha még nem hozott létre MQ-kapcsolatokat, a rendszer kéri, hogy [hozza létre ezt a kapcsolódást](#create-connection). Ellenkező esetben a rendszer alapértelmezés szerint az első korábban konfigurált kapcsolatokat használja. Új kapcsolatok létrehozásához válassza a **Kapcsolódás módosítása**lehetőséget. Vagy válasszon másikat.

1. Adja meg a művelet adatait.

1. Mentse és futtassa a logikai alkalmazást.

   A logikai alkalmazás futásának befejeződése után íme néhány példa a **Tallózás üzenetei** műveletből:

   ![Példa az "üzenetek tallózása" kimenetre](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Egyetlen üzenet fogadása

Az **üzenet fogadása** művelettel azonos bemenetek és kimenetek jelennek meg a **tallózási üzenet** művelettel. Ha a **fogadási üzenetet**használja, a rendszer törli az üzenetet a várólistából.

## <a name="receive-multiple-messages"></a>Több üzenet fogadása

Az **üzenetek fogadása** művelet ugyanazokat a bemeneteket és kimeneteket adja meg, mint a **Tallózás üzenetei** művelet. Ha **fogadási üzeneteket**használ, a rendszer törli az üzeneteket a várólistából.

> [!NOTE]
> Ha olyan várólistán futtat egy tallózási vagy fogadási műveletet, amely nem tartalmaz üzeneteket, a művelet a következő kimenettel meghiúsul:
>
> ![MQ "nincs üzenet" hiba](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Üzenet küldése

1. Kövesse az előző lépéseket, de ehelyett adja hozzá az **üzenet küldése** műveletet.

1. Ha még nem hozott létre MQ-kapcsolatokat, a rendszer kéri, hogy [hozza létre ezt a kapcsolódást](#create-connection). Ellenkező esetben a rendszer alapértelmezés szerint az első korábban konfigurált kapcsolatokat használja. Új kapcsolatok létrehozásához válassza a **Kapcsolódás módosítása**lehetőséget. Vagy válasszon másikat.

1. Adja meg a művelet adatait. A **MessageType**mezőben válasszon egy érvényes üzenetet: **datagram**, **Válasz**vagy **kérelem**

   ![Az "üzenetküldési művelet" tulajdonságai](media/connectors-create-api-mq/send-message-properties.png)

1. Mentse és futtassa a logikai alkalmazást.

   A logikai alkalmazás futásának befejeződése után a következő példa az **üzenet küldése** műveletből származó kimenet:

   ![Példa az "üzenet küldése" kimenetre](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Összekötő-referencia

A műveletekkel és korlátokkal kapcsolatos technikai részletekért, amelyeket az összekötő hencegő leírása ismertet, tekintse át az összekötő [hivatkozási oldalát](/connectors/mq/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
