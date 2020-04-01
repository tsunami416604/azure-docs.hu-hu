---
title: Csatlakozás az IBM MQ kiszolgálóhoz
description: Üzenetek küldése és beolvasása Azure-ban vagy helyszíni IBM MQ-kiszolgálóval és Azure Logic Apps alkalmazásokkal
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410272"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Csatlakozás IBM MQ-kiszolgálóhoz az Azure Logic Apps alkalmazásból

Az IBM MQ-összekötő a helyszínen vagy az Azure-ban egy IBM MQ-kiszolgálón tárolt üzeneteket küld és olvas le. Ez az összekötő egy Microsoft MQ-ügyfelet tartalmaz, amely egy távoli IBM MQ-kiszolgálóval kommunikál tcp/IP hálózaton keresztül. Ez a cikk egy kezdő útmutatót tartalmaz az MQ-csatlakozó használatához. Először egy üzenetet böngészhet egy várólistán, majd próbálkozzon más műveletekkel.

Az IBM MQ-összekötő tartalmazza ezeket a műveleteket, de nem biztosít eseményindítókat:

- Egyetlen üzenet ben tallózhat anélkül, hogy az IBM MQ kiszolgálóról ki nem vette volna az üzenetet.
- Tallózzon az üzenetek egy kötegében anélkül, hogy az IBM MQ kiszolgálóról származó üzeneteket lelenne sújtané.
- Egyetlen üzenet fogadása és az üzenet törlése az IBM MQ kiszolgálóról.
- Fogadjon egy köteg üzenetet, és törölje az üzeneteket az IBM MQ szerverről.
- Egyetlen üzenet küldése az IBM MQ-kiszolgálónak.

Itt vannak a hivatalosan támogatott IBM WebSphere MQ verziók:

  * MQ 7,5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Előfeltételek

* Ha helyszíni MQ-kiszolgálót használ, [telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) a hálózaton belüli kiszolgálóra. Annak a kiszolgálónak, ahol a helyszíni adatátjáró telepítve van, az MQ-összekötő működéséhez is telepítve kell lennie .NET Framework 4.6 rendszerrel.

  Miután befejezte az átjáró telepítését, létre kell hoznia egy erőforrást az Azure-ban a helyszíni adatátjáróhoz. További információt [az Adatátjáró-kapcsolat beállítása](../logic-apps/logic-apps-gateway-connection.md)című témakörben talál.

  Ha az MQ-kiszolgáló nyilvánosan elérhető vagy elérhető az Azure-ban, nem kell használnia az adatátjárót.

* Az a logikai alkalmazás, amelyhez hozzá szeretné adni az MQ-műveletet. Ennek a logikai alkalmazásnak ugyanazt a helyet kell használnia, mint a helyszíni adatátjáró-kapcsolatnak, és már rendelkeznie kell egy eseményindítóval, amely elindítja a munkafolyamatot.

  Az MQ-összekötő nem rendelkezik eseményindítókkal, ezért először hozzá kell adnia egy eseményindítót a logikai alkalmazáshoz. Használhatja például az Ismétlődés eseményindítót. Ha most jön a logikai alkalmazások, próbálja meg ezt a [rövid útmutatót az első logikai alkalmazás létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>MQ-kapcsolat létrehozása

Ha még nem rendelkezik MQ-kapcsolattal, amikor MQ-műveletet ad hozzá, a rendszer kéri a kapcsolat létrehozását, például:

![Kapcsolati információk biztosítása](media/connectors-create-api-mq/connection-properties.png)

1. Ha helyszíni MQ-kiszolgálóhoz csatlakozik, válassza a **Csatlakozás helyszíni adatátjárón keresztül**lehetőséget.

1. Adja meg az MQ-kiszolgáló csatlakozási adatait.

   * Server esetén **megadhatja**az MQ-kiszolgáló nevét, vagy megadhatja az IP-címet, amelyet kettőspont és a portszám követ.

   * A Secure Sockets Layer (SSL) használatához válassza **az SSL engedélyezése lehetőséget.**

     Az MQ-összekötő jelenleg csak a kiszolgáló hitelesítését támogatja, az ügyfélhitelesítést nem. További információt a [Csatlakozási és hitelesítési problémák című témakörben talál.](#connection-problems)

1. Az **átjáró** szakaszban hajtsa végre az alábbi lépéseket:

   1. Az **Előfizetés** ek listájából válassza ki az Azure-átjáró-erőforráshoz társított Azure-előfizetést.

   1. A **Connection Gateway** listából válassza ki a használni kívánt Azure-átjáró-erőforrást.

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Csatlakozási és hitelesítési problémák

Amikor a logikai alkalmazás megpróbál csatlakozni a helyszíni MQ-kiszolgálóhoz, a következő hibaüzenet jelenhet meg:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Ha az MQ-összekötőt közvetlenül az Azure-ban használja, az MQ-kiszolgálónak egy megbízható hitelesítésszolgáltató által kiállított tanúsítványt kell [használnia.](https://www.ssl.com/faqs/what-is-a-certificate-authority/)

* Ha a helyszíni adatátjárót használja, ha lehetséges, próbáljon meg egy megbízható [hitelesítésszolgáltató](https://www.ssl.com/faqs/what-is-a-certificate-authority/) által kiállított tanúsítványt használni. Ha azonban ez a beállítás nem lehetséges, használhat önaláírt tanúsítványt, amelyet nem megbízható [hitelesítésszolgáltató](https://www.ssl.com/faqs/what-is-a-certificate-authority/) állít ki, és kevésbé biztonságosnak tekinthető.

  A kiszolgáló önaláírt tanúsítványának telepítéséhez használja a **Windows Tanúsítványkezelő** (certmgr.msc) eszközt. Ebben az esetben a helyi számítógépen, ahol a helyszíni adatátjáró szolgáltatás fut, telepítenie kell a tanúsítványt a **helyi számítógép** tanúsítványtárolójában a **Megbízható legfelső szintű hitelesítésszolgáltatók** szintjén.

  1. Azon a számítógépen, amelyen a helyszíni adatátjáró szolgáltatás fut, nyissa meg a Start menüt, és válassza **a Felhasználói tanúsítványok kezelése parancsot.**

  1. A Windows Certification Manager eszköz megnyitása után nyissa meg a **Tanúsítványok - Helyi számítógép** >  **megbízható legfelső szintű hitelesítésszolgáltatók** mappáját, és telepítse a tanúsítványt.

     > [!IMPORTANT]
     > Győződjön meg arról, hogy a **tanúsítványokat** > a Tanúsítványok - Helyi számítógép**megbízható legfelső szintű hitelesítésszolgáltatók** tárolójában telepíti.

* Az MQ-kiszolgáló megköveteli, hogy meghatározza az SSL-kapcsolatokhoz használni kívánt titkosítási specifikációt. Az SsLStream a .NET-ben azonban nem teszi lehetővé a titkosítási specifikációk sorrendjének megadását. A korlátozás megkerüléséhez módosíthatja az MQ-kiszolgáló konfigurációját úgy, hogy megfeleljen az összekötő által az SSL-egyeztetésben küldött csomag első titkosítási specifikációjának.

  A kapcsolat megkísérlésekor az MQ-kiszolgáló naplóz egy eseményüzenetet, amely azt jelzi, hogy a kapcsolat nem sikerült, mert a másik végpont helytelen titkosítási specifikációt használt. Az eseményüzenet a listában először megjelenő titkosítási specifikációt tartalmazza. Frissítse a titkosítási specifikációt a csatornakonfigurációban, hogy megfeleljen az eseményüzenetben lévő titkosítási specifikációnak.

## <a name="browse-single-message"></a>Egyetlen üzenet böngészése

1. A logikai alkalmazásban az eseményindító vagy egy másik művelet alatt válassza az **Új lépés**lehetőséget.

1. A keresőmezőbe írja `mq`be a t, és válassza az **Üzenet tallózása** műveletet.

   ![Válassza az "Üzenet tallózása" műveletet](media/connectors-create-api-mq/browse-message.png)

1. Ha még nem hozott létre MQ-kapcsolatot, a rendszer kéri a [kapcsolat létrehozását.](#create-connection)

1. A kapcsolat létrehozása után állítsa be a **Tallózás üzenetművelet** tulajdonságait:

   | Tulajdonság | Leírás |
   |----------|-------------|
   | **Várólista** | Ha eltér a kapcsolatban megadott várólistától, adja meg a várólistát. |
   | **MessageId**, **CorrelationId**, **GroupId**és egyéb tulajdonságok | Üzenet tallózása az MQ üzenet különböző tulajdonságai alapján |
   | **IncludeInfo** | Ha további üzenetadatokat szeretne felvenni a kimenetbe, válassza a **True**lehetőséget. Ha ki szeretné hagyni a kimenetben lévő további üzenetadatokat, válassza a **false**lehetőséget. |
   | **Időtúllépés** | Adjon meg egy értéket annak meghatározásához, hogy mennyi ideig várjon az üzenet üres várólistába érkezésére. Ha semmit sem ad meg, a rendszer beolvassa a várólista első üzenetét, és nincs idő arra, hogy megvárja az üzenet megjelenését. |
   |||

   Példa:

   ![Az "Üzenet tallózása" művelet tulajdonságai](media/connectors-create-api-mq/browse-message-properties.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.** Az alkalmazás teszteléséhez válassza a **Futtatás lehetőséget.**

   A futtatás befejezése után a tervező megjeleníti a munkafolyamat lépéseit és azok állapotát, hogy áttekinthesse a kimenetet.

1. Az egyes lépésekkel kapcsolatos részletek megtekintéséhez kattintson a lépés címsorára. Ha további információt szeretne áttekinteni egy lépés kimenetéről, válassza **a Nyers kimenetek megjelenítése**lehetőséget.

   ![Üzenet kimenetének tallózása](media/connectors-create-api-mq/browse-message-output.png)

   Itt van néhány minta nyers kimenet:

   ![Üzenet nyers kimenetének tallózása](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Ha **az IncludeInfo értéket** **igaz**értékre állítja, további kimenet jelenik meg:

   ![Tallózás üzenet információkat tartalmaz](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Több üzenet böngészése

Az **Üzenetek tallózása** művelet tartalmaz egy **BatchSize** beállítást, amely jelzi, hogy hány üzenetet szeretne visszaküldeni a várólistából. Ha **a BatchSize** nem rendelkezik értékkel, a rendszer az összes üzenetet visszaadja. A visszaadott kimenet üzenetek tömbje.

1. Kövesse az előző lépéseket, de adja hozzá helyette az **Üzenetek tallózása** műveletet.

1. Ha még nem hozott létre MQ-kapcsolatot, a rendszer kéri a [kapcsolat létrehozását.](#create-connection) Ellenkező esetben alapértelmezés szerint az első korábban konfigurált kapcsolat lesz használatban. Új kapcsolat létrehozásához válassza a **Kapcsolat módosítása**lehetőséget. Vagy válasszon másik kapcsolatot.

1. Adja meg a művelethez szükséges információkat.

1. Mentse és futtassa a logikai alkalmazást.

   Miután a logikai alkalmazás futása befejeződik, itt van néhány minta kimenet a **Tallózás üzenetek** művelet:

   ![Minta "Üzenetek tallózása" kimenet](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Egyetlen üzenet fogadása

Az **Üzenet fogadása** művelet ugyanazokat a bemeneteket és kimeneteket, mint a **Tallózás üzenet** művelet. A **Fogadás üzenet**használatakor az üzenet törlődik a várólistából.

## <a name="receive-multiple-messages"></a>Több üzenet fogadása

Az **Üzenetek fogadása** művelet ugyanazokat a bemeneteket és kimeneteket tartalmaz, mint az **Üzenetek tallózása** művelet. Az **üzenetek fogadása**használatakor az üzenetek törlődnek a várólistából.

> [!NOTE]
> Ha olyan várólistán fut, amely nem tartalmaz üzeneteket, a művelet sikertelen lesz ezzel a kimenettel:
>
> ![MQ "nincs üzenet" hiba](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Üzenet küldése

1. Kövesse az előző lépéseket, de adja hozzá helyette az **Üzenet küldése** műveletet.

1. Ha még nem hozott létre MQ-kapcsolatot, a rendszer kéri a [kapcsolat létrehozását.](#create-connection) Ellenkező esetben alapértelmezés szerint az első korábban konfigurált kapcsolat lesz használatban. Új kapcsolat létrehozásához válassza a **Kapcsolat módosítása**lehetőséget. Vagy válasszon másik kapcsolatot.

1. Adja meg a művelethez szükséges információkat. A MessageType ( **MessageType**) mezőben válasszon érvényes üzenettípust: **Datagram**, **Reply**vagy **Request**

   ![Az "Üzenet küldése művelet" tulajdonságai](media/connectors-create-api-mq/send-message-properties.png)

1. Mentse és futtassa a logikai alkalmazást.

   Miután a logikai alkalmazás futása befejeződik, itt van néhány minta kimenet az **Üzenet küldése** műveletből:

   ![Minta "Üzenet küldése" kimenet](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő Swagger-leírása által leírt műveletek és korlátok technikai részleteiért tekintse át az összekötő [referenciaoldalát.](/connectors/mq/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
